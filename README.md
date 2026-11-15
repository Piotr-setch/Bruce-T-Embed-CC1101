name: Build Bruce Security+ for T-Embed CC1101

on:
  workflow_dispatch:

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Bruce with Security+ support
        uses: actions/checkout@v4
        with:
          repository: Geeblish/firmware
          ref: codex/security-plus-rolling-remotes
          path: firmware
          fetch-depth: 0

      - name: Merge Bruce upstream and PR #3086
        working-directory: firmware
        run: |
          set -e
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git remote add bruce-upstream https://github.com/BruceDevices/firmware.git
          git fetch bruce-upstream dev
          git fetch bruce-upstream pull/3086/head:refs/remotes/bruce-upstream/pr-3086
          git merge --no-edit bruce-upstream/dev
          git merge --no-edit refs/remotes/bruce-upstream/pr-3086

      - name: Build T-Embed CC1101 firmware
        working-directory: firmware
        run: |
          docker compose run --rm platformio_build bash -lc "git config --global --add safe.directory /work && pio run -e lilygo-t-embed-cc1101 -t build-firmware"

      - name: Upload firmware
        uses: actions/upload-artifact@v4
        with:
          name: Bruce-SecurityPlus-PR3086-lilygo-t-embed-cc1101
          path: firmware/Bruce-lilygo-t-embed-cc1101.bin
          if-no-files-found: error
          retention-days: 14

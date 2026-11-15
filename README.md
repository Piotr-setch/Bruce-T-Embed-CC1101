      - name: Apply Bruce PR #3086
        working-directory: firmware
        run: |
          git remote add bruce-upstream https://github.com/BruceDevices/firmware.git
          git fetch bruce-upstream pull/3086/head:refs/remotes/bruce-upstream/pr-3086
          git merge --no-edit refs/remotes/bruce-upstream/pr-3086

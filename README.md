# dipper kernel builder — ReSukiSU selinux_hide status 修复

用途：在 GitHub Actions 上构建带补丁的 dipper (Mi 8) LineageOS 22.2 内核。

- `patches/0001-selinux-hide-live-status.patch`
  修复 ReSukiSU `selinux_hide` 的冻结假 status 页问题：
  - `my_sel_open_handle_status` 对 uid>=10000 改为服务**实时真实** selinux status 页（pass-through）
  - `initialize_fake_status` 记录真实页指针 `real_status_page`
  - 新增一次性日志 `serving LIVE real status page` 便于设备端验证

构建链路（与 Coconutat 官方一致）：
1. Coconutat kernel @ branch `lineage-22.2-ReSukiSU`（RC3 同源）
2. ReSukiSU pinned @ `4c5c8cedaf3cecf297f9b529362e529f506e6891` + 本补丁
3. clang-r547379（README 官方指定工具链）
4. 产出 `Image.gz` / `Image.gz-dtb`

产物处理：用 `kernel_work/tools/repack_boot.py` 将 `Image.gz` 装入原机 boot.img（保留 DTB trailer），
先 `fastboot boot` 测试，再正式刷入。

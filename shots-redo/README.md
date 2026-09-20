# Round 2 step-by-step 截圖(2026-09-20)

每個階段一組,檔名前綴 = 階段。`*-sddcm-tasks` = SDDC Manager Dashboard + Tasks 面板;`*-sddcm-hosts-*` = Hosts 頁;`*-vc-*` = vCenter。
`.log` 是該階段腳本的完整輸出。

| 前綴 | 階段 | 看什麼 |
|---|---|---|
| `00-unstretch.log` | `vcf_stretch.py unstretch --watch` | 57/57 Successful,~20 分 |
| `01-unstretch-running-*` | unstretch 進行中 | Tasks 面板 29% `Disable Stretched Cluster` |
| `02-unstretched-*` | unstretch 完成 | Fault Domains 回到非 stretched;cluster 3 台;esx05-07 `UNASSIGNED_UNUSEABLE` |
| `03-decommission.log` | `DELETE /v1/hosts` esx05-07 | 15/15 Successful |
| `04-commission-validate.log` | `commission --validate-only` | 最後一次 SUCCEEDED(之前每次驗證擋的訊息見 STRETCH-RUNBOOK Round 2) |
| `05-commission.log` / `05-commission-running-*` | `commission` | Tasks 面板 Commissioning host(s) 進行中 |
| `06-commissioned-*` | commission 完成 | Unassigned Hosts 出現 esx05-07 |
| `07-witness-prep.log` / `07-witness-jumbo-ping.log` / `07-witness-*` | `witness_prep.py` | vmk1 140.68 MTU 9000;8972 ping 140.5-7 全通;vCenter 樹多了 esx-witness |
| `08-stretch-validate.log` | `stretch --validate-only` | SUCCEEDED |
| `09-stretch.log` / `09a-stretch-running-*` | `stretch --watch` 第一次 | witness VMkernel/vSwitch 頁;task 到 67/114 Failed(vmk 搬 VDS,esx07) |
| `09b-stretch-failed-*` / `09b-stretch-retry1.log` | retry #1 | 同樣掛 esx07 |
| `09c-stretch-retry2.log` | retry #2 | esx07 過、esx05 掛 → 找到 vmk0 MAC 根因 |
| `09d-stretch-retry3.log` | retry #3 | **Successful 110/110** |
| `10-policy-sftt0.log` / `10-policy-resync-*` | `vsan_policy_sftt0.py` | policy SFTT=1→0、reapply;Fault Domains 已是 Stretched;Resyncing Objects;vSAN Health |
| `11-verify.log` / `11-success-*` | 完成驗證 | `stretched=True`、6 台 ASSIGNED、7 members、witness vmk1 |
| `12-final-*` | resync 歸零後 | vSAN Health 綠、81 objects healthy |

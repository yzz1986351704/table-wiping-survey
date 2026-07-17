# 附件 A · Exylos 基本事实卡

## A.1 主集 `table_spill_cleanup_bimanual`

| 字段 | 值 | 来源 |
|------|-----|------|
| HF 链接 | https://huggingface.co/datasets/ExylosAi/table_spill_cleanup_bimanual | 官方 |
| 定位 | 50-episode public preview / inspection sample | README |
| Episodes | 50 | README / meta |
| Frames | 67,461 | README / meta |
| Duration | 2,248.70 s（约 37.5 min） | README |
| FPS | 30 | README / meta |
| Robot | Bimanual Franka Emika Panda，18D state/action | README |
| Task | 移除干扰物 + 海绵擦液体溢洒 | README |
| RGB | 7 路 · 1280×960 · H.264 · 无音频 | README |
| Mask | 4 路 PNG 实例分割 | README |
| Depth | 无 | README |
| F/T | 无（文档未提供） | README |
| Object poses | sponge / cup / liquid_spill | README |
| Cleanup metric | `liquid_spill.dirty_fraction` ∈ [0,1] | README |
| Success mix | 27 success / 23 failure | README |
| Failure reasons | 20 cleanup_incomplete / 3 operator_abort | README |
| Frozen frames | 8,181 | README |
| Format | LeRobot Parquet + MP4 + PNG | README |
| License | Apache 2.0 | dataset card |
| Split | `train: 0:50` | meta/info.json |

### 本地可复现加载（本仓库现状）

| 路径 | 内容 | 可做什么 |
|------|------|----------|
| `datasets/exylos_table_spill_50ep_meta/` | README、annotations.json、meta/ | 查 schema、成败与阶段元数据 |
| `datasets/exylos_table_spill_50ep_sample/` | 至少 1 ep parquet + 对应视频 | 冒烟读 dirty_fraction / 播视频 |
| 完整 50 ep 媒体 | 未完整落盘（需 HF 下载，官方约 22.5 GB 量级） | — |

---

## A.2 扩展集（带过）`…_rgbd_segmentation_poses`

| 字段 | 值 | 来源 |
|------|-----|------|
| HF 链接 | https://huggingface.co/datasets/ExylosAi/table_spill_cleanup_bimanual_rgbd_segmentation_poses | 官方 |
| Episodes | 5 | README |
| Frames | 6,736 | README |
| 相对主集增量 | +4 路 depth（float32 `.npy`，米） | README |
| Success mix | 3 success / 2 cleanup_incomplete | README |
| License | Apache 2.0 | README |
| 本地 | `datasets/exylos_table_spill_rgbd_5ep/`（含 README、annotations、至少 1 ep parquet） | 仓库核对 |

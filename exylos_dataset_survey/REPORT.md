# Exylos 双臂台面溢洒清理数据集调研报告

- **主集**：[ExylosAi/table_spill_cleanup_bimanual](https://huggingface.co/datasets/ExylosAi/table_spill_cleanup_bimanual)
- **推荐阅读**：[网页版 index.html](index.html)
- **证据口径**：仅官方文档与随包元数据；未公开项见 [attachments/reproducibility_boundary.md](attachments/reproducibility_boundary.md)

---

## 1. 基本事实卡

### 规模速览

| 项 | 主集（50 ep preview） |
|----|----------------------|
| Episodes / Frames / FPS | 50 · 67,461 · 30 |
| 本体 | 双臂 Franka Emika Panda（各 7-DoF + 平行夹爪） |
| State / Action | 各 18 维 |
| 格式 | LeRobot：Parquet + MP4 + PNG |

### 任务定义

移开遮挡/干扰物（如需要），再用海绵擦掉台面液体溢洒。

| 概念 | 官方定义 |
|------|----------|
| 清洁质量门槛 | 终端帧 `liquid_spill.dirty_fraction ≤ 0.01`（剩余污渍 ≤ 原始的 1%） |
| 记录成功 | `next.success`（仅末帧）及 `annotations.json` 中的 `success` / `task_success` |
| 权威说明 | 记录成功还考虑失败语义（如 `operator_abort`）；**以 `next.success` 与 annotations 为准**，不能只用 dirty_fraction |

### 观测与动作接口

| 通道 | 主集 |
|------|------|
| RGB | 7 路：front / left / left_cam_45 / right / top / wrist_l / wrist_r；1280×960；H.264；30 FPS |
| Depth | **无**（仅 RGBD 扩展集有 4 路） |
| Mask | 4 路 PNG 实例分割（label 0–6，含 liquid_spill=4） |
| State / Action | 各 18 维 float32：左右臂各 7 关节 + 2 finger |
| Force/Torque | **无** |
| Object state | 每帧 `sponge` / `cup` / `liquid_spill`（含 dirty_fraction） |

RGBD 扩展集：5 ep，在主集模态上增加 4 路 float32 depth（`.npy`）。

---

## 2. 数据从哪来（采集链路）

官方将本样本定位为介于「真机」与「纯仿真」之间的第三条路径：

```text
人在 VR 虚拟环境中完成任务（意图、时序、双臂协调、纠错）
        ↓
retarget → 虚拟双臂 Franka Panda
        ↓
程序化扩展 + 视觉域随机
        ↓
LeRobot 打包
```

| 角色 | 贡献（有据） |
|------|----------------|
| **人** | VR 中完成任务示范；提供意图、时序、协调与纠错行为 |
| **系统/仿真** | 映射到 Panda；物理一致变体扩展；渲染多视角；写出特权状态（如 dirty_fraction）、mask、poses、阶段标注等 |

场景与机器人画面为**仿真合成**，不是真机实验室实拍。

**未公开**：VR 硬件型号、retarget 算法细节、扩展参数范围、本 HF sample 所用仿真引擎版本、seed↔variant 对应表。

---

## 3. 标注与指标

### 3.1 阶段枚举（含义与顺序）

官方给出阶段名；典型成功 episode 中操作链大致如下（双臂可时间重叠）。`clean_swipe_pass` 嵌在 `clean_surface` 时间窗内。

| # | 阶段 | 含义 | 类型 |
|---|------|------|------|
| 1 | `approach` | 接近目标物体 | 主流程 |
| 2 | `grasp` | 抓取 | 主流程 |
| 3 | `transport` | 搬运 / 移动 | 主流程 |
| 4 | `place` | 放置 / 松开 | 主流程 |
| 5 | `retract` | 收回手臂 | 主流程 |
| 6 | `clean_surface` | 台面清洁总时段（父阶段） | 主流程 · 擦拭 |
| 7 | `clean_swipe_pass` | 单次擦拭划过（子阶段） | 主流程 · 擦拭 |

每段含：`phase`、`frame_range`、`execution_quality`、`task_alignment`、`hand`，可选 `notes`。

### 3.2 清洁度 `dirty_fraction`

| 项 | 内容 |
|----|------|
| 是什么 | `liquid_spill` 上的 `remaining_dirty_fraction`，∈ [0, 1] |
| 语义 | 相对**原始污渍面积**的剩余比例 |
| 用途（有据） | 终端清洁质量门槛；与成功标签一起支撑可审计结局 |
| **特权量？** | **是**：仿真任务状态，非真机传感器读数 |
| **真机部署是否可用？** | **通常否**：在线控制一般不读该字段 |

另有 `scores`（D1–D7）/ `derived` / grade：**公式未公开**，与 dirty_fraction 不是同一套清洁度定义。

---

## 4. 对你方的可用性判断

| 用法 | 判断 | 说明 |
|------|------|------|
| 直接当真机同分布预训练数据 | 弱 / 需谨慎 | 场景与本体均为仿真 Panda |
| 参考 LeRobot 格式与多模态 schema | **强** | 适合做格式与标注设计模板 |
| 借用评价思想 | **强** | 客观剩余污渍门槛 + 失败原因 + 阶段；真机不可读 dirty_fraction |

**结论**：更适合作为格式 / 标注 / 评价语义参考，以及可选的仿真侧模仿学习试验；不宜默认当作真机可直接迁移的同款数据。

---

## 5. 可复现边界

详见 [attachments/reproducibility_boundary.md](attachments/reproducibility_boundary.md)。

**已公开**：目录结构、parquet 列、相机与 mask label、state/action 顺序、dirty 语义与 0.01 门槛、阶段名枚举、加载示例。

**未公开**：污渍面积计算公式、VR→Panda retarget、域随机参数表、phase 自动切分算法、D1–D7 评分公式、全量包规模与 seed 映射。

---

## 6. 和同类对比

详见 [attachments/comparison_table.md](attachments/comparison_table.md)。

- vs **Unitree G1 Wipe**：Exylos = 仿真人种 + 富标注；Unitree = 真机 AVP 遥操作擦桌子水。
- vs **ForceFlow**：Exylos 无力觉；ForceFlow = 真机接触 + F/T。
- vs **DROID / ALOHA / RoboCOIN wipe**：多为真机；Exylos 以合成 preview + 特权清洁指标见长。

---

## 7. 风险

| 风险 | 说明 |
|------|------|
| sim2real | 官方自标 synthetic；真机需验证 |
| 污渍可观测性 | 真机清水渍仅靠 RGB 难稳定度量 |
| 指标无法上真机 | 真机测试不能依赖 dirty_fraction 判停/判成功 |
| preview 偏差 | 50 ep 未必代表全量包 |
| 接触信息缺失 | 公开包无 F/T |
| 本体缺口 | Panda 轨迹不能直接当其它机器人同构动作 |

---

## 参考来源

1. Hugging Face：`ExylosAi/table_spill_cleanup_bimanual` README / dataset card
2. Hugging Face：`ExylosAi/table_spill_cleanup_bimanual_rgbd_segmentation_poses` README
3. 本地：`datasets/exylos_table_spill_50ep_meta/`
4. 本地冒烟：`datasets/exylos_table_spill_50ep_sample/`
5. 对比集本地 README / meta；RoboCOIN 见本仓库既有调研文档

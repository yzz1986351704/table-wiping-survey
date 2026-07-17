# 附件 C · 同类擦拭相关数据集对比

证据：各数据集 HF README / 本地随包 meta；RoboCOIN 规模来自本仓库既有调研文档引用的公开信息（完整下载需授权）。未写明的格子标「未公开 / 文档未写」。

| 维度 | Exylos spill（主集） | Exylos RGBD（扩展） | Unitree G1 Wipe | ForceFlow（擦拭相关） | DROID wipe table | ALOHA mobile wipe wine | RoboCOIN wipe/clean 子集 |
|------|---------------------|---------------------|-----------------|----------------------|------------------|------------------------|---------------------------|
| 链接 | [HF](https://huggingface.co/datasets/ExylosAi/table_spill_cleanup_bimanual) | [HF](https://huggingface.co/datasets/ExylosAi/table_spill_cleanup_bimanual_rgbd_segmentation_poses) | [HF](https://huggingface.co/datasets/unitreerobotics/G1_Dex1_Wipe_Table) | [HF](https://huggingface.co/datasets/JokerESC/ForceFlow) | [HF](https://huggingface.co/datasets/jellyho/droid_wipe_table) | [HF](https://huggingface.co/datasets/lerobot/aloha_mobile_wipe_wine) | [RoboCOIN](https://huggingface.co/RoboCOIN) |
| Episodes | 50 | 5 | 200 | clean_whiteboard 100；clean_vase 50（全集 7 任务共 350） | 20 | 50 | 文档记载 wipe 相关子集合计约 1176 ep 量级（分平台） |
| 真机/仿真 | 仿真合成 + VR 人种 | 同左 | 真机 | 真机 | 真机（Franka） | 真机（Mobile ALOHA） | 真机多平台 |
| 采集方式 | VR → retarget → 程序化扩展 | 同左 | AVP 遥操作 | SpaceMouse + xArm6 | 文档未细写（LeRobot/DROID 系） | Mobile ALOHA 体系 | 多平台遥操作 + RTML 等标注 |
| 本体 | 双臂 Panda | 同左 | G1 双臂 7-DoF | xArm6 | Franka | ALOHA 移动双臂 | Galaxea / AgileX / Realman 等 |
| FPS | 30 | 30 | 30 | 文档强调 F/T 高频；视觉在 zarr 中 | 10 | 50 | 约 30–50（既有调研） |
| RGB | 7× 1280×960 | 7× | 腕+头，640×480 | 腕+固定，240×320（JPEG in zarr） | side + wrist 等 | 多视角（含 cam_high 等） | 多视角 |
| Depth | 无 | 有（4 路） | 文档未写为标配 depth 流 | 文档未写 depth 为标配 | 视具体字段 | 视具体字段 | 视平台 |
| F/T | 无 | 无 | 文档未写 | **有**（force / delta_force） | 文档未写为本子集核心 | 文档未写 | 视平台 |
| Mask / poses | 有 mask + object_poses | 同左 + depth | 文档未写同等 dense mask/pose | 无（本 README 结构） | 未作为卖点写出 | 未作为卖点写出 | 视标注方案 |
| 清洁/成功指标 | dirty_fraction≤0.01 + failure 语义 | 同左（本 sample 与 success 对齐） | 任务描述擦桌子水；客观面积公式未写 | 接触力跟踪导向；非 dirty_fraction | 未写 Exylos 式 dirty | 未写 Exylos 式 dirty | 原子动作/任务标注体系 |
| 格式 | LeRobot Parquet+MP4+PNG | +NPY depth | LeRobot | Zarr | LeRobot | LeRobot | 平台相关；需授权 |
| License | Apache-2.0 | Apache-2.0 | Apache-2.0 | MIT | Apache-2.0 | MIT | 需按 HF 组织条款 |
| 对擦拭的独特价值 | 特权清洁量、阶段、失败语义、双臂仿真样本 | 富模态检查 | 真机人形擦桌子 | **力觉擦拭** | 真机小样本 wipe | 移动双臂 wipe | 大规模真机 wipe/clean 子集 |

### 读表要点（有据）

1. **Exylos 不是真机擦桌子数据**；与 Unitree / ForceFlow / DROID / ALOHA / RoboCOIN 的迁移假设不同。  
2. **唯一在公开 README 中把「剩余污渍比例」写成客观成功门槛的**，在本对比表范围内是 Exylos。  
3. **需要力控擦拭研究时**，ForceFlow 比 Exylos 更直接相关。  
4. **需要真机分布时**，优先 Unitree / RoboCOIN / DROID / ALOHA，而非 Exylos preview。

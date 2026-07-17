# 附件 D · 可复现边界清单

证据口径：**A = 有据可查（可按文档复现加载/解读）** · **U = 未公开**

## D.1 已公开（A）

| 条目 | 说明 |
|------|------|
| 任务文字定义 | 移干扰物 + 海绵擦溢洒 |
| 目录与文件布局 | data/ videos/ meta/ annotations.json |
| Parquet 列集合 | 主集 21 列；扩展集 25 列（+depth） |
| State/action 维数与电机名 | 18D，左右 Panda 关节顺序 |
| 相机列表与分辨率/FPS/编码 | 7 RGB，1280×960，30 FPS，H.264 |
| Mask 四路与 label 0–6 | README 表 |
| object_poses 三对象与字段 | position / orientation / velocity / dirty_fraction |
| dirty_fraction 语义与门槛 | remaining ratio；终端 ≤ 0.01 |
| 记录成功权威字段 | next.success、annotations |
| 主集成败计数与 failure_reason | 27/20/3 |
| 阶段名枚举 | approach…task_attempt |
| 官方 Python 加载示例 | 读 parquet / mask / terminal dirty |
| 许可证 | Apache 2.0 |
| 采集链路定性描述 | VR 人种 → retarget → 域随机扩展 → 打包 |

## D.2 未公开（U）

| 条目 | 影响 |
|------|------|
| dirty_fraction 面积如何计算 | 无法在自建仿真中 1:1 复现同款数值器 |
| 是否由 mask label=4 导出 | 不能用像素比冒充官方指标 |
| VR 设备与采集端频率 | 无法复现采集台架 |
| retarget 算法与标定 | 无法复现人→Panda 映射 |
| 域随机参数范围与每 seed 扩展倍数 | 无法复现同分布变体 |
| 本 sample 仿真引擎/版本 | Isaac/Newton 为公司表述，未绑定本包 |
| phase 边界如何自动产生 | 无法复现同款阶段机 |
| D1–D7 / grade 评分公式 | 只能读分，不能复现打分器 |
| seed↔episode 对应与全量包规模 | preview 外不可知 |
| 力/接触模型细节 | 公开包无力觉，接触物理细节未给 |

## D.3 本仓库可核对但不等于「机制公开」的项

| 核对项 | 含义 |
|--------|------|
| annotations 中 phase 字段结构 | 发布数据长什么样，不是算法源码 |
| 本地 1 ep dirty 曲线 | 验证字段可读，不是公式推导 |
| 成败计数与 README 一致 | 元数据一致性检查 |

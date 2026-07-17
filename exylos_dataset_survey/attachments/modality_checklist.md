# 附件 B · 观测 / 动作 / 标注字段清单

主集为准。来源：HF README、`meta/info.json`。

## B.1 核心轨迹字段

| 字段 | 形状/类型 | 含义 |
|------|-----------|------|
| `observation.state` | float32[18] | 双臂关节 + finger |
| `action` | float32[18] | 同序控制/动作 |
| `timestamp` | — | 时间戳 |
| `frame_index` / `episode_index` / `index` / `task_index` | — | 索引 |
| `next.done` | bool | 仅末帧 true |
| `next.success` | bool | 仅成功 episode 末帧 true |

### 电机顺序（state / action 共用）

```
left_panda_joint1..7, left_panda_finger_joint1..2,
right_panda_joint1..7, right_panda_finger_joint1..2
```

## B.2 RGB

| 字段 |
|------|
| `observation.images.front_cam` |
| `observation.images.left_cam` |
| `observation.images.left_cam_45` |
| `observation.images.right_cam` |
| `observation.images.top_cam` |
| `observation.images.wrist_cam_l` |
| `observation.images.wrist_cam_r` |

## B.3 Mask

| 字段 | 存储 |
|------|------|
| `observation.masks.top_cam_seg` | PNG 路径 + label_map |
| `observation.masks.left_cam_45_seg` | 同上 |
| `observation.masks.wrist_cam_l_seg` | 同上 |
| `observation.masks.wrist_cam_r_seg` | 同上 |

### Mask label map

| ID | Class |
|----|-------|
| 0 | background |
| 1 | left_franka_panda |
| 2 | right_franka_panda |
| 3 | sponge |
| 4 | liquid_spill |
| 5 | cup |
| 6 | table_surface |

官方**未声明** `dirty_fraction` = mask 像素比。

## B.4 Object poses

每帧列表三项：`sponge`、`cup`、`liquid_spill`。

| 子字段 | 说明 |
|--------|------|
| `object_id` | 字符串 |
| `position` | float32[3]，米，右手系世界坐标 |
| `orientation` | float32[4]，quaternion_xyzw；liquid_spill 可 null |
| `velocity_linear` | float32[3]，m/s，有限差分；可 null |
| `dirty_fraction` | float32 [0,1]；remaining_dirty_fraction；sponge/cup 常为 null |

## B.5 Depth（仅扩展集）

| 字段 | 备注 |
|------|------|
| `observation.depths.top_cam_seg` | float32 `.npy`，米 |
| `observation.depths.left_cam_45_seg` | 同上 |
| `observation.depths.wrist_cam_l_seg` | 同上 |
| `observation.depths.wrist_cam_r_seg` | 同上 |

## B.6 Episode 级 annotations.json

| 字段 | 说明 |
|------|------|
| `episode_id` | 如 episode_000000 |
| `success` / `task_success` | 布尔 |
| `failure_reason` | 如 none / cleanup_incomplete / operator_abort |
| `duration_sec` | 时长 |
| `frozen_frames` | 冻结帧计数 |
| `phase_annotations` | 阶段列表 |
| `scores` | D1–D7（公式未公开） |
| `derived` | efficiency / precision / confidence / composite_score / episode_grade |
| `raw_measurements` | path_ratio、误差与运动诊断等 |
| `scorer_id` | 如 auto_v1.1.0 |

### phase 记录字段

`phase` · `frame_range` · `execution_quality` · `task_alignment` · `hand` · 可选 `notes`

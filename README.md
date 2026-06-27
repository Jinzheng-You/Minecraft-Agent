# Minecraft Agent

本项目是《强化学习》课程大作业，主题为基于 Double DQN 的 Minecraft 智能体训练与演示。项目采用“Python 仿真训练 + Mineflayer 真实游戏执行”的结构：先在简化仿真环境中训练资源采集和工具制作策略，再在 Minecraft Java 世界中用 bot 执行对应任务。

## 主要功能

- 使用 Double DQN 训练技能级 Minecraft 智能体。
- 支持经验回放、目标网络、epsilon-greedy、动作掩码、课程学习和断点保存。
- 支持 5 个核心任务：
  - 采集木头 `wood`
  - 制作木镐 `wooden_pickaxe`
  - 制作石镐 `stone_pickaxe`
  - 制作铁镐 `iron_pickaxe`
  - 制作钻石镐 `diamond_pickaxe`
- 输出训练曲线、评估图、最终路径效果图和模型文件。
- Mineflayer bot 可连接 PCL2 开放的局域网世界，进行真实游戏演示。

## 目录结构

```text
Minecraft-Agent/
├─ mineflayer/
│  ├─ package.json
│  ├─ package-lock.json
│  └─ src/
│     ├─ index.js          # bot 入口和聊天指令
│     ├─ runner.js         # 自动重启
│     ├─ bridge.js         # 通信服务
│     └─ skillManager.js   # Minecraft 技能执行
├─ python/
│  ├─ train_sim.py         # 仿真训练
│  ├─ evaluate_sim.py      # 模型评估
│  ├─ plot_sim_results.py  # 绘制训练曲线
│  ├─ results_dashboard.py # Streamlit 展示页面
│  ├─ env/                 # Gymnasium 仿真环境
│  ├─ agent/               # Double DQN 模型和经验池
│  └─ results/             # 最终模型和实验结果
├─ report_materials/       # 报告用图表和路径图
├─ README.md
└─ LICENSE
```

## 环境准备

```bat
cd /d D:\PythonProject\RL_Final\RLFinal\Minecraft-Agent
conda activate D:\PythonProject\RL_Final\.conda
```

Mineflayer 依赖安装：

```bat
cd /d D:\PythonProject\RL_Final\RLFinal\Minecraft-Agent\mineflayer
npm install
```

Python 代码会自动检测 GPU：

```python
torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

## 仿真训练

```bat
cd /d D:\PythonProject\RL_Final\RLFinal\Minecraft-Agent
D:\PythonProject\RL_Final\.conda\python.exe python\train_sim.py --episodes 1800 --curriculum --difficulty normal --max-steps 140 --action-mask --eval-action-mask --eval-task all --out-dir python\results\sim_ddqn_curriculum_mask_v2
```

训练结果保存在：

```text
python\results\sim_ddqn_curriculum_mask_v2
```

其中常用文件包括：

```text
best_model.pt
final_model.pt
metrics.csv
reward_curve.png
success_rate_curve.png
episode_steps_curve.png
invalid_actions_curve.png
training_overview.png
```

## 模型评估

```bat
cd /d D:\PythonProject\RL_Final\RLFinal\Minecraft-Agent
D:\PythonProject\RL_Final\.conda\python.exe python\evaluate_sim.py --model python\results\sim_ddqn_curriculum_mask_v2\best_model.pt --episodes 100 --task all --difficulty hard --max-steps 160 --action-mask
```

## 绘图和展示

重新绘制训练曲线：

```bat
D:\PythonProject\RL_Final\.conda\python.exe python\plot_sim_results.py --metrics python\results\sim_ddqn_curriculum_mask_v2\metrics.csv --out-dir python\results\sim_ddqn_curriculum_mask_v2
```

启动 Streamlit 页面：

```bat
streamlit run python\results_dashboard.py
```

最终路径效果图已保存在 `report_materials\final_path_effect.png`。

## 真实游戏演示

1. 用 PCL2 启动 Minecraft Java。
2. 进入单人世界并开放局域网。
3. 记下局域网端口，例如 `11111`。
4. 启动 Mineflayer bot：

```bat
cd /d D:\PythonProject\RL_Final\RLFinal\Minecraft-Agent\mineflayer
set MC_HOST=localhost
set MC_PORT=11111
set MC_VERSION=1.20.1
set MC_USERNAME=DQN_bot
set ENABLE_STUCK_DETECTOR=0
set SPAWN_STABILIZE=0
set SAFE_SPAWN_CHECK=0
set SKILL_TIMEOUT_MS=900000
D:\PythonProject\RL_Final\.conda\node.exe src\runner.js
```

如果游戏版本是 1.21.1：

```bat
set MC_VERSION=1.21.1
```

## 游戏内指令

```text
!help
!where
!inventory
!dropall
!surface
!task wood
!task wooden_pickaxe
!task stone_pickaxe
!task iron_pickaxe
!task diamond
!task diamond_pickaxe
!pause
!resume
!reset
```

推荐演示顺序：

```text
!task wood
!task wooden_pickaxe
!task stone_pickaxe
!task iron_pickaxe
!task diamond_pickaxe
```

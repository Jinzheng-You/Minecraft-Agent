# Minecraft HRL Agent

这是当前大作业主项目。项目采用“Python 仿真训练 + Mineflayer 真实游戏演示”的结构。

## 功能简介

- Python 中训练技能级 Minecraft 智能体。
- 算法使用 Double DQN，支持经验回放、目标网络、动作掩码、课程学习和断点保存。
- 仿真任务包括：
  - 采集 wood
  - 制作 wooden pickaxe
  - 制作 stone pickaxe
  - 制作 iron pickaxe
  - 挖 diamond
  - 制作 diamond pickaxe
- Mineflayer 可连接 PCL2 或 Paper 本地服务器，在真实游戏中执行任务。

## 目录结构

```text
Minecraft-HRL-Agent/
├─ mineflayer/
│  ├─ package.json
│  └─ src/
│     ├─ index.js          # bot 入口和聊天指令
│     ├─ runner.js         # 自动重启
│     ├─ bridge.js         # Python 与 Mineflayer 通信
│     └─ skillManager.js   # 技能执行
├─ python/
│  ├─ train_sim.py         # 仿真训练
│  ├─ evaluate_sim.py      # 模型评估
│  ├─ plot_sim_results.py  # 绘制曲线
│  ├─ results_dashboard.py # Streamlit 展示
│  ├─ env/                 # Gymnasium 环境
│  ├─ agent/               # Double DQN / PPO 相关代码
│  └─ results/             # 模型和训练结果
├─ scripts/
│  └─ run_training_stack.py # 启动 Paper + Mineflayer
├─ minecraft-server/        # Paper 1.20.1 服务端
└─ README.md
```

## 环境

```bat
cd /d D:\PythonProject\RL_Final\Minecraft-HRL-Agent
conda activate D:\PythonProject\RL_Final\.conda
```

Python 会自动使用 GPU：

```python
torch.device("cuda" if torch.cuda.is_available() else "cpu")
```

## 仿真训练

推荐训练命令：

```bat
D:\PythonProject\RL_Final\.conda\python.exe python\train_sim.py --episodes 1800 --curriculum --difficulty normal --max-steps 140 --action-mask --eval-action-mask --eval-task all --out-dir python\results\sim_ddqn_curriculum_mask_v2
```

当前推荐保留的模型目录：

```text
python\results\sim_ddqn_curriculum_mask_v2
```

## 评估

```bat
D:\PythonProject\RL_Final\.conda\python.exe python\evaluate_sim.py --model python\results\sim_ddqn_curriculum_mask_v2\best_model.pt --episodes 100 --task all --difficulty hard --max-steps 160 --action-mask
```

## 绘图和网页展示

```bat
D:\PythonProject\RL_Final\.conda\python.exe python\plot_sim_results.py --metrics python\results\sim_ddqn_curriculum_mask_v2\metrics.csv --out-dir python\results\sim_ddqn_curriculum_mask_v2
streamlit run python\results_dashboard.py
```

## 真实游戏演示

先在 PCL2 中启动 Minecraft，并开放局域网，记下端口号。然后运行：

```bat
cd /d D:\PythonProject\RL_Final\Minecraft-HRL-Agent\mineflayer
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

如果你的游戏是 1.21.1：

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

建议按顺序测试：

```text
!task wood
!task wooden_pickaxe
!task stone_pickaxe
!task iron_pickaxe
!task diamond_pickaxe
```

## 本地 Paper 服务端

如果不使用 PCL2，可以启动项目内 Paper：

```bat
cd /d D:\PythonProject\RL_Final\Minecraft-HRL-Agent
D:\PythonProject\RL_Final\.conda\python.exe scripts\run_training_stack.py --mc-port 25565 --bridge-port 8765 --username DQN_bot --memory 3G
```

重置地图：

```bat
D:\PythonProject\RL_Final\.conda\python.exe scripts\run_training_stack.py --reset-world
```

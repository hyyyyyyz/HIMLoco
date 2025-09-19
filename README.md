# 新疆大学创新实验室一些记录
## 一、对 reward 的解释（可能会有一些解释的不足，请在issue提交建议，感谢理解）

| Number 序号 |        Reward Functions 奖励函数        | Descriptions 简介                                            | Function Purpose 函数功能                                    | Parameter adjustment suggestions 调参建议 |
| :---------: | :-------------------------------------: | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------- |
|      1      |  def _reward_tracking_lin_vel(*self*)   | 追踪xy轴线速度的奖励函数，采用以e为底的指数函数，实际线速度与期望线速度越接近，reward越接近1，反之越接近0。 | 控制机器人实际xy轴线速度按照人为规定的执行。                 |                                           |
|      2      |  def _reward_tracking_ang_vel(*self*)   | 追踪偏航角yaw角速度的奖励函数，采用以e为底的指数函数，实际角速度与期望角速度越接近，reward越接近1，反之越接近0。 | 控制机器人实际偏航角yaw角速度按照人为规定的执行。            |                                           |
|      3      |      def _reward_lin_vel_z(*self*)      | 对z轴线速度的观测，采用平方的函数，z轴上的线速度绝对值越大，总reward越大。 | 控制机器人的运动平稳，控制Z轴线速度，可以让机器人不要过分震荡或跳跃。 |                                           |
|      4      |     def _reward_ang_vel_xy(*self*)      | 对roll和pitch两个轴角速度的观测，采用平方的函数，两轴角速度越大绝对值越大，总reward越大。 | 控制机器人的运动平稳，控制roll和pitch两轴角速度，可以让机器人不要过分翻滚。 |                                           |
|      5      |     def _reward_orientation(*self*)     | 对机体坐标系水平面透影的引力分量观测，通过平方的函数，越大表示越倾斜，从而reward越大。 | 控制机器人在不同地形通过时的机体平稳性。                     |                                           |
|      6      |       def _reward_dof_acc(*self*)       | 对关节dof的加速的观测，通过last和now两次关节速度的差对时间进行微分，得到加速度，差异越大从而reward越大。 | 鼓励机器人运动时机器人关节的平滑控制，避免机械冲撞和震动。   |                                           |
|      7      |     def _reward_joint_power(*self*)     | 对关节的能耗功率的观测，通过公式关节速度与力矩的乘积的出功率能耗，乘积越大从而reward越大。 | 控制机器人的节能控制，降低电机的负载。                       |                                           |
|      8      |     def _reward_base_height(*self*)     | 对机器人本体高度的观测，通过当前机体高度与config中规定的高度进行一个差值求平方，差值越大，reward越大。 | 控制机器人运动时的机体高度，实现人为限定的要求。             |                                           |
|      9      |   def _reward_foot_clearance(*self*)    | 对机器人运动时的抬腿高度与速度的乘积进行观测，乘积越大，reward越大。 | 控制机器人运动时的抬腿高度，保证机器人的越障能力，以抬腿高度以及速度乘积形式进行观测是为了避免机器人静止时的抬腿高度被误奖励。 |                                           |
|     10      |     def _reward_action_rate(*self*)     | 对连续动作的一个观测，通过计算连续动作的一个差平方，差越大，reward越大。 | 控制机器人动作连续之间的平滑，减少动作的跳变。               |                                           |
|     11      |     def _reward_smoothness(*self*)      | 同上类似，但是是一个进一步的观测，对两组相邻连续动作之间的观测，差值越大，reward越大。 | 控制机器人动作连续之间的平滑，避免动作的二阶差。             |                                           |
|     12      |       def _reward_torques(*self*)       | 对机器人关节力矩的观测，力矩越大，reward越大。               | 控制机器人的关节力矩，避免大力矩大动作的震荡。               |                                           |
|     13      |       def _reward_dof_vel(*self*)       | 对机器人关节速度的观测，关节速度越大，reward越大。           | 控制机器人的关节速度，保障关节的运动平缓。                   |                                           |
|     14      |      def _reward_collision(*self*)      | 计数（或惩罚）被列为 penalise 的 body 上的接触事件（当接触力 norm > 0.1） | 惩罚不希望发生的接触（例如躯干与地面/障碍物接触），避免卡住或损伤。 |                                           |
|     15      |     def _reward_termination(*self*)     | 对envs被reset但是不是因为timeout的返回True。                 | 对导致 episode 提前终止的失败赋固定惩罚（如跌倒、严重碰撞等），这是一个强约束以避免不良策略。 |                                           |
|     16      |   def _reward_dof_pos_limits(*self*)    | 对关节位置超出软限制范围（超下限或超上限）进行惩罚，惩罚与超出量成正比。 | 避免关节撞到机械极限，保护机械结构。                         |                                           |
|     17      |   def _reward_dof_vel_limits(*self*)    | 对关节速度的观测，实际关节速度超出阈值越多，reward越大，clip 最大值 1 用来避免单步惩罚爆炸。 | 防止机器人高速旋转关节损伤或不稳定。                         |                                           |
|     18      |    def _reward_torque_limits(*self*)    | 对机器人关节扭矩的观测，实际扭矩越接近或者超出扭矩极限，reward就会越大。 | 鼓励电机在不超出扭矩范围下安全运行，保护电机。               |                                           |
|     19      |        def _reward_feet_air_time        | 对机器人足端腾空时间的一个观测，在有速度命令时，合适的腾空时间给予更大的reward。 | 鼓励合适的步幅/摆腿（更明显的抬脚），避免短小无效摆动。      |                                           |
|     21      |           def _reward_stumble           | 对机器人足端接触力的观测，横向接触力远大于纵向接触力，返回True。 | 避免机器人脚碰到竖直障碍物侧面，避免不正常接触。             |                                           |
|     22      |     def _reward_stand_still(*self*)     | 对没有移动命令时，关节的位置进行观测，实际关节位置与默认关节位置差异越大，reward越大。 | 促使机器人站立的稳定性，避免在没有移动命令时，关节的乱动。   |                                           |
|     23      | def _reward_feet_contact_forces(*self*) | 对机器人足端接触力的一个观测，超出量越大，reward 越大。      | 控制机器人足端着地的接触力，保护机器人温和着地。             |                                           |




## Learning-based Locomotion Control from OpenRobotLab
This repository contains learning-based locomotion control research from OpenRobotLab, currently including [Hybrid Internal Model](/projects/himloco/README.md) & [H-Infinity Locomotion Control](/projects/h_infinity/README.md).
## 🔥 News
- [2024-04] Code of HIMLoco is released.
- [2024-04] We release the [paper](https://arxiv.org/abs/2404.14405) of H-Infinity Locomotion Control. Please check the :point_right: [webpage](https://junfeng-long.github.io/HINF/) :point_left: and view our demos! :sparkler:
- [2024-01] HIMLoco is accepted by ICLR 2024.
- [2023-12] We release the [paper](https://arxiv.org/abs/2312.11460) of HIMLoco. Please check the :point_right: [webpage](https://junfeng-long.github.io/HIMLoco/) :point_left: and view our demos! :sparkler:

## 📝 TODO List
- \[x\] Release the training code of HIMLoco, please see `rsl_rl/rsl_rl/algorithms/him_ppo.py`.
- \[ \] Release deployment guidance of HIMLoco.
- \[ \] Release the training code of H-Infinity Locomotion Control.
- \[ \] Release deployment guidance of H-Infinity Locomotion Control.

## 📚 Getting Started

### Installation

We test our codes under the following environment:

- Ubuntu 20.04
- NVIDIA Driver: 525.147.05
- CUDA 12.0
- Python 3.7.16
- PyTorch 1.10.0+cu113
- Isaac Gym: Preview 4

1. Create an environment and install PyTorch:

  - `conda create -n himloco python=3.7.16`
  - `conda activate himloco`
  - `pip3 install torch==1.10.0+cu113 torchvision==0.11.1+cu113 torchaudio==0.10.0+cu113 -f https://download.pytorch.org/whl/cu113/torch_stable.html`

2. Install Isaac Gym:
  - Download and install Isaac Gym Preview 4 from https://developer.nvidia.com/isaac-gym
  - `cd isaacgym/python && pip install -e .`

3. Clone this repository.

  - `git clone https://github.com/OpenRobotLab/HIMLoco.git`
  - `cd HIMLoco`


4. Install HIMLoco.
  - `cd rsl_rl && pip install -e .`
  - `cd ../legged_gym && pip install -e .`

**Note:** Please use legged_gym and rsl_rl provided in this repo, we have modefications on these repos.

### Tutorial

1. Train a policy:

  - `cd legged_gym/legged_gym/scripts`
  - `python train.py`

2. Play and export the latest policy:
  - `cd legged_gym/legged_gym/scripts`
  - `python play.py`


## 🔗 Citation

If you find our work helpful, please cite:

```bibtex
@inproceedings{long2023him,
  title={Hybrid Internal Model: Learning Agile Legged Locomotion with Simulated Robot Response},
  author={Long, Junfeng and Wang, ZiRui and Li, Quanyi and Cao, Liu and Gao, Jiawei and Pang, Jiangmiao},
  booktitle={The Twelfth International Conference on Learning Representations},
  year={2024}
}

@misc{long2024hinf,
  title={Learning H-Infinity Locomotion Control}, 
  author={Junfeng Long and Wenye Yu and Quanyi Li and Zirui Wang and Dahua Lin and Jiangmiao Pang},
  year={2024},
  eprint={2404.14405},
  archivePrefix={arXiv},
}
```

## 📄 License
<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/80x15.png" /></a>
<br />
This work is under the <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

## 👏 Acknowledgements
- [legged_gym](https://github.com/leggedrobotics/legged_gym): Our codebase is built upon legged_gym.

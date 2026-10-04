# 实体寻路机制
## 寻路基本框架
### PathNavigation
每个实体生成时会创建一个 PathNavigation 对象，负责管理整个寻路机制
* 创建 PathNavigation 对象时，会创建 PathFinder 对象
* tick 的时候会定时重新计算道路
* moveTo 会创建一条到达目标位置的 Path，同时修剪 Path
* 如果是骑乘在某个生物上，则使用被骑乘生物的导航器
#### GroundPathNavigation
陆地导航器，可以设置是否避开太阳
#### WallClimberNavigation
蜘蛛的爬墙导航，在 tick 的时候如果 Path 已经到达尽头，则设置MoveControl的目标
### PathFinder
PathNavigation 创建新 Path 时通过 PathFinder 返回一条 Path
* 包含一个 NodeEvaluator 对象
* findPath 实际就是一个多目标 A* 算法
  * 终止条件：找到了至少一个到目标的曼哈顿距离小于 DIS
    * 如果找到了就选择 Node 数量最少的 Path，否则选择终点距离目标最近的 Path
  * 如果当前 Node 到起点的欧几里得距离不超过 DIS2，则继续找邻居 Node 遍历
    * 遍历累加 walkedDistance（欧几里得距离）
    * 优先队列总代价 = 上个 Node 的代价 + 当前欧几里得距离 + 当前 Node 的权重 + bestH（启发式） * 1.5 = 欧几里得距离总和 + 权重总和
* 寻路限制了最多访问多少节点
### NodeEvaluator
* 会设置实体的长宽高度，至少是 1x1x1 的体积（向下取整）
* getStart 获取路径的起点 Node
* getNeighbors 获取邻居 Node 数组
#### WalkNodeEvaluator
* getStart：考虑液体行走、漂浮等情况
* getNeighbors：
### Node
* 包含三维坐标（int）
* 路径类型
* 权重（costMalus）
### MoveControl
移动控制器在构造函数中创建
* serverAiStep 时被 tick 
* STRAFE 模式表示横着走
* MOVE_TO 模式表示往前后移动
  * 如果目标位置较高 & 距离较近则会跳起进入 JUMPING
* JUMPING 状态如果落地，则会变成 WAIT
* WAIT 状态速度置为 0
* 如果是骑乘在某个生物上，则使用被骑乘生物的移动控制器
### JumpControl
跳跃控制器在构造函数中创建
* 仅维护一个是否跳跃中字段，跳跃只有一个 tick
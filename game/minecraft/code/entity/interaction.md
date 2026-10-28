# 玩家交互机制
* 监听右击按键
* Minecraft#startUseItem
* 遍历主副手
  * InputEvent.InteractionKeyMappingTriggered，如果取消则return
  * 如果是和实体交互，校验边界和实体交互距离
    * MultiPlayerGameMode#interact
    * ServerboundInteractPacket（C）-> ServerGamePacketListenerImpl#handleInteract（S）
    * PlayerInteractEvent.EntityInteractSpecific（C/S）
    * 如果interactionResult==null，则 Player#interactOn（C/S）
    * 如果interactionResult==null，PlayerInteractEvent.EntityInteract（C/S）
    * Entity#interact（C/S）
    * 如果不是消耗结果，则 ItemStack#interactLivingEntity（C/S）
    * Item#interactLivingEntity（C/S）
  * 如果是和方块交互
    * MultiPlayerGameMode#useItemOn
    * ServerboundUseItemOnPacket（C）-> ServerPlayerGameMode#useItemOn（S）
    * PlayerInteractEvent.RightClickBlock（C/S）
    * UseItem != False，则 ItemStack#onItemUseFirst（C/S)
    * UseItemOnBlockEvent-ITEM_BEFORE_BLOCK（C/S)
    * Item#onItemUseFirst（C/S)
    * 如果成功使用方块，则 BlockState#useItemOn（C/S)
      * UseItemOnBlockEvent-BLOCK（C/S)
      * Block#useItemOn（C/S)
      * 如果结果能尝试空手，则 BlockState#useWithoutItem（C/S)
        * Block#useWithoutItem（C/S)
    * 如果成功使用物品，则 ItemStack#useOn（C/S)
      * UseItemOnBlockEvent-ITEM_AFTER_BLOCK（C/S)
      * Item#useOn（C/S)
  * 如果手上没有物品并且命中空气
    * PlayerInteractEvent.RightClickEmpty（C）
  * 如果手持物品不为空
    * MultiPlayerGameMode#useItem（C）
    * ServerboundUseItemPacket（C）-> ServerGamePacketListenerImpl#handleUseItem
    * PlayerInteractEvent.RightClickItem（C/S)
    * 如果没有取消，则 ItemStack#use（C/S)
    * Item#use（C/S)
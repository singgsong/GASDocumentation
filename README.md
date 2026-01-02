# GAS文档
我对虚幻引擎5的GameplayAbilitySystem插件（GAS）的理解，附带一个简单的多人游戏示例项目。本文并非官方文档，本项目及作者均与Epic Games无隶属关系。本文信息准确性不作任何保证。

本文档的目标是解释GAS中的核心概念与类，并根据个人使用经验提供额外见解。社区中存在大量关于GAS的"部落知识"，我旨在分享所有个人积累的经验。

示例项目与文档基于**虚幻引擎5.3**（UE5）版本。本文档存在针对旧版本引擎的分支，但这些分支已停止维护，可能存在错误或过时信息。请使用与您引擎版本匹配的分支。

[GASShooter](https://github.com/tranek/GASShooter)是姊妹示例项目，展示了在多人FPS/TPS中使用GAS的进阶技巧。

最佳文档始终是插件源代码本身。

<a name="table-of-contents"></a>

## 目录
> 1. [GameplayAbilitySystem 插件简介](#1-gameplayabilitysystem-插件简介)
> 2. [示例项目](#2-示例项目)
> 3. [使用 GAS 搭建项目](#3-使用-gas-搭建项目)
> 4. [GAS 概念](#4-gas-概念)
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1 [Ability System Component](#41-ability-system-component)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1 [复制模式](#411-复制模式)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2 [设置与初始化](#412-设置与初始化)  
>    4.2 [Gameplay Tag](#42-gameplay-tag)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1 [响应 Gameplay Tag 的变化](#421-响应-gameplay-tag-的变化)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2 [从插件的 .ini 文件加载 Gameplay Tag](#422-从插件的-ini-文件加载-gameplay-tag)  
>    4.3 [Attributes(属性)](#43-attributes属性)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1 [属性定义](#431-属性定义)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2 [BaseValue vs CurrentValue](#432-basevalue-与-currentvalue)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3 [meta attributes(元属性)](#433-元属性meta-attributes)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4 [响应属性变化](#434-响应属性变化)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5 [派生属性](#435-派生属性)  
>    4.4 [Attribute Set](#44-attribute-set)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.1 [Attribute Set 定义](#441-attribute-set-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2 [Attribute Set 设计](#concepts-as-design)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.1 [具有独立属性的子组件](#4421-具有独立属性的子组件)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.2 [运行时添加和移除属性集](#4422-运行时添加和移除属性集)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3 [物品属性（武器弹药）](#4423-物品属性武器弹药)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.1 [物品上的纯浮点数](#44231-物品上的纯浮点数)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.2 [物品的 AttributeSet](#44232-物品的-attributeset)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.2.3.3 [物品的 ASC](#44233-物品的-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.3 [定义属性](#443-定义属性)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.4 [初始化属性](#444-初始化属性)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.5 [PreAttributeChange()](#445-preattributechange)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.6 [PostGameplayEffectExecute()](#446-postgameplayeffectexecute)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.4.7 [OnAttributeAggregatorCreated()](#447-onattributeaggregatorcreated)  
>    4.5 [Gameplay Effects](#45-gameplay-effects)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.1 [定义 Gameplay Effect](#451-定义-gameplay-effect)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.2 [应用 Gameplay Effects](#452-应用-gameplay-effects)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.3 [移除 Gameplay Effects](#453-移除-gameplay-effects)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4 [Modifiers(修饰符)](#454-数值设计-modifiers修饰符)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.1 [乘除运算修饰符](#4541-乘除运算修饰符)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.4.2 [Gameplay Tags 在修饰符上的应用](#4542-gameplay-tags-在修饰符上的应用)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.5 [叠加游戏效果](#455-叠加游戏效果)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.6 [授予能力](#456-授予能力)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.7 [Gameplay Effect Tags](#457-gameplay-effect-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.8 [免疫](#458-免疫)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9 [Gameplay Effect Spec(游戏效果规范)](#459-gameplay-effect-spec游戏效果规范)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.9.1 [SetByCaller](#4591-setbycallers)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.10 [Gameplay Effect Context](#4510-gameplay-effect-context)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.11 [Modifier Magnitude Calculation(修改幅度计算)](#4511-modifier-magnitude-calculation修改幅度计算)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12 [Gameplay Effect Execution Calculation](#4512-gameplay-effect-execution-calculation)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1 [向 Execution Calculation 传递数据](#45121-向-execution-calculation-传递数据)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.1 [SetByCaller](#451211-setbycaller)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.2 [基于后备数据的属性计算修饰符](#451212-基于后备数据的属性计算修饰符)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.3 [基于后备数据的临时变量计算修饰符](#451213-基于后备数据的临时变量计算修饰符)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.12.1.4 [Gameplay Effect 上下文](#451214-gameplay-effect-上下文)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.13 [自定义应用条件](#4513-car自定义应用条件)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.14 [消耗 Gameplay Effect](#4514-cost-gameplay-effect)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15 [冷却 Gameplay Effect](#4515-cooldown-gameplay-effect)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.1 [获取冷却 Gameplay Effect 的剩余时间](#45151-获取冷却-gameplay-effect-的剩余时间)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.2 [监听冷却开始与结束](#45152-监听冷却开始与结束)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.15.3 [冷却预测](#-predicting-cooldowns)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.16 [修改激活中的 Gameplay Effect 持续时间](#-changing-active-gameplay-effect-duration)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.17 [运行时创建动态 Gameplay Effect](#-creating-dynamic-gameplay-effects-at-runtime)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.5.18 [Gameplay Effect 容器](#-gameplay-effect-containers)  
>    4.6 [Gameplay Ability](#-gameplay-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1 [Gameplay Ability 定义](#-gameplay-ability-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.1 [复制策略](#-replication-policy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.2 [服务器尊重远端 Ability 取消](#-server-respects-remote-ability-cancellation)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.1.3 [直接复制输入](#-replicate-input-directly)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2 [将输入绑定到 ASC](#-binding-input-to-the-asc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.2.1 [仅绑定输入而不激活 Ability](#-binding-to-input-without-activating-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.3 [授予 Ability](#-granting-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4 [激活 Ability](#-activating-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.1 [被动 Ability](#4641-passive-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.4.2 [激活失败标签](#-activation-failed-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.5 [取消 Ability](#-canceling-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.6 [获取激活中的 Ability](#-getting-active-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.7 [实例化策略](#-instancing-policy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.8 [网络执行策略](#-net-execution-policy)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.9 [Ability 标签](#-ability-tags)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.10 [Gameplay Ability 规格](#-gameplay-ability-spec)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.11 [向 Ability 传递数据](#-passing-data-to-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.12 [Ability 消耗与冷却](#-ability-cost-and-cooldown)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.13 [Ability 升级](#-leveling-up-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.14 [Ability 集合](#-ability-sets)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.15 [Ability 批处理](#-ability-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.6.16 [网络安全策略](#-net-security-policy)  
>    4.7 [Ability 任务](#-ability-tasks)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.1 [Ability 任务定义](#-ability-task-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.2 [自定义 Ability 任务](#-custom-ability-tasks)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.3 [使用 Ability 任务](#-using-ability-tasks)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.7.4 [根运动源 Ability 任务](#-root-motion-source-ability-tasks)  
>    4.8 [Gameplay Cue](#-gameplay-cues)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.1 [Gameplay Cue 定义](#-gameplay-cue-definition)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.2 [触发 Gameplay Cue](#-triggering-gameplay-cues)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.3 [本地 Gameplay Cue](#-local-gameplay-cues)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.4 [Gameplay Cue 参数](#-gameplay-cue-parameters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.5 [Gameplay Cue 管理器](#-gameplay-cue-manager)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.6 [阻止 Gameplay Cue 触发](#-prevent-gameplay-cues-from-firing)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7 [Gameplay Cue 批处理](#-gameplay-cue-batching)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.1 [手动 RPC](#-manual-rpc)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.7.2 [单个 GE 上的多个 GC](#-multiple-gcs-on-one-ge)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.8 [Gameplay Cue 事件](#-gameplay-cue-events)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.8.9 [Gameplay Cue 可靠性](#-gameplay-cue-reliability)  
>    4.9 [Ability System 全局配置](#-ability-system-globals)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.9.1 [初始化全局数据](#-initglobaldata)  
>    4.10 [预测](#-prediction)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.1 [预测键](#-prediction-key)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.2 [在 Ability 中创建新的预测窗口](#-creating-new-prediction-windows-in-abilities)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.3 [预测性生成 Actor](#-predictively-spawning-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.4 [GAS 中预测的未来](#-future-of-prediction-in-gas)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.10.5 [网络预测插件](#-network-prediction-plugin)  
>    4.11 [目标系统](#-targeting)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.1 [目标数据](#-target-data)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.2 [目标 Actor](#-target-actors)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.3 [目标数据过滤器](#-target-data-filters)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.4 [Gameplay Ability 世界准星](#-gameplay-ability-world-reticles)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.11.5 [Gameplay Effect 容器目标选择](#-gameplay-effect-containers-targeting)  
> 5. [常用的 Ability 和 Effect 实现](#5-commonly-implemented-abilities-and-effects)  
>    5.1 [眩晕](#51-stun)  
>    5.2 [冲刺](#52-sprint)  
>    5.3 [瞄准](#53-aim-down-sights)  
>    5.4 [吸血](#54-lifesteal)  
>    5.5 [在客户端和服务器上生成随机数](#55-generating-a-random-number-on-client-and-server)  
>    5.6 [暴击](#56-critical-hits)  
>    5.7 [非叠加 Gameplay Effect，但仅最大数值影响目标](#57-non-stacking-gameplay-effects-but-only-the-greatest-magnitude-actually-affects-the-target)  
>    5.8 [在游戏暂停时生成目标数据](#58-generate-target-data-while-game-is-paused)  
>    5.9 [一键交互系统](#59-one-button-interaction-system)  
> 6. [GAS 调试](#6-debugging-gas)  
>    6.1 [showdebug abilitysystem](#61-showdebug-abilitysystem)  
>    6.2 [Gameplay 调试器](#62-gameplay-debugger)  
>    6.3 [GAS 日志记录](#63-gas-logging)  
> 7. [优化](#7-optimizations)  
>    7.1 [Ability 批处理](#71-ability-batching)  
>    7.2 [Gameplay Cue 批处理](#72-gameplay-cue-batching)  
>    7.3 [AbilitySystemComponent 复制模式](#73-abilitysystemcomponent-replication-mode)  
>    7.4 [属性代理复制](#74-attribute-proxy-replication)  
>    7.5 [ASC 懒加载](#75-asc-lazy-loading)  
> 8. [提高工作效率的建议](#8-quality-of-life-suggestions)  
>    8.1 [Gameplay Effect 容器](#81-gameplay-effect-containers)  
>    8.2 [绑定到 ASC 委托的蓝图异步任务](#82-blueprint-asynctasks-to-bind-to-asc-delegates)  
> 9. [故障排除](#9-troubleshooting)  
>    9.1 [LogAbilitySystem: 警告：非本地无法激活 LocalOnly 或 LocalPredicted 的 Ability %s!](#91-logabilitysystem-warning-cant-activate-localonly-or-localpredicted-ability-s-when-not-local)  
>    9.2 [ScriptStructCache 错误](#92-scriptstructcache-errors)  
>    9.3 [动画蒙太奇未复制到客户端](#93-animation-montages-are-not-replicating-to-clients)  
>    9.4 [复制蓝图 Actor 时 AttributeSets 被设为 nullptr](#94-duplicating-blueprint-actors-is-setting-attributesets-to-nullptr)  
>    9.5 [未解决的外部符号 UEPushModelPrivate::MarkPropertyDirty(int,int)](#95-unresolved-external-symbol-uepushmodelprivate-markpropertydirtyintint)  
>    9.6 [枚举名称现在以路径名表示](#96-enum-names-are-now-represented-by-path-name)  
> 10. [常用 GAS 缩写](#10-common-gas-acronyms)  
> 11. [其他资源](#11-other-resources)  
>    11.1 [Epic 游戏 Dave Ratti 问答](#111-qa-with-epic-games-dave-ratti)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.1 [社区问题 1](#1111-community-questions-1)  
>    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;11.1.2 [社区问题 2](#1112-community-questions-2)  
> 12. [GAS 更新日志](#12-gas-changelog)  
>    * [5.3](#53)  
>    * [5.2](#52)  
>    * [5.1](#51)  
>    * [5.0](#50)  
>    * [4.27](#427)  
>    * [4.26](#426)  
>    * [4.25.1](#4251)  
>    * [4.25](#425)  
>    * [4.24](#424)  

         
<a name="intro"></a>

## 1. GameplayAbilitySystem 插件简介

摘自 [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)：

> Gameplay Ability System 是一个高度灵活的框架，用于构建你在 RPG 或 MOBA 游戏中常见的能力与属性。你可以为游戏角色构建可使用的主动或被动能力、状态效果（这些效果会因能力的使用而逐步增强或削弱各种属性）、实现用于限制能力使用的“冷却时间”或资源消耗、在不同等级下改变能力及其效果、触发粒子或音效等。简而言之，该系统可以帮助你设计、实现并高效地进行网络同步——无论是像跳跃这样简单的能力，还是任何现代 RPG 或 MOBA 游戏中你最喜欢角色的复杂技能组合。

GameplayAbilitySystem 插件由 Epic Games 开发，并随 Unreal Engine 一同提供。它已经在《Paragon》《Fortnite》等 AAA 商业游戏中经过了实战检验。

该插件为单机和多人游戏提供了开箱即用的解决方案，用于：

* 实现基于等级的角色能力或技能，并可选地包含消耗与冷却时间（[GameplayAbilities](#concepts-ga)）
* 操作属于 Actor 的数值型 `Attributes`（[Attributes](#concepts-a)）
* 将状态效果应用到 Actor（[GameplayEffects](#concepts-ge)）
* 将 `GameplayTags` 应用于 Actor（[GameplayTags](#concepts-gt)）
* 生成视觉或音效效果（[GameplayCues](#concepts-gc)）
* 对以上所有内容进行网络复制

在多人游戏中，GAS 还支持以下内容的 [客户端预测](#concepts-p)：

* 能力激活
* 播放动画蒙太奇
* `Attributes` 的变化
* 应用 `GameplayTags`
* 生成 `GameplayCues`
* 通过与 `CharacterMovementComponent` 关联的 `RootMotionSource` 函数进行移动

**GAS 必须使用 C++ 进行初始化配置**，但 `GameplayAbilities` 和 `GameplayEffects` 可以由设计人员在 Blueprint 中创建。

GAS 当前存在的问题：

* `GameplayEffect` 的延迟回滚（无法预测能力冷却，导致高延迟玩家在低冷却能力上的射速低于低延迟玩家）。
* 无法预测 `GameplayEffects` 的移除。不过可以通过预测性地添加具有相反效果的 `GameplayEffects` 来达到移除效果，但这并不总是合适或可行，仍然是一个问题。
* 缺乏样板模板、多人示例和文档，希望本文能在一定程度上弥补这些不足。

**[⬆ 返回顶部](#目录)**

<a name="sp"></a>

## 2. 示例项目

本说明文档包含一个**多人第三人称射击**示例项目，面向**初次接触 GameplayAbilitySystem 插件、但并非 Unreal Engine 新手**的用户。
假定使用者已经掌握 UE 中的 C++、Blueprint、UMG、网络复制以及其他中级主题。
该项目演示了如何搭建一个基础的、支持多人游戏的第三人称射击项目：

* 对于玩家 / AI 控制的英雄角色，将 `AbilitySystemComponent`（`ASC`）放在 `PlayerState` 上
* 对于 AI 控制的杂兵（Minion），将 `ASC` 放在 `Character` 上

本项目的目标是在保持简单的前提下，展示 GAS 的基础用法，并通过注释充分的代码演示一些常被请求的能力实现。
由于面向初学者，项目未涉及诸如 [预测生成投射物](#concepts-p-spawn) 等高级主题。

### 演示的概念包括：

* `ASC` 位于 `PlayerState` 与 `Character` 的对比
* 可复制的 `Attributes`
* 可复制的动画蒙太奇
* `GameplayTags`
* 在 `GameplayAbilities` 内部及外部应用与移除 `GameplayEffects`
* 应用经护甲减免的伤害以改变角色生命值
* `GameplayEffectExecutionCalculations`
* 眩晕效果
* 死亡与复活
* 在服务器上由能力生成 Actor（投射物）
* 使用瞄准与冲刺时，预测性地改变本地玩家移动速度
* 冲刺时持续消耗体力
* 使用法力值施放能力
* 被动能力
* `GameplayEffects` 的叠加
* 目标选择
* 使用 Blueprint 创建的 `GameplayAbilities`
* 使用 C++ 创建的 `GameplayAbilities`
* 每 `Actor` 实例化的 `GameplayAbilities`
* 非实例化的 `GameplayAbilities`（Jump）
* 静态 `GameplayCues`（开火投射物命中粒子效果）
* Actor `GameplayCues`（冲刺与眩晕粒子效果）

### 英雄类包含以下能力：

| 能力                 | 输入绑定 | 预测 | C++ / Blueprint | 描述                                                                         |
| -------------------- | -------- | ---- | --------------- | ---------------------------------------------------------------------------- |
| Jump                 | 空格键   | 是   | C++             | 使英雄跳跃                                                                   |
| Gun                  | 鼠标左键 | 否   | C++             | 从英雄的枪械发射投射物。动画是预测的，但投射物不是                           |
| Aim Down Sights      | 鼠标右键 | 是   | Blueprint       | 按住时英雄移动变慢，摄像机拉近，以便更精准射击                               |
| Sprint               | 左 Shift | 是   | Blueprint       | 按住时英雄移动更快并持续消耗体力                                             |
| Forward Dash         | Q        | 是   | Blueprint       | 消耗体力向前冲刺                                                             |
| Passive Armor Stacks | 被动     | 否   | Blueprint       | 每 4 秒获得一层护甲，最多 4 层。受到伤害会移除一层护甲                       |
| Meteor               | R        | 否   | Blueprint       | 玩家指定位置召唤陨石，对敌人造成伤害并眩晕。目标选择是预测的，但陨石生成不是 |

`GameplayAbilities` 使用 C++ 或 Blueprint 创建并无区别。本示例中混合使用了两者，以展示各自的实现方式。

杂兵（Minion）不包含任何预定义的 `GameplayAbilities`。
红色杂兵拥有更高的生命回复，而蓝色杂兵拥有更高的初始生命值。

在 `GameplayAbility` 的命名中，使用 `_BP` 后缀表示该能力逻辑是通过 Blueprint 创建的；没有后缀则表示使用 C++ 创建。

### Blueprint 资源命名前缀

| 前缀 | 资源类型        |
| ---- | --------------- |
| GA_  | GameplayAbility |
| GC_  | GameplayCue     |
| GE_  | GameplayEffect  |

**[⬆ 返回顶部](#目录)**

<a name="setup"></a>

## 3. 使用 GAS 搭建项目

使用 GAS 搭建项目的基本步骤：

1. 在编辑器中启用 GameplayAbilitySystem 插件
2. 编辑 `YourProjectName.Build.cs`，在 `PrivateDependencyModuleNames` 中添加 `"GameplayAbilities", "GameplayTags", "GameplayTasks"`
3. 刷新 / 重新生成 Visual Studio 项目文件
4. 从 4.24 到 5.2，若要使用 [`TargetData`](#concepts-targeting-data)，必须调用 `UAbilitySystemGlobals::Get().InitGlobalData()`。示例项目在 `UAssetManager::StartInitialLoading()` 中完成了此调用。从 5.3 开始将自动调用。更多信息请参见 [`InitGlobalData()`](#concepts-asg-initglobaldata)。

以上就是启用 GAS 所需的全部步骤。接下来，只需在你的 `Character` 或 `PlayerState` 上添加一个 [`ASC`](#concepts-asc) 和 [`AttributeSet`](#concepts-as)，然后开始创建 [`GameplayAbilities`](#concepts-ga) 和 [`GameplayEffects`](#concepts-ge) 即可！

**[⬆ 返回顶部](#目录)**

<a name="concepts"></a>

## 4. GAS 概念

#### 章节

> 4.1 [Ability System Component](#concepts-asc)
> 4.2 [Gameplay Tags](#concepts-gt)
> 4.3 [Attributes](#concepts-a)
> 4.4 [Attribute Set](#concepts-as)
> 4.5 [Gameplay Effects](#concepts-ge)
> 4.6 [Gameplay Abilities](#concepts-ga)
> 4.7 [Ability Tasks](#concepts-at)
> 4.8 [Gameplay Cues](#concepts-gc)
> 4.9 [Ability System Globals](#concepts-asg)
> 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>

### 4.1 Ability System Component

`AbilitySystemComponent`（`ASC`）是 GAS 的核心。它是一个 `UActorComponent`（[`UAbilitySystemComponent`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)），负责处理与该系统相关的所有交互。任何希望使用 [`GameplayAbilities`](#concepts-ga)、拥有 [`Attributes`](#concepts-a) 或接收 [`GameplayEffects`](#concepts-ge) 的 `Actor`，都必须附加一个 `ASC`。这些对象都存在于 `ASC` 内部，并由其进行管理和网络复制（`Attributes` 例外，它们由各自的 [`AttributeSet`](#concepts-as) 负责复制）。开发者可以选择继承该类，但并非强制要求。

附加了 `ASC` 的 `Actor` 被称为该 `ASC` 的 `OwnerActor`。
`ASC` 的物理表现 `Actor` 被称为 `AvatarActor`。
在 MOBA 游戏中，一个简单的 AI 杂兵，其 `OwnerActor` 与 `AvatarActor` 可以是同一个 `Actor`；而对于玩家控制的英雄角色，`OwnerActor` 通常是 `PlayerState`，而 `AvatarActor` 则是英雄的 `Character`。大多数 `Actor` 会将 `ASC` 直接放在自身上。如果你的 `Actor` 会重生，并且需要在多次重生之间保持 `Attributes` 或 `GameplayEffects` 的持久性（例如 MOBA 中的英雄），那么将 `ASC` 放在 `PlayerState` 上是理想的做法。

**注意：** 如果你的 `ASC` 位于 `PlayerState` 上，你需要提高 `PlayerState` 的 `NetUpdateFrequency`。`PlayerState` 的默认值非常低，可能会导致客户端在接收 `Attributes`、`GameplayTags` 等变化时出现延迟或感知上的卡顿。务必启用 [`Adaptive Network Update Frequency`](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency)，《Fortnite》正是使用了该机制。

如果 `OwnerActor` 与 `AvatarActor` 是不同的 `Actor`，那么二者都应实现 `IAbilitySystemInterface`。该接口只有一个必须重写的函数：
`UAbilitySystemComponent* GetAbilitySystemComponent() const`，用于返回指向其 `ASC` 的指针。`ASC` 在系统内部正是通过查找该接口函数来相互交互的。

`ASC` 使用 `FActiveGameplayEffectsContainer ActiveGameplayEffects` 来保存当前激活的 `GameplayEffects`。

`ASC` 使用 `FGameplayAbilitySpecContainer ActivatableAbilities` 来保存被授予的 `Gameplay Abilities`。当你计划遍历 `ActivatableAbilities.Items` 时，务必在循环之前添加 `ABILITYLIST_SCOPE_LOCK();`，以防止能力列表在遍历过程中发生变化（例如能力被移除）。每一个作用域内的 `ABILITYLIST_SCOPE_LOCK();` 都会递增 `AbilityScopeLockCount`，并在作用域结束时递减。不要在 `ABILITYLIST_SCOPE_LOCK();` 的作用域内尝试移除能力（清除能力的函数会在内部检查 `AbilityScopeLockCount`，以防在列表被锁定时移除能力）。

<a name="concepts-asc-rm"></a>

### 4.1.1 复制模式

`ASC` 定义了三种不同的复制模式，用于复制 `GameplayEffects`、`GameplayTags` 和 `GameplayCues`：`Full`、`Mixed` 和 `Minimal`。
`Attributes` 由各自的 `AttributeSet` 负责复制。

| 复制模式  | 使用场景                     | 描述                                                                                       |
| --------- | ---------------------------- | ------------------------------------------------------------------------------------------ |
| `Full`    | 单机游戏                     | 每一个 `GameplayEffect` 都会复制到所有客户端                                               |
| `Mixed`   | 多人游戏，玩家控制的 `Actor` | `GameplayEffects` 只复制给所属客户端；只有 `GameplayTags` 和 `GameplayCues` 会复制给所有人 |
| `Minimal` | 多人游戏，AI 控制的 `Actor`  | `GameplayEffects` 不会复制给任何人；只有 `GameplayTags` 和 `GameplayCues` 会复制给所有人   |

**注意：** `Mixed` 复制模式要求 `OwnerActor` 的 `Owner` 必须是 `Controller`。`PlayerState` 的 `Owner` 默认就是 `Controller`，但 `Character` 不是。如果在 `OwnerActor` 不是 `PlayerState` 的情况下使用 `Mixed` 复制模式，则需要对 `OwnerActor` 调用 `SetOwner()`，并传入一个有效的 `Controller`。

从 4.24 开始，`PossessedBy()` 会将 `Pawn` 的 Owner 设置为新的 `Controller`。

**[⬆ 返回顶部](#目录)**

<a name="concepts-asc-setup"></a>

### 4.1.2 设置与初始化

`ASC` 通常在 `OwnerActor` 的构造函数中创建，并显式标记为可复制。**这一点必须使用 C++ 完成**。

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`ASC` 需要在服务器和客户端两端，使用其 `OwnerActor` 和 `AvatarActor` 进行初始化。
你应当在 `Pawn` 的 `Controller` 已经被设置之后（即被占有之后）再进行初始化。
单机游戏只需要关注服务器路径即可。

对于 `ASC` 位于 `Pawn` 上的玩家控制角色，通常在服务器端的 `Pawn::PossessedBy()` 中初始化，在客户端的 `PlayerController::AcknowledgePossession()` 中初始化。

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于 `ASC` 位于 `PlayerState` 上的玩家控制角色，通常在服务器端的 `Pawn::PossessedBy()` 中初始化，在客户端的 `Pawn::OnRep_PlayerState()` 中初始化，以确保客户端已经拥有 `PlayerState`。

```c++
// Server only
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
// Client only
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its ASC when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果你遇到错误信息
`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`
说明你没有在客户端初始化你的 `ASC`。

**[⬆ 返回顶部](#目录)**


<a name="concepts-gt"></a>

### 4.2 Gameplay Tag

[`FGameplayTag`](https://docs.unrealengine.com/en-US/API/Runtime/GameplayTags/FGameplayTag/index.html) 是一种分层命名的标签，格式为 `Parent.Child.Grandchild...`，并由 `GameplayTagManager` 统一注册。这些标签在对对象状态进行分类和描述时非常有用。例如，当一个角色被眩晕时，我们可以在眩晕持续期间为其添加一个 `State.Debuff.Stun` 的 `GameplayTag`。

你会逐渐发现，很多过去通过布尔值或枚举处理的逻辑，都可以被 `GameplayTags` 取代，并通过判断对象是否拥有某些 `GameplayTags` 来进行布尔逻辑判断。

在为对象赋予标签时，如果对象拥有 `ASC`，通常会将标签添加到其 `ASC` 上，以便 GAS 能够与之交互。`UAbilitySystemComponent` 实现了 `IGameplayTagAssetInterface`，提供了用于访问其所拥有 `GameplayTags` 的函数。

多个 `GameplayTags` 可以存储在一个 `FGameplayTagContainer` 中。相比 `TArray<FGameplayTag>`，更推荐使用 `GameplayTagContainer`，因为它在内部做了一些效率优化。虽然标签本质上是标准的 `FName`，但在启用了项目设置中的 `Fast Replication` 后，`FGameplayTagContainer` 可以将标签高效打包用于网络复制。`Fast Replication` 要求服务器和客户端拥有完全相同的 `GameplayTags` 列表，这通常不是问题，因此建议启用该选项。`GameplayTagContainer` 也可以在需要遍历时返回一个 `TArray<FGameplayTag>`。

存储在 `FGameplayTagCountContainer` 中的 `GameplayTags` 包含一个 `TagMap`，用于记录该 `GameplayTag` 的实例数量。即使某个 `GameplayTag` 仍存在于容器中，其 `TagMapCount` 也可能为 0。在调试时，如果发现 `ASC` 似乎仍然拥有某个 `GameplayTag`，你可能会遇到这种情况。所有诸如 `HasTag()`、`HasMatchingTag()` 等函数都会检查 `TagMapCount`，如果该 `GameplayTag` 不存在或其 `TagMapCount` 为 0，则返回 false。

`GameplayTags` 必须预先定义在 `DefaultGameplayTags.ini` 中。Unreal Engine 编辑器在项目设置中提供了一个界面，允许开发者无需手动编辑 `DefaultGameplayTags.ini` 就能管理 `GameplayTags`。`GameplayTag` 编辑器支持创建、重命名、查找引用以及删除 `GameplayTags`。

![项目设置中的 GameplayTag 编辑器](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaytageditor.png)

搜索 `GameplayTag` 的引用会在编辑器中打开熟悉的“引用查看器（Reference Viewer）”图表，显示所有引用了该 `GameplayTag` 的资源。但这不会显示任何在 C++ 中引用该 `GameplayTag` 的类。

重命名 `GameplayTags` 会创建一个重定向，使仍引用旧 `GameplayTag` 的资源自动指向新的 `GameplayTag`。我个人更倾向于在可能的情况下新建一个 `GameplayTag`，手动更新所有引用到新的 `GameplayTag`，然后删除旧的 `GameplayTag`，以避免产生重定向。

除了 `Fast Replication` 之外，`GameplayTag` 编辑器还提供了一个选项，用于填充常用的可复制 `GameplayTags`，以进一步优化复制性能。

如果 `GameplayTags` 是通过 `GameplayEffect` 添加的，它们会被复制。`ASC` 还允许添加不会被复制、需要手动管理的 `LooseGameplayTags`。示例项目使用了一个 `LooseGameplayTag`：`State.Dead`，以便所属客户端在生命值降为 0 时能立即作出响应。复活时会手动将 `TagMapCount` 重置为 0。只有在处理 `LooseGameplayTags` 时才应手动调整 `TagMapCount`。相比之下，更推荐使用 `UAbilitySystemComponent::AddLooseGameplayTag()` 和 `UAbilitySystemComponent::RemoveLooseGameplayTag()`，而不是手动修改 `TagMapCount`。

在 C++ 中获取 `GameplayTag` 的方式：

```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于更高级的 `GameplayTag` 操作（例如获取父级或子级 `GameplayTags`），可以查看 `GameplayTagManager` 提供的相关函数。要访问 `GameplayTagManager`，需要包含 `GameplayTagManager.h`，并通过 `UGameplayTagManager::Get().FunctionName` 调用。`GameplayTagManager` 实际上是以关系节点（父、子等）的形式存储 `GameplayTags`，相比频繁的字符串操作和比较，处理效率更高。

`GameplayTags` 和 `GameplayTagContainer` 可以使用可选的 `UPROPERTY` 说明符
`Meta = (Categories = "GameplayCue")`，用于在 Blueprint 中过滤标签，只显示父标签为 `GameplayCue` 的 `GameplayTags`。当你确定某个 `GameplayTag` 或 `GameplayTagContainer` 变量只会用于 `GameplayCues` 时，这非常有用。

另外，还有一个独立的结构体 `FGameplayCueTag`，它封装了一个 `FGameplayTag`，并且在 Blueprint 中会自动只显示父标签为 `GameplayCue` 的 `GameplayTags`。

如果你想在函数参数中对 `GameplayTag` 进行过滤，可以使用 `UFUNCTION` 说明符
`Meta = (GameplayTagFilter = "GameplayCue")`。函数参数中的 `GameplayTagContainer` 无法进行过滤。如果你希望修改引擎以支持该功能，可以参考
`Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagGraphPin.cpp` 中的 `SGameplayTagGraphPin::ParseDefaultValueData()`，它调用了
`FilterString = UGameplayTagsManager::Get().GetCategoriesMetaFromField(PinStructType);`，并在 `SGameplayTagGraphPin::GetListContent()` 中将 `FilterString` 传递给 `SGameplayTagWidget`。而 `Engine\Plugins\Editor\GameplayTagsEditor\Source\GameplayTagsEditor\Private\SGameplayTagContainerGraphPin.cpp` 中对应的实现并未检查 meta 字段属性，也没有传递过滤条件。

示例项目大量使用了 `GameplayTags`。

**[⬆ 返回顶部](#目录)**

<a name="concepts-gt-change"></a>

### 4.2.1 响应 Gameplay Tag 的变化

`ASC` 提供了一个委托，用于在 `GameplayTags` 被添加或移除时触发。它接收一个 `EGameplayTagEventType`，用于指定仅在标签被添加 / 移除时触发，还是在该 `GameplayTag` 的 `TagMapCount` 发生任意变化时触发。

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(
	FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")),
	EGameplayTagEventType::NewOrRemoved
).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数会接收触发的 `GameplayTag` 以及新的 `TagCount`。

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 返回顶部](#目录)**

<a name="concepts-gt-loadfromplugin"></a>

### 4.2.2 从插件的 .ini 文件加载 Gameplay Tag

如果你创建了一个包含自身 `GameplayTags` 的插件，并在其中定义了 .ini 文件，可以在插件的 `StartupModule()` 函数中加载该插件的 `GameplayTag` .ini 目录。

例如，随 Unreal Engine 提供的 CommonConversation 插件就是这样做的：

```c++
void FCommonConversationRuntimeModule::StartupModule()
{
	TSharedPtr<IPlugin> ThisPlugin = IPluginManager::Get().FindPlugin(TEXT("CommonConversation"));
	check(ThisPlugin.IsValid());
	
	UGameplayTagsManager::Get().AddTagIniSearchPath(
		ThisPlugin->GetBaseDir() / TEXT("Config") / TEXT("Tags")
	);

	//...
}
```

这会在引擎启动且插件被启用时，查找 `Plugins\CommonConversation\Config\Tags` 目录，并将其中包含 `GameplayTags` 的所有 .ini 文件加载到项目中。

**[⬆ 返回顶部](#目录)**


<a name="concepts-a"></a>

### 4.3 Attributes(属性)

<a name="concepts-a-definition"></a>

#### 4.3.1 属性定义

`Attribute` 是由结构体 [`FGameplayAttributeData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html) 定义的浮点数值。它们可以表示从角色的生命值、角色等级，到药水剩余使用次数等任何数值。如果这是一个属于某个 `Actor` 的、与玩法相关的数值型数据，你就应该考虑使用 `Attribute`。一般来说，`Attribute` 只应该通过 [`GameplayEffects`](#concepts-ge) 来修改，这样 ASC 才能对这些变化进行[预测](#concepts-p)。

`Attribute` 定义在并存在于 [`AttributeSet`](#concepts-as) 中。`AttributeSet` 负责对被标记为可复制的 `Attribute` 进行网络同步。关于如何定义 `Attribute`，请参阅 [`AttributeSets`](#concepts-as) 章节。

**提示：** 如果你不希望某个 `Attribute` 显示在编辑器的 `Attributes` 列表中，可以使用 `Meta = (HideInDetailsView)` 这个 `property specifier`。

**[⬆ 返回顶部](#目录)**

<a name="concepts-a-value"></a>

#### 4.3.2 BaseValue 与 CurrentValue

一个 `Attribute` 由两个值组成：`BaseValue` 和 `CurrentValue`。`BaseValue` 是该 `Attribute` 的永久值，而 `CurrentValue` 则是 `BaseValue` 加上来自 `GameplayEffects` 的临时修改值。

例如，你的 `Character` 可能有一个移动速度 `Attribute`，其 `BaseValue` 为每秒 600 单位。在还没有任何 `GameplayEffects` 修改移动速度时，`CurrentValue` 也同样是 600 u/s。如果角色获得了一个临时的 +50 u/s 移速加成，那么 `BaseValue` 仍然保持为 600 u/s，而 `CurrentValue` 则变为 600 + 50 = 650 u/s。当移速加成效果结束后，`CurrentValue` 会恢复为 `BaseValue` 的 600 u/s。

GAS 的初学者经常会把 `BaseValue` 误认为是 `Attribute` 的最大值，并尝试以此方式使用它。这是错误的做法。那些会变化、或需要在技能或 UI 中被引用的最大值，应该被当作独立的 `Attribute` 来处理。

对于硬编码的最大值和最小值，可以通过定义一个包含 `FAttributeMetaData` 的 `DataTable` 来设置，但 Epic 在该结构体上方的注释中将其称为“仍在开发中的功能（work in progress）”。更多信息请参阅 `AttributeSet.h`。为了避免混淆，我建议：

* 会在技能或 UI 中被引用的最大值，定义为独立的 `Attribute`；
* 仅用于限制（Clamp）`Attribute` 的硬编码最大值和最小值，直接在 `AttributeSet` 中用硬编码的 float 来定义。

`Attribute` 的 Clamp 处理，在修改 `CurrentValue` 时位于 [PreAttributeChange()](#concepts-as-preattributechange)，在通过 `GameplayEffects` 修改 `BaseValue` 时位于 [PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)。

对 `BaseValue` 的永久性修改来自 `Instant` 类型的 `GameplayEffects`；而 `Duration` 和 `Infinite` 类型的 `GameplayEffects` 会修改 `CurrentValue`。周期性（Periodic）的 `GameplayEffects` 被当作 Instant `GameplayEffects` 处理，因此会修改 `BaseValue`。

**[⬆ 返回顶部](#目录)**

<a name="concepts-a-meta"></a>

#### 4.3.3 元属性（Meta Attributes）

有些 `Attribute` 被当作临时数值的占位符，用来与其他 `Attribute` 交互，这类属性称为 `Meta Attributes`。例如，我们通常会将“伤害”定义为一个 `Meta Attribute`。与其让 `GameplayEffect` 直接修改生命值 `Attribute`，不如使用一个名为 Damage 的 `Meta Attribute` 作为占位符。

这样一来，伤害数值就可以在 [`GameplayEffectExecutionCalculation`](#concepts-ge-ec) 中通过 Buff 和 Debuff 进行修改，并且还能在 `AttributeSet` 中进一步处理，例如：先从当前的护盾 `Attribute` 中扣除伤害，再将剩余的伤害扣除到生命值 `Attribute` 上。Damage 这个 `Meta Attribute` 在不同 `GameplayEffects` 之间不会持久化，每次都会被新的效果覆盖。`Meta Attributes` 通常也不会被复制。

`Meta Attributes` 在逻辑上很好地分离了诸如伤害和治疗这类流程中的两个问题：“我们造成了多少伤害？”以及“我们如何处理这些伤害？”。这种逻辑分离意味着我们的 `GameplayEffects` 和 `Execution Calculations` 不需要关心目标是如何处理伤害的。

继续以伤害为例：`GameplayEffect` 负责计算造成了多少伤害，而 `AttributeSet` 决定如何应用这些伤害。并非所有角色都一定拥有相同的 `Attributes`，尤其是在你使用继承自基类的 `AttributeSet` 时。基础的 `AttributeSet` 类可能只包含生命值 `Attribute`，而其子类的 `AttributeSet` 可能会额外增加一个护盾 `Attribute`。带有护盾 `Attribute` 的子类 `AttributeSet`，在分配所受伤害时，其逻辑就会与基础 `AttributeSet` 不同。

虽然 `Meta Attributes` 是一种很好的设计模式，但并非强制要求。如果你始终只使用一个 `Execution Calculation` 来处理所有伤害实例，并且所有角色共用同一个 `AttributeSet` 类，那么你完全可以在 `Execution Calculation` 内部直接完成对生命值、护盾等 `Attributes` 的伤害分配并直接修改它们。这样做只是牺牲了一定的灵活性，但在你的项目中这可能是可以接受的。

**[⬆ 返回顶部](#目录)**

<a name="concepts-a-changes"></a>

#### 4.3.4 响应属性变化

如果你想在 `Attribute` 发生变化时更新 UI 或触发其他玩法逻辑，可以使用
`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`。

该函数会返回一个委托（delegate），你可以将其绑定到回调函数上。当对应的 `Attribute` 发生变化时，该回调会被自动调用。委托会提供一个 `FOnAttributeChangeData` 参数，其中包含 `NewValue`、`OldValue` 以及 `FGameplayEffectModCallbackData`。**注意：** `FGameplayEffectModCallbackData` 只会在服务器端被设置。

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);
```

```c++
virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

示例项目在 `GDPlayerState` 中绑定了 `Attribute` 数值变化的委托，用于更新 HUD，并在生命值降为 0 时响应玩家死亡。

示例项目还包含了一个自定义的蓝图节点，将上述逻辑封装成一个 `AsyncTask`。该节点在 `UI_HUD` 的 UMG Widget 中使用，用于更新生命、法力和体力值。这个 `AsyncTask` 会一直存在，直到手动调用 `EndTask()`；示例中是在 UMG Widget 的 `Destruct` 事件中调用的。相关代码请参阅 `AsyncTaskAttributeChanged.h/cpp`。

![监听 Attribute 变化的蓝图节点](https://github.com/tranek/GASDocumentation/raw/master/Images/attributechange.png)

**[⬆ 返回顶部](#目录)**

#### 4.3.5 派生属性

要创建一个其数值部分或全部由一个或多个其他 `Attribute` 派生而来的 `Attribute`，可以使用一个 `Infinite` 的 `GameplayEffect`，并在其中添加一个或多个 **Attribute Based** 或 [`MMC`](#concepts-ge-mmc) [`Modifiers`](#concepts-ge-mods)。
当其所依赖的任意 `Attribute` 发生更新时，**派生属性（Derived Attribute）** 会自动更新。

派生属性上所有 `Modifiers` 的最终计算公式，与 `Modifier Aggregators` 使用的公式相同。如果你需要按照特定顺序进行计算，请将所有计算逻辑放在一个 `MMC` 中完成。

```
((CurrentValue + Additive) * Multiplicitive) / Division
```

**注意：**
如果在 PIE 中使用多个客户端进行测试，需要在编辑器首选项中禁用 **Run Under One Process**，否则在第一个客户端之外的其他客户端上，当其独立 `Attributes` 更新时，派生属性将不会更新。

在这个示例中，我们有一个 `Infinite` 的 `GameplayEffect`，用于从 `Attributes`：`TestAttrB` 和 `TestAttrC` 派生出 `TestAttrA`，公式为：

`TestAttrA = (TestAttrA + TestAttrB) * (2 * TestAttrC)`

只要任意一个相关 `Attribute` 的数值发生变化，`TestAttrA` 就会自动重新计算其值。

![派生属性示例](https://github.com/tranek/GASDocumentation/raw/master/Images/derivedattribute.png)

**[⬆ 回到目录](#目录)**

### 4.4 Attribute Set（属性集）

#### 4.4.1 Attribute Set 定义

`AttributeSet` 用于定义、持有并管理 `Attributes` 的变化。开发者应当继承自 [`UAttributeSet`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html)。
在 `OwnerActor` 的构造函数中创建 `AttributeSet` 会自动将其注册到该 Actor 的 `ASC` 中。**这一过程必须在 C++ 中完成**。

**[⬆ 回到目录](#目录)**

#### 4.4.2 Attribute Set 设计

一个 `ASC` 可以拥有一个或多个 `AttributeSet`。`AttributeSet` 的内存开销可以忽略不计，因此使用多少个 `AttributeSet` 本质上是一个由开发者自行决定的组织结构问题。

你可以让游戏中所有 `Actor` 共享一个庞大的、单体的 `AttributeSet`，只在需要时使用对应的属性，而忽略未使用的属性。

或者，你也可以选择使用多个 `AttributeSet`，每个代表一组相关的 `Attributes`，并根据需要有选择地添加到 `Actor` 上。例如，你可以有一个与生命值相关的 `AttributeSet`，一个与法力值相关的 `AttributeSet`，等等。在 MOBA 游戏中，英雄可能需要法力值，但小兵不需要，因此英雄会拥有法力 `AttributeSet`，而小兵则不会。

此外，`AttributeSet` 还可以通过继承来实现另一种选择性配置 `Actor` 属性的方式。`Attributes` 在内部以 `AttributeSetClassName.AttributeName` 的形式进行引用。当你继承一个 `AttributeSet` 时，父类中的所有 `Attributes` 仍然会使用父类名作为前缀。

虽然你可以拥有多个 `AttributeSet`，但不应在同一个 `ASC` 上添加多个**同一类**的 `AttributeSet`。如果存在同类的多个 `AttributeSet`，系统将无法确定应使用哪一个，并会随意选择其中一个。

##### 4.4.2.1 具有独立属性的子组件

在一个 `Pawn` 上存在多个可受伤害子组件的场景下（例如可单独受损的护甲部件），如果你能预先确定一个 `Pawn` 最多会有多少个可受伤害组件，我建议在同一个 `AttributeSet` 中创建对应数量的生命值 `Attributes`——例如 `DamageableCompHealth0`、`DamageableCompHealth1` 等，用来表示这些可受伤害组件的逻辑“槽位”。
在具体的可受伤组件实例中，指定一个槽位编号 `Attribute`，供 `GameplayAbilities` 或 [`Execution`](#concepts-ge-ec) 读取，从而知道应当对哪个 `Attribute` 施加伤害。
拥有少于最大数量甚至没有可受伤组件的 `Pawn` 也完全没有问题。`AttributeSet` 中存在某个 `Attribute` 并不意味着你必须使用它，未使用的 `Attributes` 只占用极少量的内存。

如果你的子组件各自需要大量 `Attributes`，或者子组件数量可能是无限的，或者子组件可以被拆卸并由其他玩家使用（例如武器），又或者由于其他原因这种方式不适合你，那么我建议放弃使用 `Attributes`，改为在组件上直接存储普通的 `float` 变量。可参考 [Item Attributes](#concepts-as-design-itemattributes)。

<a name="concepts-as-design-addremoveruntime"></a>

##### 4.4.2.2 运行时添加与移除 AttributeSet

`AttributeSet` 可以在运行时被添加到或从 `ASC` 中移除；然而，**移除 `AttributeSet` 是有风险的**。例如，如果客户端在服务器之前移除了某个 `AttributeSet`，而此时服务器又将该 `Attribute` 的数值变化同步到客户端，客户端将找不到对应的 `AttributeSet`，从而导致游戏崩溃。

武器加入背包时：

```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

武器从背包移除时：

```c++
AbilitySystemComponent->GetSpawnedAttributes_Mutable().Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

##### 4.4.2.3 物品属性（武器、弹药）

实现带有 `Attributes` 的可装备物品（如武器弹药、护甲耐久度等）有几种方式。所有这些方式都会将数值**直接存储在物品本身**。这对于在其生命周期内可能被多个玩家装备的物品来说是必要的。

> 1. 在物品上使用普通的浮点数（**推荐**）
> 2. 在物品上使用独立的 `AttributeSet`
> 3. 在物品上使用独立的 `ASC`

<a name="concepts-as-design-itemattributes-plainfloats"></a>

###### 4.4.2.3.1 物品上的纯浮点数

不使用 `Attributes`，而是在物品类实例上直接存储普通的 `float` 数值。Fortnite 和 [GASShooter](https://github.com/tranek/GASShooter) 的枪械弹药就是这样处理的。
对于一把枪，将弹匣最大容量、当前弹匣弹药量、备用弹药量等，直接作为复制的浮点数（`COND_OwnerOnly`）存储在枪的实例上。
如果多把武器共享备用弹药，则可以将备用弹药放到角色身上，作为共享弹药 `AttributeSet` 中的一个 `Attribute`（装填技能可以使用一个 `Cost GE`，从备用弹药中扣除并填充到武器的浮点弹匣弹药中）。

由于你没有使用 `Attributes` 来表示当前弹匣弹药量，因此需要重写 `UGameplayAbility` 中的一些函数，用来基于武器上的浮点数检查和消耗弹药。
在授予技能时，将枪设置为 [`GameplayAbilitySpec`](https://github.com/tranek/GASDocumentation#concepts-ga-spec) 的 `SourceObject`，即可在技能内部访问到授予该技能的武器。

为了防止在自动射击期间，武器把弹药数值从服务器复制回来并覆盖本地预测的弹药数量，可以在 `PreReplication()` 中，当玩家拥有 `IsFiring` `GameplayTag` 时禁用该变量的复制。
本质上，这是在自行实现一套本地预测。

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

优点：

1. 避开了使用 `AttributeSet` 的各种限制（见下文）

限制：

1. 无法使用现有的 `GameplayEffect` 工作流（例如用于弹药消耗的 `Cost GE`）
2. 需要额外工作，重写 `UGameplayAbility` 中的关键函数，以便基于武器上的浮点数检查和应用弹药消耗

<a name="concepts-as-design-itemattributes-attributeset"></a>

###### 4.4.2.3.2 物品的 `AttributeSet`

在物品上使用一个独立的 `AttributeSet`，并在物品加入玩家背包时将其[添加到玩家的 `ASC`](#concepts-as-design-addremoveruntime)，这种方式是可行的，但存在一些严重限制。我在 [GASShooter](https://github.com/tranek/GASShooter) 的早期版本中用这种方式实现过武器弹药。

武器将其 `Attributes`（例如弹匣最大容量、当前弹匣弹药量、备用弹药量等）存储在一个存在于武器类上的 `AttributeSet` 中。
如果多把武器共享备用弹药，则可以将备用弹药移到角色身上的一个共享弹药 `AttributeSet` 中。

当服务器端将武器加入玩家背包时，武器会把它的 `AttributeSet` 添加到玩家的 `ASC::SpawnedAttributes` 中，随后服务器会将其复制到客户端。
当武器从背包中移除时，则会从 `ASC::SpawnedAttributes` 中移除对应的 `AttributeSet`。

当 `AttributeSet` 不存在于 `OwnerActor` 上（而是存在于武器等对象上）时，最初你会在 `AttributeSet` 中遇到一些编译错误。解决方法是：
在 `BeginPlay()` 中而不是构造函数中创建 `AttributeSet`，并在武器上实现 `IAbilitySystemInterface`（在武器加入玩家背包时设置指向 `ASC` 的指针）。

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

可以通过查看这个 [较早版本的 GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735) 来了解实际用法。

优点：

1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流（例如用于弹药消耗的 `Cost GE`）
2. 对于物品数量非常少的情况，配置起来较为简单

限制：

1. 每一种武器类型都需要一个新的 `AttributeSet` 类。`ASC` 在功能上只能对同一个 `AttributeSet` 类使用一个实例，因为对某个 `Attribute` 的修改只会查找 `ASC::SpawnedAttributes` 中该 `AttributeSet` 类的**第一个**实例，后续同类实例会被忽略。
2. 基于上一点，玩家背包中同一类型的武器只能存在一把。
3. 移除 `AttributeSet` 是危险的。在 GASShooter 中，如果玩家被火箭筒炸死，角色会立刻把火箭筒从背包中移除（同时也会把对应的 `AttributeSet` 从 `ASC` 中移除）。当服务器随后同步火箭筒弹药 `Attribute` 的变化时，客户端的 `ASC` 上已经不存在该 `AttributeSet`，从而导致游戏崩溃。

###### 4.4.2.3.3 物品的 `ASC`

在每个物品上都放一个完整的 `AbilitySystemComponent` 是一种**非常极端**的做法。我本人没有这样做过，也没有在实际项目中见过这种方案。要让它正常工作，需要投入大量工程成本。

> 是否可行：存在多个 `AbilitySystemComponent`，它们拥有相同的 Owner，但使用不同的 Avatar（例如 Pawn 和 武器 / 物品 / 投射物，Owner 都设置为 PlayerState）？
>
> 我首先看到的问题是，需要在 Owner Actor 上实现 `IGameplayTagAssetInterface` 和 `IAbilitySystemInterface`。
> 前者也许还能实现：把所有 ASC 的 Tag 聚合起来即可（但要注意：`HasAllMatchingGameplayTags` 可能只会通过**跨 ASC 聚合**才能满足，简单地把调用转发给每个 ASC 再把结果 OR 在一起是不够的）。
> 后者就更加棘手了：**哪个 ASC 才是权威的？** 如果有人要应用一个 `GameplayEffect`，应该作用到哪一个 ASC 上？这些问题也许可以被解决，但这是整个方案中最困难的一部分：一个 Owner 底下会挂着多个 ASC。
>
> 不过，将 Pawn 和 武器分开、各自拥有独立 ASC，在概念上是说得通的。例如，用来区分“描述武器的 Tags”和“描述持有者 Pawn 的 Tags”。也许可以设计成：授予武器的 Tags 同样“作用”到 Owner 上，但仅此而已（例如 Attributes 和 GEs 彼此独立，但 Owner 会像我上面描述的那样聚合其拥有物品的 Tags）。这套方案理论上是可行的。
> 但如果多个 ASC 拥有**同一个 Owner**，事情就会变得非常棘手。

*Dave Ratti（Epic）对 [社区问题 #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

优点：

1. 可以使用现有的 `GameplayAbility` 和 `GameplayEffect` 工作流（例如用于弹药消耗的 `Cost GE`）
2. 可以复用 `AttributeSet` 类（每把武器的 ASC 上各有一份）

限制：

1. 工程成本未知
2. 是否真的可行，本身就是个问题

**[⬆ 回到目录](#目录)**

#### 4.4.3 定义属性

**`Attribute` 只能在 C++ 中定义**，并且必须写在 `AttributeSet` 的头文件里。建议在每个 `AttributeSet` 头文件的顶部添加下面这一组宏，它会为你的 `Attribute` 自动生成 Getter 和 Setter 函数。

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

一个可复制（Replicated）的生命值属性可以这样定义：

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

同时在头文件中声明 `OnRep` 函数：

```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

在 `AttributeSet` 的 `.cpp` 文件中，实现 `OnRep` 函数，并使用预测系统所需的 `GAMEPLAYATTRIBUTE_REPNOTIFY` 宏：

```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后，需要把该 `Attribute` 添加到 `GetLifetimeReplicatedProps` 中：

```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPNOTIFY_Always` 表示：即使本地值已经等于从服务器同步下来的值（通常是由于预测造成的），也依然会触发 `OnRep` 函数。默认情况下，如果本地值和服务器同步值相同，是不会调用 `OnRep` 的。

如果该 `Attribute` 不需要复制（例如 `Meta Attribute`），那么可以跳过 `OnRep` 和 `GetLifetimeReplicatedProps` 这两个步骤。

**[⬆ 回到目录](#目录)**

#### 4.4.4 初始化属性

有多种方式可以初始化 `Attributes`（即设置它们的 `BaseValue`，从而也设置 `CurrentValue` 的初始值）。Epic 推荐使用一个 **瞬时（Instant）`GameplayEffect`**。示例项目中也是采用的这种方式。

可以查看示例项目中的 `GE_HeroAttributes` 蓝图，了解如何创建一个用于初始化 `Attributes` 的瞬时 `GameplayEffect`。这个 `GameplayEffect` 的应用是在 C++ 中完成的。

如果你在定义 `Attributes` 时使用了 `ATTRIBUTE_ACCESSORS` 宏，那么在 `AttributeSet` 上会自动为每个 `Attribute` 生成一个初始化函数，你可以在 C++ 中随时调用。

```c++
// InitHealth(float InitialValue) 是为使用 `ATTRIBUTE_ACCESSORS` 宏定义的 Health 属性自动生成的函数
AttributeSet->InitHealth(100.0f);
```

更多初始化 `Attributes` 的方式可以查看 `AttributeSet.h`。

**注意：** 在 4.24 之前，`FAttributeSetInitterDiscreteLevels` 不能与 `FGameplayAttributeData` 一起使用。它是在 `Attributes` 还是原始 float 类型时创建的，因此会因为 `FGameplayAttributeData` 不是“Plain Old Data（POD）”而报错。这个问题在 4.24 中已修复：[https://issues.unrealengine.com/issue/UE-76557。](https://issues.unrealengine.com/issue/UE-76557。)

**[⬆ 回到目录](#目录)**

<a name="concepts-as-preattributechange"></a>

#### 4.4.5 PreAttributeChange()

`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)` 是 `AttributeSet` 中用于在 `Attribute` 的 `CurrentValue` 发生变化之前响应修改的主要函数之一。它是通过引用参数 `NewValue` 对即将生效的 `CurrentValue` 进行限制（Clamp）的理想位置。

例如，示例项目中对移动速度的修改进行了如下限制：

```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// 不能低于 150 units/s，也不能高于 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```

`GetMoveSpeedAttribute()` 函数是由我们在 `AttributeSet.h` 中添加的宏块自动生成的（参见 [定义属性](#concepts-as-attributes)）。

该函数会在任何 `Attribute` 发生变化时触发，无论是通过 `Attribute` 的 setter（由 `AttributeSet.h` 中的宏定义生成，参见 [定义属性](#concepts-as-attributes)），还是通过 [`GameplayEffects`](#concepts-ge)。

**注意：** 这里进行的任何限制都不会永久改变 `ASC` 上的 Modifier。它只会影响查询时返回的数值。这意味着，任何重新基于所有 Modifier 计算 `CurrentValue` 的系统（例如 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 和 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)）都需要再次实现相同的限制逻辑。

**注意：** Epic 在 `PreAttributeChange()` 的注释中明确指出，不应在这里处理游戏玩法事件，而应主要用于数值限制。推荐在 `Attribute` 发生变化时处理玩法事件的位置是
`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`（参见 [响应属性变化](#concepts-a-changes)）。

**[⬆ 回到目录](#目录)**

#### 4.4.6 PostGameplayEffectExecute()

`PostGameplayEffectExecute(const FGameplayEffectModCallbackData& Data)` **只会在瞬时（Instant）[`GameplayEffect`](#concepts-ge) 修改某个 `Attribute` 的 `BaseValue` 之后触发**。当 `Attribute` 因 `GameplayEffect` 发生变化时，这是一个合法的位置，用来做进一步的属性处理。

例如，在示例项目中，就是在这里将最终计算出的伤害 `Meta Attribute` 从生命值 `Attribute` 中扣除。如果还存在护盾 `Attribute`，则会先从护盾中扣除伤害，再将剩余伤害扣到生命值上。示例项目也在这里触发受击反应动画、显示飘字伤害数值，以及给击杀者分配经验和金币奖励。按设计，伤害 `Meta Attribute` **始终** 通过瞬时 `GameplayEffect` 传入，而不会通过 `Attribute` 的 setter。

其他同样只会通过瞬时 `GameplayEffects` 修改 `BaseValue` 的 `Attributes`（例如法力、体力），也可以在这里被限制（Clamp）到其对应的最大值 `Attribute`。

**注意：** 当 `PostGameplayEffectExecute()` 被调用时，`Attribute` 的数值修改已经发生，但尚未同步（replicate）到客户端，因此在这里进行限制不会导致客户端收到两次网络更新。客户端只会收到限制后的最终结果。

**[⬆ 回到目录](#目录)**

<a name="concepts-as-onattributeaggregatorcreated"></a>

#### 4.4.7 OnAttributeAggregatorCreated()

`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)` 会在该 `AttributeSet` 中为某个 `Attribute` 创建 `Aggregator` 时触发。它允许你对 [`FAggregatorEvaluateMetaData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html) 进行自定义设置。

`AggregatorEvaluateMetaData` 用于指导 `Aggregator` 如何根据应用到该属性上的所有 [`Modifiers`](#concepts-ge-mods) 来计算最终的 `CurrentValue`。默认情况下，这个元数据主要用于决定哪些 `Modifiers` 有资格参与计算。一个典型示例是 `MostNegativeMod_AllPositiveMods`：
允许 **所有正向 Modifier** 生效，但 **负向 Modifier 只取数值最负的那个**。

Paragon 就使用了这一机制：无论角色身上同时存在多少个减速效果，只会应用最强的那个减速，而所有加速效果都会叠加生效。那些不符合条件的 `Modifiers` 仍然存在于 `ASC` 中，只是不会被计入最终的 `CurrentValue`。当条件发生变化时（例如当前最负的减速效果结束），下一个最负的 `Modifier` 就可能变为合格并开始生效。

要实现“只允许最负的 `Modifier`，并允许所有正向 `Modifiers`”的效果，可以这样做：

在头文件中声明：

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;
```

在 cpp 中实现：

```c++
void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

你自定义的、用于判断 Modifier 合格条件的 `AggregatorEvaluateMetaData`，应当以静态变量的形式添加到 `FAggregatorEvaluateMetaDataLibrary` 中。

**[⬆ 回到目录](#目录)**

### 4.5 Gameplay Effects
#### 4.5.1 定义 Gameplay Effect
[`GameplayEffects`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html)（`GE`）是技能用来修改自身或他人 [`Attributes`](#concepts-a) 和 [`GameplayTags`](#concepts-gt) 的载体。它们既可以造成即时的 `Attribute` 变化，例如伤害或治疗，也可以施加长期的状态效果，如移动速度提升或眩晕。`UGameplayEffect` 类被设计为一个**纯数据（data-only）**类，用于定义单一的游戏效果，不应在 `GameplayEffects` 中添加额外逻辑。通常由策划创建大量继承自 `UGameplayEffect` 的蓝图子类。

`GameplayEffects` 通过 [`Modifiers`](#concepts-ge-mods) 和 [`Executions`（`GameplayEffectExecutionCalculation`）](#concepts-ge-ec) 来修改 `Attributes`。

`GameplayEffects` 具有三种持续类型：`Instant(瞬时)`、`Duration(持续时间)` 和 `Infinite(无限)`。

此外，`GameplayEffects` 还可以添加或触发 [`GameplayCues`](#concepts-gc)。`Instant` 类型的 `GameplayEffect` 会对 `GameplayCue` 的 `GameplayTags` 调用 `Execute`，而 `Duration` 或 `Infinite` 类型的 `GameplayEffect` 则会对 `GameplayCue` 的 `GameplayTags` 调用 `Add` 和 `Remove`。

| 持续类型   | GameplayCue 事件 | 使用场景                                                                                                                                                              |
| ---------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`  | Execute          | 用于对 `Attribute` 的 `BaseValue` 进行即时且永久的修改。不会应用任何 `GameplayTags`，甚至不会存在一帧。                                                               |
| `Duration` | Add & Remove     | 用于对 `Attribute` 的 `CurrentValue` 进行临时修改，并应用在 `GameplayEffect` 过期或被手动移除时会移除的 `GameplayTags`。持续时间在 `UGameplayEffect` 类或蓝图中指定。 |
| `Infinite` | Add & Remove     | 用于对 `Attribute` 的 `CurrentValue` 进行临时修改，并应用在 `GameplayEffect` 被移除时才会移除的 `GameplayTags`。它们不会自行过期，必须由技能或 `ASC` 手动移除。       |

`Duration` 和 `Infinite` 类型的 `GameplayEffects` 可以选择启用 `Periodic Effects(周期效果)`，按照其 `Period(周期)` 中定义的时间间隔（每 X 秒）周期性地应用其 `Modifiers` 和 `Executions`。在修改 `Attribute` 的 `BaseValue` 以及执行 `GameplayCues` 时，**`Periodic Effects` 会被当作 `Instant` 类型的 `GameplayEffects` 处理**。这种机制非常适合用于持续伤害（DOT）类效果。**注意：** `Periodic Effects` 无法被[预测](#concepts-p)。

`Duration` 和 `Infinite` 类型的 `GameplayEffects` 在应用后，如果其 `Ongoing Tag Requirements`（持续标签需求）未满足或重新满足（参见 [Gameplay Effect Tags](#concepts-ge-tags)），可以被临时关闭或重新开启。关闭 `GameplayEffect` 会移除其 `Modifiers` 和所应用的 `GameplayTags` 的效果，但并不会移除该 `GameplayEffect` 本身；重新开启时，则会再次应用其 `Modifiers` 和 `GameplayTags`。

如果你需要手动重新计算某个 `Duration` 或 `Infinite` 类型 `GameplayEffect` 的 `Modifiers`（例如使用了一个不依赖于 `Attributes` 的 `MMC`），可以调用
`UAbilitySystemComponent::ActiveGameplayEffects.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`，并传入与当前相同的等级，该等级可通过
`UAbilitySystemComponent::ActiveGameplayEffects.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()` 获取。
基于底层 `Attributes` 的 `Modifiers` 会在这些 `Attributes` 发生变化时自动更新。`SetActiveGameplayEffectLevel()` 在更新 `Modifiers` 时的关键函数如下：

```C++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// 私有函数，否则我们就可以直接调用这三个函数，而不必把 Level 设置成原本的值
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffects` 通常不会被实例化。当某个技能或 `ASC` 需要应用一个 `GameplayEffect` 时，会基于该 `GameplayEffect` 的 `ClassDefaultObject` 创建一个 [`GameplayEffectSpec`](#concepts-ge-spec)。成功应用的 `GameplayEffectSpec` 会被添加到一个名为 `FActiveGameplayEffect` 的新结构体中，而 `ASC` 会在一个名为 `ActiveGameplayEffects` 的特殊容器结构中对这些 `FActiveGameplayEffect` 进行管理。

**[⬆ 回到目录](#目录)**

#### 4.5.2 应用 Gameplay Effects
`GameplayEffects` 可以通过多种方式被应用，通常来自 [`GameplayAbilities`](#concepts-ga) 或 `ASC` 上的相关函数，一般表现为 `ApplyGameplayEffectTo` 这一类接口。这些不同的函数本质上都是便捷封装，最终都会在目标（`Target`）上调用 `UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`。

如果需要在 `GameplayAbility` 之外应用 `GameplayEffects`（例如由一个投射物触发），你需要先获取目标（`Target`）的 `ASC`，然后调用其 `ApplyGameplayEffectToSelf` 相关函数之一。

你可以通过绑定 `ASC` 的委托，来监听任何 `Duration` 或 `Infinite` 类型的 `GameplayEffects` 被应用到该 `ASC` 上的事件：
```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```

对应的回调函数签名为：
```c++
virtual void OnActiveGameplayEffectAddedCallback(
    UAbilitySystemComponent* Target,
    const FGameplayEffectSpec& SpecApplied,
    FActiveGameplayEffectHandle ActiveHandle
);
```

无论复制模式如何，服务器端都会调用该函数。自治代理（Autonomous Proxy）只会在 `Full` 和 `Mixed` 复制模式下，对已复制的 `GameplayEffects` 调用该回调；而模拟代理（Simulated Proxy）则仅会在 `Full` [复制模式](#concepts-asc-rm) 下调用该回调。

**[⬆ 回到目录](#目录)**

#### 4.5.3 移除 Gameplay Effects
`GameplayEffects` 可以通过多种方式被移除，通常来自 [`GameplayAbilities`](#concepts-ga) 或 `ASC` 上的相关函数，一般表现为 `RemoveActiveGameplayEffect` 这一类接口。这些不同的函数本质上都是便捷封装，最终都会在目标（`Target`）上调用 `FActiveGameplayEffectsContainer::RemoveActiveEffects()`。

如果需要在 `GameplayAbility` 之外移除 `GameplayEffects`，你需要先获取目标（`Target`）的 `ASC`，然后调用其 `RemoveActiveGameplayEffect` 相关函数之一。

你可以通过绑定 `ASC` 的委托，来监听任何 `Duration` 或 `Infinite` 类型的 `GameplayEffects` 从该 `ASC` 上被移除的事件：

```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```

对应的回调函数签名为：

```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

无论复制模式如何，服务器端都会调用该回调函数。自治代理（Autonomous Proxy）只会在 `Full` 和 `Mixed` 复制模式下，对已复制的 `GameplayEffects` 调用该回调；而模拟代理（Simulated Proxy）则仅会在 `Full` [复制模式](#concepts-asc-rm) 下调用该回调。

**[⬆ 回到目录](#目录)**
##### 4.5.4 数值设计 Modifiers(修饰符)
`Modifiers` 用于修改 `Attribute`，并且是**唯一**可以进行[预测性（predictively）](#concepts-p) `Attribute` 修改的方式。一个 `GameplayEffect` 可以包含零个或多个 `Modifiers`。每个 `Modifier` 只负责通过指定的运算方式修改**一个** `Attribute`。

| 运算类型       | 描述                                              |
| ---------- | ----------------------------------------------- |
| `Add`      | 将结果加到该 `Modifier` 指定的 `Attribute` 上。使用负值即可表示减法。 |
| `Multiply` | 将结果乘到该 `Modifier` 指定的 `Attribute` 上。            |
| `Divide`   | 用结果去除该 `Modifier` 指定的 `Attribute`。              |
| `Override` | 使用结果直接覆盖该 `Modifier` 指定的 `Attribute`。           |

`Attribute` 的 `CurrentValue` 是其 `BaseValue` 加上所有 `Modifiers` 聚合后的结果。`Modifiers` 的聚合公式在 `GameplayEffectAggregator.cpp` 的 `FAggregatorModChannel::EvaluateWithBase` 中定义如下：

```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

任何 `Override` 类型的 `Modifier` 都会覆盖最终结果，并且**最后应用的** `Modifier` 具有最高优先级。

**注意：** 对于百分比形式的修改，请务必使用 `Multiply` 运算，以确保其发生在加法之后。
**注意：** [预测（Prediction）](#concepts-p) 在处理百分比变化时存在一定问题。

`Modifiers` 一共有四种类型：`Scalable Float`、`Attribute Based`、`Custom Calculation Class` 和 `Set By Caller`。它们都会生成一个浮点值，然后根据 `Modifier` 的运算类型，用该值去修改指定的 `Attribute`。

| `Modifier` 类型            | 描述   |
| -------------------------- | -------- |
| `Scalable Float`           | `FScalableFloats` 是一种结构体，可以指向一个 Data Table，其中变量作为行、等级作为列。`Scalable Float` 会自动根据技能的当前等级（或在 [`GameplayEffectSpec`](#concepts-ge-spec) 中被覆盖的等级）读取指定表行中的数值。该数值还可以通过一个系数进一步调整。如果未指定 Data Table 或 Row，则该值默认为 1，此时可以通过系数来在所有等级下硬编码一个固定值。 |
| `Attribute Based`          | `Attribute Based` 类型的 `Modifier` 会从 `Source`（创建 `GameplayEffectSpec` 的对象）或 `Target`（接收 `GameplayEffectSpec` 的对象）身上，读取某个底层 `Attribute` 的 `CurrentValue` 或 `BaseValue`，然后再通过系数、系数前加值和系数后加值进行进一步计算。`Snapshotting` 表示在创建 `GameplayEffectSpec` 时捕获该 `Attribute` 的值；未启用快照则表示在应用 `GameplayEffectSpec` 时才捕获该 `Attribute` 的值。 |
| `Custom Calculation Class` | `Custom Calculation Class` 为复杂 `Modifiers` 提供了最大的灵活性。该类型使用一个 [`ModifierMagnitudeCalculation`](#concepts-ge-mmc) 类来计算数值，并同样可以通过系数、系数前加值和系数后加值对最终结果进行调整。 |
| `Set By Caller`            | `SetByCaller` 类型的 `Modifier` 的值在运行时由技能或创建 `GameplayEffectSpec` 的对象在 `GameplayEffectSpec` 上设置。例如，当你希望伤害值取决于玩家按住按钮蓄力的时间时，就可以使用 `SetByCaller`。`SetByCallers` 本质上是存储在 `GameplayEffectSpec` 上的 `TMap<FGameplayTag, float>`。`Modifier` 只是告诉 `Aggregator` 去查找与所提供 `GameplayTag` 关联的 `SetByCaller` 值。`Modifiers` 只能使用基于 `GameplayTag` 的 `SetByCaller`，`FName` 版本在这里被禁用。如果 `Modifier` 被设置为 `SetByCaller`，但在 `GameplayEffectSpec` 上找不到对应 `GameplayTag` 的 `SetByCaller` 值，游戏将在运行时抛出错误并返回 0，这在 `Divide` 运算中可能会导致问题。关于 `SetByCallers` 的使用方式，请参阅 [`SetByCallers`](#4591-setbycallers)。 |

**[⬆ 回到目录](#目录)**

###### 4.5.4.1 乘除运算修饰符
默认情况下，所有 `Multiply` 和 `Divide` 类型的 `Modifiers` 会先相加，然后再统一乘或除到 `Attribute` 的 `BaseValue` 上。

以下是GameplayAbilitySystem源码*摘自 `GameplayEffectAggregator.cpp`*
```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}

float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```

在这个公式中，`Multiply` 和 `Divide` 类型的 `Modifier` 的 `Bias` 值均为 `1`（而 `Add` 的 `Bias` 为 `0`），因此其计算形式大致如下：

```
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

这个公式会导致一些不太直观的结果。首先，它会在将结果乘或除到 `BaseValue` 之前，把所有 `Modifier` 的值先加在一起。大多数人会期望它们是相互乘或相互除的。举例来说，如果你有两个 `1.5` 的 `Multiply` 修饰器，通常会期望 `BaseValue` 乘以 `1.5 × 1.5 = 2.25`。但实际上，该公式会把两个 `1.5` 相加，使 `BaseValue` 乘以 `2`（即“提升50%  + 另一个提升 50%  = 提升100% ”）。这也正是 `GameplayPrediction.h` 示例中的情况：基础移速为 `500`，一个 `10%` 的移速加成后变为 `550`，再加一个 `10%` 的移速加成则变为 `600`。

其次，这个公式对可用数值存在一些未文档化的限制，因为它最初是为 《虚幻争霸》 设计的。

`Multiply` 和 `Divide` 的“乘法加法”规则如下：

* `(最多只能有一个值 < 1) 且 (任意数量的值在 [1, 2) 区间内)`
* `或者（存在一个值 >= 2）`

公式中的 `Bias` 实际上会抵消 `[1, 2)` 区间内数值的整数部分。第一个 `Modifier` 的 `Bias` 会从初始的 `Sum`（在循环前被设置为 `Bias`）中被抵消，这也解释了为什么单个值总是能正常工作，以及为什么一个 `< 1` 的值可以与 `[1, 2)` 区间内的值一起使用。

以下是一些 `Multiply` 的示例：

乘数：`0.5`
`1 + (0.5 - 1) = 0.5`，结果正确

乘数：`0.5, 0.5`
`1 + (0.5 - 1) + (0.5 - 1) = 0`，结果不正确，期望值为 `1`？多个小于 `1` 的乘数在“加法式乘法”中是没有意义的。Paragon 的设计是：[只使用数值最小（最负）的 `Multiply` `Modifier`](#cae-nonstackingge)，因此在任何时候，乘到 `BaseValue` 上的小于 `1` 的值最多只会有一个。

乘数：`1.1, 0.5`
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`，结果正确

乘数：`5, 5`
`1 + (5 - 1) + (5 - 1) = 9`，结果不正确，期望值为 `10`。结果始终为 `修饰器之和 - 修饰器数量 + 1`。

许多游戏都希望 `Multiply` 和 `Divide` 类型的 `Modifiers` 在应用到 `BaseValue` 之前，先彼此相乘或相除。要实现这一点，你需要**修改引擎代码**，调整 `FAggregatorModChannel::EvaluateWithBase()` 的实现。

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	float Division = MultiplyMods(Mods[EGameplayModOp::Division], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}

float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ 回到目录](#目录)**

###### 4.5.4.2 Gameplay Tags 在修饰符上的应用
每个 [Modifier](#454-修改-gameplay-effect) 都可以设置 `SourceTags` 和 `TargetTags`。它们的作用与 `GameplayEffect` 的 [`Application Tag requirements`](#457-gameplay-effect-tags) 类似，即只有在效果应用时才会考虑这些标签。也就是说，对于周期性（Periodic）或无限（Infinite）效果，这些标签仅在效果首次应用时生效，而不会在每次周期性执行时重新检查。

`Attribute Based` 类型的 Modifier 还可以设置 `SourceTagFilter(过滤器)` 和 `TargetTagFilter`。在确定该 `Attribute Based` Modifier 的源属性（source attribute）数值时，这些过滤器用于排除某些不符合条件的 Modifier。如果某个 Modifier 的源（source）或目标（target）不包含过滤器要求的全部标签，则该 Modifier 会被排除。

具体来说：`GameplayEffects` 会捕获源 ASC 和目标 ASC 的标签。源 ASC 的标签在创建 `GameplayEffectSpec` 时捕获，目标 ASC 的标签在效果执行时捕获。当判断某个无限或持续（Duration）效果的 Modifier 是否“符合条件”以应用（即其 Aggregator 是否符合条件）且设置了这些过滤器时，就会将捕获到的标签与过滤器进行比对。

**[⬆ 回到目录](#目录)**

##### 4.5.5 叠加游戏效果
默认情况下，`GameplayEffects` 会应用新的 `GameplayEffectSpec` 实例，这些实例在应用时不会关注或考虑之前已经存在的 `GameplayEffectSpec`。`GameplayEffects` 可以设置为**叠加（stacking）**，此时不会创建新的 `GameplayEffectSpec` 实例，而是改变当前已存在 `GameplayEffectSpec` 的叠加数量。叠加仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。

叠加有两种类型：按源聚合（Aggregate by Source）和按目标聚合（Aggregate by Target）。

| 叠加类型                | 描述                                                           |
| ------------------- | ------------------------------------------------------------ |
| Aggregate by Source | 对目标（Target）上的每个源 ASC（Source ASC）分别维护独立的叠加实例。每个源可以应用指定数量的叠加。  |
| Aggregate by Target | 目标（Target）上只维护一个叠加实例，不论源（Source）有多少。每个源可以施加叠加，但总叠加数量受共享上限限制。 |

叠加还可以设置过期策略、持续时间刷新以及周期重置策略。在 `GameplayEffect` 的 Blueprint 中，这些叠加选项通常带有悬浮提示（hover tooltip）方便理解。

示例项目中包含一个自定义 Blueprint 节点，用于监听 `GameplayEffect` 的叠加数量变化。HUD 的 UMG Widget 使用它来更新玩家拥有的被动护甲叠加数。这个 `AsyncTask` 会一直存在，直到手动调用 `EndTask()`，在示例中是在 UMG Widget 的 `Destruct` 事件中调用。请参见 `AsyncTaskEffectStackChanged.h/cpp`。

![监听 GameplayEffect 叠加变化的 BP 节点](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 回到目录](#目录)**

#### 4.5.6 授予能力
`GameplayEffects` 可以为 `ASC` 授予新的 [`GameplayAbilities`](#46-gameplay-abilities)。只有 `Duration` 和 `Infinite` 类型的 `GameplayEffects` 可以授予技能。

一个常见的用例是当你想让另一个玩家执行某个动作，例如击退或拉动时。你可以给他们应用一个 `GameplayEffect`，该效果会授予一个自动激活的技能（参见 [Passive Abilities](#4641-passive-abilities) 了解如何在授予技能时自动激活），从而让他们执行所需的动作。

设计者可以选择 `GameplayEffect` 授予哪些技能、授予的等级、绑定的 [输入](#462-binding-input-to-the-asc) 以及授予技能的移除策略。

| 移除策略                       | 描述                                                    |
| -------------------------- | ----------------------------------------------------- |
| Cancel Ability Immediately | 当授予技能的 `GameplayEffect` 从目标移除时，该技能会立即被取消并移除。          |
| Remove Ability on End      | 授予的技能可以完成当前动作，然后再从目标移除。                               |
| Do Nothing                 | 授予的技能不受授予 `GameplayEffect` 移除的影响。目标将永久拥有该技能，直到之后手动移除。 |

**[⬆ 回到目录](#目录)**

#### 4.5.7 Gameplay Effect Tags
`GameplayEffects` 携带多个 [`GameplayTagContainers`](#42-gameplay-tag)。设计者可以编辑每个类别的 `Added` 和 `Removed` `GameplayTagContainers`，编译后结果会显示在 `Combined` `GameplayTagContainer` 中。`Added` 标签表示此 `GameplayEffect` 添加的新标签，这些标签在其父类中不存在。`Removed` 标签表示父类中存在但子类中不再拥有的标签。

| 类别                              | 描述 |
| --------------------------------- | ---- |
| Gameplay Effect Asset Tags        | `GameplayEffect` 自身拥有的标签，这些标签本身没有功能，仅用于描述该 `GameplayEffect`。 |
| Granted Tags                      | 存在于 `GameplayEffect` 的标签，同时也会授予应用该 `GameplayEffect` 的 `ASC`。当 `GameplayEffect` 被移除时，这些标签会从 `ASC` 上移除。仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。    |
| Ongoing Tag Requirements          | 应用后，这些标签决定 `GameplayEffect` 是否处于激活状态。一个 `GameplayEffect` 即便被关闭仍然可以存在。如果由于未满足 Ongoing Tag Requirements 而关闭，但随后条件满足，`GameplayEffect` 会重新激活并重新应用其修饰器。仅适用于 `Duration` 和 `Infinite` 类型的 `GameplayEffects`。 |
| Application Tag Requirements      | 目标（Target）上的标签，用于判断 `GameplayEffect` 是否可以应用到该目标。如果不满足这些条件，则 `GameplayEffect` 不会应用。   |
| Remove Gameplay Effects with Tags | 当此 `GameplayEffect` 成功应用时，目标上拥有任何这些标签（无论是 Asset Tags 还是 Granted Tags）的 `GameplayEffects` 会被移除。    |

**[⬆ 回到目录](#目录)**

#### 4.5.8 免疫
`GameplayEffects` 可以授予免疫效果，基于 [`GameplayTags`](#concepts-gt) 阻止其他 `GameplayEffects` 的应用。虽然免疫也可以通过其他方式实现，比如 `Application Tag Requirements`，但使用此系统可以提供一个委托，用于在因免疫而阻止 `GameplayEffects` 时触发：`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`。

`GrantedApplicationImmunityTags` 会检查源 ASC（包括源技能的 `AbilityTags`，如果存在的话）是否拥有指定的标签。这是一种基于标签，为特定角色或来源的所有 `GameplayEffects` 提供免疫的方法。

`Granted Application Immunity Query` 会检查传入的 `GameplayEffectSpec` 是否匹配任何查询，以决定是否阻止或允许其应用。

这些查询在 `GameplayEffect` 的 Blueprint 中通常带有悬浮提示（hover tooltip），方便理解和使用。

**[⬆ 回到目录](#目录)**

#### 4.5.9 Gameplay Effect Spec(游戏效果规范)
[`GameplayEffectSpec`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html)（简称 `GESpec`）可以看作是 `GameplayEffects` 的实例化。它们保存了所代表的 `GameplayEffect` 类的引用、创建时的等级以及创建者信息。与应由设计者在运行前创建的 `GameplayEffects` 不同，`GameplayEffectSpecs` 可以在运行时自由创建和修改。在应用 `GameplayEffect` 时，会从该 `GameplayEffect` 创建一个 `GameplayEffectSpec`，实际应用到目标的就是这个实例。

`GameplayEffectSpecs` 是通过 `UAbilitySystemComponent::MakeOutgoingSpec()` 创建的，该函数可在 Blueprint 中调用（BlueprintCallable）。`GameplayEffectSpecs` 不必立即应用。常见做法是将一个 `GameplayEffectSpec` 传给由技能生成的投射物，投射物可以在命中目标时应用该效果。当 `GameplayEffectSpecs` 成功应用后，会返回一个新的结构体 `FActiveGameplayEffect`。

`GameplayEffectSpec` 的主要内容包括：

* 此 `GameplayEffectSpec` 所创建自的 `GameplayEffect` 类。
* 此 `GameplayEffectSpec` 的等级。通常与创建该 `GameplayEffectSpec` 的技能等级相同，但可以不同。
* 此 `GameplayEffectSpec` 的持续时间。默认为 `GameplayEffect` 的持续时间，但可以不同。
* 周期性效果的周期（Period）。默认为 `GameplayEffect` 的周期，但可以不同。
* 此 `GameplayEffectSpec` 当前的叠加数量（stack count）。叠加上限由 `GameplayEffect` 决定。
* [`GameplayEffectContextHandle`](#concepts-ge-context)，记录谁创建了此 `GameplayEffectSpec`。
* 创建时因快照（snapshotting）捕获的 `Attributes`。
* `DynamicGrantedTags`，在 `GameplayEffect` 授予的 `GameplayTags` 基础上，额外授予目标的标签。
* `DynamicAssetTags`，在 `GameplayEffect` 拥有的 `AssetTags` 基础上，额外拥有的标签。
* `SetByCaller` 的 `TMaps`。

**[⬆ 回到目录](#目录)**

##### 4.5.9.1 SetByCallers
`SetByCallers` 允许 `GameplayEffectSpec` 携带与某个 `GameplayTag` 或 `FName` 关联的 `float` 数值。它们分别存储在 `GameplayEffectSpec` 上对应的 `TMap` 中：`TMap<FGameplayTag, float>` 和 `TMap<FName, float>`。这些数值既可以作为 `GameplayEffect` 上的 `Modifiers` 使用，也可以作为一种通用手段来传递浮点数。一个常见用法是，将在能力内部生成的数值数据通过 `SetByCallers` 传递给 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 或 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc)。

| `SetByCaller` 用途 | 说明                                                                                                                                                                                                                                                   |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Modifiers`      | 必须事先在 `GameplayEffect` 类中定义。只能使用 `GameplayTag` 版本。如果在 `GameplayEffect` 类中定义了某个 `SetByCaller`，但对应的 `GameplayEffectSpec` 中没有该 tag 与 float 值的配对，那么在应用 `GameplayEffectSpec` 时会发生运行时错误，并返回 0。这在 `Divide` 运算中尤其可能成为问题。参见 [`Modifiers`](#concepts-ge-mods)。 |
| 其他用途             | 不需要在任何地方提前定义。读取一个在 `GameplayEffectSpec` 中不存在的 `SetByCaller` 时，可以返回开发者定义的默认值，并可选择是否输出警告。                                                                                                                                                              |

在 Blueprint 中设置 `SetByCaller` 数值时，使用对应版本的 Blueprint 节点（`GameplayTag` 或 `FName`）：

![Assigning SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/setbycaller.png)

在 Blueprint 中读取 `SetByCaller` 数值时，需要在你的 Blueprint Library 中创建自定义节点。

在 C++ 中设置 `SetByCaller` 数值时，使用你需要的函数版本（`GameplayTag` 或 `FName`）：

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

在 C++ 中读取 `SetByCaller` 数值时，使用你需要的函数版本（`GameplayTag` 或 `FName`）：

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议优先使用 `GameplayTag` 版本而不是 `FName` 版本，这样可以避免在 Blueprint 中出现拼写错误。

**[⬆ 回到目录](#目录)**
用来在诸如 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、[`AttributeSets`](#concepts-as) 和 [`GameplayCues`](#concepts-gc) 等位置之间传递任意数据。

#### 4.5.10 Gameplay Effect Context
[`GameplayEffectContext`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html) 结构体用于保存 `GameplayEffectSpec` 的施加者（instigator）以及 [`TargetData`](#concepts-targeting-data) 等信息。
它同样非常适合被继承，用来在诸如 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) / [`GameplayEffectExecutionCalculations`](#concepts-ge-ec)、[`AttributeSets`](#concepts-as) 和 [`GameplayCues`](#concepts-gc) 等位置之间传递任意数据。

要继承 `GameplayEffectContext`：
1. 继承 `FGameplayEffectContext`
2. 重写 `FGameplayEffectContext::GetScriptStruct()`
3. 重写 `FGameplayEffectContext::Duplicate()`
4. 如果你的新数据需要进行网络复制，重写 `FGameplayEffectContext::NetSerialize()`
5. 为你的子类实现 `TStructOpsTypeTraits`，方式与父结构体 `FGameplayEffectContext` 相同
6. 在你的 [`AbilitySystemGlobals`](#concepts-asg) 类中重写 `AllocGameplayEffectContext()`，返回该子类的新对象

[GASShooter](https://github.com/tranek/GASShooter) 使用了一个继承自 `GameplayEffectContext` 的子类来添加 `TargetData`，并在 `GameplayCues` 中访问这些数据，主要用于霰弹枪这种可以命中多个敌人的情况。

**[⬆ 回到目录](#目录)**

#### 4.5.11 Modifier Magnitude Calculation(MMC)
[`ModifierMagnitudeCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html)（`ModMagCalc` 或 `MMC`）是用于 `GameplayEffects` 中作为 [`Modifiers`](#454-数值设计-modifiers修饰符) 的强大类。它们在功能上类似于 [`GameplayEffectExecutionCalculations`](#4512-gameplay-effect-execution-calculation)，但能力更受限，更重要的是——它们可以被[预测](#concepts-p)。
`MMC` 的唯一职责是在 `CalculateBaseMagnitude_Implementation()` 中返回一个 `float` 值。你可以在 Blueprint 和 C++ 中继承并重写该函数。

`MMCs` 可以用于任何持续类型的 `GameplayEffects` —— `Instant`、`Duration`、`Infinite` 或 `Periodic`。

`MMCs` 的强大之处在于：它们能够捕获 `GameplayEffect` 的 `Source` 或 `Target` 上任意数量的 `Attributes`，并且可以完整访问 `GameplayEffectSpec`，以读取 `GameplayTags` 和 `SetByCallers`。`Attributes` 可以选择是否进行快照（snapshot）。被快照的 `Attributes` 会在创建 `GameplayEffectSpec` 时捕获；未快照的 `Attributes` 会在 `GameplayEffectSpec` 应用时捕获，并且对于 `Infinite` 和 `Duration` 类型的 `GameplayEffects`，当 `Attribute` 发生变化时会自动更新。捕获 `Attributes` 时，会基于 `ASC` 上已有的修饰器重新计算它们的 `CurrentValue`。这个重新计算过程**不会**调用 `AbilitySet` 中的 [`PreAttributeChange()`](#445-preattributechange)，因此任何数值钳制（clamping）都需要在这里再次处理。

| Snapshot | Source or Target | 在 `GameplayEffectSpec` 的何时捕获 | 对于 `Infinite` 或 `Duration` `GE`，当 `Attribute` 变化时是否自动更新 |
| -------- | ---------------- | ---------------------------- | ------------------------------------------------------- |
| Yes      | Source           | 创建时                          | No                                                      |
| Yes      | Target           | 应用时                          | No                                                      |
| No       | Source           | 应用时                          | Yes                                                     |
| No       | Target           | 应用时                          | Yes                                                     |

`MMC` 返回的最终 `float` 值，还可以在 `GameplayEffect` 的 `Modifier` 中进一步通过系数（coefficient）以及系数前、系数后的加法进行修饰。

下面是一个示例 `MMC`，它捕获 `Target` 的法力（mana）`Attribute`，并在中毒效果中减少法力值；减少的数值取决于 `Target` 当前的法力比例，以及 `Target` 是否拥有某个特定的 tag：

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在 `MMC` 构造函数中将 `FGameplayEffectAttributeCaptureDefinition` 添加到 `RelevantAttributesToCapture`，却尝试去捕获 `Attributes`，就会得到一个关于在捕获时缺少 Spec 的错误。如果你不需要捕获任何 `Attributes`，那么就不必向 `RelevantAttributesToCapture` 中添加任何内容。

**[⬆ 回到目录](#目录)**

#### 4.5.12 Gameplay Effect Execution Calculation
[`GameplayEffectExecutionCalculations`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)（简称 `ExecutionCalculation`、`Execution`（在插件源码中常用此术语）或 `ExecCalc`）是 `GameplayEffects` 对 `ASC` 进行修改的最强大方式。与 [`ModifierMagnitudeCalculations`](#concepts-ge-mmc) 类似，这些类也可以捕获 `Attributes`，并可选择是否进行快照（snapshot）。不同于 `MMC`，`ExecutionCalculations` 可以修改多个 `Attribute`，并基本上可以实现程序员希望的任何逻辑。其代价是，它们无法被[预测](#concepts-p)，并且必须用 C++ 实现。

`ExecutionCalculations` 仅能用于 `Instant` 和 `Periodic` 类型的 `GameplayEffects`。凡是名称中带有 “Execute” 的通常指的就是这两种类型的 `GameplayEffects`。

快照（snapshot）会在 `GameplayEffectSpec` 创建时捕获 `Attribute`；而不快照的情况下，会在 `GameplayEffectSpec` 应用时捕获 `Attribute`。捕获 `Attributes` 时，会根据 `ASC` 上现有的修饰器重新计算它们的 `CurrentValue`。这个重新计算过程**不会**调用 `AbilitySet` 中的 [`PreAttributeChange()`](#concepts-as-preattributechange)，因此任何数值钳制（clamping）都需要在这里再次处理。

| Snapshot | Source or Target | 在 `GameplayEffectSpec` 捕获时机 |
| -------- | ---------------- | --------------------------- |
| Yes      | Source           | 创建时                         |
| Yes      | Target           | 应用时                         |
| No       | Source           | 应用时                         |
| No       | Target           | 应用时                         |

设置 `Attribute` 捕获的方式，遵循 Epic 的 ActionRPG Sample Project 的模式：定义一个结构体，用于保存捕获信息和定义捕获方式，并在该结构体的构造函数中创建一份副本。每个 `ExecCalc` 都会有一个类似的结构体。**注意：** 每个结构体必须有唯一名称，因为它们共享同一个命名空间。结构体名称重复会导致捕获 `Attributes` 行为异常（通常是捕获了错误的 `Attributes` 值）。

对于 `Local Predicted`、`Server Only` 和 `Server Initiated` 的 [`GameplayAbilities`](#concepts-ga)，`ExecCalc` 仅在服务器端调用。

最常见的 `ExecCalc` 示例是根据复杂公式计算受到的伤害，该公式会读取 `Source` 和 `Target` 上的多个 `Attributes`。示例项目中包含了一个简单的 `ExecCalc` 来计算伤害：它从 `GameplayEffectSpec` 的 [`SetByCaller`](#concepts-ge-spec-setbycaller) 读取伤害值，然后根据从 `Target` 捕获的护甲（armor）`Attribute` 来减免伤害。具体实现见 `GDDamageExecCalculation.cpp/.h`。

**[⬆ 回到目录](#目录)**

##### 4.5.12.1 向 Execution Calculation 传递数据
除了捕获 `Attributes` 外，还有几种方式可以向 `ExecutionCalculation` 传递数据。

###### 4.5.12.1.1 SetByCaller
任何在 `GameplayEffectSpec` 上设置的 [`SetByCallers`](#concepts-ge-spec-setbycaller) 都可以在 `ExecutionCalculation` 中直接读取。

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

###### 4.5.12.1.2 基于后备数据的属性计算修饰符
如果你想要为 `GameplayEffect` 硬编码数值，可以通过 `CalculationModifier` 传入，这个 `CalculationModifier` 使用某个已捕获的 `Attribute` 作为基础数据（backing data）。

在这个示例截图中，我们在捕获的 Damage `Attribute` 上增加了 50。你也可以将模式设置为 `Override`，这样就只使用硬编码的值，而忽略原始 `Attribute`。

![Backing Data Attribute Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdataattribute.png)

当 `ExecutionCalculation` 捕获该 `Attribute` 时，就会读取这个值。

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

###### 4.5.12.1.3 基于后备数据的临时变量计算修饰符
如果你想为 `GameplayEffect` 硬编码数值，可以通过 `CalculationModifier` 传入，这里使用的是 C++ 中称为 `Temporary Variable` 或 `Transient Aggregator` 的机制。每个 `Temporary Variable` 都关联一个 `GameplayTag`。

在示例截图中，我们通过 `Data.Damage` 的 `GameplayTag` 将 50 添加到一个 `Temporary Variable`。

![Backing Data Temporary Variable Calculation Modifier](https://github.com/tranek/GASDocumentation/raw/master/Images/calculationmodifierbackingdatatempvariable.png)

在 `ExecutionCalculation` 的构造函数中添加这些基础 `Temporary Variables`：

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation` 使用类似于 `Attribute` 捕获的特殊函数读取这个值：

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

###### 4.5.12.1.4 Gameplay Effect 上下文
你可以通过自定义的 [`GameplayEffectContext` 在 `GameplayEffectSpec`](#459-gameplay-effect-spec游戏效果规范) 上传递数据到 `ExecutionCalculation`。

在 `ExecutionCalculation` 中，可以通过 `FGameplayEffectCustomExecutionParameters` 访问 `EffectContext`：

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你需要修改 `GameplayEffectSpec` 或 `EffectContext`：

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

在 `ExecutionCalculation` 中修改 `GameplayEffectSpec` 时需要小心。参考 `GetOwningSpecForPreExecuteMod()` 的注释：

```c++
/** 非 const 访问。特别是在捕获 Attribute 后修改 Spec 时，请小心。 */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 回到目录](#目录)**

#### 4.5.13 CAR(自定义应用条件)
[`CustomApplicationRequirement`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)（简称 `CAR`）类让设计师可以比 `GameplayEffect` 上的简单 `GameplayTag` 检查更精细地控制一个 `GameplayEffect` 是否可以被应用。
在 Blueprint 中可以通过重写 `CanApplyGameplayEffect()` 实现，在 C++ 中则通过重写 `CanApplyGameplayEffect_Implementation()` 实现。

使用 `CAR` 的典型场景示例：
* `Target` 必须拥有一定数量的某个 `Attribute`
* `Target` 必须拥有某个 `GameplayEffect` 的一定层数（stacks）

`CAR` 还可以实现更高级的逻辑，例如检查 `Target` 上是否已经存在该 `GameplayEffect` 的实例，并在已有实例上[修改持续时间](#concepts-ge-duration)而不是应用新的实例（此时 `CanApplyGameplayEffect()` 返回 false）。

**[⬆ 回到目录](#目录)**

#### 4.5.14 Cost Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 可以选择性地使用一个 `GameplayEffect` 来作为能力的消耗（Cost）。消耗表示 `ASC` 需要拥有多少某个 `Attribute` 才能激活该 `GameplayAbility`。如果 `GA` 无法支付这个 `Cost GE`，就无法激活能力。这个 `Cost GE` 应该是一个 `Instant` 类型的 `GameplayEffect`，包含一个或多个从 `Attributes` 中扣除数值的 `Modifiers`。默认情况下，`Cost GEs` 设计为可预测（predicted），因此推荐保持这一能力，不要使用 `ExecutionCalculations`。对于复杂的消耗计算，使用 `MMCs` 是完全可行且推荐的。

初期，通常每个有消耗的 `GA` 会有一个独立的 `Cost GE`。更高级的技巧是为多个 `GAs` 重用同一个 `Cost GE`，然后在从 `Cost GE` 创建的 `GameplayEffectSpec` 上修改特定于 `GA` 的数据（消耗值定义在 `GA` 上）。**这只适用于 `Instanced` 能力。**

重用 `Cost GE` 的两种方法：

1. **使用 `MMC`。** 这是最简单的方法。创建一个 [`MMC`](#concepts-ge-mmc)，从 `GameplayEffectSpec` 中获取的 `GameplayAbility` 实例读取消耗值：
```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个例子中，消耗值是我在 `GameplayAbility` 子类中添加的一个 `FScalableFloat`：

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/costmmc.png)

2. **重写 `UGameplayAbility::GetCostGameplayEffect()`。** 重写此函数，并[在运行时创建 `GameplayEffect`](#concepts-ge-dynamic)，读取 `GameplayAbility` 上的消耗值。


**[⬆ 回到目录](#目录)**

#### 4.5.15 Cooldown Gameplay Effect
[`GameplayAbilities`](#concepts-ga) 可以选择性地使用一个 `GameplayEffect` 作为能力的冷却（Cooldown）。冷却决定了能力激活后，需要多久才能再次激活。如果 `GA` 仍在冷却中，则无法激活。这个 `Cooldown GE` 应该是一个 `Duration` 类型的 `GameplayEffect`，不包含 `Modifiers`，并且每个 `GameplayAbility` 或每个能力槽（如果游戏允许可互换的能力共享冷却）都应该有唯一的 `GameplayTag`，放在 `GameplayEffect` 的 `GrantedTags` 中（称为“Cooldown Tag”）。`GA` 实际上是检查 `Cooldown Tag` 的存在，而不是 `Cooldown GE` 本身。默认情况下，`Cooldown GEs` 设计为可预测（predicted），因此推荐保持这一能力，不要使用 `ExecutionCalculations`。对于复杂冷却计算，使用 `MMCs` 是完全可行且推荐的。

初期，通常每个有冷却的 `GA` 会有一个独立的 `Cooldown GE`。更高级的技巧是为多个 `GAs` 重用同一个 `Cooldown GE`，然后在从 `Cooldown GE` 创建的 `GameplayEffectSpec` 上修改特定于 `GA` 的数据（冷却时长和 `Cooldown Tag` 定义在 `GA` 上）。**这只适用于 `Instanced` 能力。**

重用 `Cooldown GE` 的两种方法：

---

##### 1. 使用 [`SetByCaller`](#concepts-ge-spec-setbycaller)

这是最简单的方法。将共享 `Cooldown GE` 的持续时间设置为 `SetByCaller`，并指定一个 `GameplayTag`。在 `GameplayAbility` 子类中，定义一个 float / `FScalableFloat` 用于持续时间，一个 `FGameplayTagContainer` 保存唯一的 `Cooldown Tag`，以及一个临时的 `FGameplayTagContainer`，用于返回 `Cooldown Tag` 与 `Cooldown GE` 标签的联合结果。

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// 临时容器，用于在 GetCooldownTags() 中返回。
// 它是 CooldownTags 与 Cooldown GE 的 cooldown tags 的联合。
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

重写 `UGameplayAbility::GetCooldownTags()` 返回 `Cooldown Tags` 与 `Cooldown GE` 标签的联合：

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset(); // 清空临时标签，以防能力冷却标签发生变化
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

重写 `UGameplayAbility::ApplyCooldown()`，注入 `Cooldown Tags` 并将 `SetByCaller` 添加到冷却 `GameplayEffectSpec`：

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("OurSetByCallerTag")), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

在示例中，冷却持续时间的 `Modifier` 使用 `SetByCaller`，其 `Data Tag` 为 `Data.Cooldown`（对应代码中的 `OurSetByCallerTag`）。

![Cooldown GE with SetByCaller](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownsbc.png)

---

##### 2. 使用 [`MMC`](#concepts-ge-mmc)

与上面方法类似，但不使用 `SetByCaller` 设置持续时间，而是将持续时间设置为一个自定义计算类（`MMC`）：

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// 临时容器，用于在 GetCooldownTags() 中返回
UPROPERTY(Transient)
FGameplayTagContainer TempCooldownTags;
```

同样重写 `UGameplayAbility::GetCooldownTags()` 返回联合标签：

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	MutableTags->Reset();
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

重写 `UGameplayAbility::ApplyCooldown()`，注入 `Cooldown Tags` 到冷却 `GameplayEffectSpec`：

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

创建一个 `MMC` 来计算冷却持续时间：

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownmmc.png)

**[⬆ 回到目录](#目录)**

##### 4.5.15.1 Get the Cooldown Gameplay Effect's Remaining Time
```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		// 创建一个查询，用于匹配任何拥有的冷却标签
		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);

		if (DurationAndTimeRemaining.Num() > 0)
		{
			// 找到持续时间最长的冷却效果
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**注意：** 在客户端查询冷却剩余时间，需要客户端能够接收已复制的 `GameplayEffects`。这取决于它们的 `ASC` [复制模式](#concepts-asc-rm)。

##### 4.5.15.2 Listening for Cooldown Begin and End
要监听冷却开始，你可以选择以下两种方式：

1. 监听 `Cooldown GE` 被应用：绑定到

   ```c++
   AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf
   ```

   这样你可以获取触发的 `GameplayEffectSpec`，从中判断这个 `Cooldown GE` 是本地预测的还是服务器修正的。
2. 监听 `Cooldown Tag` 被添加：绑定到

   ```c++
   AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)
   ```

   推荐使用监听 `Cooldown GE` 被添加的方式，因为你可以访问应用它的 `GameplayEffectSpec`。

---

要监听冷却结束，你也有两种方式：

1. 监听 `Cooldown GE` 被移除：绑定到

   ```c++
   AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()
   ```
2. 监听 `Cooldown Tag` 被移除：绑定到

   ```c++
   AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)
   ```

   推荐监听 `Cooldown Tag` 被移除，因为当服务器的修正 `Cooldown GE` 到来时，它会移除本地预测的 `Cooldown GE`，此时 `OnAnyGameplayEffectRemovedDelegate()` 会触发，即使仍在冷却中。而 `Cooldown Tag` 在移除预测的 `Cooldown GE` 并应用服务器修正的 `Cooldown GE` 时不会发生变化。

---

**注意：** 在客户端监听 `GameplayEffect` 的添加或移除，需要客户端能够接收已复制的 `GameplayEffects`。这取决于它们的 `ASC` [复制模式](#concepts-asc-rm)。

示例项目提供了一个自定义 Blueprint 节点，用于监听冷却开始和结束。HUD 的 UMG Widget 使用它来更新陨石技能的冷却剩余时间。这个 `AsyncTask` 会一直存在，直到手动调用 `EndTask()`，通常在 UMG Widget 的 `Destruct` 事件中执行。参考 [`AsyncTaskCooldownChanged.h/cpp`](Source/GASDocumentation/Private/Characters/Abilities/AsyncTaskCooldownChanged.cpp)。

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

##### 4.5.15.3 Predicting Cooldowns
冷却目前实际上无法被真正预测。我们可以在本地预测的 `Cooldown GE` 被应用时启动 UI 上的冷却计时器，但 `GameplayAbility` 的实际冷却依赖于服务器上冷却剩余时间。由于玩家的网络延迟，本地预测的冷却可能已经结束，但服务器上的能力仍在冷却中，这会阻止该能力立即重新激活，直到服务器的冷却结束。

示例项目的处理方式是：当本地预测的冷却开始时，将陨石技能的 UI 图标置灰，然后在服务器修正的 `Cooldown GE` 到来时启动冷却计时器。

这带来的游戏影响是：网络延迟高的玩家在短冷却能力的使用频率上会低于延迟低的玩家，从而处于劣势。Fortnite 通过武器使用自定义记账机制，而不是依赖冷却 `GameplayEffects`，来规避这个问题。

真正实现预测冷却（即玩家本地冷却结束时就能激活能力，而服务器仍在冷却）是 Epic 希望在 [未来版本的 GAS](#concepts-p-future) 中实现的功能。

**[⬆ 回到目录](#目录)**

#### 4.5.16 Changing Active Gameplay Effect Duration
要修改 `Cooldown GE` 或任何 `Duration` `GameplayEffect` 的剩余时间，需要修改该效果的 `GameplayEffectSpec` 的 `Duration`，并更新以下内容：

* `StartServerWorldTime`
* `CachedStartServerWorldTime`
* `StartWorldTime`
* 重新运行 `CheckDuration()` 检查持续时间

在服务器上执行以上操作并标记 `FActiveGameplayEffect` 为脏项，会将更改同步到客户端。

**注意：** 这个实现涉及 `const_cast`，可能不是 Epic 官方推荐的修改持续时间的方法，但实际使用中效果良好。

示例代码：

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	// 使用 const_cast 修改 FActiveGameplayEffect
	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();

	// 标记效果为脏项并重新检查持续时间
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	// 广播持续时间变更事件
	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

这段代码的核心思路是直接修改 `ActiveGameplayEffect` 的持续时间，并同步到系统中以触发客户端更新。

**[⬆ 回到目录](#目录)**

#### 4.5.17 Creating Dynamic Gameplay Effects at Runtime
在运行时创建动态 `GameplayEffects` 是一个高级用法，一般不需要频繁使用。

* 只有 `Instant` 类型的 `GameplayEffects` 可以在 C++ 中从零动态创建。
* `Duration` 和 `Infinite` 类型的 `GameplayEffects` **不能**在运行时动态创建，因为它们在复制到客户端时会查找对应的 `GameplayEffect` 类定义，而这个类在运行时不存在。要实现类似功能，应当像在编辑器中一样创建一个原型（archetype）`GameplayEffect` 类，然后在运行时通过修改 `GameplayEffectSpec` 实例来定制所需行为。

运行时创建的 `Instant` `GameplayEffects` 也可以在 [本地预测](#concepts-p) 的 `GameplayAbility` 内调用。不过，目前尚不清楚这种动态创建是否会产生副作用。

##### Examples

示例项目创建了一个动态 `GameplayEffect`，用于在角色被击杀时将金币和经验点发送给击杀者，在其 `AttributeSet` 中处理：

```c++
// 创建一个瞬时动态 Gameplay Effect 来发放奖励
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个示例展示了在本地预测的 `GameplayAbility` 内创建运行时 `GameplayEffect`。请自行评估风险（参见代码注释）：

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// 在运行时创建 GE
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // 仅瞬时 GE 可用

		// 添加一个简单的可缩放 float Modifier，将 MyAttribute 覆盖为 42
		// 在实际应用中，可通过 TriggerEventData 传递信息
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// 创建 GESpec 避免 ASC 从 GE 的类默认对象创建 Spec
		// 由于这里是动态 GE，如果使用默认行为，会创建基于 GameplayEffect 类的 Spec，从而丢失 Modifier
		// 注意：目前未知这种“hack”是否有副作用
		// Spec 会持有 GE 的 UPROPERTY，避免 GE 被垃圾回收器回收
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 回到目录](#目录)**

#### 4.5.18 Gameplay Effect Containers
Epic 的 [Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 实现了一个名为 `FGameplayEffectContainer` 的结构。在原生 GAS 中没有这个概念，但它在管理 `GameplayEffects` 和 [`TargetData`](#concepts-targeting-data) 时非常方便。它会自动处理一些工作，例如从 `GameplayEffects` 创建 `GameplayEffectSpecs`，并在其 `GameplayEffectContext` 中设置默认值。在 `GameplayAbility` 中创建一个 `GameplayEffectContainer` 并将其传递给生成的投射物非常简单直观。我在包含的示例项目中没有实现 `GameplayEffectContainers`，以展示在原生 GAS 中如何操作，但我强烈建议你了解它们，并考虑将其加入你的项目。

要访问 `GameplayEffectContainers` 内的 `GESpecs`（例如添加 `SetByCallers`），可以解构 `FGameplayEffectContainer`，然后通过 `GESpecs` 数组中的索引访问对应的 `GESpec` 引用。这要求你事先知道要访问的 `GESpec` 的索引。

![SetByCaller with a GameplayEffectContainer](https://github.com/tranek/GASDocumentation/raw/master/Images/gecontainersetbycaller.png)

`GameplayEffectContainers` 还包含一种可选的高效 [目标选择](#concepts-targeting-containers) 方法。

**[⬆ 回到目录](#目录)**

### 4.6 Gameplay Abilities
#### 4.6.1 Gameplay Ability Definition
[`GameplayAbilities`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)（`GA`）是指游戏中 `Actor` 可以执行的任何动作或技能。例如，一个角色可以同时拥有多个 `GameplayAbility`，比如奔跑和开枪。它们可以通过 Blueprint 或 C++ 创建。

`GameplayAbilities` 示例：
* 跳跃
* 奔跑
* 开枪
* 每隔 X 秒被动格挡一次攻击
* 使用药水
* 开门
* 收集资源
* 建造建筑

不建议用 `GameplayAbilities` 实现的事情：
* 基础移动输入
* 与 UI 的部分交互——例如不要用 `GameplayAbility` 来购买商店中的物品

这些不是硬性规则，仅为建议。你的设计和实现可以有所不同。

`GameplayAbilities` 内置默认功能，可通过等级来修改属性变化量，或者改变 `GameplayAbility` 的功能。

`GameplayAbilities` 在拥有者客户端和/或服务器上运行，具体取决于 [`Net Execution Policy`](#concepts-ga-net)，但不会在模拟代理（simulated proxies）上运行。`Net Execution Policy` 决定了 `GameplayAbility` 是否会被本地[预测](#concepts-p)。它们包括对[可选消耗和冷却 GameplayEffects](#concepts-ga-commit)的默认处理。`GameplayAbilities` 使用 [`AbilityTasks`](#concepts-at) 来处理需要随时间发生的动作，例如等待事件、等待属性变化、等待玩家选择目标，或者通过 `Root Motion Source` 移动 `Character`。**模拟客户端不会运行 `GameplayAbilities`**，服务器执行能力时，所有视觉效果（如动画蒙太奇）会通过 `AbilityTasks` 或 [`GameplayCues`](#concepts-gc) 进行复制或 RPC，以呈现声音和粒子等效果。

所有 `GameplayAbilities` 都会重写 `ActivateAbility()` 函数来实现你的游戏逻辑。额外逻辑可以添加到 `EndAbility()` 中，当 `GameplayAbility` 完成或被取消时运行。

简单 `GameplayAbility` 流程图：
![Simple GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartsimple.png)

复杂 `GameplayAbility` 流程图：
![Complex GameplayAbility Flowchart](https://github.com/tranek/GASDocumentation/raw/master/Images/abilityflowchartcomplex.png)

复杂能力可以通过多个相互作用的 `GameplayAbilities`（激活、取消等）来实现。

##### 4.6.1.1 复制策略（Replication Policy）

不要使用此选项。名称具有误导性，而且你并不需要它。[`GameplayAbilitySpecs`](#concepts-ga-spec) 默认会从服务器复制到拥有的客户端。如上所述，**`GameplayAbilities` 不会在模拟代理（simulated proxies）上运行**。它们使用 `AbilityTasks` 和 `GameplayCues` 来将视觉变化复制或通过 RPC 同步给模拟代理。Epic 的 Dave Ratti 表示希望在未来[移除此选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

##### 4.6.1.2 服务器是否尊重远程能力取消（Server Respects Remote Ability Cancellation）

此选项往往会带来问题。它的含义是，如果客户端的 `GameplayAbility` 因取消或自然完成而结束，将强制服务器端的对应能力也结束，不管服务器端是否已完成。后者尤其重要，尤其是对延迟较高的玩家使用的本地预测 `GameplayAbilities`。一般情况下，你会希望禁用此选项。

##### 4.6.1.3 直接复制输入（Replicate Input Directly）

启用此选项会始终将输入的按下和释放事件复制到服务器。Epic 建议不要使用此选项，而是依赖内置于现有输入相关 [`AbilityTasks`](#concepts-at) 的“通用复制事件（Generic Replicated Events）”，前提是你的[输入绑定到 `ASC`](#concepts-ga-input)。

Epic 的评论：

```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 回到目录](#目录)**

#### 4.6.2 将输入绑定到 ASC

`ASC` 允许你直接将输入动作绑定到它，并在授予 `GameplayAbilities` 时将这些输入分配给能力。当按下输入动作且满足 `GameplayTag` 要求时，分配了输入的 `GameplayAbilities` 会自动被激活。要使用内置的、响应输入的 `AbilityTasks`，必须为能力分配输入动作。

除了用于激活 `GameplayAbilities` 的输入动作外，`ASC` 还接受通用的 `Confirm` 和 `Cancel` 输入。这些特殊输入被 `AbilityTasks` 用于确认诸如 [`Target Actors`](#concepts-targeting-actors) 之类的操作，或将其取消。

要将输入绑定到 `ASC`，你必须首先创建一个枚举，用于将输入动作名称映射为一个字节。枚举名必须与项目设置中输入动作所使用的名称**完全一致**。`DisplayName` 并不重要。

示例项目：

```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	None			UMETA(DisplayName = "None"),// 0 None
	Confirm			UMETA(DisplayName = "Confirm"),// 1 Confirm
	Cancel			UMETA(DisplayName = "Cancel"),// 2 Cancel
	Ability1		UMETA(DisplayName = "Ability1"),// 3 LMB
	Ability2		UMETA(DisplayName = "Ability2"),// 4 RMB
	Ability3		UMETA(DisplayName = "Ability3"),// 5 Q
	Ability4		UMETA(DisplayName = "Ability4"),// 6 E
	Ability5		UMETA(DisplayName = "Ability5"),// 7 R
	Sprint			UMETA(DisplayName = "Sprint"),// 8 Sprint
	Jump			UMETA(DisplayName = "Jump")// 9 Jump
};
```

如果你的 `ASC` 挂在 `Character` 上，那么在 `SetupPlayerInputComponent()` 中包含用于绑定 `ASC` 的函数：

```c++
// Bind to AbilitySystemComponent
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的 `ASC` 挂在 `PlayerState` 上，那么在 `SetupPlayerInputComponent()` 中可能会出现潜在的竞态条件：此时 `PlayerState` 可能尚未复制到客户端。因此，建议在 `SetupPlayerInputComponent()` 和 `OnRep_PlayerState()` 中都尝试进行输入绑定。仅依赖 `OnRep_PlayerState()` 并不充分，因为可能存在这样的情况：`PlayerState` 在 `PlayerController` 通知客户端调用 `ClientRestart()`（该调用会创建 `InputComponent`）之前就已经完成复制，此时 `Actor` 的 `InputComponent` 仍然为 `null`。示例项目展示了在这两个位置都尝试绑定的做法，并通过一个布尔值进行控制，确保实际只会绑定一次输入。

**注意：** 在示例项目中，枚举里的 `Confirm` 和 `Cancel` 与项目设置中的输入动作名称（`ConfirmTarget` 和 `CancelTarget`）并不匹配，但我们在 `BindAbilityActivationToInputComponent()` 中提供了它们之间的映射关系。这两个是特殊情况，因为映射是显式提供的，所以名称不必匹配（当然也可以匹配）。枚举中其余所有输入都必须与项目设置中的输入动作名称一致。

对于那些只会由一个输入触发的 `GameplayAbilities`（它们始终存在于同一个“槽位”中，例如 MOBA 游戏里的技能），我更倾向于在自己的 `UGameplayAbility` 子类中添加一个变量，用于定义其对应的输入。这样在授予能力时，就可以从该类的 `ClassDefaultObject` 中读取这个输入配置。

##### 4.6.2.1 在不激活能力的情况下绑定输入

如果你不希望 `GameplayAbilities` 在按下输入时自动激活，但仍希望将它们绑定到输入以供 `AbilityTasks` 使用，可以在你的 `UGameplayAbility` 子类中添加一个新的布尔变量 `bActivateOnInput`（默认为 `true`），并重写 `UAbilitySystemComponent::AbilityLocalInputPressed()`。

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// 如果这个 InputID 被泛用 Confirm/Cancel 重载且回调已绑定，则先消费该输入
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// 调用 InputPressed 事件。此处不进行复制。如果有人在监听，可能会将 InputPressed 事件复制到服务器。
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// 能力未激活，则尝试激活它
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 回到目录](#目录)**

#### 4.6.3 授予能力

将一个 `GameplayAbility` 授予 `ASC` 会将其添加到 `ASC` 的 `ActivatableAbilities` 列表中，从而允许它在满足 [`GameplayTag` 要求](#concepts-ga-tags) 时自行激活该能力。

我们在服务器上授予 `GameplayAbilities`，然后自动将 [`GameplayAbilitySpec`](#concepts-ga-spec) 复制到拥有该 ASC 的客户端。其他客户端/模拟代理不会收到该 `GameplayAbilitySpec`。

示例项目在 `Character` 类上存储了一个 `TArray<TSubclassOf<UGDGameplayAbility>>`，在游戏开始时读取并授予能力：

```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// 授予能力，但仅在服务器上执行
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->bCharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->bCharacterAbilitiesGiven = true;
}
```

在授予这些 `GameplayAbilities` 时，我们创建了 `GameplayAbilitySpecs`，包含 `UGameplayAbility` 类、能力等级、绑定的输入以及 `SourceObject`（即授予该 `GameplayAbility` 给这个 `ASC` 的对象）。

**[⬆ 回到目录](#目录)**

#### 4.6.4 激活能力

如果一个 `GameplayAbility` 被分配了输入动作，当按下输入且满足其 `GameplayTag` 要求时，它会被自动激活。不过，这并不总是激活 `GameplayAbility` 的理想方式。`ASC` 提供了另外四种激活 `GameplayAbilities` 的方法：通过 `GameplayTag`、`GameplayAbility` 类、`GameplayAbilitySpec` handle，以及通过事件激活。通过事件激活 `GameplayAbility` 可以[携带数据负载](#concepts-ga-data)。

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec, const FGameplayEventData* GameplayEventData);
```

要通过事件激活 `GameplayAbility`，该能力必须在 `GameplayAbility` 内设置好 `Triggers`。分配一个 `GameplayTag` 并选择 `GameplayEvent` 的选项。要发送事件，可以使用函数：

```c++
UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)
```

通过事件激活能力可以传入数据负载。

`GameplayAbility` 的 `Triggers` 还允许在添加或移除 `GameplayTag` 时激活该能力。

**注意：** 在 Blueprint 中通过事件激活能力时，必须使用 `ActivateAbilityFromEvent` 节点。

**注意：** 除非你的 `GameplayAbility` 是始终运行的（如被动能力），否则不要忘记在能力应终止时调用 `EndAbility()`。

**本地预测能力的激活流程：**

1. **拥有客户端** 调用 `TryActivateAbility()`
2. 调用 `InternalTryActivateAbility()`
3. 调用 `CanActivateAbility()`，返回是否满足 `GameplayTag` 要求、ASC 是否有足够资源支付成本、能力是否不在冷却中、是否没有其他实例正在激活
4. 调用 `CallServerTryActivateAbility()`，传入客户端生成的 `Prediction Key`
5. 调用 `CallActivateAbility()`
6. 调用 `PreActivate()`（Epic 称为“样板初始化操作”）
7. 调用 `ActivateAbility()`，最终激活能力

**服务器端** 接收 `CallServerTryActivateAbility()`

1. 调用 `ServerTryActivateAbility()`
2. 调用 `InternalServerTryActivateAbility()`
3. 调用 `InternalTryActivateAbility()`
4. 调用 `CanActivateAbility()`，返回是否满足 `GameplayTag` 要求、ASC 是否有足够资源支付成本、能力是否不在冷却中、是否没有其他实例正在激活
5. 如果成功，调用 `ClientActivateAbilitySucceed()`，通知客户端更新其 `ActivationInfo`，确认激活已由服务器确认，并广播 `OnConfirmDelegate` 委托（这不同于输入确认）
6. 调用 `CallActivateAbility()`
7. 调用 `PreActivate()`（Epic 称为“样板初始化操作”）
8. 调用 `ActivateAbility()`，最终激活能力

如果服务器在任何时候激活失败，它会调用 `ClientActivateAbilityFailed()`，立即终止客户端的 `GameplayAbility` 并撤销任何预测的变更。

##### 4.6.4.1 被动能力

要实现**被动 `GameplayAbility`**（自动激活并持续运行），可以重写 `UGameplayAbility::OnAvatarSet()`。当一个 `GameplayAbility` 被授予且 `AvatarActor` 被设置时，这个函数会被自动调用，在其中调用 `TryActivateAbility()` 即可。

建议在你自定义的 `UGameplayAbility` 类中添加一个 `bool`，用于标记该 `GameplayAbility` 是否应在被授予时自动激活。示例项目在其**被动护甲叠加能力**中采用了这种做法。

被动 `GameplayAbilities` 通常会使用 [`Net Execution Policy`](#concepts-ga-net) 为 `Server Only`。

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (bActivateAbilityOnGranted)
	{
		ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic 将这个函数描述为**启动被动能力**以及执行类似 `BeginPlay` 行为的正确位置。

**[⬆ 回到目录](#目录)**

##### 4.6.4.2 激活失败标签

能力自带逻辑，可以告诉你为什么能力激活失败。要启用此功能，必须为默认失败情况设置对应的 `GameplayTags`。

向项目添加以下标签（或自定义命名规则）：

```
+GameplayTagList=(Tag="Activation.Fail.BlockedByTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.CantAffordCost",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.IsDead",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.MissingTags",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.Networking",DevComment="")
+GameplayTagList=(Tag="Activation.Fail.OnCooldown",DevComment="")
```

然后将它们添加到 [`GASDocumentation\Config\DefaultGame.ini`](https://github.com/tranek/GASDocumentation/blob/master/Config/DefaultGame.ini#L8-L13)：

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
ActivateFailIsDeadName=Activation.Fail.IsDead
ActivateFailCooldownName=Activation.Fail.OnCooldown
ActivateFailCostName=Activation.Fail.CantAffordCost
ActivateFailTagsBlockedName=Activation.Fail.BlockedByTags
ActivateFailTagsMissingName=Activation.Fail.MissingTags
ActivateFailNetworkingName=Activation.Fail.Networking
```

现在，每当能力激活失败时，相应的 `GameplayTag` 会包含在输出日志中，或在 `showdebug AbilitySystem` HUD 上显示：

```
LogAbilitySystem: Display: InternalServerTryActivateAbility. Rejecting ClientActivation of Default__GA_FireGun_C. InternalTryActivateAbility failed: Activation.Fail.BlockedByTags
LogAbilitySystem: Display: ClientActivateAbilityFailed_Implementation. PredictionKey :109 Ability: Default__GA_FireGun_C
```

![在 showdebug AbilitySystem 中显示激活失败标签](https://github.com/tranek/GASDocumentation/raw/master/Images/activationfailedtags.png)

**[⬆ 回到目录](#目录)**

#### 4.6.5 取消能力

要从内部取消一个 `GameplayAbility`，你可以调用 `CancelAbility()`。这会调用 `EndAbility()` 并将其 `WasCancelled` 参数设为 true。

要从外部取消一个 `GameplayAbility`，`ASC` 提供了几种函数：

```c++
/** 取消指定的能力 CDO。 */
void CancelAbility(UGameplayAbility* Ability);	

/** 根据传入的 spec handle 取消能力。如果 handle 在已激活能力中未找到，则不会发生任何操作。 */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** 取消所有具有指定标签的能力。不会取消 Ignore 实例 */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** 取消所有能力，不论标签如何。不会取消 Ignore 实例 */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** 取消所有能力并销毁所有剩余的实例化能力 */
virtual void DestroyActiveState();
```

**注意：** 我发现如果你有 `Non-Instanced`（非实例化）`GameplayAbilities`，`CancelAllAbilities` 似乎不太起作用。它似乎会碰到 `Non-Instanced` `GameplayAbility` 后直接放弃。而 `CancelAbilities` 对 `Non-Instanced` `GameplayAbilities` 的处理更好，这也是示例项目使用的方法（跳跃是一个非实例化的 `GameplayAbility`）。效果可能因项目而异。

**[⬆ 回到目录](#目录)**

<a name="concepts-ga-definition-activeability"></a>

#### 4.6.6 获取激活中的能力

初学者经常问：“如何获取当前激活的能力？” 可能是为了设置变量或取消它。实际上，一个时间点可以有多个 `GameplayAbility` 被激活，所以没有单一的“当前激活能力”。你必须在 `ASC` 的 `ActivatableAbilities` 列表（ASC 拥有的已授予能力）中搜索，找到匹配你想要的 [`Asset` 或 `Granted` `GameplayTag`](#concepts-ga-tags) 的那个。

`UAbilitySystemComponent::GetActivatableAbilities()` 会返回一个 `TArray<FGameplayAbilitySpec>`，方便你遍历。

ASC 还有一个辅助函数，可以传入一个 `GameplayTagContainer` 来帮助搜索，而不必手动遍历 `GameplayAbilitySpecs` 列表。参数 `bOnlyAbilitiesThatSatisfyTagRequirements` 将仅返回满足其 `GameplayTag` 要求且当前可以被激活的 `GameplayAbilitySpecs`。例如，你可能有两个基础攻击能力，一个带武器，一个徒手，正确的能力会根据是否装备了武器而激活，设置对应的 `GameplayTag` 要求。查看 Epic 对该函数的注释了解更多信息：

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(
    const FGameplayTagContainer& GameplayTagContainer, 
    TArray<struct FGameplayAbilitySpec*>& MatchingGameplayAbilities, 
    bool bOnlyAbilitiesThatSatisfyTagRequirements = true
)
```

一旦获取到你想要的 `FGameplayAbilitySpec`，可以调用它的 `IsActive()` 来判断是否处于激活状态。

**[⬆ 回到目录](#目录)**

#### 4.6.7 实例化策略（Instancing Policy）

一个 `GameplayAbility` 的 `Instancing Policy` 决定该能力在激活时是否以及如何被实例化。

| `Instancing Policy`     | 描述                                                   | 使用示例                                                                                                                                     |
| ----------------------- | ---------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Instanced Per Actor     | 每个 `ASC` 仅拥有一个该 `GameplayAbility` 的实例，并在多次激活间重复使用。   | 这可能是你最常用的实例化策略。适用于任何能力，并提供跨激活的持久性。设计者负责在每次激活之间手动重置需要的变量。                                                                                 |
| Instanced Per Execution | 每次激活 `GameplayAbility` 时都会创建一个新的能力实例。                | 这种能力的优点是每次激活时变量都会重置。但性能比 `Instanced Per Actor` 差，因为每次激活都会生成新的能力实例。示例项目没有使用这种策略。                                                          |
| Non-Instanced           | `GameplayAbility` 直接操作其 `ClassDefaultObject`，不会创建实例。 | 性能最好，但功能最受限。`Non-Instanced` 能力无法存储状态，意味着不能有动态变量，也不能绑定 `AbilityTask` 委托。最适合用于频繁使用的简单能力，例如 MOBA 或 RTS 中的普通攻击。示例项目中的跳跃能力就是 `Non-Instanced`。 |

**[⬆ 回到目录](#目录)**

<a name="concepts-ga-net"></a>

#### 4.6.8 网络执行策略（Net Execution Policy）

一个 `GameplayAbility` 的 `Net Execution Policy` 决定谁运行该能力以及运行顺序。

| `Net Execution Policy` | 描述                                                                           |
| ---------------------- | ---------------------------------------------------------------------------- |
| `Local Only`           | 该能力仅在拥有它的客户端上运行。适用于只做本地视觉效果的能力。单机游戏应使用 `Server Only`。                        |
| `Local Predicted`      | `Local Predicted` 能力先在客户端激活，再在服务器激活。服务器会纠正客户端预测错误的部分。详见 [预测机制](#concepts-p)。 |
| `Server Only`          | 该能力仅在服务器上运行。被动能力通常是 `Server Only`。单机游戏应使用此策略。                                |
| `Server Initiated`     | `Server Initiated` 能力先在服务器激活，再在拥有客户端上激活。我个人几乎没有使用过这种策略。                      |

**[⬆ 回到目录](#目录)**

<a name="concepts-ga-tags"></a>

#### 4.6.9 能力标签（Ability Tags）

`GameplayAbilities` 带有内置逻辑的 `GameplayTagContainers`。这些 `GameplayTags` 都不会被复制（不 replicated）。

| `GameplayTag Container`     | 描述                                                          |
| --------------------------- | ----------------------------------------------------------- |
| `Ability Tags`              | 能力自身拥有的 `GameplayTags`，仅用于描述该能力。                            |
| `Cancel Abilities with Tag` | 激活此能力时，拥有这些 `GameplayTags` 的其他能力会被取消。                       |
| `Block Abilities with Tag`  | 激活此能力时，拥有这些 `GameplayTags` 的其他能力将被阻止激活。                     |
| `Activation Owned Tags`     | 激活能力时赋予能力拥有者的标签。注意，这些标签不被复制。                                |
| `Activation Required Tags`  | 拥有者必须拥有**所有**这些标签，该能力才能被激活。                                 |
| `Activation Blocked Tags`   | 拥有者如果拥有**任何**这些标签，该能力将无法被激活。                                |
| `Source Required Tags`      | 当触发能力的来源（Source）拥有**所有**这些标签时，该能力才能被激活。只有通过事件触发的能力才会设置来源标签。 |
| `Source Blocked Tags`       | 当触发能力的来源（Source）拥有**任何**这些标签时，该能力无法被激活。只有通过事件触发的能力才会设置来源标签。 |
| `Target Required Tags`      | 目标（Target）必须拥有**所有**这些标签，该能力才能被激活。只有通过事件触发的能力才会设置目标标签。      |
| `Target Blocked Tags`       | 目标（Target）如果拥有**任何**这些标签，该能力无法被激活。只有通过事件触发的能力才会设置目标标签。      |

**[⬆ 回到目录](#目录)**

#### 4.6.10 Gameplay Ability Spec

当一个 `GameplayAbility` 被授予给 `ASC` 后，会在 `ASC` 上生成一个 `GameplayAbilitySpec`，用于定义可激活的能力——包括能力类（`GameplayAbility` class）、等级、输入绑定以及必须与 `GameplayAbility` 类分离的运行时状态。

当服务器授予 `GameplayAbility` 时，会将该 `GameplayAbilitySpec` 复制到拥有客户端，使客户端能够激活它。

激活一个 `GameplayAbilitySpec` 时，会根据其 `Instancing Policy` 创建能力实例（`Non-Instanced` 能力则不会创建实例）。

**[⬆ 回到目录](#目录)**

#### 4.6.11 向能力传递数据

`GameplayAbilities` 的一般范式是 `Activate -> Generate Data -> Apply -> End`。有时你需要操作已有数据。GAS 提供了几种将外部数据传入能力的方法：

| 方法                                   | 描述                                                                                                                                                                                                                                                                                                                                                        |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 通过事件激活 `GameplayAbility`             | 使用包含数据负载的事件激活能力。对于本地预测的能力，事件的负载会从客户端复制到服务器。使用两个 `Optional Object` 或 [`TargetData`](#concepts-targeting-data) 变量传递任意不适合已有变量的数据。缺点是无法通过输入绑定激活能力。要通过事件激活能力，必须在能力中设置 `Triggers`，分配一个 `GameplayTag` 并选择 `GameplayEvent`。发送事件使用函数 `UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`。 |
| 使用 `WaitGameplayEvent` `AbilityTask` | 使用 `WaitGameplayEvent` `AbilityTask` 激活后监听事件负载。事件负载与发送方式与通过事件激活能力相同。缺点是 `AbilityTask` 不会复制事件，建议只用于 `Local Only` 或 `Server Only` 能力。你也可以自定义 `AbilityTask` 来复制事件负载。                                                                                                                                                                                         |
| 使用 `TargetData`                      | 自定义 `TargetData` 结构是客户端与服务器之间传递任意数据的好方法。                                                                                                                                                                                                                                                                                                                  |
| 存储在 `OwnerActor` 或 `AvatarActor` 上   | 将数据存储在可复制变量中，例如 `OwnerActor`、`AvatarActor` 或其他可引用的对象。此方法最灵活，适用于通过输入绑定激活的能力。但无法保证在使用时数据已同步，你必须提前确保——例如设置复制变量后立即激活能力，可能由于网络延迟导致接收端顺序不确定。                                                                                                                                                                                                                    |

**[⬆ 回到目录](#目录)**

#### 4.6.12 能力消耗与冷却

`GameplayAbilities` 支持可选的消耗与冷却机制。消耗是 `ASC` 必须拥有的预定义 `Attribute` 数值，用于激活能力，通过 `Instant` 类型的 `GameplayEffect` 实现（[`Cost GE`](#concepts-ge-cost)）。冷却是防止能力在一定时间内重复激活的计时器，通过 `Duration` 类型的 `GameplayEffect` 实现（[`Cooldown GE`](#concepts-ge-cooldown)）。

在调用 `UGameplayAbility::Activate()` 之前，会先调用 `UGameplayAbility::CanActivateAbility()`，该函数会检查拥有的 `ASC` 是否能支付消耗（`UGameplayAbility::CheckCost()`），并确保能力不在冷却中（`UGameplayAbility::CheckCooldown()`）。

在调用 `Activate()` 后，可以随时通过 `UGameplayAbility::CommitAbility()` 提交消耗和冷却，该函数内部会调用 `UGameplayAbility::CommitCost()` 和 `UGameplayAbility::CommitCooldown()`。设计者也可以选择分别调用 `CommitCost()` 或 `CommitCooldown()`，如果它们不应同时提交。提交消耗和冷却会再次调用 `CheckCost()` 和 `CheckCooldown()`，这是能力失败的最后机会——因为在激活后，拥有的 `ASC` 的属性可能会变化，从而无法满足消耗条件。消耗和冷却的提交在提交时如果 [预测键](#concepts-p-key) 有效，可进行 [本地预测](#concepts-p)。

具体实现细节请参见 [`CostGE`](#concepts-ge-cost) 和 [`CooldownGE`](#concepts-ge-cooldown)。

**[⬆ 回到目录](#目录)**

#### 4.6.13 Leveling Up Abilities
升级能力有两种常用方法：

| 升级方法                         | 描述                                                                          |
| ---------------------------- | --------------------------------------------------------------------------- |
| 取消授予并以新等级重新授予                | 从 `ASC` 中取消（移除）该 `GameplayAbility`，然后在服务器上以新等级重新授予。若能力在取消时处于激活状态，会被终止。      |
| 提升 `GameplayAbilitySpec` 的等级 | 在服务器上找到对应的 `GameplayAbilitySpec`，提升其等级，并标记为脏以便复制到拥有客户端。若能力在提升时处于激活状态，不会被终止。 |

两种方法的主要区别在于你是否希望在升级时取消正在激活的能力。根据不同能力类型，你可能会同时使用两种方法。我建议在自定义的 `UGameplayAbility` 子类中添加一个 `bool`，用于指定使用哪种方法。

**[⬆ 回到目录](#目录)**

#### 4.6.14 Ability Sets

`GameplayAbilitySets` 是便捷的 `UDataAsset` 类，用于存储输入绑定和角色的初始能力列表，并带有授予能力的逻辑。子类也可以包含额外的逻辑或属性。Paragon 为每个英雄都创建了一个 `GameplayAbilitySet`，包含该英雄的所有授予能力。

我认为这个类并非必需，至少根据目前看到的用法。示例项目将 `GameplayAbilitySets` 的所有功能都放在了 `GDCharacterBase` 及其子类中实现。

**[⬆ 回到目录](#目录)**

#### 4.6.15 Ability Batching
传统的 `Gameplay Ability` 生命周期至少涉及客户端到服务器的两个或三个 RPC 调用。

1. `CallServerTryActivateAbility()`
2. `ServerSetReplicatedTargetData()`（可选）
3. `ServerEndAbility()`

如果一个 `GameplayAbility` 在一帧内将所有这些操作以原子方式执行，我们可以优化这个流程，将两个或三个 RPC 合并为一个 RPC。`GAS` 将这种 RPC 优化称为 **Ability Batching（能力批处理）**。常见的使用场景是 hitscan 枪械。Hitscan 枪械在激活、进行线性追踪、将 [`TargetData`](#concepts-targeting-data) 发送到服务器并结束能力时，全部在同一帧的原子操作中完成。[GASShooter](https://github.com/tranek/GASShooter) 示例项目展示了这一技术在 hitscan 枪械中的应用。

半自动武器的最佳情况是将 `CallServerTryActivateAbility()`、`ServerSetReplicatedTargetData()`（子弹命中结果）和 `ServerEndAbility()` 三个 RPC 合并为一个 RPC，而不是三个。

全自动/连发武器则在第一颗子弹时将 `CallServerTryActivateAbility()` 和 `ServerSetReplicatedTargetData()` 合并为一个 RPC，其后每颗子弹都是独立的 `ServerSetReplicatedTargetData()` RPC。当停止射击时，`ServerEndAbility()` 作为单独的 RPC 发送。这是最坏的情况，我们只在第一颗子弹节省了一个 RPC，而不是两个。该场景也可以通过 [`Gameplay Event`](#concepts-ga-data) 激活能力实现，将子弹的 `TargetData` 放入 `EventPayload` 发送到服务器。缺点是 `TargetData` 必须在能力外部生成，而批处理方式在能力内部生成 `TargetData`。

`Ability Batching` 默认在 [`ASC`](#concepts-asc) 上被禁用。要启用它，需要重写 `ShouldDoServerAbilityRPCBatch()` 并返回 true：

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

启用 `Ability Batching` 后，在激活希望批处理的能力之前，必须先创建一个 `FScopedServerAbilityRPCBatcher` 结构体。该结构体会尝试将其作用域内随后激活的能力进行批处理。一旦 `FScopedServerAbilityRPCBatcher` 作用域结束，随后激活的能力将不会尝试批处理。

`FScopedServerAbilityRPCBatcher` 的工作原理是，在每个可以批处理的函数中有专门代码拦截 RPC 调用，而是将消息打包到批处理结构体中。当结构体作用域结束时，它会自动通过 `UAbilitySystemComponent::EndServerAbilityRPCBatch()` 将批处理结构体发送到服务器。服务器在 `UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)` 接收批处理 RPC。`BatchInfo` 参数包含能力是否应该结束、激活时输入是否被按下，以及是否包含 `TargetData` 等标志。可以在此函数上设置断点以确认批处理是否正常工作。或者使用 cvar `AbilitySystem.ServerRPCBatching.Log 1` 来启用专用的能力批处理日志。

此机制只能在 C++ 中使用，并且只能通过 `FGameplayAbilitySpecHandle` 激活能力。

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter 为半自动和全自动枪械复用同一个批处理的 `GameplayAbility`，它们从不直接调用 `EndAbility()`（由本地-only 能力管理玩家输入和调用批处理能力来处理）。由于所有 RPC 必须在 `FScopedServerAbilityRPCBatcher` 的作用域内完成，因此提供了 `EndAbilityImmediately` 参数，以便本地-only 控制器/管理器指定该能力是否应批处理 `EndAbility()`（半自动），或者不批处理（全自动），并在稍后通过独立 RPC 调用 `EndAbility()`。

GASShooter 还暴露了一个 Blueprint 节点来触发批处理能力，供上述本地-only 能力使用。

![Activate Batched Ability](https://github.com/tranek/GASDocumentation/raw/master/Images/batchabilityactivate.png)

**[⬆ 回到目录](#目录)**

#### 4.6.16 Net Security Policy
`GameplayAbility` 的 `NetSecurityPolicy` 用于确定能力在网络上应由谁执行，同时防止客户端尝试执行受限制的能力。

| `NetSecurityPolicy`     | 描述                                     |
| ----------------------- | -------------------------------------- |
| `ClientOrServer`        | 没有安全限制。客户端或服务器可以自由触发能力的执行和结束。          |
| `ServerOnlyExecution`   | 客户端请求执行该能力会被服务器忽略，但客户端仍可请求服务器取消或结束该能力。 |
| `ServerOnlyTermination` | 客户端请求取消或结束该能力会被服务器忽略，但客户端仍可请求执行该能力。    |
| `ServerOnly`            | 服务器控制能力的执行和结束。客户端的任何请求都会被忽略。           |

---

### Ability Tasks（能力任务）

`GameplayAbilities` 本身在一帧内执行完成，这意味着它们无法直接处理需要跨多帧或异步的操作。为此，GAS 提供了 **AbilityTasks**，它们是一种延迟操作（latent action），可以在能力执行期间持续运行或响应外部事件。

**常见内置 AbilityTasks 示例：**

* 移动角色（RootMotionSource）
* 播放动画蒙太奇
* 响应 `Attribute` 变化
* 响应 `GameplayEffect` 变化
* 响应玩家输入

> 注意：`UAbilityTask` 构造函数限制同时运行的 AbilityTasks 数量为 1000。设计能力时要考虑在大量角色同时存在（如 RTS 游戏）时的性能影响。

---

#### 自定义 Ability Tasks

通常你会需要自己创建 `AbilityTasks`（C++）。示例项目中有两个自定义的 `AbilityTasks`：

1. `PlayMontageAndWaitForEvent`：结合了 `PlayMontageAndWait` 与 `WaitGameplayEvent`，允许动画蒙太奇通过 `AnimNotifies` 向启动它的能力发送事件，用于动画中特定时间触发动作。
2. `WaitReceiveDamage`：监听 `OwnerActor` 受到伤害，示例中用于被动护甲叠加能力在角色受伤时减少护甲层数。

**AbilityTasks 构成要素：**

* 一个静态函数，用于创建新实例
* 在任务完成时广播的委托（delegate）
* `Activate()`：开始任务、绑定外部委托等
* `OnDestroy()`：清理，包括解绑外部委托
* 回调函数，用于处理绑定的外部委托
* 内部成员变量及辅助函数

> 注意：AbilityTasks 只能声明一种类型的输出委托，所有输出委托都必须使用该类型。未使用的参数可传递默认值。

**运行环境：**

* AbilityTasks 默认只在拥有该能力的客户端或服务器上运行
* 可以设置 `bSimulatedTask = true` 让任务在模拟客户端上运行（如 RootMotionSource 任务模拟移动）
* AbilityTasks 可以跨帧 `Tick`，通过设置 `bTickingTask = true` 并重写 `TickTask(float DeltaTime)` 实现平滑值插值，例如移动到位置任务 `AbilityTask_MoveToLocation`

这种机制允许能力执行跨帧、异步的复杂逻辑，同时保持网络同步和预测控制。

**[⬆ 回到目录](#目录)**

### 使用 Ability Tasks

在 C++ 中创建并激活一个 `AbilityTask` 的示例（来自 `GDGA_FireGun.cpp`）：

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(
    this,               // 所属 GameplayAbility
    NAME_None,          // Task 名称，可选
    MontageToPlay,      // 要播放的动画蒙太奇
    FGameplayTagContainer(), // 要监听的事件标签
    1.0f,               // 播放速度
    NAME_None,          // 起始蒙太奇段落
    false,              // 是否只播放一次
    1.0f                // 动画比例
);

// 绑定委托
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);

// 手动激活任务
Task->ReadyForActivation();
```

#### Blueprint 使用

在 Blueprint 中，AbilityTask 的节点已经封装了激活逻辑，所以不需要手动调用 `ReadyForActivation()`。
UE 的 `K2Node_LatentGameplayTaskCall` 会自动：

* 调用 `ReadyForActivation()`
* 调用 `BeginSpawningActor()` 和 `FinishSpawningActor()`（如果存在）

> 这个“自动魔法”仅在 Blueprint 中生效，C++ 里仍需手动调用。

#### 取消 AbilityTask

手动取消 AbilityTask 可以调用：

```c++
// C++
Task->EndTask();
```

在 Blueprint 中对应的 Async Task Proxy 节点同样提供了 `End Task` 方法。

---

### Root Motion Source (RMS) Ability Tasks

GAS 提供了专门的 AbilityTasks 来驱动角色的 Root Motion 运动，用于：

* 击退（Knockbacks）
* 复杂跳跃（Jumps）
* 拉拽（Pulls）
* 冲刺（Dashes）

这些任务会通过 `CharacterMovementComponent` 挂载的 Root Motion Sources 来实现。

> **注意:** Root Motion 预测在 Unreal Engine 4.19 及 4.25+ 有效；4.20–4.24 版本存在预测 Bug，但在多人游戏中仍能正常执行，仅有少量网络修正。
> 可将 4.25 的预测修复 cherry-pick 到 4.20–4.24 引擎版本以解决此问题。

**[⬆ 回到目录](#目录)**

### 4.8 Gameplay Cues

#### 4.8.1 Gameplay Cue 定义

`GameplayCues`（`GC`）用于执行非游戏玩法相关的操作，如音效、粒子效果、相机震动等。`GameplayCues` 通常会被复制（除非明确在本地 `Executed`、`Added` 或 `Removed`），并且支持预测。

我们通过向 `GameplayCueManager`（通过 `ASC`）发送带有 **必须的父级名称 `GameplayCue.`** 的 `GameplayTag` 以及事件类型（`Execute`、`Add` 或 `Remove`）来触发 `GameplayCues`。实现了 `IGameplayCueInterface` 的 `GameplayCueNotify` 对象或其他 `Actor` 可以根据 `GameplayCue` 的 `GameplayTag`（`GameplayCueTag`）订阅这些事件。

**注意：** 再次强调，`GameplayCue` 的 `GameplayTags` 必须以父级 `GameplayTag` `GameplayCue` 开头。例如，一个有效的 `GameplayCue` `GameplayTag` 可以是 `GameplayCue.A.B.C`。

`GameplayCueNotifies` 有两类：`Static` 和 `Actor`。它们响应不同的事件，不同类型的 `GameplayEffects` 可以触发它们。可在相应事件中重写逻辑。

| `GameplayCue` 类                                                                                                                      | 事件               | `GameplayEffect` 类型     | 描述                                                                                                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------------ | ---------------- | ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [`GameplayCueNotify_Static`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html) | `Execute`        | `Instant` 或 `Periodic`  | 静态 `GameplayCueNotifies` 作用于 `ClassDefaultObject`（意味着没有实例化对象），非常适合一次性的效果，如攻击命中冲击。                                                                                                                                                |
| [`GameplayCueNotify_Actor`](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)                           | `Add` 或 `Remove` | `Duration` 或 `Infinite` | Actor 类型的 `GameplayCueNotifies` 在 `Added` 时会生成新实例。由于这些是实例化的，它们可以在一段时间内执行动作，直到被 `Removed`。适合循环播放的音效或粒子效果，当关联的 `Duration` 或 `Infinite` `GameplayEffect` 被移除，或者通过手动调用移除时，这些效果也会停止。同时提供选项来管理允许同时 `Added` 的数量，避免同一效果的多次应用重复启动音效或粒子效果。 |

`GameplayCueNotifies` 技术上可以响应任意事件，但通常按上述方式使用。

**注意：** 使用 `GameplayCueNotify_Actor` 时，请检查 `Auto Destroy on Remove`，否则对同一 `GameplayCueTag` 的后续 `Add` 调用将无法生效。

在使用 `ASC` [Replication Mode](#concepts-asc-rm) 非 `Full` 模式时，`Add` 和 `Remove` GC 事件会在服务器玩家（监听服务器）上触发两次——一次是应用 `GE`，另一次是通过 “Minimal” `NetMultiCast` 通知客户端。然而，`WhileActive` 事件仍只会触发一次。客户端上所有事件只会触发一次。

示例项目包含用于眩晕和冲刺效果的 `GameplayCueNotify_Actor`，以及用于 FireGun 投射物命中的 `GameplayCueNotify_Static`。这些 `GC` 可以通过 [本地触发](#concepts-gc-local) 来进一步优化，而不是通过 `GE` 复制。我在示例项目中选择了展示初学者使用方式。

**[⬆ 回到目录](#目录)**

#### 4.8.2 触发 Gameplay Cues

在 `GameplayEffect` 成功应用时（未被标签或免疫阻挡），填写所有需要触发的 `GameplayCues` 的 `GameplayTags`。

![GameplayCue Triggered from a GameplayEffect](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromge.png)

`UGameplayAbility` 提供 Blueprint 节点来 `Execute`、`Add` 或 `Remove` `GameplayCues`。

![GameplayCue Triggered from a GameplayAbility](https://github.com/tranek/GASDocumentation/raw/master/Images/gcfromga.png)

在 C++ 中，可以直接在 `ASC` 上调用函数（或在 `ASC` 子类中暴露给 Blueprint）：

```c++
/** GameplayCues 也可以独立触发。可选传入 effect context 以传递命中结果等信息 */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 添加一个持续存在的 GameplayCue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** 移除一个持续存在的 GameplayCue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** 移除任何独立添加的 GameplayCue，即不属于 GameplayEffect 的 */
void RemoveAllGameplayCues();
```

**[⬆ 回到目录](#目录)**

#### 4.8.3 本地 Gameplay Cues
从 `GameplayAbilities` 和 `ASC` 调用的触发 `GameplayCues` 函数默认是复制的。每个 `GameplayCue` 事件都是一个 multicast RPC，这可能导致大量 RPC 调用。GAS 对每个网络更新中相同的 `GameplayCue` RPC 数量限制为最多两个。我们通过使用本地 `GameplayCues` 来避免这种情况。本地 `GameplayCues` 只会在单个客户端上 `Execute`、`Add` 或 `Remove`。

可以使用本地 `GameplayCues` 的场景：

* 投射物命中
* 近战碰撞命中
* 来自动画蒙太奇触发的 `GameplayCues`

应在 `ASC` 子类中添加以下本地 `GameplayCue` 函数：

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果一个 `GameplayCue` 是本地 `Added` 的，它也应本地 `Removed`；如果是通过复制 `Added` 的，则应通过复制 `Removed`。

**[⬆ 回到目录](#目录)**

#### 4.8.4 Gameplay Cue 参数

`GameplayCues` 会接收一个 `FGameplayCueParameters` 结构体，作为传入 `GameplayCue` 的额外信息参数。如果你从 `GameplayAbility` 或 `ASC` 的函数手动触发 `GameplayCue`，则必须手动填充传递给 `GameplayCue` 的 `GameplayCueParameters` 结构体。如果 `GameplayCue` 是由 `GameplayEffect` 触发的，则以下变量会自动填充到 `GameplayCueParameters` 结构体中：

* `AggregatedSourceTags`
* `AggregatedTargetTags`
* `GameplayEffectLevel`
* `AbilityLevel`
* [EffectContext](#concepts-ge-context)
* `Magnitude`（如果 `GameplayEffect` 在 `GameplayCue` 标签容器上方的下拉菜单中选择了某个用于幅度的 `Attribute`，并且有相应影响该 `Attribute` 的 `Modifier`）

在 `GameplayCueParameters` 结构体中，`SourceObject` 变量是手动触发 `GameplayCue` 时传递任意数据的好位置。

**注意：** 参数结构体中的一些变量（如 `Instigator`）可能已存在于 `EffectContext` 中。`EffectContext` 还可以包含 `FHitResult`，用于确定在世界中生成 `GameplayCue` 的位置。对子类化 `EffectContext` 是向 `GameplayCues` 传递更多数据的好方法，尤其是由 `GameplayEffect` 触发的 `GameplayCues`。

更多信息请参阅 [`UAbilitySystemGlobals`](#concepts-asg) 中的三个函数，它们用于填充 `GameplayCueParameters` 结构体。它们是虚函数，可以重写以自动填充更多信息。

```c++
/** 初始化 GameplayCue 参数 */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ 回到目录](#目录)**

#### 4.8.5 Gameplay Cue 管理器
默认情况下，`GameplayCueManager` 会扫描整个游戏目录中的 `GameplayCueNotifies` 并在游戏运行时加载到内存中。我们可以通过在 `DefaultGame.ini` 中设置扫描路径来修改扫描位置。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们希望 `GameplayCueManager` 能扫描并找到所有 `GameplayCueNotifies`，但不希望在运行时异步加载每一个。这会把每个 `GameplayCueNotify` 及其引用的音效和粒子全部加载到内存中，即使它们在当前关卡中并未使用。在大型游戏（如 Paragon）中，这可能导致内存中存在数百 MB 的无用资源，并在启动时造成卡顿或游戏冻结。

一种替代方案是在游戏中触发 `GameplayCues` 时才异步加载它们。这可以减少不必要的内存占用和启动时的硬卡顿，但首次触发某个 `GameplayCue` 时可能会出现轻微延迟。对于 SSD，这种延迟几乎不存在，HDD 上未测试。如果在 UE 编辑器中使用此选项，首次加载 `GameplayCues` 时可能会因为需要编译粒子系统而出现轻微卡顿或冻结。在打包构建中不会有此问题，因为粒子系统已预编译。

首先，我们需要对子类化 `UGameplayCueManager`，并在 `DefaultGame.ini` 中告诉 `AbilitySystemGlobals` 使用我们的 `UGameplayCueManager` 子类。

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的 `UGameplayCueManager` 子类中，重写 `ShouldAsyncLoadRuntimeObjectLibraries()`：

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ 回到目录](#目录)**

#### 4.8.6 阻止 Gameplay Cues 触发

有时我们不希望 `GameplayCues` 被触发。例如，如果我们阻挡了一次攻击，可能不想播放附加在伤害 `GameplayEffect` 上的命中效果，或者想播放自定义效果。可以在 [`GameplayEffectExecutionCalculations`](#concepts-ge-ec) 内通过调用 `OutExecutionOutput.MarkGameplayCuesHandledManually()` 来实现，然后手动将 `GameplayCue` 事件发送给目标或源的 `ASC`。

如果你希望某个 `ASC` 上的任何 `GameplayCues` 都不触发，可以设置：

```c++
AbilitySystemComponent->bSuppressGameplayCues = true;
```

**[⬆ 回到目录](#目录)**

#### 4.8.7 Gameplay Cue 批处理

每个触发的 `GameplayCue` 都是一个不可靠的 NetMulticast RPC。当同时触发多个 `GCs` 时，可以通过一些优化方法将它们合并为一个 RPC，或通过发送更少的数据来节省带宽。

##### 4.8.7.1 手动 RPC

例如，你有一把霰弹枪发射八颗子弹，这会触发八个 trace 和命中 `GameplayCues`。[GASShooter](https://github.com/tranek/GASShooter) 采用了懒人方法：将所有 trace 信息存储到 [`EffectContext`](#concepts-ge-ec) 的 [`TargetData`](#concepts-targeting-data) 中，然后通过一个 RPC 发送。这样虽然把 RPC 数量从 8 降到了 1，但这 1 个 RPC 仍会发送大量数据（约 500 字节）。更优化的方法是使用自定义结构体发送 RPC，高效编码命中位置，或者给一个随机种子，让接收端重新生成或近似命中位置。客户端收到后解包自定义结构体，并转换为 [本地执行的 `GameplayCues`](#concepts-gc-local)。

工作流程如下：

1. 声明一个 `FScopedGameplayCueSendContext`。它会抑制 `UGameplayCueManager::FlushPendingCues()`，直到该上下文超出作用域，意味着所有 `GameplayCues` 都会排队等待执行。
2. 重写 `UGameplayCueManager::FlushPendingCues()`，根据自定义 `GameplayTag` 将可以合并的 `GameplayCues` 合并到自定义结构体中，并通过 RPC 发送给客户端。
3. 客户端接收自定义结构体，并解包为本地执行的 `GameplayCues`。

这种方法也适用于需要特定参数而 `GameplayCueParameters` 无法提供，且又不想加入 `EffectContext` 的场景，例如伤害数值、暴击标记、护盾破碎标记、致命一击标记等。

参考：[https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager](https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager)

<a name="concepts-gc-batching-gcsonge"></a>

##### 4.8.7.2 一个 GE 上的多个 GCs

一个 `GameplayEffect` 上的所有 `GameplayCues` 已经会在一个 RPC 中发送。默认情况下，`UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()` 会将整个 `GameplayEffectSpec`（但转换为 `FGameplayEffectSpecForRPC`）通过不可靠 NetMulticast 发送，无论 `ASC` 的 `Replication Mode` 是什么。这可能会消耗大量带宽，取决于 `GameplayEffectSpec` 中的数据。

我们可以通过设置 cvar：

```c++
AbilitySystem.AlwaysConvertGESpecToGCParams 1
```

来优化。这会将 `GameplayEffectSpecs` 转换为 `FGameplayCueParameters` 结构体，并通过 RPC 发送，而不是发送整个 `FGameplayEffectSpecForRPC`。这样可以节省带宽，但信息量较少，具体取决于 `GESpec` 转换为 `GameplayCueParameters` 的方式以及你的 `GCs` 需要哪些信息。

**[⬆ 回到目录](#目录)**

#### 4.8.8 Gameplay Cue 事件

`GameplayCues` 会响应特定的 `EGameplayCueEvents`：

| `EGameplayCueEvent` | 描述         |
| ------------------- | ------------ |
| `OnActive`          | 当 `GameplayCue` 被激活（添加）时调用。                                                                                                                                                                    |
| `WhileActive`       | 当 `GameplayCue` 正在生效时调用，即使它并非刚刚应用（加入中、进度中等）。这不是 `Tick`！当 `GameplayCueNotify_Actor` 被添加或变得相关时，只调用一次，就像 `OnActive` 一样。如果需要 `Tick()` 行为，请直接使用 `GameplayCueNotify_Actor` 的 `Tick()`，毕竟它是 `AActor`。 |
| `Removed`           | 当 `GameplayCue` 被移除时调用。对应 Blueprint 的函数是 `OnRemove`。                                       |
| `Executed`          | 当 `GameplayCue` 被执行时调用：即时效果或周期性 `Tick()`。对应 Blueprint 的函数是 `OnExecute`。                       |

使用 `OnActive` 来处理 `GameplayCue` 开始时需要发生的逻辑，即便后加入的玩家可能错过也无妨。使用 `WhileActive` 来处理 `GameplayCue` 中的持续效果，以便后加入的玩家也能看到。例如，在 MOBA 游戏中，如果某座塔发生爆炸，你可以将初始爆炸粒子效果和爆炸音效放在 `OnActive`，而将残留的持续火焰粒子或音效放在 `WhileActive`。在这种情况下，后加入的玩家不需要重新播放 `OnActive` 的初始爆炸，但仍希望看到爆炸后地面上持续循环的火焰效果，这就由 `WhileActive` 处理。

`OnRemove` 应清理在 `OnActive` 和 `WhileActive` 中添加的内容。每次一个 Actor 进入 `GameplayCueNotify_Actor` 的相关范围时，`WhileActive` 会被调用；每次 Actor 离开该范围时，`OnRemove` 会被调用。

**[⬆ 回到目录](#目录)**

#### 4.8.9 Gameplay Cue 可靠性

通常情况下，`GameplayCues` 应被视为不可靠，因此不适合用于直接影响游戏玩法的逻辑。

**Executed `GameplayCues`：** 这类 `GameplayCues` 通过不可靠的 multicast 触发，因此始终不可靠。

**由 `GameplayEffects` 应用的 `GameplayCues`：**

* 自主代理（Autonomous Proxy）可靠接收 `OnActive`、`WhileActive` 和 `OnRemove`
  `FActiveGameplayEffectsContainer::NetDeltaSerialize()` 会调用 `UAbilitySystemComponent::HandleDeferredGameplayCues()` 来触发 `OnActive` 和 `WhileActive`，而 `FActiveGameplayEffectsContainer::RemoveActiveGameplayEffectGrantedTagsAndModifiers()` 会触发 `OnRemoved`。
* 模拟代理（Simulated Proxy）可靠接收 `WhileActive` 和 `OnRemove`
  `UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制会调用 `WhileActive` 和 `OnRemove`，`OnActive` 事件则由不可靠 multicast 触发。

**未通过 `GameplayEffect` 应用的 `GameplayCues`：**

* 自主代理可靠接收 `OnRemove`
  `OnActive` 和 `WhileActive` 事件由不可靠 multicast 触发。
* 模拟代理可靠接收 `WhileActive` 和 `OnRemove`
  `UAbilitySystemComponent::MinimalReplicationGameplayCues` 的复制会调用 `WhileActive` 和 `OnRemove`，`OnActive` 事件由不可靠 multicast 触发。

如果希望某个 `GameplayCue` 的内容“可靠”，应通过 `GameplayEffect` 应用，并使用 `WhileActive` 添加特效，`OnRemove` 移除特效。

**[⬆ 回到目录](#目录)**

### 4.9 Ability System Globals
[`AbilitySystemGlobals`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html) 类保存了 GAS 的全局信息。大多数变量可以在 `DefaultGame.ini` 中进行设置。通常你不需要直接操作这个类，但应了解它的存在。如果你需要对子类化 [`GameplayCueManager`](#concepts-gc-manager) 或 [`GameplayEffectContext`](#concepts-ge-context)，必须通过 `AbilitySystemGlobals` 来实现。

要对子类化 `AbilitySystemGlobals`，在 `DefaultGame.ini` 中设置类名：

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

#### 4.9.1 InitGlobalData()
在 UE 4.24 到 5.2 之间，使用 [`TargetData`](#concepts-targeting-data) 前必须调用：

```c++
UAbilitySystemGlobals::Get().InitGlobalData()
```

否则会出现与 `ScriptStructCache` 相关的错误，并且客户端会被服务器断开连接。这个函数在项目中只需要调用一次。Fortnite 在 `UAssetManager::StartInitialLoading()` 中调用它，Paragon 在 `UEngine::Init()` 中调用。我认为像示例项目中那样放在 `UAssetManager::StartInitialLoading()` 是一个不错的选择。这可以视为样板代码，应复制到你的项目中，以避免 `TargetData` 相关的问题。从 UE 5.3 开始，这个函数会自动调用。

如果在使用 `AbilitySystemGlobals` 的 `GlobalAttributeSetDefaultsTableNames` 时遇到崩溃，可能需要像 Fortnite 一样，在 `AssetManager` 或 `GameInstance` 中稍后调用 `UAbilitySystemGlobals::Get().InitGlobalData()`。

**[⬆ 回到目录](#目录)**

### 4.10 预测（Prediction）

GAS 自带对客户端预测的支持，但它并不会预测所有内容。GAS 中的客户端预测意味着客户端不必等待服务器许可就能激活 `GameplayAbility` 并应用 `GameplayEffects`。客户端可以“预测”服务器会允许它这么做，并预测这些 `GameplayEffects` 将作用的目标。随后服务器会在客户端激活后的网络延迟时间运行该 `GameplayAbility`，并告知客户端预测是否正确。如果客户端预测错误，它会从“错误预测”中回滚自己的更改以与服务器同步。

GAS 预测的权威来源是插件源码中的 `GameplayPrediction.h`。

Epic 的思路是只预测“你可以安全预测的内容”。例如，Paragon 和 Fortnite 并不预测伤害。他们很可能使用 [`ExecutionCalculations`](#concepts-ge-ec) 来处理伤害，而这本身不可预测。这并不意味着你不能尝试预测某些内容，比如伤害。如果你尝试并且效果良好，那自然很好。

> “…我们也并不追求‘全预测：无缝自动’的方案。我们仍然认为玩家预测应尽量保持最小化（即：尽量只预测最必要的内容）。”
> — Dave Ratti, Epic，在新 [Network Prediction Plugin](#concepts-p-npp) 中的评论

**可预测内容：**

> * 能力激活（Ability Activation）
> * 触发事件（Triggered Events）
> * `GameplayEffect` 应用：
>
>   * 属性修改（注意：Executions 目前不可预测，仅属性 Modifier 可预测）
>   * `GameplayTag` 修改
> * `GameplayCue` 事件（来自预测性 GameplayEffect 或独立触发）
> * 动画蒙太奇（Montages）
> * 移动（内置于 UE 的 `UCharacterMovement`）

**不可预测内容：**

> * `GameplayEffect` 移除
> * `GameplayEffect` 周期性效果（如 DOT 伤害）

*摘自 `GameplayPrediction.h`*

虽然可以预测 `GameplayEffect` 的应用，但无法预测其移除。一种解决方法是在移除 `GameplayEffect` 时预测相反效果。例如，预测一个移动速度减慢 40%，可以通过预测性地应用一个移动速度加成 40% 来“移除”减速效果，然后同时移除两个 `GameplayEffects`。这并不适用于所有场景，因此仍然需要支持预测 `GameplayEffect` 移除。Epic 的 Dave Ratti 表示希望在未来版本的 GAS 中加入此功能 [未来迭代](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)。

由于无法预测 `GameplayEffect` 移除，也无法完全预测 `GameplayAbility` 的冷却时间，而且冷却没有对应的逆 `GameplayEffect` 可用。服务器复制的 `Cooldown GE` 会存在于客户端，任何尝试绕过（例如使用 `Minimal` 复制模式）都会被服务器拒绝。这意味着高延迟玩家需要更长时间通知服务器进入冷却，并接收服务器的 `Cooldown GE` 移除，从而导致高延迟玩家射击频率低于低延迟玩家。Fortnite 通过使用自定义记账而非 `Cooldown GEs` 避免了这个问题。

关于预测伤害，我个人并不推荐，尽管大多数初学 GAS 的人都会尝试预测伤害，尤其不推荐尝试预测死亡。虽然可以预测伤害，但操作较复杂。如果错误预测伤害，玩家会看到敌人的血量回升。如果错误预测死亡，可能出现角色先 ragdoll 再被服务器纠正继续攻击你的情况，非常尴尬和令人沮丧。

**注意：**
`Instant GameplayEffects`（例如消耗类 GE）修改自身属性可以无缝预测，对其他角色预测 `Instant` 属性变化会短暂出现异常或“闪烁”。预测的 `Instant GameplayEffects` 实际上被当作 `Infinite GameplayEffects` 处理，以便错误预测时可以回滚。当服务器应用 `GameplayEffect` 时，短时间内可能会存在两个相同的 `GameplayEffect`，导致 Modifier 被应用两次或一次都未应用。最终会自行修正，但玩家有时会注意到闪烁。

GAS 预测实现尝试解决的问题：

> 1. “我能做吗？” — 基础预测协议。
> 2. “撤销” — 当预测失败时如何撤销副作用。
> 3. “重做” — 如何避免重新执行已在本地预测的副作用，但也会从服务器复制过来。
> 4. “完整性” — 如何确保预测了所有副作用。
> 5. “依赖关系” — 如何管理依赖预测和预测事件链。
> 6. “覆盖” — 如何预测性覆盖本应由服务器复制/拥有的状态。

*摘自 `GameplayPrediction.h`*
**[⬆ 回到目录](#目录)**

#### 4.10.1 预测键（Prediction Key）

GAS 的预测基于 `Prediction Key` 的概念——客户端在激活 `GameplayAbility` 时生成的整数标识符。

* 客户端在激活 `GameplayAbility` 时生成一个预测键，这就是 **Activation Prediction Key**。

* 客户端通过 `CallServerTryActivateAbility()` 将预测键发送到服务器。

* 客户端在预测键有效期间，将该预测键添加到它应用的所有 `GameplayEffects` 上。

* 客户端的预测键超出作用域。同一 `GameplayAbility` 中后续的预测效果需要一个新的 [Scoped Prediction Window](#concepts-p-windows)。

* 服务器接收客户端发送的预测键。

* 服务器将该预测键添加到它应用的所有 `GameplayEffects` 上。

* 服务器将预测键复制回客户端。

* 客户端接收服务器复制的 `GameplayEffects`，其中带有用于应用它们的预测键。如果任何复制的 `GameplayEffects` 与客户端使用相同预测键应用的 `GameplayEffects` 匹配，则预测正确。在客户端移除其预测效果之前，目标上会暂时存在两份同样的 `GameplayEffect`。

* 客户端从服务器接收到预测键返回值，即 **Replicated Prediction Key**，该预测键现在被标记为过期。

* 客户端移除使用现在过期的复制预测键创建的 **所有** `GameplayEffects`。服务器复制的 `GameplayEffects` 会保留。客户端添加但未收到服务器匹配复制版本的 `GameplayEffects` 则属于错误预测。

预测键在 `GameplayAbilities` 的指令“原子分组窗口”内保证有效，从激活预测键开始算起。你可以把它理解为仅在一帧内有效。来自潜在动作（latent action） `AbilityTasks` 的回调将不再拥有有效的预测键，除非该 `AbilityTask` 自带同步点（Synch Point），会生成一个新的 [Scoped Prediction Window](#concepts-p-windows)。

**[⬆ 回到目录](#目录)**

#### 4.10.2 在能力中创建新的预测窗口

为了在 `AbilityTasks` 的回调中预测更多动作，我们需要创建一个新的 **Scoped Prediction Window** 并生成一个新的 **Scoped Prediction Key**。这有时被称为客户端和服务器之间的同步点（Synch Point）。

* 一些 `AbilityTasks`（例如所有输入相关的任务）自带创建新 scoped prediction window 的功能，这意味着在这些 `AbilityTasks` 回调中的原子代码可以使用有效的 scoped prediction key。
* 其他任务（例如 `WaitDelay`）没有内置创建 scoped prediction window 的功能。如果你需要在这些任务之后预测动作，就必须手动创建。可以使用 `WaitNetSync` `AbilityTask` 并设置选项 `OnlyServerWait` 来实现。

当客户端遇到带 `OnlyServerWait` 的 `WaitNetSync`：

1. 根据 `GameplayAbility` 的激活预测键生成新的 scoped prediction key。
2. 将这个键通过 RPC 发送给服务器。
3. 将这个键添加到客户端应用的任何新 `GameplayEffects` 上。

当服务器遇到带 `OnlyServerWait` 的 `WaitNetSync`：

* 服务器会等待，直到收到客户端的新的 scoped prediction key，然后再继续执行。

这个 scoped prediction key 的处理方式与激活预测键相同：

* 应用于 `GameplayEffects`，
* 复制回客户端以标记为过期（stale）。

Scoped prediction key 在作用域内有效，超出作用域后预测窗口关闭。因此，只有原子操作（atomic operations），而非潜在动作（latent actions），可以使用新的 scoped prediction key。

你可以根据需要创建任意多个 scoped prediction windows。

如果希望给自定义 `AbilityTasks` 添加同步点功能，可以参考输入相关任务是如何将 `WaitNetSync` `AbilityTask` 注入到它们中的。

**注意：** 使用 `WaitNetSync` 会阻塞服务器的 `GameplayAbility` 执行，直到收到客户端的响应。如果恶意用户故意延迟发送新的 scoped prediction key，可能会被利用。Epic 建议谨慎使用 `WaitNetSync`，如果担心此问题，可以考虑创建一个延迟版的 `AbilityTask`，在未收到客户端响应时自动继续。

示例项目在冲刺（Sprint）`GameplayAbility` 中使用 `WaitNetSync`，每次应用耐力消耗时创建新的 scoped prediction window，以便进行预测。理想情况下，我们希望在应用消耗和冷却时有有效的预测键。

如果你发现预测的 `GameplayEffect` 在拥有客户端上播放了两次，说明你的预测键已过期，出现了“redo”问题。通常可以通过在应用 `GameplayEffect` 前加入一个带 `OnlyServerWait` 的 `WaitNetSync` `AbilityTask` 来创建新的 scoped prediction key，从而解决问题。

**[⬆ 回到目录](#目录)**

#### 4.10.3 预测性生成 Actor

在客户端预测性生成 `Actors` 是一个高级话题。GAS 默认并不提供这一功能（`SpawnActor` `AbilityTask` 仅在服务器生成 Actor）。核心概念是，在客户端和服务器上都生成可复制（replicated）的 Actor。

* 如果 Actor 只是视觉效果或不影响游戏玩法，简单的做法是重写 Actor 的 `IsNetRelevantFor()` 函数，阻止服务器向拥有客户端复制该 Actor。拥有客户端将使用本地生成的版本，服务器和其他客户端使用服务器复制的版本：

```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

* 如果生成的 Actor 会影响游戏玩法，例如需要预测伤害的投射物，则需要更复杂的逻辑，这超出了本手册的范围。可以参考 UnrealTournament 在 Epic Games GitHub 上的实现：它们在拥有客户端生成一个“假”投射物（dummy projectile），与服务器复制的投射物同步。

**[⬆ 回到目录](#目录)**

<a name="concepts-p-future"></a>

#### 4.10.4 GAS 预测的未来

`GameplayPrediction.h` 指出，未来可能会增加对 `GameplayEffect` 移除和周期性 `GameplayEffects` 的预测功能。

Epic 的 Dave Ratti [表达过兴趣](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 修复预测冷却（cooldown）的“延迟校正”问题，以解决高延迟玩家相比低延迟玩家的劣势。

Epic 新开发的 [`Network Prediction` 插件](#concepts-p-npp) 预计将完全兼容 GAS，就像之前的 `CharacterMovementComponent` 一样。

**[⬆ 回到目录](#目录)**

<a name="concepts-p-npp"></a>

#### 4.10.5 Network Prediction 插件

Epic 最近启动了一个计划，用新的 `Network Prediction` 插件替代 `CharacterMovementComponent`。该插件仍处于早期阶段，但已在 Unreal Engine GitHub 上提供非常早期访问版本。尚无法确定它将在引擎的哪个未来版本中作为实验性 Beta 推出。

**[⬆ 回到目录](#目录)**

### 4.11 目标系统（Targeting）

#### 4.11.1 目标数据（Target Data）

[`FGameplayAbilityTargetData`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html) 是用于跨网络传递的通用目标数据结构。`TargetData` 通常包含 `AActor`/`UObject` 引用、`FHitResults` 以及其他通用的位置信息、方向或起点信息。不过，你可以继承它来放入任意你想传递的数据，这是在 `GameplayAbilities` 中客户端与服务器之间传递数据的一种简单方式。基础结构 `FGameplayAbilityTargetData` 本身不建议直接使用，而是应该被继承。GAS 默认提供了一些继承自 `FGameplayAbilityTargetData` 的结构体，定义在 `GameplayAbilityTargetTypes.h` 中。

`TargetData` 通常由 [`Target Actors`](#concepts-targeting-actors) 生成，或者 **手动创建**，并由 [`AbilityTasks`](#concepts-at) 和 [`GameplayEffects`](#concepts-ge) 通过 [`EffectContext`](#concepts-ge-context) 消费。由于它存在于 `EffectContext` 中，[`Executions`](#concepts-ge-ec)、[`MMCs`](#concepts-ge-mmc)、[`GameplayCues`](#concepts-gc) 以及 [`AttributeSet`](#concepts-as) 后端的函数都可以访问 `TargetData`。

我们通常不会直接传递 `FGameplayAbilityTargetData`，而是使用 [`FGameplayAbilityTargetDataHandle`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html)，它内部维护了指向 `FGameplayAbilityTargetData` 的 TArray。这个中间结构提供了对 `TargetData` 的多态支持。

示例：继承 `FGameplayAbilityTargetData`

```c++
USTRUCT(BlueprintType)
struct MYGAME_API FGameplayAbilityTargetData_CustomData : public FGameplayAbilityTargetData
{
    GENERATED_BODY()
public:

    FGameplayAbilityTargetData_CustomData() { }

    UPROPERTY()
    FName CoolName = NAME_None;

    UPROPERTY()
    FPredictionKey MyCoolPredictionKey;

    // FGameplayAbilityTargetData 的所有子结构都必须实现
    virtual UScriptStruct* GetScriptStruct() const override
    {
        return FGameplayAbilityTargetData_CustomData::StaticStruct();
    }

    // FGameplayAbilityTargetData 的所有子结构都必须实现
    bool NetSerialize(FArchive& Ar, class UPackageMap* Map, bool& bOutSuccess)
    {
        CoolName.NetSerialize(Ar, Map, bOutSuccess);
        MyCoolPredictionKey.NetSerialize(Ar, Map, bOutSuccess);
        bOutSuccess = true;
        return true;
    }
};

template<>
struct TStructOpsTypeTraits<FGameplayAbilityTargetData_CustomData> : public TStructOpsTypeTraitsBase2<FGameplayAbilityTargetData_CustomData>
{
    enum
    {
        WithNetSerializer = true // 必须设置，FGameplayAbilityTargetDataHandle 网络序列化才能生效
    };
};
```

向 Handle 添加 Target Data 的示例：

```c++
UFUNCTION(BlueprintPure)
FGameplayAbilityTargetDataHandle MakeTargetDataFromCustomName(const FName CustomName)
{
    // 创建自定义 Target Data
    FGameplayAbilityTargetData_CustomData* MyCustomData = new FGameplayAbilityTargetData_CustomData();
    MyCustomData->CoolName = CustomName;
    
    // 创建 Handle 封装给 Blueprint 使用
    FGameplayAbilityTargetDataHandle Handle;
    Handle.Add(MyCustomData); // 将 Target Data 添加到 Handle
    return Handle; // 返回给 Blueprint
}
```

获取 Target Data 值需要进行类型检查，因为从 Handle 中获取的数据需要通过通用的 C/C++ cast，这本身 **不安全**，可能导致对象切片或崩溃。常用的类型检查方法有两种：

1. **Gameplay Tag(s)：** 可以使用子类层级，通过获取基类的 Gameplay Tag 来判断并进行安全 cast。
2. **Script Struct & Static Struct：** 直接进行类比较（可能需要多个 if 或模板函数）。每个继承类都必须在 `GetScriptStruct()` 中返回自己的 `StaticStruct`，可以用它来进行类型匹配。

类型检查示例：

```c++
UFUNCTION(BlueprintPure)
FName GetCoolNameFromTargetData(const FGameplayAbilityTargetDataHandle& Handle, const int Index)
{
    FGameplayAbilityTargetData* Data = Handle.Get(Index); // 自动检查索引有效性

    if (Data == nullptr)
        return NAME_None;

    // 类型检查，通过 GetScriptStruct() 比较
    if (Data->GetScriptStruct() == FGameplayAbilityTargetData_CustomData::StaticStruct())
    {
        FGameplayAbilityTargetData_CustomData* CustomData = static_cast<FGameplayAbilityTargetData_CustomData*>(Data);
        return CustomData->CoolName;
    }

    return NAME_None;
}
```

这种方法确保在使用 Handle 获取自定义 Target Data 时不会发生对象切片，同时提供了安全的类型判断机制。

**[⬆ 回到目录](#目录)**

#### 4.11.2 目标 Actor

`GameplayAbilities` 使用 `WaitTargetData` `AbilityTask` 来生成 [`TargetActors`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)，用于在世界中可视化并捕获目标信息。`TargetActors` 可选择使用 [`GameplayAbilityWorldReticles`](#concepts-targeting-reticles) 来显示当前目标。当确认后，目标信息会以 [`TargetData`](#concepts-targeting-data) 的形式返回，然后可以传入 `GameplayEffects`。

`TargetActors` 基于 `AActor`，因此可以拥有任何类型的可见组件来表示**目标的位置和方式**，例如静态网格（Static Mesh）或贴花（Decal）。静态网格可用于可视化角色将要建造的物体的位置；贴花可用于显示地面的范围效果（AOE）。示例项目使用 [`AGameplayAbilityTargetActor_GroundTrace`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html) 并在地面上放置贴花来表示陨石技能的伤害范围。同时，`TargetActors` 也可以不显示任何内容。例如，对于瞬发命中扫描枪（Hitscan Gun）来说，显示内容就没有意义，正如 [GASShooter](https://github.com/tranek/GASShooter) 中的实现。

它们通过基础的追踪（trace）或碰撞重叠（collision overlap）捕获目标信息，并根据 `TargetActor` 的实现，将结果转换为 `FHitResults` 或 `AActor` 数组，然后生成 `TargetData`。`WaitTargetData` `AbilityTask` 通过其 `TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType` 参数确定何时确认目标。当**不使用** `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 时，`TargetActor` 通常在 `Tick()` 中执行 trace/overlap，并根据实现更新其位置到 `FHitResult`。虽然这会在 `Tick()` 中执行 trace/overlap，但通常不会有太大问题，因为它不会被复制（replicate），而且通常同一时间不会运行超过一个（当然可以运行更多）`TargetActor`。需要注意的是，它会使用 `Tick()`，一些复杂的 `TargetActors` 可能会在 `Tick()` 中执行大量操作，例如 GASShooter 中火箭发射器的二次技能。如果在 `Tick()` 中追踪导致客户端响应非常快，但性能开销过大，可以考虑降低 `TargetActor` 的 tick 频率。在 `TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant` 情况下，`TargetActor` 会立即生成，产生 `TargetData` 并销毁，`Tick()` 永远不会被调用。

| `EGameplayTargetingConfirmation::Type` | 确认目标的时机                                                                                                                                                                                       |
| -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Instant`                              | 目标立即被确认，无需特殊逻辑或用户输入来决定“触发”时机。                                                                                                                                                                 |
| `UserConfirmed`                        | 当用户确认目标时触发，例如技能绑定了 `Confirm` 输入（#concepts-ga-input）或调用 `UAbilitySystemComponent::TargetConfirm()`。`TargetActor` 也会响应绑定的 `Cancel` 输入或调用 `UAbilitySystemComponent::TargetCancel()` 来取消目标。       |
| `Custom`                               | GameplayTargeting Ability 负责决定何时准备好目标数据，通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()`。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 来取消目标。                         |
| `CustomMulti`                          | GameplayTargeting Ability 负责决定何时准备好目标数据，通过调用 `UGameplayAbility::ConfirmTaskByInstanceName()`。`TargetActor` 也会响应 `UGameplayAbility::CancelTaskByInstanceName()` 来取消目标。生成数据时不应结束 `AbilityTask`。 |

并非每个 `TargetActor` 都支持所有 `EGameplayTargetingConfirmation::Type`。例如，`AGameplayAbilityTargetActor_GroundTrace` 不支持 `Instant` 确认。

`WaitTargetData` `AbilityTask` 会以 `AGameplayAbilityTargetActor` 类作为参数，在每次激活时生成实例，并在 `AbilityTask` 结束时销毁 `TargetActor`。`WaitTargetDataUsingActor` `AbilityTask` 接受已生成的 `TargetActor`，但同样会在 `AbilityTask` 结束时销毁它。这些 `AbilityTasks` 效率较低，因为每次使用都需要生成或使用新生成的 `TargetActor`。它们适合原型开发，但在生产中，如果频繁生成 `TargetData`（如自动步枪），可能需要优化。GASShooter 提供了 `AGameplayAbilityTargetActor` 的自定义子类，以及从零实现的 `WaitTargetDataWithReusableActor` `AbilityTask`，允许重复使用 `TargetActor` 而不销毁。

`TargetActors` 默认不复制（replicate），但如果游戏需要展示本地玩家的目标位置给其他玩家，可以启用复制。它们包括通过 `WaitTargetData` `AbilityTask` 与服务器通信的默认功能。如果 `TargetActor` 的 `ShouldProduceTargetDataOnServer` 属性为 `false`，客户端将在确认时通过 `UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()` 调用 `CallServerSetReplicatedTargetData()` 将 `TargetData` RPC 发送给服务器。如果为 `true`，客户端会发送通用确认事件 `EAbilityGenericReplicatedEvent::GenericConfirm` RPC，服务器收到后执行 trace 或 overlap 以在服务器生成数据。如果客户端取消目标，会发送通用取消事件 `EAbilityGenericReplicatedEvent::GenericCancel` RPC 给服务器。可以看到，`TargetActor` 和 `WaitTargetData` `AbilityTask` 上都有很多代理（delegate）。`TargetActor` 响应输入来生成并广播 `TargetData` ready、确认或取消代理。`WaitTargetData` 监听 `TargetActor` 的 `TargetData` ready、确认和取消代理，并将信息传回 `GameplayAbility` 和服务器。如果将 `TargetData` 发送到服务器，建议在服务器端进行验证以防作弊。在服务器端直接生成 `TargetData` 可以完全避免此问题，但可能导致客户端预测错误。

根据使用的 `AGameplayAbilityTargetActor` 子类不同，`WaitTargetData` `AbilityTask` 节点会暴露不同的 `ExposeOnSpawn` 参数。一些常见参数包括：

| 常用 `TargetActor` 参数 | 定义                                                                                                                                          |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Debug               | 如果为 `true`，在非发布版本中 `TargetActor` 执行 trace 时会绘制调试信息。非 `Instant` `TargetActor` 会在 `Tick()` 中执行 trace，因此这些调试绘制也会在 `Tick()` 中发生。                |
| Filter              | [可选] 特殊结构体，用于在 trace/overlap 发生时过滤（移除）目标中的 `Actors`。典型用例：过滤玩家的 `Pawn`，要求目标为特定类。更高级用法见 [Target Data Filters](#concepts-target-data-filters)。 |
| Reticle Class       | [可选] `AGameplayAbilityWorldReticle` 的子类，`TargetActor` 将生成该类实例。                                                                              |
| Reticle Parameters  | [可选] 配置 Reticle，参见 [Reticles](#concepts-targeting-reticles)。                                                                                |
| Start Location      | 用于追踪的起始位置特殊结构体，通常是玩家视角、武器枪口或 `Pawn` 的位置。                                                                                                    |

对于默认 `TargetActor` 类，只有直接位于 trace/overlap 内的 `Actors` 才是有效目标。如果它们离开 trace/overlap（移动或视角离开），则不再有效。如果希望 `TargetActor` 记住上一次的有效目标，需要在自定义 `TargetActor` 类中添加该功能。我称之为持久目标（persistent targets），它们会一直存在，直到 `TargetActor` 收到确认或取消、在 trace/overlap 中找到新的有效目标，或目标不再有效（被销毁）。GASShooter 在火箭发射器二次技能的自导火箭目标中使用了持久目标。

**[⬆ 回到目录](#目录)**

#### 4.11.3 目标数据过滤器（Target Data Filters）

使用 `Make GameplayTargetDataFilter` 和 `Make Filter Handle` 节点，可以过滤掉玩家的 `Pawn` 或只选择特定类。如果需要更高级的过滤，可以继承 `FGameplayTargetDataFilter` 并重写 `FilterPassesForActor` 函数。

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** 如果 Actor 通过过滤器且将被作为目标，则返回 true */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
```

但是，这种自定义过滤器不能直接用于 `Wait Target Data` 节点，因为它需要一个 `FGameplayTargetDataFilterHandle`。因此必须创建一个新的自定义 `Make Filter Handle` 来接受子类：

```c++
FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ 回到目录](#目录)**

#### 4.11.4 游戏能力世界准星（Gameplay Ability World Reticles）

[`AGameplayAbilityWorldReticles`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)（`Reticles`）用于在使用非 `Instant` 确认的 [`TargetActors`](#concepts-targeting-actors) 时可视化 **你正在瞄准的对象**。`TargetActors` 负责所有 `Reticles` 的生成与销毁生命周期。`Reticles` 基于 `AActor`，因此可以使用任何类型的可视组件进行表示。一个常见实现（如 [GASShooter](https://github.com/tranek/GASShooter) 所示）是使用 `WidgetComponent` 在屏幕空间显示 UMG Widget（始终面向玩家相机）。`Reticles` 本身不知道它们附着在哪个 `AActor` 上，但你可以在自定义 `TargetActor` 中为其增加该功能。`TargetActors` 通常会在每个 `Tick()` 中更新 `Reticle` 的位置到目标位置。

GASShooter 使用 `Reticles` 显示火箭发射器二次技能自导火箭的锁定目标。敌人上的红色指示器就是 `Reticle`，类似的白色图像是火箭发射器的准星。
![Reticles in GASShooter](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplayabilityworldreticle.png)

`Reticles` 提供了一些 `BlueprintImplementableEvents` 供设计师在蓝图中实现：

```c++
/** 当 bIsTargetValid 改变时调用 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** 当 bIsTargetAnActor 改变时调用 */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticles` 可以选择使用 `TargetActor` 提供的 [`FWorldReticleParameters`](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html) 进行配置。默认结构体仅提供一个变量 `FVector AOEScale`。虽然技术上可以继承该结构体，但默认 `TargetActors` 只接受基础结构体。这在默认实现中有些局限，但如果你自定义 `TargetActor`，可以提供自己的 Reticle 参数结构体，并在生成 `AGameplayAbilityWorldReticles` 子类时手动传入。

`Reticles` 默认不复制（replicate），但如果希望在游戏中让其他玩家看到本地玩家的目标，可以启用复制。

使用默认 `TargetActors` 时，`Reticles` 只会显示在当前有效目标上。例如，如果使用 `AGameplayAbilityTargetActor_SingleLineTrace` 进行目标追踪，只有敌人直接位于追踪路径上时才会显示 `Reticle`；如果视角移开，敌人不再是有效目标，`Reticle` 会消失。如果希望 `Reticle` 保持在上一次有效目标上，需要自定义 `TargetActor` 记住上一个有效目标，并保持 `Reticle` 显示。我称这些为持久目标（persistent targets），它们会一直存在，直到 `TargetActor` 收到确认或取消、在 trace/overlap 中找到新的有效目标，或目标不再有效（被销毁）。GASShooter 在火箭发射器二次技能自导火箭目标中使用了持久目标。

**[⬆ 回到目录](#目录)**

#### 4.11.5 Gameplay Effect Containers Targeting
[`GameplayEffectContainers`](#concepts-ge-containers) 提供了一种可选的、高效生成 [`TargetData`](#concepts-targeting-data) 的方式。当 `EffectContainer` 在客户端和服务器上被应用时，这种目标处理会立即发生。它比 [`TargetActors`](#concepts-targeting-actors) 更高效，因为它在目标对象的 CDO 上运行（无需生成和销毁 `Actors`），但缺点是缺少玩家输入、立即发生无需确认、无法取消，也无法从客户端发送数据到服务器（在客户端和服务器上都生成数据）。它非常适合瞬发追踪（instant traces）和碰撞重叠（collision overlaps）。Epic 的 [Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) 包含两种容器目标示例——瞄准技能拥有者以及从事件中获取 `TargetData`。它还在蓝图中实现了一个示例，用于在玩家的某个偏移位置（由子蓝图类设置）进行瞬发球体追踪。你可以在 C++ 或蓝图中继承 `URPGTargetType` 来创建自己的目标类型。

**[⬆ 回到目录](#目录)**

## 5. 常见实现的技能与效果
### 5.1 晕眩（Stun）
通常对于晕眩效果，我们希望取消一个 `Character` 的所有激活中的 `GameplayAbilities`，阻止新的 `GameplayAbility` 激活，并在晕眩持续时间内阻止移动。示例项目中，陨石技能的 `GameplayAbility` 会对命中的目标施加晕眩。

要取消目标激活中的 `GameplayAbilities`，当晕眩 [`GameplayTag` 被添加](#concepts-gt-change) 时，我们调用 `AbilitySystemComponent->CancelAbilities()`。

要在晕眩期间阻止新的 `GameplayAbilities` 激活，将晕眩 `GameplayTag` 添加到它们的 [`Activation Blocked Tags` `GameplayTagContainer`](#concepts-ga-tags) 中。

要在晕眩期间阻止移动，我们重写 `CharacterMovementComponent` 的 `GetMaxSpeed()` 函数，当拥有者拥有晕眩 `GameplayTag` 时返回 0。

**[⬆ 回到目录](#目录)**

### 5.2 冲刺（Sprint）

示例项目提供了冲刺的实现——按住 `Left Shift` 时加快移动速度。

加速移动由 `CharacterMovementComponent` 预测性处理，通过网络向服务器发送标志位。详情见 `GDCharacterMovementComponent.h/cpp`。

`GA` 处理响应 `Left Shift` 输入，通知 `CMC` 开始和停止冲刺，并在按住 `Left Shift` 时预测性消耗耐力。详情见 `GA_Sprint_BP`。

**[⬆ 回到目录](#目录)**

### 5.3 瞄准（Aim Down Sights）
示例项目的实现方式与冲刺完全相同，但减少移动速度而不是增加。

预测性减少移动速度的实现见 `GDCharacterMovementComponent.h/cpp`。

输入处理详情见 `GA_AimDownSight_BP`。瞄准时不消耗耐力。

**[⬆ 回到目录](#目录)**

### 5.4 吸血（Lifesteal）

我在伤害 [`ExecutionCalculation`](#concepts-ge-ec) 中处理吸血。`GameplayEffect` 上会有一个类似 `Effect.CanLifesteal` 的 `GameplayTag`。`ExecutionCalculation` 会检查 `GameplayEffectSpec` 是否包含该 `Effect.CanLifesteal` `GameplayTag`。如果存在该 `GameplayTag`，`ExecutionCalculation` 会[创建一个动态 `Instant` `GameplayEffect`](#concepts-ge-dynamic)，将要恢复的生命值作为修改量，并将其应用回来源的 `ASC`。

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 回到目录](#目录)**

### 5.5 在客户端和服务器上生成随机数
有时你需要在 `GameplayAbility` 内生成“随机”数，例如子弹后坐力或弹道偏移。客户端和服务器都希望生成相同的随机数。为此，我们必须在 `GameplayAbility` 激活时设置相同的 `random seed`。每次激活 `GameplayAbility` 时都应设置 `random seed`，以防客户端误预测激活导致随机数序列与服务器不同步。

| Seed 设置方法                          | 描述                                                                                                                                                                   |
| ---------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 使用激活预测键（activation prediction key） | `GameplayAbility` 的激活预测键是一个 int16，保证在客户端和服务器的 `Activation()` 中同步可用。可以在客户端和服务器上都设置为 `random seed`。缺点是预测键每次游戏开始都会从零开始，并持续递增用于生成键的值，这意味着每场比赛的随机数序列都完全相同。对于某些需求，这可能不够随机。 |
| 在激活 `GameplayAbility` 时通过事件负载发送种子  | 通过事件激活 `GameplayAbility`，并通过可复制事件负载将客户端生成的随机种子发送到服务器。这允许更多随机性，但客户端可能轻易篡改游戏，每次只发送相同种子。通过事件激活 `GameplayAbilities` 还会阻止它们通过输入绑定激活。                                      |

如果偏差很小，大多数玩家不会注意每场比赛序列相同，使用激活预测键作为 `random seed` 就足够了。如果你做的是更复杂且需要防作弊的系统，可能使用“服务器发起”的 `GameplayAbility` 更合适，由服务器创建预测键或生成 `random seed` 并通过事件负载发送。

**[⬆ 回到目录](#目录)**

### 5.6 Critical Hits
I handle critical hits inside of the damage [`ExecutionCalculation`](#concepts-ge-ec). The `GameplayEffect` will have a `GameplayTag` on it like `Effect.CanCrit`. The `ExecutionCalculation` checks if the `GameplayEffectSpec` has that `Effect.CanCrit` `GameplayTag`. If the `GameplayTag` exists, the `ExecutionCalculation` generates a random number corresponding to the critical hit chance (`Attribute` captured from the `Source`) and adds the critical hit damage (also an `Attribute` captured from the `Source`) if it succeeded. Since I don't predict damage, I don't have to worry about synchronizing the random number generators on the client and server since the `ExecutionCalculation` will only run on the server. If you tried to do this predictively using an `MMC` to do your damage calculation, you would have to get a reference to the `random seed` from the `GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`.

See how [GASShooter](https://github.com/tranek/GASShooter) does headshots. It's the same concept except that it does not rely on a random number for chance and instead checks the `FHitResult` bone name.

**[⬆ 回到目录](#目录)**

### 5.7 非叠加的 Gameplay Effects，但仅最大数值生效
Paragon 中的减速效果不会叠加。每个减速实例会按正常方式应用并跟踪其持续时间，但只有最大数值的减速效果实际影响 `Character`。GAS 提供了 `AggregatorEvaluateMetaData` 来直接处理这种场景。详情和实现见 [`AggregatorEvaluateMetaData()`](#concepts-as-onattributeaggregatorcreated)。

**[⬆ 回到目录](#目录)**

### 5.8 在游戏暂停时生成目标数据
如果你需要在生成 [`TargetData`](#concepts-targeting-data)（通过玩家的 `WaitTargetData` `AbilityTask`）时暂停游戏，我建议不要真正暂停，而是使用 `slomo 0`。

**[⬆ 回到目录](#目录)**

### 5.9 一键交互系统
[GASShooter](https://github.com/tranek/GASShooter) 实现了一键交互系统，玩家可以按下或按住 ‘E’ 与可交互对象互动，例如复活玩家、打开武器箱或开关滑动门。

**[⬆ 回到目录](#目录)**

## 6. 调试 GAS
在调试与 GAS 相关的问题时，你可能想知道：

> * “我的属性值是多少？”
> * “我有哪些 Gameplay Tags？”
> * “我当前拥有哪些 Gameplay Effects？”
> * “我被授予了哪些能力，哪些正在运行，哪些被阻止激活？”

GAS 提供了两种在运行时查看这些信息的方法——[`showdebug abilitysystem`](#debugging-sd) 和 [`GameplayDebugger`](#debugging-gd) 中的钩子。

**提示：** Unreal Engine 会对 C++ 代码进行优化，这可能会使某些函数难以调试。当你深入追踪代码时偶尔会遇到这种情况。如果将 Visual Studio 解决方案配置设置为 `DebugGame Editor` 仍无法追踪代码或查看变量，可以通过在优化函数前后使用 `UE_DISABLE_OPTIMIZATION` 和 `UE_ENABLE_OPTIMIZATION` 宏（或 `CoreMiscDefines.h` 中定义的 ship 版本）来禁用所有优化。这不能直接用于插件代码，除非你从源码重新编译插件。对于内联函数，是否有效取决于其内容和位置。调试完成后务必移除宏！

```c++
UE_DISABLE_OPTIMIZATION
void MyClass::MyFunction(int32 MyIntParameter)
{
	// 我的代码
}
UE_ENABLE_OPTIMIZATION
```

**[⬆ 回到目录](#目录)**


### 6.1 showdebug abilitysystem
在游戏内控制台输入 `showdebug abilitysystem`。该功能分为三个“页面”。三个页面都会显示你当前拥有的 `GameplayTags`。在控制台输入 `AbilitySystem.Debug.NextCategory` 可在页面间切换。

第一页显示所有属性（Attributes）的 `CurrentValue`：
![First Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage1.png)

第二页显示你身上的所有 `Duration` 和 `Infinite` `GameplayEffects`，它们的堆叠数量、提供的 `GameplayTags` 以及 `Modifiers`：
![Second Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage2.png)

第三页显示所有被授予你的 `GameplayAbilities`，它们是否正在运行、是否被阻止激活，以及当前运行的 `AbilityTasks` 状态：
![Third Page of showdebug abilitysystem](https://github.com/tranek/GASDocumentation/raw/master/Images/showdebugpage3.png)

要在目标间切换（由 Actor 周围的绿色长方体表示），使用 `PageUp` 键或控制台命令 `NextDebugTarget` 切换到下一个目标，使用 `PageDown` 键或 `PreviousDebugTarget` 切换到上一个目标。

**注意：** 为了让能力系统信息根据当前选定的调试 Actor 更新，需要在 `AbilitySystemGlobals` 中设置 `bUseDebugTargetFromHud=true`，例如在 `DefaultGame.ini` 中：

```
[/Script/GameplayAbilities.AbilitySystemGlobals]
bUseDebugTargetFromHud=true
```

**注意：** 要让 `showdebug abilitysystem` 生效，GameMode 中必须选择实际的 HUD 类，否则会显示“Unknown Command”。

**[⬆ 回到目录](#目录)**

### 6.2 Gameplay Debugger
GAS 为 Gameplay Debugger 添加了功能。使用 Apostrophe (') 键打开 Gameplay Debugger。按小键盘的 3 键启用 Abilities 类别。根据你安装的插件，这个类别可能会不同。如果你的键盘没有小键盘（例如笔记本），可以在项目设置中修改按键绑定。

当你想查看 **其他** `Character` 的 `GameplayTags`、`GameplayEffects` 和 `GameplayAbilities` 时使用 Gameplay Debugger。不幸的是，它不会显示目标 `Attributes` 的 `CurrentValue`。它会自动锁定屏幕中心的 `Character`。你可以在编辑器的 World Outliner 中选择目标，或看向不同的 `Character` 并再次按 Apostrophe (') 键切换。当前被检查的 `Character` 头上会显示最大的红色圆圈。

![Gameplay Debugger](https://github.com/tranek/GASDocumentation/raw/master/Images/gameplaydebugger.png)

**[⬆ 回到目录](#目录)**

### 6.3 GAS 日志记录（Logging）
GAS 源码中包含大量不同冗余级别的日志语句，通常以 `ABILITY_LOG()` 的形式出现。默认的冗余级别是 `Display`，高于该级别的日志默认不会显示在控制台。

要更改日志类别的冗余级别，可以在控制台输入：

```
log [category] [verbosity]
```

例如，要启用 `ABILITY_LOG()` 语句，可以在控制台输入：

```
log LogAbilitySystem VeryVerbose
```

要重置为默认值，输入：

```
log LogAbilitySystem Display
```

显示所有日志类别，输入：

```
log list
```

与 GAS 相关的重要日志类别：

| 日志类别（Logging Category）    | 默认冗余级别（Default Verbosity Level） |
| ------------------------- | ------------------------------- |
| LogAbilitySystem          | Display                         |
| LogAbilitySystemComponent | Log                             |
| LogGameplayCueDetails     | Log                             |
| LogGameplayCueTranslator  | Display                         |
| LogGameplayEffectDetails  | Log                             |
| LogGameplayEffects        | Display                         |
| LogGameplayTags           | Log                             |
| LogGameplayTasks          | Log                             |
| VLogAbilitySystem         | Display                         |

更多信息请参见 [Logging Wiki](https://unrealcommunity.wiki/logging-lgpidy6i)。

**[⬆ 回到目录](#目录)**

## 7. 优化
### 7.1 能力批处理（Ability Batching）
那些在一帧内激活、可选地向服务器发送 `TargetData` 并结束的 [`GameplayAbilities`](#concepts-ga) 可以[批处理，将两到三个 RPC 合并为一个 RPC](#concepts-ga-batching)。这类技能通常用于 hitscan 枪械。


### 7.2 Gameplay Cue 批处理（Gameplay Cue Batching）
如果同时发送大量 [`GameplayCues`](#concepts-gc)，可以考虑[将它们批处理为一个 RPC](#concepts-gc-batching)。目的是减少 RPC 数量（`GameplayCues` 是不可靠的 NetMulticast）并尽量少发送数据。

### 7.3 AbilitySystemComponent 复制模式（Replication Mode）
默认情况下，[`ASC`](#concepts-asc) 处于[`完全复制模式`](#concepts-asc-rm)，会将所有 [`GameplayEffects`](#concepts-ge) 复制到每个客户端（单机游戏没有问题）。在多人游戏中，将玩家拥有的 `ASCs` 设置为混合复制模式（Mixed Replication Mode），AI 控制的角色设置为最小复制模式（Minimal Replication Mode）。这样，玩家角色上的 `GEs` 只复制给该玩家自己，而 AI 控制角色上的 `GEs` 永远不会复制给客户端。[`GameplayTags`](#concepts-gt) 仍会复制，[`GameplayCues`](#concepts-gc) 仍然是不可靠的 NetMulticast 发送给所有客户端，无论复制模式如何。这样可以减少不必要的网络数据传输。

### 7.4 属性代理复制（Attribute Proxy Replication）
在大型多人游戏（如 Fortnite Battle Royale, FNBR）中，会有很多 [`ASCs`](#concepts-asc) 存在于始终相关的 `PlayerStates` 上，复制大量 [`Attributes`](#concepts-a)。为了优化这个瓶颈，Fortnite 在 **模拟玩家控制的代理** 上完全禁用了 `ASC` 及其 [`AttributeSets`](#concepts-as) 的复制（在 `PlayerState::ReplicateSubobjects()` 中）。自主代理（Autonomous Proxy）和 AI 控制的 `Pawn` 仍根据其[`Replication Mode`](#concepts-asc-rm) 完全复制。

FNBR 没有在始终相关的 `PlayerStates` 上复制 `Attributes`，而是在玩家的 `Pawn` 上使用一个可复制的代理结构。当服务器的 `ASC` 上的 `Attributes` 变化时，也会同步到代理结构。客户端从代理结构接收复制的 `Attributes` 并将变化推回本地 `ASC`。这样可以利用 `Pawn` 的 relevancy 和 `NetUpdateFrequency` 来复制属性。该代理结构还会以位掩码方式复制一小部分白名单的 `GameplayTags`。该优化减少了网络数据量，并充分利用了 `Pawn` 的相关性。AI 控制的 `Pawn` 的 `ASC` 本身已经使用了其相关性，因此不需要这个优化。

> 我不确定随着其他服务器端优化（如 Replication Graph 等）实施后，这个方法是否仍然必要，而且这不是最易维护的模式。
> *——Dave Ratti（Epic）在 [community questions #3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89) 的回答*

<a name="optimizations-asclazyloading"></a>

### 7.5 ASC 延迟加载（Lazy Loading）
Fortnite Battle Royale (FNBR) 世界中有很多可受伤的 `AActors`（树、建筑等），每个都有一个 [`ASC`](#concepts-asc)。这会增加内存开销。FNBR 通过延迟加载 `ASCs` 来优化——只有在第一次受到玩家伤害时才加载。这样可以降低整体内存占用，因为一些 `AActors` 在比赛中可能永远不会受到伤害。

**[⬆ 回到目录](#目录)**

## 8. 提升开发体验的建议（Quality of Life Suggestions）

<a name="qol-gameplayeffectcontainers"></a>

### 8.1 Gameplay Effect Containers

[GameplayEffectContainers](#concepts-ge-containers) 将 [`GameplayEffectSpecs`](#concepts-ge-spec)、[`TargetData`](#concepts-targeting-data)、[简单目标选择](#concepts-targeting-containers) 及相关功能组合成易于使用的结构。这对于将 `GameplayEffectSpecs` 传递给由能力生成的投射物，然后在碰撞时应用效果非常方便。

<a name="qol-asynctasksascdelegates"></a>

### 8.2 绑定到 ASC 委托的 Blueprint AsyncTasks

为了提高面向设计师的迭代效率，尤其是在为 UI 设计 UMG Widgets 时，可以创建 Blueprint AsyncTasks（C++）来直接绑定到 `ASC` 上的常用变更委托，从而在 UMG Blueprint 图表中使用。唯一需要注意的是，它们必须手动销毁（例如在 Widget 被销毁时），否则会一直驻留在内存中。示例项目包含了三个 Blueprint AsyncTasks。

监听 `Attribute` 变化：

![Listen for Attributes Changes BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/attributeschange.png)

监听冷却变化：

![Listen for Cooldown Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/cooldownchange.png)

监听 `GameplayEffect` 堆叠变化：

![Listen for GameplayEffect Stack Change BP Node](https://github.com/tranek/GASDocumentation/raw/master/Images/gestackchange.png)

**[⬆ 回到目录](#目录)**

## 9. 故障排查（Troubleshooting）

### 9.1 `LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!`

你需要在客户端[初始化 `ASC`](#concepts-asc-setup)。

**[⬆ 回到目录](#目录)**

<a name="troubleshooting-scriptstructcache"></a>

### 9.2 `ScriptStructCache` 错误

你需要调用 [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata)。

**[⬆ 回到目录](#目录)**

<a name="troubleshooting-replicatinganimmontages"></a>

### 9.3 动画 Montages 没有复制到客户端

确保在 [GameplayAbilities](#concepts-ga) 中使用 `PlayMontageAndWait` Blueprint 节点，而不是 `PlayMontage`。这个 [AbilityTask](#concepts-at) 会通过 `ASC` 自动复制 Montage，而 `PlayMontage` 节点不会。

**[⬆ 回到目录](#目录)**

<a name="troubleshooting-duplicatingblueprintactors"></a>

### 9.4 复制 Blueprint Actors 导致 AttributeSets 变为 nullptr

Unreal Engine 有一个[已知 Bug](https://issues.unrealengine.com/issue/UE-81109)，会将从现有 Blueprint Actor 类复制的 Blueprint Actor 类中的 `AttributeSet` 指针置为 nullptr。
解决方法有几种。我实践中成功的方法是**不在类中创建自定义 AttributeSet 指针**（在 `.h` 中不声明指针，也不在构造函数中调用 `CreateDefaultSubobject`），而是直接在 `PostInitializeComponents()` 中将 `AttributeSets` 添加到 `ASC`（示例项目未展示）。复制的 `AttributeSets` 仍会存在于 `ASC` 的 `SpawnedAttributes` 数组中，例如：

```c++
void AGDPlayerState::PostInitializeComponents()
{
	Super::PostInitializeComponents();

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->AddSet<UGDAttributeSetBase>();
		// ... 添加其他可能的 AttributeSets
	}
}
```

在这种情况下，你应通过 `ASC` 上的函数读取和设置 `AttributeSet` 的值，而不是通过宏生成的 `AttributeSet` 函数：

```c++
/** 获取属性的当前（最终）值 */
float GetNumericAttribute(const FGameplayAttribute &Attribute) const;

/** 设置属性的基础值。现有的激活 Modifier 不会被清除，会作用于新基础值。 */
void SetNumericAttributeBase(const FGameplayAttribute &Attribute, float NewBaseValue);
```

例如，获取生命值 `GetHealth()`：

```c++
float AGDPlayerState::GetHealth() const
{
	if (AbilitySystemComponent)
	{
		return AbilitySystemComponent->GetNumericAttribute(UGDAttributeSetBase::GetHealthAttribute());
	}

	return 0.0f;
}
```

设置（初始化）生命值属性：

```c++
const float NewHealth = 100.0f;
if (AbilitySystemComponent)
{
	AbilitySystemComponent->SetNumericAttributeBase(UGDAttributeSetBase::GetHealthAttribute(), NewHealth);
}
```

提醒：每个 `AttributeSet` 类在 `ASC` 中最多只会有一个对象实例。

**[⬆ 回到目录](#目录)**

### 9.5 未解析的外部符号 `UEPushModelPrivate::MarkPropertyDirty(int,int)`

如果你遇到如下编译器错误：

```
error LNK2019: unresolved external symbol "__declspec(dllimport) void __cdecl UEPushModelPrivate::MarkPropertyDirty(int,int)" (__imp_?MarkPropertyDirty@UEPushModelPrivate@@YAXHH@Z) referenced in function "public: void __cdecl FFastArraySerializer::IncrementArrayReplicationKey(void)" (?IncrementArrayReplicationKey@FFastArraySerializer@@QEAAXXZ)
```

这是因为在 `FFastArraySerializer` 上调用 `MarkItemDirty()` 时出现问题。我在更新 `ActiveGameplayEffect`（例如更新冷却时间）时遇到过：

```c++
ActiveGameplayEffects.MarkItemDirty(*AGE);
```

问题的根源在于 `WITH_PUSH_MODEL` 在多个地方被定义。`PushModelMacros.h` 将其定义为 0，而在多个其他地方又定义为 1。结果 `PushModel.h` 看到了 1，而 `PushModel.cpp` 看到了 0。

解决方法是：在项目的 `Build.cs` 文件的 `PublicDependencyModuleNames` 中加入 `NetCore`。

**[⬆ 回到目录](#目录)**

<a name="troubleshooting-enumnamesarenowpathnames"></a>

### 9.6 枚举名称现在使用路径名表示

如果你收到如下编译器警告：

```
warning C4996: 'FGameplayAbilityInputBinds::FGameplayAbilityInputBinds': Enum names are now represented by path names. Please use a version of FGameplayAbilityInputBinds constructor that accepts FTopLevelAssetPath. Please update your code to the new API before upgrading to the next release, otherwise your project will no longer compile.
```

UE5.1 弃用了在 `BindAbilityActivationToInputComponent()` 构造函数中使用 `FString` 的方式。现在必须传入 `FTopLevelAssetPath`。

**旧的、已弃用方式：**

```c++
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

**新的方式：**

```c++
FTopLevelAssetPath AbilityEnumAssetPath = FTopLevelAssetPath(FName("/Script/GASDocumentation"), FName("EGDAbilityInputID"));
AbilitySystemComponent->BindAbilityActivationToInputComponent(InputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"),
	FString("CancelTarget"), AbilityEnumAssetPath, static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

更多信息请参见：`Engine\Source\Runtime\CoreUObject\Public\UObject\TopLevelAssetPath.h`。

**[⬆ 回到目录](#目录)**

## 10. 常见 GAS 缩写

| 名称                                                                                          | 缩写                  |
| ------------------------------------------------------------------------------------------- | ------------------- |
| AbilitySystemComponent                                                                      | ASC                 |
| AbilityTask                                                                                 | AT                  |
| [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg) | ARPG, ARPG Sample   |
| CharacterMovementComponent                                                                  | CMC                 |
| GameplayAbility                                                                             | GA                  |
| GameplayAbilitySystem                                                                       | GAS                 |
| GameplayCue                                                                                 | GC                  |
| GameplayEffect                                                                              | GE                  |
| GameplayEffectExecutionCalculation                                                          | ExecCalc, Execution |
| GameplayTag                                                                                 | Tag, GT             |
| ModifierMagnitudeCalculation                                                                | ModMagCalc, MMC     |

**[⬆ 回到目录](#目录)**

<a name="resources"></a>

## 11. 其他资源

* [官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html)
* 源代码！

  * 尤其是 `GameplayPrediction.h`
* [Epic 的 Lyra 示例项目](https://unrealengine.com/marketplace/en-US/learn/lyra)
* [Epic 的 Action RPG 示例项目](https://www.unrealengine.com/marketplace/en-US/product/action-rpg)
* [Unreal Slackers Discord](https://unrealslackers.org/) 提供专门的 GAS 文本频道 `#gameplay-ability-system`

  * 可查看置顶消息
* [Dan 'Pan' 的资源 GitHub 仓库](https://github.com/Pantong51/GASContent)
* [SabreDartStudios 的 YouTube 视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-)

### 11.1 与 Epic 游戏的 Dave Ratti 的问答
#### 11.1.1 社区问题 1
[Dave Ratti 对 Unreal Slackers Discord 社区关于 GAS 的提问的回答](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)

1. 如何在 `GameplayAbilities` 之外或不依赖它们时按需创建作用域预测窗口？例如，一个“开火即忘”的投射物如何在击中敌人时本地预测造成的 `GameplayEffect`？

> PredictionKey 系统并不是专门为这个设计的。其基本工作方式是：客户端发起预测动作，用一个 key 通知服务器，然后客户端和服务器都执行相同操作，并将预测性副作用与该 key 关联。例如，“我正在预测性激活一个 Ability”或“我已经生成了目标数据，并将预测性执行 WaitTargetData 任务之后的 Ability 图”。
>
> 在这种模式下，PredictionKey 会通过 `UAbilitySystemComponent::ReplicatedPredictionKeyMap`（可复制属性）从服务器返回客户端。一旦 key 被服务器复制回来，客户端就可以撤销所有本地预测副作用（GameplayCues、GameplayEffects）：复制版本会存在，如果不存在，则说明预测错误。准确知道何时撤销预测副作用至关重要：过早会看到间隙，过晚会出现“双重”。（这里指的是有状态预测，如持续 GameplayEffect 的循环 GameplayCue。爆发型 GameplayCues 和即时 GameplayEffects 永远不会被撤销，如果它们关联了 PredictionKey，则在客户端会被跳过。）
>
> 进一步说明，预测动作必须是服务器不会自行执行的，只有客户端告知服务器后才执行。因此，通用的“按需创建 key 并告诉服务器以运行某些操作”不可行，除非该操作仅在客户端指示时才由服务器执行。
>
> 回到原问题：像“开火即忘”的投射物。Paragon 和 Fortnite 都有使用 GameplayCues 的投射物 Actor 类，但我们不使用 PredictionKey 系统来处理它们。我们使用非复制（Non-Replicated）GameplayCues。这些 GameplayCues 只在本地触发，服务器完全跳过。它们直接调用 `UGameplayCueManager::HandleGameplayCue`，不经过 `UAbilitySystemComponent`，不做 key 检查或提前返回。
>
> 非复制 GameplayCues 的缺点是它们不会被复制。因此投射物类或 Blueprint 必须确保调用这些函数的代码在所有客户端上都运行。我们通常在事件中处理投射物启动（BeginPlay）、爆炸、碰撞墙/角色等。
>
> 这些事件已经在客户端生成，所以调用非复制 GameplayCue 没问题。复杂的 Blueprint 需要作者自己确认每段代码运行的位置。


2. When using a `WaitNetSync` `AbilityTask` with `OnlyServerWait` to create a scoped prediction window in a locally predicted `GameplayAbility`, could players potentially cheat by delaying their packets to the Server to control `GameplayAbility` timing since the Server is waiting for their RPC withtheir prediction key? Was this ever an issue in Paragon or Fortnite, and if so, what did Epic do to remedy it?

> Yes, this is a valid concern. Any ability blueprint running on the server that is waiting for a client “signal” is potentially vulnerable to lag switch type exploits.
>
> Paragon had a custom targeting task similar to UAbilityTask_WaitTargetData. In this task we had timeouts, or a “max delay” that we would wait on the client for instantaneous targeting modes. If the targeting mode was waiting for user confirmation (button press) then it would be ignored since the user is allowed to take his time. But for abilities that instantly confirmed targeting we would only wait a certain amount of time before either A) generating the target data server side or B) canceling the ability.
>
> We never had such mechanisms for WaitNetSync, which we used pretty sparingly.
>
> I don’t believe Fortnite makes use of anything like this though. The weapon abilities in Fortnite are special cased batched to a single fortnite-specific RPC: one RPC to activate the ability, provide target data, and end the ability. So weapon abilities are intrinsically not vulnerable to this in Battle Royale.
>
> My take is that this is something that could probably be solved system wide but I don’t see us making the change ourselves anytime soon. Spot fixing WaitNetSync to include a max delay for the case you mention is probably a reasonable task, but again - unlikely we will do this on our end in the immediate future.


3. Which `EGameplayEffectReplicationMode` did Paragon and Fortnite use and what are Epic’s recommendations for when to use each?

> Both games essentially use Mixed mode for their player controlled characters and Minimal for AI controlled (AI minions, jungle creeps, AI Husks, etc). This is what I would recommend most people using the system in a multiplayer game. The sooner into your project you set these, the better.
>
> Fortnite goes a few steps further with its optimizations. It actually does not replicate the UAbilitySystemComponent at all for simulated proxies. The component and attribute subobjects are skipped inside ::ReplicateSubobjects() on the owning fortnite player state class. We do push the bare minimum replicated data from the ability system component to a structure on the pawn itself (basically, a subset of attribute values and a white list subset of tags that we replicate down in a bitmask). We call this a “proxy”. On the receiving side we take the proxy data, replicated on the pawn, and push it back into ability system component on the player state. So you do have an ASC for each player in FNBR, it just doesn’t directly replicate: instead it replicates data via a minimal proxy struct on the pawn and then routes back to the ASC on receiving side. This is advantage since its A) a more minimal set of data B) takes advantage of pawn relevancy.
>
> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.


4. Since we cannot predict the removal of `GameplayEffects` as per `GameplayPrediction.h`, are there any strategies for mitigating the effects of latency on removing `GameplayEffects`? For example, when removing a movement speed slow, we currently have to wait for the Server to replicate the `GameplayEffect` removal resulting in a snap of the player’s character position.

> This is a tough one and I don’t have a good answer. We generally skirted around these problems with tolerances and smoothing. I totally agree that ability system and precise synchronization with the character movement system is not in a good place and something we do want to fix.
>
> I had a shelf of allowing predictive removal of GEs but could never work out all edge cases before having to move on. This doesn’t solve everything though since character movement still has an internal saved move buffer that does not know anything about the ability system and possible movement speed modifiers, etc. It is still possible to get into correction feedback loops even outside of not being able to predict the removal of GEs.
>
> If you think you have a case that is truly desperate, you are able to predictively add a GE that would inhibit your movement speed GEs. I’ve never done this myself but have theorized about it before. It may be able to help with a certain class of problem.


5. We know that the `AbilitySystemComponent` lives on the `PlayerState` in Paragon and Fortnite and on the `Character` in the Action RPG Sample. What are Epic’s internal rules, guidelines, or recommendations for where the AbilitySystemComponent should live, and what should its `Owner` be?

> In general I would say anything that does not need to respawn should have the Owner and Avatar actor be the same thing. Anything like AI enemies, buildings, world props, etc.
>
> Anything that does respawn should have the Owner and Avatar be different so that the Ability System Component does not need to be saved off / recreated / restored after a respawn. PlayerState is the logical choice it is replicated to all clients (where as PlayerController is not). The downside is PlayerStates are always relevant so you can run into problems in 100 player games (See notes on what FN did in question #3).


6. Is it viable to have several `AbilitySystemComponents` which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with `Owner` set to `PlayerState`)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning actor. The former may be possible: just aggregate the tags from all ASCs (but watch out - HasAllMatchingGameplayTags may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE - which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.
>
> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, attributes and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.


7. Is there a way to stop the Server from overwriting the cooldown duration of locally predicted abilities on the Owning Client? In scenarios of high latency, this would let the Owning Client "try" to activate the ability again when its local cooldown expires but it is still on cooldown on the Server. By the time the Owning Client's activation request reaches the Server over the network, the Server may be off cooldown or the Server might be able to queue the activation request for the remaining milliseconds that it has left. Otherwise as is, clients with higher latency have a longer delay before when they can reactivate an ability versus those with less latency. This is most apparent with very low cooldown abilities like a basic attack that can be less than one second of cooldown. If there isn't a way to stop the Server from overwriting the cooldown duration of locally predicted abilities, what is Epic's strategy for mitigating the effects of high latency on reactivating abilities? To word it another example-based way, how did Epic design Paragon's basic attacks and other abilities so that high latency players could attack or activate at the same speed as low latency players with local prediction?

> The short answer there is not a way to prevent this and Paragon definitely had the problem. Higher latency connections would have a lower ROF with basic attacks.
>
> I attempted to fix this by adding “GE reconciliation” where latency was taken into account when calculating GE duration. Essentially allowing the server to eat some of the total GE time so that the effective time of the GE client side would be 100% consistent with any amount of latency (though fluctuations could still cause issues). However I never got this working in a state that could ship and the project moved fast and we just never fully addressed it.
>
> Fortnite does its own bookkeeping for weapon firing rates: it does not use GEs for cooldowns on weapons. I would recommend this if this is a critical problem for your game.


8. What is Epic’s roadmap for the GameplayAbilitySystem plugin? Which features does Epic plan to add in 2019 and beyond?

> We feel that overall the system is pretty stable at this point and we don’t have anyone working on major new features. Bug fixes and small improvements occasionally are made for Fortnite or from UDN/pull requests, but that is it right now.
>
> Longer term, I think we will eventually do a “V2” or some big changes. We learned a lot from writing this system and feel we got a lot right and a lot wrong. I would love a chance to correct those mistakes and improve some of the fatal flaws that were pointed out above.
>
> If a V2 was to ever come, providing an upgrade path would be of utmost importance. We would never make a V2 and leave Fortnite on V1 forever: there would be some path or procedures that would automatically migrate as much as possible, though there would still almost certainly be some manual remaking required.
>
> The high priority fixes would be:
> * Better interoperability with the character movement system. Unifying client prediction.
> * GE removal prediction (question #4)
> * GE latency reconciliation (question #7)
> * Generalized network optimizations such as batching RPCs and proxy structures. Mostly the stuff that we’ve done for Fortnite but find ways to break it down into more generalized form, at least so that games can write their own game specific optimizations more easily.
>
> The more general refactor type of changes I would consider making:
> * I would like to look at fundamentally moving away from having GEs reference spreadsheet values directly, instead they would be able to emit parameters and those parameters could be filled by some higher level object that is bound to spreadsheet values. The problem with the current model is that GEs become unsharable due to their tight coupling with the curve table rows. I think a generalized system for parameterization could be written and be the underpinning of a V2 system.
> * Reduce number of “policies” on UGameplayAbility. I would remove ReplicationPolicy and InstancingPolicy. Replication is, imo, almost never actually needed and causes confusion. InstancingPolicy should be replaced instead by making FGameplayAbilitySpec a UObject that can be subclassed. This should have been the “non instantiated ability object” that has events and is blueprintable. The UGameplayAbility should be the “instanced per execution” object. It could be optional if you need to actually instantiate: instead “non instanced” abilities would be implemented via the new UGameplayAbilitySpec object. 
> * The system should provide more “middle level” constructs such as “filtered GE application container” (data drive what GEs to apply to which actors with higher level gameplay logic), “Overlapping volume support” (apply the “Filtered GE application container” based on collision primitive overlap events), etc. These are building blocks that every project ends up implementing in their own way. Getting them right is non trivial so I think we should do a better job providing some basic implementations. 
> * In general, reducing boilerplate needed to get your project up and running. Possibly a separate module “Ex library” or whatever that could provide things like passive abilities or basic hitscan weapons out of the box. This module would be optional but would get you up and running quickly.
> * I would like to move GameplayCues to a separate module that is not coupled with the ability system. I think there are a lot of improvements that could be made here.


> This is only my personal opinion and not a commitment from anyone. I think the most realistic course of action will be as new engine tech initiatives come through, the ability system will need to be updated and that will be a time to do this sort of thing. These initiatives could be related to scripting, networking, or physics/character movement. This is all very far looking ahead though so I cannot give commitments or estimates on timelines.

**[⬆ 回到目录](#目录)**

<a name="resources-daveratti-community2"></a>
#### 11.1.2 Community Questions 2
Community member [iniside](https://github.com/iniside)'s Q&A with Dave Ratti:

1. Is the support for decoupled fixed ticking planned? I'd like to
have Game Thread be fixed (like 30/60fps) and let the rendering thread
run wild. I ask if this is something we should expect in future or
not, to make some assumptions about how gameplay should work.
I ask mainly because there is now a fixed async tick for physics and
this poses a question how the rest of the system might work in the
future. I do not hide that having the ability to have fixed tick game
thread without also fixing tick rate of the rest of the engine would
be beyond awesome.

> There are no plans to decouple rendering frame rate and game thread tick frame rate. I think the ship has sailed on this ever happening due to the complexity of these systems and the requirement to preserve backwards compatibility with previous engine versions.
>
> Instead, the direction we've gone is to have an asynchronous "Physics Thread" which runs at a fixed tick rate, independent of the game thread. Things that need to run at a fixed rate can run here and the game thread / rendering can operate how they always have.
>
> It's worth clarifying that Network Prediction supports what it calls Independent Ticking and Fixed Ticking modes. My long term plan is to keep Independent Ticking roughly how it is today in Network Prediction where it runs on the game thread at variable frame rate and there is no "group/world" prediction, it's just the classic "clients predict their own pawn and owned actors" model. And Fixed Ticking would be what uses the async physics stuff and allows you to predict non client controlled/owned actors like physics objects and other clients/pawns/vehicles/etc.


2. Is there any plan on how the integration of Network Prediction will
look with the Ability System? Like for example, fixed frame ability
activation (so the server gets frames in which abilities were
activated and tasks executed instead of prediction keys)?

> Yes, the plan is to rewrite/remove the Ability System's prediction keys and replace them with Network Prediction constructs. The MockAbility examples in NetworkPredictionExtras show how this might work but they are more "hard coded" than what GAS will require. 
>
> The main idea would be that we remove the explicit client->server Prediction Key exchange in the ASC's RPCs. There would no longer be prediction windows or scoped prediction keys. Instead everything would be anchored around NetworkPrediction frames. The important thing is that client and server agree on when things happen. Examples would be:
>
> * When abilities were activated/ended/cancelled
> * When Gameplay Effects were applied/removed
> * Attribute values (what an attributes value was at frame X)
>
> I think this could be done generically at the ability system level. But actually making the user-defined logic inside a UGameplayAbility completely rollback-able would still take more work. We may end up having a subclass of UGameplayAbility that is fully rollbackable and has access to a more limited set of functionality or only Ability Tasks that are marked as rollback-friendly. Something like that. There are also many implications to animation events and root motion and how those are processed.
>
> Wish I had a more clear answer but it's really important we get the foundation right before touching GAS again. Movement and physics have to be solid before the higher level systems can be changed.


3. Is there a plan to move Network Prediction development toward the
main branch? Not gonna lie, I'd really like to check the latest code.
Regardless of it's state.

> We are working towards it. The system work is still all being done in NetworkPrediction (see NetworkPhysics.h) and the underlying async physics stuff should be all available (RewindData.h etc). But we also have use cases in Fortnite that we have been focused on that obviously can't be made public. We are working through bugs, performance optimizations, etc.
>
> For more context: when working on the early versions of this system, we were very focused on the "front end" of things - how state and simulations were defined and written. We learned a lot there. But as the async physics stuff has come online, we've been much more focused on just getting something real to work in this system, at the expense of throwing out some of our early abstractions. The goal here is to circle back when the real thing is working and reunifying things. E.g, get back to the "front end" and make the final version of that on top of the core pieces of tech we are working on now.


4. For some time on main branch there was a plugin for sending Gameplay
Messages (Looked like Event/Message Bus), but it was removed. Any
plans to restore it? With the Game Features/Modular Gameplay plugins,
having a generic Event Bus Dispatcher would be extremely useful.

> I think you are referring to the GameplayMessages plugin. This will probably come back at some point - the API isn't really finalized yet and the author didn't mean for it to be public yet. I agree it should be useful for modular gameplay design. But it's not really my area so I don't have much more information. 


5. I've been playing recently with async fixed physics and the results
are promising, though if there is going to be NP update in the future
I will probably just play around and wait, since to get it working I
still need to get entire engine into fixed tick and on the other hand
I try to keep physics at 33ms. Which does not make for a good
experience if everything is at 30 fps (:.

I have noticed there was some work on Async
CharacterMovementComponent, but not sure if this will be using Network
Prediction, or it is a separate effort?

Since I noticed it, I also went ahead and tried to implement my custom
async movement at fixed tick rate, which worked okay, but on top of it
I also needed to add a separate update for interpolation. The setup
was to run simulation tick on separate worker threads at fixed 33ms
update, do calculations, save result, and interpolate it at the game
thread to match current frame rate. Not perfect, but it got the job
done.

My question is, if this is something that might be easier to set up in
the future, as there is just quite a bit of boilerplate code to write,
(the interpolation part) and it's not particularly efficient to
interpolate each moving object individually.

The async stuff is really interesting, because it would allow you to
really run game simulation at fixed update rate (which would make
fixed thread unneeded) and have more predictable results. Is this
something that is intended going forward, or more of a benefit to
select systems? As far as I remember actor transforms are not updated
async and blueprints are not entirely thread safe. In other words is
it something that is planned to be supported at more of a framework
level or something that each game has to solve on it's own?

> Async CharacterMovementComponent
>
> This is basically an early prototype/experiment of porting CMC as it is to the physics thread. I don't view it as the future of CMC yet, but it could evolve into that. Right now there is no networking support so it's not something I would really follow. The people doing it are mostly concerned with measuring input latency that this system would add and how that could be mitigated.
>
> I still need to get entire engine into fixed tick and on the other hand I try to keep physics at 33ms. Which does not make for a good experience if everything is at 30 fps (:.
>
> The async stuff is really interesting, because it would allow you to really run game simulation at fixed update rate (which would make fixed thread unneeded) 
>
> Yes. The goal here is that with async physics enabled, you can run the engine at variable tick rate while the physics and "core" gameplay simulations can run at the fixed rate (such as character movement, vehicles, GAS, etc).
>
> These are the cvars that need to be set to enable this now: (I think you've figured this out)  
> `p.DefaultAsyncDt=0.03333`  
> `p.RewindCaptureNumFrames=64`
>
> Chaos does provide interpolation for the physics state (e.g, the transforms that get pushed back to the UPrimitiveComponent and are visible to the game code). There is a cvar now, `p.AsyncInterpolationMultiplier`, which controls that if you want to look at it. You should see smooth continuous motion of physics bodies without having to write any extra code. 
>
> If you want to interpolate non physics state, it is still up to you to do that right now. The example would be like a cool-down that you want to update (tick) on the async physics thread but see smooth continuous interpolation on the game thread so that every render frame the cool-down visualization is updated. We will get to this eventually but don't have examples yet.
>
> there is just quite a bit of boilerplate code to write,
>
> Yeah, so that has been a big general problem with the system up until now. We want to provide an interface that experienced programmers can use to maximize performance and safety (the ability to write gameplay code that "just works" predictively without tons of hazards and things you could-do-but-better-not). So something like CharacterMoverment might do a bunch of custom stuff to maximize its performance - e.g, writing templated code and doing batch updating, going wide, breaking the update loop into distinct phases etc. We want to provide a good "low level" interface into the async thread and rollback systems for this use case. And in this case too - it's still reasonable that the character movement system itself is extendable in its own way. For example providing a way to blueprint a custom movement mode and providing a blueprint API that is thread safe.
>
> But we recognize this is not acceptable for simpler gameplay objects that don't really need their own "system". Something more inline with Unreal is what is needed. E.g, using the reflection system, having general blueprint support, etc. There are examples of blueprints being used on other threads (see BlueprintThreadSafe keyword and what the animation system has been working towards). So I think there will be some form of this one day. But again, we aren't there yet.
>
> I realize you were just asking about interpolation but that is the general answer: right now we have you do everything manually like NetSerialize, ShouldReconcile, Interpolate, etc but eventually we'll have a way that is like "if you want to just use the reflection system, you don't have to manually write this stuff". We just don't want to *force* everyone to use the reflection system since that imposes other limitations that we think we don't want to take on the lowest levels of the system. 
>
> And then just to tie this back to what I said earlier - right now we are really focused on getting a few very specific examples working and performant and then we will turn attention back to the front end and making things friendly to use and iterate on, reducing boilerplate, etc for everybody else to use. 

**[⬆ 回到目录](#目录)**

<a name="changelog"></a>
## 12. GAS Changelog

This is a list of notable changes (fixes, changes, and new features) to GAS compiled from the official Unreal Engine upgrade changelog and from undocumented changes that I've encountered. If you've found something that isn't listed here, please make an issue or pull request.

<a name="changelog-5.3"></a>
### 5.3
* Crash Fix: Fixed a crash when trying to apply Gameplay Cues after a seamless travel.
* Crash Fix: Fixed a crash caused by GlobalAbilityTaskCount when using Live Coding.
* Crash Fix: Fixed UAbilityTask::OnDestroy to not crash if called recursively for cases like UAbilityTask_StartAbilityState.
* Bug Fix: It is now safe to call `Super::ActivateAbility` in a child class. Previously, it would call `CommitAbility`.
* Bug Fix: Added support for properly replicating different types of FGameplayEffectContext.
* Bug Fix: FGameplayEffectContextHandle will now check if data is valid before retrieving "Actors".
* Bug Fix: Retain rotation for Gameplay Ability System Target Data LocationInfo.
* Bug Fix: Gameplay Ability System now stops searching for PC only if a valid PC is found.
* Bug Fix: Use existing GameplayCueParameters if it exists instead of default parameters object in RemoveGameplayCue_Internal.
* Bug Fix: GameplayAbilityWorldReticle now faces towards the source Actor instead of the TargetingActor.
* Bug Fix: Cache trigger event data if it was passed in with GiveAbilityAndActivateOnce and the ability list was locked.
* Bug Fix: Support has been added for the FInheritedGameplayTags to update its CombinedTags immediately rather than waiting until a Save.
* Bug Fix: Moved ShouldAbilityRespondToEvent from client-only code path to both server and client.
* Bug Fix: Fixed FAttributeSetInitterDiscreteLevels from not working in Cooked Builds due to Curve Simplification.
* Bug Fix: Set CurrentEventData in GameplayAbility.
* Bug Fix: Ensure MinimalReplicationTags are set up correctly before potentially executing callbacks.
* Bug Fix: Fixed ShouldAbilityRespondToEvent from not getting called on the instanced GameplayAbility.
* Bug Fix: Gameplay Cue Notify Actors executing on Child Actors no longer leak memory when gc.PendingKill is disabled.
* Bug Fix: Fixed an issue in GameplayCueManager where GameplayCueNotify_Actors could be 'lost' due to hash collisions.
* Bug Fix: WaitGameplayTagQuery will now respect its Query even if we have no Gameplay Tags on the Actor.
* Bug Fix: PostAttributeChange and AttributeValueChangeDelegates will now have the correct OldValue.
* Bug Fix: Fixed FGameplayTagQuery from not showing a proper Query Description if the struct was created by native code.
* Bug Fix: Ensure that the UAbilitySystemGlobals::InitGlobalData is called if the Ability System is in use. Previously if the user did not call it, the Gameplay Ability System did not function correctly.
* Bug Fix: Fixed issue when linking/unlinking anim layers from UGameplayAbility::EndAbility.
* Bug Fix: Updated Ability System Component function to check the Spec's ability pointer before use.
* New: Added a GameplayTagQuery field to FGameplayTagRequirements to enable more complex requirements to be specified.
* New: Introduced FGameplayEffectQuery::SourceAggregateTagQuery to augment SourceTagQuery.
* New: Extended the functonality to execute and cancel Gameplay Abilities & Gameplay Effects from a console command.
* New: Added the ability to perform an "Audit" on Gameplay Ability Blueprints that will show information on how they're developed and intended to be used.
* Change: OnAvatarSet is now called on the primary instance instead of the CDO for instanced per Actor Gameplay Abilities.
* Change: Allow both Activate Ability and Activate Ability From Event in the same Gameplay Ability Graph.
* Change: AnimTask_PlayMontageAndWait now has a toggle to allow Completed and Interrupted after a BlendOut event.
* Change: ModMagnitudeCalc wrapper functions have been declared const.
* Change: FGameplayTagQuery::Matches now returns false for empty queries.
* Change: Updated FGameplayAttribute::PostSerialize to mark the contained attribute as a searchable name.
* Change: Updated GetAbilitySystemComponent to default parameter to Self.
* Change: Marked functions as virtual in AbilityTask_WaitTargetData.
* Change: Removed unused function FGameplayAbilityTargetData::AddTargetDataToGameplayCueParameters.
* Change: Removed vestigial GameplayAbility::SetMovementSyncPoint.
* Change: Removed unused replication flag from Gameplay tasks & Ability system components.
* Change: Moved some gameplay effect functionality into optional components. All existing content will automatically update to use components during PostCDOCompiled, if necessary.

https://docs.unrealengine.com/5.3/en-US/unreal-engine-5.3-release-notes/

<a name="changelog-5.2"></a>
### 5.2
* Bug Fix: Fixed a crash in the `UAbilitySystemBlueprintLibrary::MakeSpecHandle` function.
* Bug Fix: Fixed logic in the Gameplay Ability System where a non-Controlled Pawn would be considered remote, even if it was spawned locally on the server (e.g. Vehicles).
* Bug Fix: Correctly set activation info on predicted instanced abilities that were rejected by the server.
* Bug Fix: Fixed a bug that would cause GameplayCues to get stuck on remote instances.
* Bug Fix: Fixed a memory stomp when chaining calls to WaitGameplayEvent.
* Bug Fix: Calling the AbilitySystemComponent `GetOwnedGameplayTags()` function in Blueprint no longer retains the previous call's return values when the same node is executed multiple times.
* Bug Fix: Fixed an issue with GameplayEffectContext replicating a reference to a dynamic object that would never be replicated.
  * This prevented GameplayEffect from calling `Owner->HandleDeferredGameplayCues(this)` as `bHasMoreUnmappedReferences` would always be true.
* New: The [Gameplay Targeting System](https://docs.unrealengine.com/en-US/gameplay-targeting-system-in-unreal-engine/) is a way to create data-driven targeting requests.
* New: Added custom serialization support for GameplayTag Queries.
* New: Added support for replicating derived FGameplayEffectContext types.
* New: Gameplay Attributes in assets are now registered as searchable names on save, allowing for references to attributes to be seen in the reference viewer.
* New: Added some basic unit tests for the AbilitySystemComponent.
* New: Gameplay Ability System Attributes now respect Core Redirects. This means you can now rename Attribute Sets and their Attributes in code and have them load properly in assets saved with the old names by adding redirect entries to DefaultEngine.ini.
* Change: Allow changing the evaluation channel of a Gameplay Effect Modifier from code.
* Change: Removed previously unused variable `FGameplayModifierInfo::Magnitude` from the Gameplay Abilities Plugin.
* Change: Removed the synchronization logic between the ability system component and Smart Object instance tags.

https://docs.unrealengine.com/5.2/en-US/unreal-engine-5.2-release-notes/

<a name="changelog-5.1"></a>
### 5.1
* Bug Fix: Fixed issue where replicated loose gameplay tags were not replicating to the owner.
* Bug Fix: Fixed AbilityTask bug where abilities could be blocked from timely garbage-collection.
* Bug Fix: Fixed an issue when a gameplay ability listening to activate based on a tag would fail to be activated. This would happen if there were more than one Gameplay Ability listening to this tag, and the first one in the list was invalid or didn't have authority to activate.
* Bug Fix: Fixed GameplayEffects that use Data Registries correctly from warning on load and improved the warning text.
* Bug Fix: Removed code from UGameplayAbility that was incorrectly only registering the last instanced ability with the Blueprint debugger for breakpoints.
* Bug Fix: Fixed Gameplay Ability System Ability getting stuck if EndAbility was called during the lock inside ApplyGameplayEffectSpecToTarget.
* New: Added support for Gameplay Effects to add blocked ability tags.
* New: Added WaitGameplayTagQuery nodes. One is based off of the UAbilityTask and the other is of UAbilityAsync. This node specifies a TagQuery, and will trigger its output pin when the query becomes true or false, based on configuration.
* New: Modified AbilityTask debugging in Console Variables to enable debug recording and printing to log by default in non-shipping builds (with ability to hotfix on/off as needed).
* New: You can now set AbilitySystem.AbilityTask.Debug.RecordingEnabled to 0 to disable, 1 to enable in non-shipping builds, and 2 to enable all builds (including shipping).
* New: You can use AbilitySystem.AbilityTask.Debug.AbilityTaskDebugPrintTopNResults to only print the top N results in log (to avoid log spam).
* New: STAT_AbilityTaskDebugRecording can be used to test perf impact from these on-by-default debugging changes.
* New: Added a debug command to filter GameplayCue events.
* New: Added new debug commandsAbilitySystem.DebugAbilityTags, AbilitySystem.DebugBlockedTags, andAbilitySystem.DebugAttribute to the Gameplay Ability System.
* New: Added a Blueprint function to get a debug string representation of a Gameplay Attribute.
* New: Added a new Gameplay Task resource overlap policy to cancel existing tasks.
* Change: Now Ability Tasks should make sure to call Super::OnDestroy only after they do anything needed to the Ability pointer, as it will be nulled out after calling it.
* Change: Converted FGameplayAbilitySpec/Def::SourceObject to be a weak reference.
* Change: Made a Ability System Component reference in the Ability Task a weak pointer so Garbage Collection can delete it.
* Change: Removed redundant enum EWaitGameplayTagQueryAsyncTriggerCondition.
* Change: GameplayTasksComponent and AbilitySystemComponent now support the registered subobject API.
* Change: Added better logging to indicate why Gameplay Abilities failed to be activated.
* Change: Removed AbilitySystem.Debug.NextTarget and PrevTarget commands in favor of global HUD NextDebugTarget and PrevDebugTarget commands.

https://docs.unrealengine.com/5.1/en-US/unreal-engine-5.1-release-notes/

<a name="changelog-5.0"></a>
### 5.0

https://docs.unrealengine.com/5.0/en-US/unreal-engine-5.0-release-notes/

<a name="changelog-4.27"></a>
### 4.27
* Crash Fix: Fixed a root motion source issue where a networked client could crash when an Actor finishes executing an ability that uses a constant force root motion task with a strength-over-time modifier.
* Bug Fix: Fixed a regression in Editor loading time when using GameplayCues.
* Bug Fix: GameplayEffectsContainer's `SetActiveGameplayEffectLevel` method will no longer dirty FastArray if setting the same EffectLevel.
* Bug Fix: Fixed an edge case in GameplayEffect mixed replication mode where Actors not explicitly owned by the net connection but who utilize that connection from `GetNetConnection` will not received mixed replication updates.
* Bug Fix: Fixed an endless recursion occuring in GameplayAbility's class method `EndAbility` which was called by calling `EndAbility` again from `K2_OnEndAbility`.
* Bug Fix: GameplayTags Blueprint pins will no longer be silently cleared if they are loaded before tags are registered. They now work the same as GameplayTag variables, and the behavior for both can be changed with the ClearInvalidTags option in the Project Settings.
* Bug Fix: Improved thread safety of GameplayTag operations.
* New: Exposed SourceObject to GameplayAbility's `K2_CanActivateAbility` method.
* New: Native GameplayTags. Introducing a new `FNativeGameplayTag`, these make it possible to do one off native tags that are correctly registered and unregistered when the module is loaded and unloaded.
* New: Updated `GiveAbilityAndActivateOnce` to pass in FGameplayEventData parameter.
* New: Improved ScalableFloats in the GameplayAbilities plugin to support dynamic lookup of curve tables from the new Data Registry System. Added a ScalableFloat header for easier reuse of the generic struct outside the abilities plugin.
* New: Added code support for using the GameplayTag UI in other Editor customizations via GameplayTagsEditorModule.
* New: Modified UGameplayAbility's PreActivate method to optionally take in trigger event data.
* New: Added more support to filter GameplayTags in the Editor using a project-specific filter. `OnFilterGameplayTag` supplies the referencing property and the tag source, so you can filter tags based on what asset is requesting the tag.
* New: Added option to preserve the original captured SourceTags when GameplayEffectSpec's class method `SetContext` is called after initialization.
* New: Improved UI for registering GameplayTags from specific plugins. The new tag UI now lets you select a plugin location on disk for newly added GameplayTag sources.
* New: A new track has been added to Sequencer to allow for triggering notify states on Actors built using the GameplayAbiltiySystem. Like notifies, the GameplayCueTrack can utilize range-based events or trigger-based events.
* Change: Changed the GameplayCueInterface to pass GameplayCueParameters struct by reference.
* Optimization: Made several performance improvements to loading and regenerating the GameplayTag table were implemented so that this option would be optimized.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_27/

<a name="changelog-4.26"></a>
### 4.26
* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_26/

<a name="changelog-4.25.1"></a>
### 4.25.1
* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

<a name="changelog-4.25"></a>
### 4.25
* Fixed prediction of `RootMotionSource` `AbilityTasks`
* [`GAMEPLAYATTRIBUTE_REPNOTIFY()`](#concepts-as-attributes) now additionally takes in the old `Attribute` value. We must supply that as the optional parameter to our `OnRep` functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [`NetSecurityPolicy`](#concepts-ga-netsecuritypolicy) to `UGameplayAbility`.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GameplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GameplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behavior cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_25/

<a name="changelog-4.24"></a>
### 4.24
* Fixed blueprint node `Attribute` variables resetting to `None` on compile.
* Need to call [`UAbilitySystemGlobals::InitGlobalData()`](#concepts-asg-initglobaldata) to use [`TargetData`](#concepts-targeting-data) otherwise you will get `ScriptStructCache` errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a `GameplayTag` setter to a blueprint that didn't have the variable previously defined.
* `UGameplayAbility::MontageStop()` function now properly uses the `OverrideBlendOutTime` parameter.
* Fixed `GameplayTag` query variables on components not being modified when edited.
* Added the ability for `GameplayEffectExecutionCalculations` to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
  * Implementation basically enables `GameplayTag`-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
  * To use, an execution has to add a tag to the new member variable `ValidTransientAggregatorIdentifiers`; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted `GameplayTag` source. We no longer reset the source when adding restricted tags to make it easier to add several in a row. 
* `APawn::PossessedBy()` now sets the owner of the `Pawn` to the new `Controller`. Useful because [Mixed Replication Mode](#concepts-asc-rm) expects the owner of the `Pawn` to be the `Controller` if the `ASC` lives on the `Pawn`.
* Fixed bug with POD (Plain Old Data) in `FAttributeSetInitterDiscreteLevels`.

https://docs.unrealengine.com/en-US/WhatsNew/Builds/ReleaseNotes/4_24/

**[⬆ 回到目录](#目录)**

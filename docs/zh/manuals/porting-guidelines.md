---
title: 移植指南
brief: 本手册重点介绍了将游戏移植到新平台时需要考虑的一些事项
---

# 移植指南

将 Defold 游戏移植到新平台通常是一个比较简单的过程. 理论上只要在 *game.project* 文件里配置好就行了, 但是处于对平台充分利用的考量还是推荐针对目标平台进行适配. 本教程包含一些通用移植的最佳实践和一些平台相关的细节.


## 最佳实践

### 输入
确保游戏适配平台的输入法. 只要支持 [游戏手柄](/manuals/input-gamepads) 的平台就做好手柄支持! 确保游戏有一个暂停菜单 - 万一输入控制器突然断掉, 游戏有必要暂停!


### 本地化
本地化翻译游戏中的任何文本以及商店页面中的文本，因为这将对销售产生积极影响! 对于本地化, 确保玩家可以在游戏的不同语言之间轻松切换 (通过暂停菜单).


### 保存游戏进度

#### 在桌面设备, 手机和 web 游戏里保存进度
保存游戏状态可以使用 Defold API 函数 `sys.save(filename, data)` 然后使用 `sys.load(filename)` 加载. 可以使用 `sys.get_save_file(application_id, name)` 获取不同系统上文件保存的路径, 一般就是登录用户的 home 文件夹.

#### 在游戏主机里保存进度
用 `sys.get_save_file()` 和 `sys.save()` 能在大多数平台上顺利工作, 但是在游戏主机上推荐另一个方法. 游戏主机平台通常将用户与每个连接的手柄相关联, 这样当保存进度时, 成就和其他功能应与其各自的用户相关联.

游戏手柄输入事件将包含一个用户 id, 可用于将手柄的操作与控制台上的用户关联起来.

主机平台及其原生扩展会暴露相关 API 函数，以保存和加载与特定用户关联的数据. 在主机上使用这些 API 以实现保存和载入游戏.

主机平台的文件操作 API 通常都是异步的. 在给主机开发跨平台游戏时推荐把所有文件操作做成异步的, 不管目标是哪个平台. 例如:

```lua
local function save_game(data, user_id, cb)
	if console then
		local filename = "savegame"
		consoleapi.save(user_id, filename, data, cb)
	else
		local filename = sys.get_save_file("mygame", "savegame" .. user_id)
		local success = sys.save(filename, data)
		cb(success)
	end
end
```


### 性能
用真机进行调试! 必要的话查找性能瓶颈然后进行优化. 可以使用 [性能分析器](/manuals/profiling) 分析找出代码性能瓶颈.


### 屏幕分辨率
对于具有固定方向和屏幕分辨率的平台: 检查游戏能否在目标平台屏幕分辨率和长宽比上正常运行. 对于具有可变屏幕分辨率和长宽比的平台: 检查游戏是否适用于各种屏幕分辨率和长宽比. 考虑在渲染脚本和摄像机中使用什么样的 [视口映射](/manuals/render/#default-view-projection) 最好.

移动平台可以在 *game.project* 配置中锁定屏幕方向, 或者做好适配确保游戏在横向和纵向模式下都能运行.


### Nintendo Switch
集成目标平台代码 - 对于 Nintendo Switch 就有一个特殊的扩展包以及很多工具供用户使用.

Defold 的 Nintendo Switch 使用 Vulkan 作为图形后端 - 要使用 [Vulkan 图形后端](https://github.com/defold/extension-vulkan) 测试游戏.


### PlayStation®4
集成目标平台代码 - 对于 PlayStation®4 就有一个特殊的扩展包以及很多工具供用户使用.


### HTML5
在手机上玩网页游戏正越来越流行 - 当然前提是要确保游戏在手机浏览器可以流畅运行! 还要注意的是网页游戏要能快速载入! - 换句话说就是优化游戏体积. 同时还要考虑加载速度，以免造成不必要的玩家流失.


# XDUCraft MC Update

基于 NoneBot 2 与 OneBot v11 的 Minecraft 官方版本更新监控与群推送插件。

通过 APScheduler 定时轮询 Mojang 官方版本清单，检测正式版与快照版的发布动态，并向所有已订阅的群推送通知。

运行整套机器人请看 [FlyingPig278/XDUCraft-bot](https://github.com/FlyingPig278/XDUCraft-bot)。

## 安装与加载

本软件包未发布至公共 PyPI 仓库，请使用 Git 地址安装。

### 1. 安装核心包与插件

在现有的 NoneBot 2.4.x 环境中，先安装 Core 核心包，再安装本插件：

```bash
pip install "nonebot-plugin-xducraft-core[onebot] @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-core.git@v0.3.0"
pip install "nonebot-plugin-xducraft-mc-update @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-mc-update.git@v0.1.0"
```

### 2. 配置插件加载

在 NoneBot 项目的 pyproject.toml 中向现有 plugins 列表追加以下模块（请保留已有的其他插件和适配器）：

```toml
[tool.nonebot]
plugins = [
    "xducraft_config",
    "xducraft_mc_update",
]
```

## 启用与首次使用

插件安装后，全局总开关与各群的订阅状态默认处于关闭状态。

### 1. 开启插件

方式一：使用 Web 管理面板

如果配置了 [nonebot-plugin-xducraft-admin](https://github.com/FlyingPig278/nonebot-plugin-xducraft-admin)，访问管理面板（默认地址 http://127.0.0.1:8765）：
1. 在“插件”页面开启“MC 版本订阅”全局开关。
2. 在“群聊”页面选择对应群，开启本群的“MC 版本订阅”插件开关。

方式二：不使用 Web 管理面板

如果不运行管理面板，请参考 Core 文档中的[不使用网页时启用插件](https://github.com/FlyingPig278/nonebot-plugin-xducraft-core#不使用网页时启用插件)。通过一次性脚本调用 Core 配置接口，同时开启全局开关与目标群的插件开关。由于 Core 会在前置检查中拦截未在群中启用的插件消息，必须在 Core 中同时配置全局与群开关。

### 2. 群内订阅指令与权限控制

群内订阅指令仅限超级用户（SUPERUSER）执行。开启全局与本群开关后，超级用户可在群内发送以下指令管理订阅：

| 指令 | 别名 | 权限要求 | 说明 |
| :--- | :--- | :--- | :--- |
| `/MC更新 订阅` | `/mcup on` | 超级用户 | 开启本群 Minecraft 版本更新推送订阅 |
| `/MC更新 取消订阅` | `/mcup off` | 超级用户 | 取消本群 Minecraft 版本更新推送订阅 |

若安装了 [nonebot-plugin-xducraft-features](https://github.com/FlyingPig278/nonebot-plugin-xducraft-features) 插件，超级用户亦可在群内发送 `/功能 on MC 更新推送`（或 off）管理订阅。由于本插件仅提供一项功能，修改该功能状态会自动同步群内插件总开关。

订阅成功后，当 Mojang 官方发布新版本时，机器人将自动向已订阅群发送版本更新公告。

## 业务数据存储

- 群订阅列表与版本检查游标保存在 `<DATA_DIR>/plugins/mc_update/subscription.json` 文件中。该文件属于业务数据，由插件代码自行维护，不写入 Core 配置数据库。
- 如需自定义数据存放目录，请在启动 Python 进程前配置操作系统环境变量 `XDUCRAFT_DATA_DIR` 为绝对路径。

## 本地开发与测试

克隆本仓库后，可以在本地虚拟环境中安装开发依赖并运行测试：

```bash
pip install "nonebot-plugin-xducraft-core[onebot] @ git+https://github.com/FlyingPig278/nonebot-plugin-xducraft-core.git@v0.3.0"
pip install -e ".[dev]"
pytest
python -m build
```

## 许可证说明

本项目源码采用 MIT 许可证开源，详见 [LICENSE](LICENSE)。

Mojang 与 Minecraft 商标及版本数据版权归 Mojang AB / Microsoft 所有。

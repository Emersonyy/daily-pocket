# 每日口袋 Daily Pocket

面向在日本生活、学习人群的原生安卓记账应用。采用记事本式月度布局，支持日元换算人民币、日常预算管理，以及通过授权后的QQ通知自动记账。

**当前版本：1.15（versionCode 16） · Android 8.0 / API 26及以上**

## 下载与安装

[下载 DailyPocket-v1.15.apk](releases/DailyPocket-v1.15.apk)。如果GitHub显示文件预览页，点击下载原始文件。

将APK发送到安卓手机，按系统提示安装。已安装本项目旧版时先导出JSON备份，再覆盖安装，避免卸载或清除数据。此目录附带的APK使用原项目签名，可覆盖原版；自行构建的新签名APK无法覆盖原版。

APK校验值见 [SHA256SUMS.txt](releases/SHA256SUMS.txt)。本项目尚未上传至应用商店。

## 主要功能

- 月度记事本：固定费用区可折叠，按日期记录日常消费；支持新增、编辑、备注和删除。
- 人民币直接记入；日元通过Frankfurter每日参考汇率换算，保留每笔原金额和换算结果。汇率并非秒级实时行情。
- 每天75元日常预算，每周额外150元。额外额度手动计入，未计入时在下周一00:00自动结算；时间统一按日本时间。
- 周页区分含150元的整周计划余额与已生效余额；首页月余额只累计已到来的每日预算。固定费用不参与日常预算加减。
- 超市、饭等标签分组展示，按周和月汇总；流水可左滑备注、删除。
- QQ通知自动记账：纯数字默认日元，人民币/rmb后缀表示人民币；支持“超市288”“饭23.86rmb”。
- 本地存储、JSON导出及恢复；后台状态通知、监听恢复和诊断。

详细的预算、跨月规则、操作方式和版本历史见 [使用说明](docs/使用说明.md)。

## QQ自动记账与限制

需要用户在安卓系统中授予通知访问权限。当前目标通知昵称在 `QqRules.java` 中设为“夏日漱石”；本项目不登录QQ、不索取密码，也无法凭通知昵称验证QQ号码。开发者如需更换目标，请检查相关识别常量与界面文案。

只有QQ实际提供了可读的金额通知，才能尝试自动记账；不读取聊天历史。折叠通知与重复通知受到平台提供的信息限制，同额消息仍可能存在重复或漏记边界。后台运行受手机权限和系统策略影响，不承诺任何情况下永久连接。

开启步骤见 [后台记账启用与测试](docs/后台记账启用与测试.md)。当前未实现微信、支付宝等其他应用的自动读取。

## 代码结构

```text
app/src/main/       Android清单、Java源码和图标资源
tests/              纯Java计算与规则检查
build.py            无Gradle的APK构建脚本
run_tests.py        纯Java规则检查入口
docs/               使用说明、后台运行指南
releases/           可安装APK及SHA-256校验值
```

## 构建

准备Python 3、JDK 17、Android SDK Platform 35及Build Tools 35.0.0。此项目使用原生Java及自带构建脚本，不包含Gradle工程；请在终端运行构建命令。

```sh
python3 build.py \
  --jdk /path/to/jdk-17 \
  --platform /path/to/android-sdk/platforms/android-35/android.jar \
  --tools /path/to/android-sdk/build-tools/35.0.0 \
  --out build/apk \
  --keystore /private/path/daily-pocket.keystore
```

macOS的JDK路径一般需要指向`Contents/Home`。构建脚本会使用或创建别名`daily-pocket`的本地开发密钥，开发口令为`android`；这不是正式发布的密钥管理方案。请勿将生成的私钥提交到GitHub。本目录不含原项目签名私钥。

构建产物为`build/apk/DailyPocket.apk`。默认脚本按macOS/Linux工具名称执行；Windows环境需适配SDK工具的可执行文件后缀。

## 规则检查

```sh
python3 run_tests.py --jdk /path/to/jdk-17
```

当前共263项规则断言，覆盖金额识别、通知去重、分类合计、日/周/月预算、跨月额度、滑动方向及监听恢复策略。APK已通过编译和签名验证。纯Java检查不等于真实安卓界面的测试；尚未在开发环境执行实体手机或模拟器上的安装、触摸、QQ通知与后台生命周期集成测试。

## 数据与隐私

账本和通知处理诊断保存在手机本地；联网获取汇率只请求货币与日期。卸载应用或清除数据会删除本地账本，请定期备份。JSON导入会覆盖现有账本。

本仓库材料不包含个人账本、通知正文导出、聊天截图、签名私钥或本机SDK。后续提交前也请检查这些文件是否被意外加入。

## 上传到GitHub

在GitHub创建空仓库，把本文件夹内的内容上传到仓库根目录即可；保留`app`、`tests`、`docs`、`releases`目录结构。使用命令行时可在本目录执行：

```sh
git init
git add .
git commit -m "Initial release: Daily Pocket v1.15"
git branch -M main
git remote add origin <你的GitHub仓库地址>
git push -u origin main
```

发布者可在GitHub Releases中另外附上`releases/DailyPocket-v1.15.apk`。本目录未指定开源许可证；如需授权他人使用、修改或再发布源码，请由项目所有者选择并添加合适的LICENSE。

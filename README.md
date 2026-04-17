# 📦 墨鱼 CAD 工具箱 (MoYu CAD Toolbox)

![AutoCAD](https://img.shields.io/badge/AutoCAD-Compatible-blue)
![LISP](https://img.shields.io/badge/Language-AutoLISP-green)
![Status](https://img.shields.io/badge/Status-Active-success)
![License](https://img.shields.io/badge/License-MIT-yellow)

---

## 📖 项目简介 | Description

### 中文

本项目整理并收集了网络上的各类 CAD 插件，同时结合 AI 辅助开发了一部分实用工具。

由于本人编程经验有限，项目整体框架较多依赖 AI 生成，因此代码中可能存在一定程度的重复、冗余或结构不够精简的问题。

现将该工具箱开源分享，希望能够为有需要的用户提供帮助。同时也诚挚欢迎各位开发者参与优化与完善，让项目变得更加高效、稳定。

---

### English

This project is a collection of various CAD plugins gathered from the internet, along with additional tools generated with the help of AI.

As I am not very experienced in programming, much of the code structure was created by AI, which may lead to some duplicated or redundant code.

This repository is shared in the hope that it can be useful to others. Contributions, optimizations, and improvements from experienced developers are highly welcome!

---

## ✨ 功能特点 | Features

* 🔧 常用 CAD 工具整合
* 🤖 AI 辅助生成部分插件
* 🧩 模块化结构，便于扩展
* 🔍 支持工具搜索（工具箱版本）
* ⚡ 轻量、易集成

---

## 🖼️ 界面示意 | Preview

> （可在这里放截图或 GIF）

```
建议截图内容：
- 工具箱界面
- 搜索功能
- 功能按钮
```

---

## 🚀 安装方法 | Installation

### 方法一：直接加载（推荐）

1. 下载项目源码
2. 打开 CAD
3. 输入命令：

```
APPLOAD
```

4. 选择 `.lsp` 文件加载

---

### 方法二：启动自动加载

将 LISP 文件加入启动加载列表，实现自动加载。

---

## 🧠 使用方法 | Usage

在 CAD 命令行输入：

```
MYBOX
```

即可打开工具箱界面。

---

## ⚙️ 自定义工具 | Custom Tools

你可以通过修改以下配置来添加工具：

```lisp
(setq *MYTOOLS*
  '(
    ("绘图" ("直线" "圆"))
    ("修改" ("移动" "复制"))
  )
)
```

👉 添加新工具无需修改核心代码

---

## 🧩 功能扩展 | Extending

你可以在代码中绑定具体命令，例如：

```lisp
(cond
  ((= RES "直线") (command "LINE"))
  ((= RES "圆") (command "CIRCLE"))
)
```

---

## 🤝 贡献 | Contributing

欢迎任何形式的贡献：

* 🐛 提交 Issue
* 🔧 提交 Pull Request
* 💡 提出优化建议

---

### 开发建议

* 优化冗余代码结构
* 提高运行效率
* 改进 UI 交互
* 增强模块解耦

---

## ⚠️ 已知问题 | Known Issues

* 部分代码存在冗余（AI生成）
* UI 基于 DCL，扩展性有限
* 不同 CAD 版本兼容性需进一步验证

---

## 📌 未来计划 | Roadmap

* [ ] UI 升级（更现代界面）
* [ ] 分类分组优化
* [ ] 图标支持
* [ ] 命令搜索增强（拼音/模糊搜索）
* [ ] 插件化管理

---

## 📄 许可证 | License

本项目采用 MIT License 开源。

---

## ⭐ 支持项目 | Support

如果这个项目对你有帮助，欢迎点个 ⭐ 支持一下！

---

## 🙏 致谢 | Acknowledgements

* 感谢开源社区提供的 CAD 插件资源
* 感谢 AI 工具在代码生成中的帮助

---

## 📬 联系方式 | Contact

如有问题或建议，欢迎提交 Issue 或联系作者。

---

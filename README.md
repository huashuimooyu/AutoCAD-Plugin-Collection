# 📦 墨鱼 CAD 工具箱（MoYu CAD Toolbox）

一套面向 AutoCAD 的高效 LISP 插件工具集合，整合常用绘图、修改、标注与批处理功能，提供统一、便捷的操作体验。

---

# 📖 项目简介 | Description

## 中文

本项目整理并收集了网络上的各类 CAD 插件，同时结合 AI 辅助开发了一部分实用工具。

由于本人编程经验有限，项目整体框架较多依赖 AI 生成，因此代码中可能存在一定程度的重复、冗余或结构不够精简的问题。

现将该工具箱开源分享，希望能够为有需要的用户提供帮助，同时也欢迎开发者参与优化与改进，使项目更加高效稳定。

---

## English

This project is a collection of various CAD plugins gathered from the internet, along with additional tools generated with the help of AI.

As I am not very experienced in programming, much of the code structure was created with AI assistance, which may result in some redundancy or non-optimized structure.

This repository is shared in the hope that it can be useful to others. Contributions and improvements are welcome.

---

# ✨ 核心功能

## 🔧 基础绘图

* 直线 `MOYU_FF_YY`
* 矩形 `MOYU_R_YY`
* 椭圆 `MOYU_FTY_YY`
* 多段线 `MOYU_FDD_YY`
* 多边形 `MOYU_FDB_YY`

## ✏️ 修改工具

* 移动 `MOYU_W_YY`
* 旋转 `MOYU_RR_YY`
* 镜像 `MOYU_WR_YY`
* 偏移 `MOYU_Q_YY`
* 倒角 `MOYU_CF_YY`
* 等分 `MOYU_DV_YY`

## 🚀 高级工具

* 连续复制 `MOYU_CCC_YY`
* 复制旋转 `MOYU_CCX_YY`
* 区域删除 `MOYU_TRR_YY`
* 一键统计 `MOYU_TH_YY`
* 框选统计 `MOYU_KH_YY`
* 批量倒角 `MOYU_FPL_YY`

## 📝 标注工具

* 线性 / 连续标注 `MOYU_DA_YY / MOYU_DC_YY`
* 对齐标注 `MOYU_DXB_YY`
* 角度 / 半径 / 直径 `MOYU_DJD_YY / MOYU_DBJ_YY / MOYU_DZJ_YY`
* 弧长标注 `MOYU_DHC_YY`
* 多重引线 `MOYU_DZ_YY`

## 🧱 图块 & 文字

* 建块 / 炸块 `MOYU_BB_YY / MOYU_BX_YY`
* 图块统计 `MOYU_BTJ_YY`
* 改名 / 改基点 `MOYU_BGM_YY / MOYU_BGD_YY`
* 文字替换 `MOYU_TTH_YY`
* 批量改字宽 `MOYU_TZK_YY`

---

# 🚀 使用方法

## 启动工具箱

在 CAD 命令行输入：

```
MYBOX
```

---

## 安装方式

### 方法一（推荐）

1. 打开 AutoCAD
2. 输入：

```
APPLOAD
```

3. 加载 `.lsp` 文件

---

### 方法二（自动加载）

将 `.lsp` 加入启动组即可

---

# ⚙️ 快捷键设置

```
MOYU_DIY_YY
```

---

# 🛠️ 开发说明

## 添加工具

```lisp
(setq MYTOOLS
  '(
    ("绘图" ("直线" "圆"))
    ("修改" ("移动" "复制"))
  )
)
```

## 功能绑定

```lisp
(cond
  ((= RES "直线") (command "LINE"))
  ((= RES "圆") (command "CIRCLE"))
)
```

---

# ⚠️ 已知问题

* 部分代码存在冗余（AI生成）
* UI 基于 DCL，扩展性有限
* 多版本 CAD 兼容性未完全测试

---

# 🚀 未来计划

* UI 优化（更现代界面）
* 工具分类优化
* 拼音 / 模糊搜索
* 插件模块化结构
* 功能持续扩展

---

# 🤝 贡献

欢迎任何贡献：

* Issue 提交
* PR 合并
* 功能建议
* 文档优化

---

# 📄 License

MIT License

---

# ⭐ 支持

如果这个项目对你有帮助，欢迎点个 ⭐

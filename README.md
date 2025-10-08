# 仓库说明

本仓库包含多套课程幻灯片与讲义资料，主要基于 Slidev 构建，同时包含部分 Markdown/Org/HTML 文档与配套素材。

## 目录结构
- Lesson01/、Lesson02/、Lesson04/：基于 Slidev 的课程幻灯片项目
- Lesson03/：Emacs 讲稿（Org/HTML/图片）及一套备用 Slidev 幻灯片
- Lesson05/：Linux 启动流程讲义（Markdown）
- 方草地公开课Git101/：Git101 课程资料（Slidev 幻灯片、讲义草稿、演示素材）
- docs/：文档索引与仓库说明

注：为避免打破现有相对路径与媒体引用，本次整理未对既有目录进行移动，仅新增文档说明与索引。

## 快速开始（运行幻灯片）
- 需安装 Node.js ≥ 18 和 npm
- 进入对应 Lesson 或 Git101 幻灯片目录：
  ```bash
  cd Lesson01
  npm install
  npm run dev   # 默认在 http://localhost:3030 打开
  ```
- 构建或导出：
  ```bash
  npm run build    # 生成静态站点
  npm run export   # 导出 PDF（部分项目需要安装 playwright-chromium）
  ```

## 幻灯片入口
- Lesson01：slides.md（Linux 基础，第1课）
- Lesson02：slides.md（Linux 基础，第2课）
- Lesson03（备用）：slides_not_used/slides.md
- Lesson04：slides.md（Linux 基础，第4课）
- Git101：方草地公开课Git101/git101_slides/slides.md

## 讲义 / 文档
- Git101 讲义草稿：方草地公开课Git101/lecture_notes_draft.md
- Emacs 讲稿（Org/HTML）：Lesson03/slides.org、Lesson03/slides.html
- Linux 启动流程讲义：Lesson05/README.md

更多请见 docs/README.md。

## 许可协议
本仓库采用 MIT License，详见 LICENSE。

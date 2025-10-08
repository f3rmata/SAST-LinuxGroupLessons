# 文档索引与分类

本目录用于集中整理仓库中的非代码文档，提供导航与使用说明。

## 分类

- 课程讲义
  - Git101：方草地公开课Git101/lecture_notes_draft.md（含图片与演示素材）
  - Emacs 讲稿：Lesson03/slides.org、Lesson03/slides.html（配套图片在 Lesson03/images/）
  - Linux 启动流程：Lesson05/README.md

- 幻灯片项目（Slidev）
  - Lesson01：Lesson01/slides.md
  - Lesson02：Lesson02/slides.md
  - Lesson03（备用）：Lesson03/slides_not_used/slides.md
  - Lesson04：Lesson04/slides.md
  - Git101：方草地公开课Git101/git101_slides/slides.md

## 使用说明（Slidev）
- Node.js ≥ 18，npm
- 在对应目录执行：
  ```bash
  npm install
  npm run dev
  ```
- 导出 PDF（如需）：
  ```bash
  npm run export
  ```
  若导出失败，可安装：
  ```bash
  npm i -D playwright-chromium
  ```

## 贡献指南
- 新增讲义：优先放在对应 Lesson 目录或 docs/ 下，并在此文件补充索引。
- 幻灯片项目：遵循目录结构（slides.md、public/、components/ 等），在根 README 与此索引增加入口。
# vant-element-docs

基于 VuePress 2 构建的 [vant-element](https://github.com/fengluo/vant-element) 文档站。

## 开发

```bash
# 安装依赖
pnpm install

# 启动本地开发
pnpm dev

# 构建静态站点
pnpm build
```

## 项目结构

```
.vuepress/
├── config.ts          # VuePress 配置
├── client.ts          # 客户端增强
├── components/        # 全局组件
├── layouts/           # 布局组件
├── styles/            # 全局样式
└── public/            # 静态资源

components/            # 组件文档（每个组件一个 .md）
  ├── button.md
  ├── input.md
  └── ...

examples/              # 示例文档
guide/                 # 使用指南
```

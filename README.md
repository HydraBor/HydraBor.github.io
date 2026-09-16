# HydraBor.github.io
Algor's blog.

## 本地开发

使用 Node.js 24 LTS 和随附的 npm，发布时需要 Git。
依赖统一由 npm 管理，提交并使用 `package-lock.json`，避免混用 Yarn 锁文件。

```powershell
npm.cmd ci
npm.cmd run build
npm.cmd run server -- --ip 127.0.0.1
```

本地预览地址为 http://127.0.0.1:4000/。

## 依赖安全检查

```powershell
npm.cmd audit
```

更新依赖后执行 `npm.cmd run clean` 和 `npm.cmd run build`，检查页面，再提交依赖和锁文件的变更。

# TURBO-4 · 四冲程涡轮增压发动机三维拆解

一台用 **Three.js 实时生成**的直列四缸涡轮增压发动机，支持部件选择、径向拆解、参数查看与自由旋转缩放。场景设定在夜间空军基地停机坪，旁边停着一架程序化建模的 F-35A。

> 纯前端静态页面：无构建步骤、无后端、无外部模型与贴图、无网络依赖。

## 功能

- **10 个可独立选择的模块**：气缸盖、正时罩盖、进气歧管、涡轮增压器、水冷中冷器、燃油供给系统、前端轮系、发动机悬置、冷却管路、曲轴活塞连杆
- **径向拆解动画**：每个模块独立位移向量与延迟，`easeInOutQuint` 缓动，收拢可逆
- **悬停参数浮标 / 点选高亮**：线框选中框 + 轻自发光，不破坏金属材质
- **曲轴运转**：标准滑块曲柄运动学，四缸相位 0/π/π/0，转速可调
- **缸体透视**：选中曲轴时自动半透明，内部运动件可见
- **单独显示 / 自动旋转 / 标注开关 / 视角复位**
- **响应式**：桌面双栏面板、平板折叠面板、手机底部参数抽屉
- **键盘快捷键**：`E` 拆解、`R` 复位、`空格` 运转、`1–0` 选择模块、`Esc` 取消

## 文件结构

```
index.html             入口（多文件版，便于开发）
styles.css             深色科技风 UI
app.js                 全部建模、材质、交互与动画
vendor/three.min.js    Three.js r147（UMD，本地）
vendor/OrbitControls.js
standalone.html        单文件版（CSS/JS/Three 全部内联，适合单文件部署）
build-standalone.mjs   重新生成 standalone.html
.nojekyll              跳过 Jekyll 处理
```

## 本地运行

直接双击 `index.html` 即可（使用经典 `<script>`，`file://` 下可正常运行）。

如需本地服务器：

```bash
python -m http.server 8080
# 或 npx serve .
```

然后访问 <http://localhost:8080>。

## 部署到 GitHub Pages

> **重要**：`index.html` 依赖同目录下的 `styles.css`、`app.js` 和 `vendor/`。只上传 `index.html` 会出现「无样式、模型不显示」的空白页——页面现在会直接给出资源缺失提示。

**方式 A：部署整个目录（推荐）**

1. 新建仓库，把本目录全部文件推到 `main` 分支根目录：

   ```bash
   git init
   git add .
   git commit -m "feat: TURBO-4 engine exploded view"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/<仓库名>.git
   git push -u origin main
   ```

2. 仓库 **Settings → Pages → Build and deployment**
   - Source 选择 **Deploy from a branch**
   - Branch 选择 **main**，目录选择 **/ (root)**
   - 保存后等待 1 分钟左右

3. 访问 `https://<你的用户名>.github.io/<仓库名>/`

所有资源均为相对路径，部署在子路径下也能正常工作；仓库已包含 `.nojekyll`，避免 Jekyll 处理静态资源。

**方式 B：单文件部署**

若只能上传一个文件（或想避免路径问题），直接部署 `standalone.html`：把它改名为 `index.html` 后上传即可，CSS / JS / Three.js 已全部内联。

修改源码后重新生成：

```bash
node build-standalone.mjs
```

### 常见问题

| 现象 | 原因 | 处理 |
| --- | --- | --- |
| 页面只有文字、没有样式和 3D | 只上传了 `index.html`，缺少 `styles.css` / `app.js` / `vendor/` | 上传整个目录，或改用 `standalone.html` |
| 双击能开，线上白屏 | 资源路径被改写 / 放在子目录但未同步 | 保持目录结构，全部使用相对路径 |
| 页面提示「资源未加载」 | 同上，页面自检未通过 | 按提示补齐文件 |

> 若希望用 GitHub Actions 部署，把 Pages 的 Source 改为 **GitHub Actions**，选择官方的 “Static HTML” 模板即可，无需额外配置文件。

## 交互说明

| 操作 | 桌面 | 移动端 |
| --- | --- | --- |
| 旋转 | 左键拖拽 | 单指拖拽 |
| 缩放 | 滚轮 | 双指捏合 |
| 平移 | 右键拖拽 | 双指拖拽 |
| 选择零件 | 点击模型 / 左侧列表 | 点击模型 / 展开列表 |
| 复位视角 | 双击空白处 / 复位按钮 | 复位按钮 |

## 技术说明

- **Three.js r147 UMD**：选用仍提供 UMD 构建与 `examples/js` 的版本，保证 `file://` 双击可运行（ES Module 会被 CORS 拦截）。
- **色彩管理**：显式关闭 `ColorManagement.legacyMode`，否则 sRGB 色值会被当作线性值输出，金属材质整体发白。
- **全程序化资产**：车身、涡轮蜗壳、皮带、F-35 均由 `ExtrudeGeometry`、`Lathe/Tube`、放样（loft）与基础几何生成；环境光照由程序化 PMREM 影棚生成，无任何图片贴图。
- **性能**：约 650 个网格；按包围球半径自动裁剪阴影投射体（约 114 个），移动端降低像素比与阴影分辨率。

## 浏览器支持

Chrome / Edge / Safari / Firefox 最近两个大版本，需支持 WebGL 1.0 与 ES2017。

## 许可

仅供学习与作品展示使用；如需商用请自行确认 Three.js（MIT）之外的素材权利。

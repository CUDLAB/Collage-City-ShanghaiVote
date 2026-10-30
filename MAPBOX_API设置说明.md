# Mapbox API 与 3D 建筑设置说明

平台已支持 Mapbox GL JS。完成下面的一次性设置后，地图会自动使用：

- Mapbox Standard 浅色单色主题；
- Mapbox 在线矢量底图；
- 两个独立建筑图层：Mapbox 2D 建筑和 Mapbox Standard 3D 建筑；
- 原有的 cluster、consistency entropy、community 高亮与投票功能。

Mapbox 模式不会读取 `data/building_mvt` 中的本地建筑 PBF。该目录暂时保留，仅供未设置 Token 或 Mapbox 加载失败时的 Leaflet 后备模式使用。

## 1. 获取公开 Access Token

1. 登录 [Mapbox Account](https://account.mapbox.com/)。
2. 打开 **Access tokens** 页面。
3. 复制一个以 `pk.` 开头的 **Public token**。默认公开 Token 即可用于底图显示。
4. 不要将以 `sk.` 开头的 Secret token 填入网页文件。

Mapbox 官方 Token 说明：<https://docs.mapbox.com/help/getting-started/access-tokens/>

## 2. 安全配置 GitHub Pages Token

线上版本不要把 Token 直接写入仓库文件。请在 GitHub 仓库中设置 Actions Secret：

1. 打开仓库 **Settings → Secrets and variables → Actions**。
2. 新建或更新名为 `MAPBOX_PUBLIC_TOKEN` 的 Repository secret。
3. 将以 `pk.` 开头的 Mapbox Public token 填入 Secret。
4. 重新运行 GitHub Pages workflow，部署脚本会在发布时注入 Token。

仓库中的 `js/mapbox_config.js` 必须保持为空配置：

```js
accessToken: '',
```

本地测试如需 Token，可创建被 Git 忽略的 `js/mapbox_config.private.js`，不要提交该文件。发布站点为：

<https://cudlab.github.io/Collage-City-ShanghaiVote/>

## 3. 启动与确认

1. 双击 `start_map.bat`。
2. 页面右下角状态文字出现 **Mapbox**，说明 Mapbox 模式已经启用。
3. 点击 **2D Buildings Hidden** 可显示完整的 Mapbox 建筑平面图层。
4. 点击 **3D Buildings Hidden** 可独立开启 Mapbox Standard 原生三维建筑；地图会倾斜以呈现高度。
5. 两个图层可独立开关；关闭 3D 建筑后地图会恢复俯视。

如果已经打开过网页，请按 `Ctrl+F5` 强制刷新。

## 4. 可调整设置

所有常用参数均位于 `js/mapbox_config.js`：

| 参数 | 默认值 | 作用 |
|---|---:|---|
| `enabled` | `true` | 是否尝试启用 Mapbox |
| `style` | `mapbox://styles/mapbox/standard` | Mapbox Standard 样式；建议保持不变 |
| `theme` | `monochrome` | 单色主题 |
| `lightPreset` | `day` | 光照，可选 `day`、`dawn`、`dusk`、`night` |
| `buildings2DVisible` | `false` | 2D 建筑是否默认开启 |
| `buildings3DVisible` | `false` | 3D 建筑是否默认开启 |
| `buildingMinZoom` | `13` | 2D 建筑开始显示的缩放级别；Mapbox Streets 建筑数据最低为 13 |
| `buildingPitch` | `42` | 开启 3D 建筑后的地图倾斜角度 |
| `buildingColor` | `#c9cdd0` | 2D 建筑颜色 |
| `showPlaceLabels` | `true` | 是否显示地名 |
| `showRoadLabels` | `true` | 是否显示道路名称 |
| `showPointOfInterestLabels` | `false` | 是否显示 POI 标签 |
| `showTransitLabels` | `false` | 是否显示公共交通标签 |

浅色单色效果依赖 Mapbox Standard 的 `monochrome` 配置。如果改成自定义 Mapbox Studio 样式，部分主题或 3D 开关可能需要在 Studio 中另行配置。

2D 与 3D 不再自动渐变或互相切换。2D 图层直接绘制 Mapbox Streets 提供的全部可用建筑面，3D 图层使用 Mapbox Standard 的原生三维效果。Mapbox Streets 在 zoom 13 只提供较显著的建筑，zoom 16 及以上才包含全部可用建筑；这是 Mapbox 数据本身的缩放规则，不是平台主动抽样或删减。

## 5. 发布与安全

- 浏览器端使用 Public token 是 Mapbox 的正常接入方式；正式发布时请在 Mapbox 后台只允许 `https://cudlab.github.io/Collage-City-ShanghaiVote/*`。
- 不要在项目中保存 Secret token。
- 不要把 Public token 直接提交到 Git；线上发布只使用 `MAPBOX_PUBLIC_TOKEN` GitHub Actions Secret。
- Mapbox 底图和 3D 建筑均需要互联网连接，并受 Mapbox 账户用量与计费规则约束。
- 如果 Token 没有填写、格式不正确或 Mapbox GL JS CDN 无法加载，平台会自动使用原 Leaflet 地图；此时右下角不会显示 **Mapbox**。

Mapbox Standard 配置参考：<https://docs.mapbox.com/map-styles/reference/standard/>

Mapbox GL JS CDN 接入参考：<https://docs.mapbox.com/mapbox-gl-js/guides/get-started/use-with-cdn/>

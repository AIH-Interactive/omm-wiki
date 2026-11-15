# 当前检查点：四边形集合矩阵变换

2026-11-15：OMM dev 27858c6d707a2a27b5373a6b379e49888b262a0a已验证推送，alpha.12新增Particle:setQuadsTransform(Matrix4,Matrix3)，复用setQuads已复制顶点、原生包围盒/配额/清理；绝对变换、nil单位矩阵、RGBA保留，新setQuad/setQuads清除结果。独立JOML/实际顶点缓冲及2048顶点默认预算测试通过（更新37条Lua指令），既有粒子/材质回归和Fabric/NeoForge构建通过。游戏视觉MANUAL/PENDING。转换器共同图层消费者仍待接入，不将API测试等同完整特效完成。

Wiki在native-geometry新增小节和已测示例，基线更新27858c6d，生产构建通过。静态包D:/UserData/Desktop/Project/OMP/psf2figura/dist/omm-wiki-alpha.12-quads-transform-dist-20261115.zip：8961955bytes，686文件/194HTML，1186本地资源引用无缺失，根index，CRC PASS；SHA256=86324fd22166955ce0fa4d25d084548a4861a9196864cca9fd47042eaf5e164f。构建/包日志在转换器reports/p4-quads-transform-wiki-build.log、p4-quads-transform-wiki-package.log。本文与页面提交推送main；原有pnpm-workspace.yaml保留不提交，旧静态ZIP保留。主转换器仍P4，P5未开始，后续保持既定推送/静态包交付流程。

以下为较早记录。

# 当前检查点：通用材质深度比较选项

2026-11-15：OMM dev已推送546123a8db9a4ae68adeda910e0dbb9376bad850，alpha.12通用setShaderMaterial增加depth_test，默认true，与depth_write独立；GUI淡出保留比较并关闭写入。源配置到实际Figura ZIP原生pipeline契约、默认/关闭/独立写入/淡出测试及既有粒子/材质回归通过，Fabric/NeoForge构建通过；游戏视觉MANUAL/PENDING。

Wiki仅修改docs/scripting/native-geometry.md材质字段表、说明及示例。生产构建通过，静态包D:/UserData/Desktop/Project/OMP/psf2figura/dist/omm-wiki-alpha.12-depth-dist-20261115.zip已打包验证：8955218bytes，686文件，194HTML，1186本地资源引用无缺失，根index.html，CRC PASS；SHA256=81b06a72fb8dced335b3415b297cdbc2ddbc61838d2f148b5995adcb681bde3f。转换器日志reports/p4-depth-wiki-build.log、p4-depth-wiki-package.log。本文与页面提交推送main；原有pnpm-workspace.yaml保留不提交。后续仍保持mod验证推dev→wiki构建推main→静态ZIP交付流程，旧ZIP不覆盖。转换器P4仍在实施，P5未开始。

以下为较早记录。

# 当前检查点：alpha.12 版本同步

用户最终更正版本为1.0.0-alpha.12。OMM dev版本提交550a5f53f1bc0ead2f6b4a9b87e9e917d8d9af90已推送。Wiki共37处当前适用版本/对应版本标签同步alpha.12，保留历史功能正文；版本页面保留API实现基线573ec549。本文档与标签一同按既定流程提交推送main，原有pnpm-workspace.yaml不提交。

Docusaurus生产构建成功，生成新版静态包：D:/UserData/Desktop/Project/OMP/psf2figura/dist/omm-wiki-alpha.12-dist-20261115.zip。8953868bytes，194HTML/686文件，1186本地静态资源引用检查无缺失，CRC通过；SHA256=62246b6575ea1e07d0c541f18279ebdff8de96b499dc0b86907adc89ca0308a3。日志位于转换器reports/p4-alpha12-wiki-build.log、p4-alpha12-wiki-package.log。此前不含alpha.12标签的旧包保留，仅作为历史交付。

当前Wiki版本同步/静态包任务完成；主转换器任务P4拖尾等继续实施，模板仍待完成，实机/Mixin/游戏视觉MANUAL/PENDING。后续保持API推送dev、Wiki同步构建推送main、提供根目录可托管ZIP的流程。

以下为较早记录。

# 当前检查点：通用API文档与可托管静态包

2026-11-15：新增 docs/scripting/native-geometry.md，更新脚本入口、自定义粒子与版本基线。文档覆盖已验证的 compose4/sample4、蒙皮逆绑定、动画边界、shader material/time、setQuad/getQuad/setQuads，源码基线为OMM dev 573ec549。只改接口说明和入口，不升级其他历史页面版本。

文档提交44207c1347de55fccb7f36d5161652cef442f2a9已推送origin/main并核对远端一致。当前分支main；原有未跟踪pnpm-workspace.yaml保留。此交接记录随文档检查点补充提交。

已有依赖下直接node Docusaurus CLI生产构建成功；仅新页面执行Prettier，其余文件保留既有无关格式，git diff --check通过。没有安装/升级依赖。构建提示已有Browserslist数据和Docusaurus更新通知，不影响退出0。

静态dist：D:/UserData/Desktop/Project/OMP/psf2figura/dist/omm-wiki-dist-20261115.zip，根目录包含index.html/页面/assets，可直接静态托管；8957561bytes，686文件，194HTML，1186本地静态资源引用检查无缺失，ZIP CRC通过。SHA256：11571a24f03964bacd941c303bbe236b91d529c70ac12e98b47aa26ef66a529c。报告与日志位于转换器reports/p4-wiki-dist.json、p4-wiki-build.log、p4-wiki-dist-package.log。

用户要求后续保持：API验证推送dev→Wiki同步→生产构建/静态产物检查→提交Wiki main→提供根目录可托管ZIP。装饰品模板最终需覆盖全部适用新增API，含拖尾模板，贴图可SVG自绘转PNG并附SVG；模板尚未交付，由psf2figura任务继续完成。Wiki当前更新/构建/打包无剩余实施；真实Minecraft/模板视觉为MANUAL/PENDING。

以下为历史交接记录。

# Agent 交接记录

## 当前任务与状态

完成 OmniMorph 1.0.0-alpha.5 文档补充，包含新增 Lua API。当前无剩余实施任务。

## 已完成

- 新增 `docs/scripting/ysm-display.md`，记录玩家状态及 YSM 运行时状态查询、返回值和示例。
- 更新 `ysm-model.md` 的状态机查询，并补充 YSM 动画、Molang 和玩家状态同步说明。
- 更新入口及版本记录；参考 `omp-ref` 提交 `fda743a` 的源码和更新日志。

## 验证与限制

- 已运行 Prettier，保留既有文件的无关格式；`git diff --check` 通过。
- 直接运行本地 Docusaurus CLI 构建成功。`pnpm exec` 因依赖目录需要重新安装且非交互环境不能确认而失败；未清理或重装依赖。
- 真实 Minecraft 画面、声音及双客户端同步为 MANUAL/PENDING，未进行实际游戏验收。

## 工作区与后续

- 分支：`main`；提交及推送状态以 `git status -sb` 和 Git 日志为准。
- 计划书：`wiki 重构计划书.md`；本次任务未定义多阶段检查点。
- 后续仅在需要验收时使用真实客户端验证；继续遵守仓库范围控制、构建日志和 Qt 禁止 offscreen 等约束。

## 当前硬件加速文档撤销

移除硬件加速专用页面、设置段落和现行文档链接；保留模型库、缓存、YSM 等文档。原历史计划不抹除。主项目 revert_acceleration 正在分阶段撤销，当前本文档不构成生产撤销完成声明。构建验证结果待记录；用户授权验证后提交推送。实机验收 SKIPPED。

本次Wiki清理验证完成：Docusaurus生产构建成功，退出0，静态页面与搜索索引已生成；git diff --check通过。安装依赖命令因忽略构建脚本返回1，但依赖下载完成，直接运行本地CLI构建成功，未升级依赖。用户明确相关模组自身加速说明保留，docs/related_mods未修改。仅删除OmniMorph自有加速页面和入口，实机SKIPPED。准备提交推送main。

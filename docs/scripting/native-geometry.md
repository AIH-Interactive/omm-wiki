---
sidebar_position: 9
title: 通用矩阵、材质与粒子几何
---

# 通用矩阵、材质与粒子几何

> 适用版本：OmniMorph 1.0.0-alpha.12（Minecraft 26.1.2）
> 可用范围：普通 Figura 脚本，以及装饰环境允许访问的本实例对象。

这些接口直接操作矩阵、Figura 模型部件、动画和自定义粒子，不要求特定模型名称、装饰类别或转换器配置。纹理仍须属于当前 Avatar/装饰实例，原有脚本、粒子、面数和顶点预算继续生效。下面的模型示例使用作者自行创建的部件；它们不是 YSM 原始骨骼接口。

## TRS 矩阵

### `matrices.compose4(position, quaternion, scale)`

输入依次为 `Vector3`、`Vector4` 和 `Vector3`，返回 `Matrix4`。四元数顺序为 `(x,y,z,w)`，会归一化；分量必须有限，四元数不能为零。结果为平移 × 旋转 × 缩放。

```lua
local transform = matrices.compose4(vectors.vec3(0, 2, 0), vectors.vec4(0, 0, 0, 1), vectors.vec3(1, 2, 1))
```

### `matrices.sample4(rest, keyframes, time, channels)`

`rest` 为包含 `position={x,y,z}`、`quaternion={x,y,z,w}`、`scale={x,y,z}` 的表。`keyframes` 是按 `time` 非递减排列的数组，每项可以包含对应通道。`time` 必须有限；单位由作者统一决定。

通道掩码：位置 `1`、旋转 `2`、缩放 `4`，可以相加；`7` 表示全部通道，`0` 保留静止变换。位置/缩放使用线性插值，旋转使用归一化、最短路径四元数 slerp。首尾时间之外取端点，同一时间的重复键取最后一项。没有关键帧、未选中或缺失的通道保留静止值。循环和动作选择由脚本控制。

```lua
local rest = {
    position = { 0, 0, 0 },
    quaternion = { 0, 0, 0, 1 },
    scale = { 1, 1, 1 },
}
local frames = { {
    time = 0,
    position = { 0, 0, 0 },
}, {
    time = 1,
    position = { 0, 4, 0 },
}, {
    time = 2,
    position = { 0, 0, 0 },
} }
local floating = matrices.sample4(rest, frames, 0.5, 1)
```

可用于挂件浮动、摆动或作者自己的动画数据，不需要模型格式解析器。

## 蒙皮逆绑定与动画边界

`weightedPart:setInverseBindMatrix(boneName, matrix)` 为已有蒙皮网格中的指定骨骼设置逆绑定矩阵。矩阵会复制，必须是有限的仿射矩阵；`boneName` 使用该网格实际绑定的骨骼名称。它不会凭空建立骨骼或顶点权重。骨骼作者通过 `setMatrix` 设置的变换会进入蒙皮计算。

```lua
-- weightedPart 和 bone 来自作者自己的加权模型。
weightedPart:setInverseBindMatrix(bone:getName(), inverseBind)
bone:setMatrix(matrices.translate4(0, 1, 0))
```

`animation:setKeyframeBoundary("PRE")` / `"POST"` 选择精确关键帧时间的前/后边界语义，默认 `PRE`。仅在作者数据需要另一种边界语义时设置 `POST`，无需更改其他动画。

## 模型部件材质

`part:setShaderMaterial(definition)` 为 Figura 部件指定原生 shader 材质；传 `nil` 清除。`part:setMaterialTime(seconds)` 更新该材质的时间输入，单位为作者约定的秒。

| 字段              | 类型与含义                                          |
| ----------------- | --------------------------------------------------- |
| `vertex_shader`   | 顶点 shader 源码字符串                              |
| `fragment_shader` | 片元 shader 源码字符串                              |
| `textures`        | sampler 名到当前实例 `Texture` 对象的映射，允许空表 |
| `blend`           | `OPAQUE`、`ALPHA` 或 `ADDITIVE`，默认 `OPAQUE`      |
| `cull`            | 是否剔除背面，默认 `false`                          |
| `depth_test`      | 是否执行深度比较，默认 `true`；`false` 时始终通过   |
| `depth_write`     | 是否写入深度；默认在 `ALPHA` 时关闭，其他模式开启   |
| `lightmap`        | 是否启用原生光照贴图，默认 `false`                  |

`depth_test` 与 `depth_write` 独立：关闭比较不自动改变写入设置。GUI 骨骼淡出沿用材质的深度比较设置，同时关闭深度写入。此选项适用于任意模型/装饰的 shader 材质，随 alpha.12 的 OMM dev 提交 `546123a8` 提供；没有特定源格式或模型名称要求。

shader 使用原生矩阵 uniform 和顶点格式；新增属性为 `SecondaryUV`、`MaterialTime`、`MaterialOrigin`。第二 UV 不占用原生 overlay/lightmap 槽，作者法线会进入实际渲染与蒙皮法线计算。shader 源码、纹理和生命周期归当前实例所有；替换和清理仍由模组管理。

下面的最小材质将第二 UV 与时间显示为颜色，`part` 应指向作者自己的网格部件：

```lua
part:setShaderMaterial({
    vertex_shader = [[#version 330
layout(std140) uniform DynamicTransforms {
    mat4 ModelViewMat;
    vec4 ColorModulator;
    vec3 ModelOffset;
    mat4 TextureMat;
};
layout(std140) uniform Projection { mat4 ProjMat; };
in vec3 Position;
in vec2 SecondaryUV;
in float MaterialTime;
out vec3 materialColor;
void main() {
    gl_Position = ProjMat * ModelViewMat * vec4(Position, 1.0);
    materialColor = vec3(SecondaryUV, fract(MaterialTime));
}
]],
    fragment_shader = [[#version 330
in vec3 materialColor;
out vec4 outputColor;
void main() { outputColor = vec4(materialColor, 1.0); }
]],
    textures = {},
    blend = "OPAQUE",
    cull = false,
    depth_test = true,
    depth_write = true,
})
part:setMaterialTime(0.75)
```

## 粒子仿射四边形

`particle:setQuad(matrix)` 复制一个有限的仿射 `Matrix4`，将以粒子位置为中心的单位四边形映射为世界单位偏移。可以独立设置宽高、三轴旋转、错切和局部平移。该矩阵替代 Billboard 的尺寸/滚转，原生粒子位置和运动仍生效。`getQuad()` 返回副本，没有仿射几何时返回 `nil`。

```lua
-- texture 是当前实例纹理，position 是世界坐标。
local particle = particles:newCustomParticle(texture, position)
particle:setQuad(matrices.zRotation4(30):rightMultiply(matrices.scale4(0.4, 0.1, 1)))
```

固定四边形仍按一个粒子面计费，不需要拖尾配额。

## 粒子逐顶点四边形

`particle:setQuads(vertices)` 复制一个连续顶点数组，每四个顶点组成一个四边形，顺序由作者决定。可表达非平行四边形、渐变颜色或由脚本计算的带状几何。

| 顶点字段   | 类型与含义                                                         |
| ---------- | ------------------------------------------------------------------ |
| `position` | 必填 `Vector3`，相对粒子位置的世界单位偏移，必须可表示为有限 float |
| `uv`       | 必填 `Vector2`，分量范围 `0..1`，在当前图层/精灵单元内映射         |
| `color`    | 可选 `Vector4`，RGBA 范围 `0..1`，默认白色，与粒子及图层颜色相乘   |

```lua
particle:setQuads({ {
    position = vectors.vec3(-0.1, 0, 0),
    uv = vectors.vec2(0, 0),
    color = vectors.vec4(1, 0, 0, 1),
}, {
    position = vectors.vec3(0.1, 0, 0),
    uv = vectors.vec2(1, 0),
    color = vectors.vec4(1, 0, 0, 1),
}, {
    position = vectors.vec3(0.3, 1, 0),
    uv = vectors.vec2(1, 1),
    color = vectors.vec4(0, 0, 1, 0.2),
}, {
    position = vectors.vec3(-0.3, 1, 0),
    uv = vectors.vec2(0, 1),
    color = vectors.vec4(0, 0, 1, 0.2),
} })
```

输入表和向量之后的修改不会改变已提交的几何。空表提交零几何；`setQuad(nil)` 或 `setQuads(nil)` 清除显式几何并恢复普通 Billboard。最后一次非空设置在仿射四边形与顶点四边形之间切换；`RIBBON` 模式仍使用自己的轨迹几何。

顶点组必须完整且有界，所有图层都会计入共享的可变几何顶点额度和面复杂度。数量不足、非法分量或超出声明上限会报错；渲染时预算不足则不提交，并可通过 `getRenderError()` 读取错误。纹理、精灵图、光照、原生粒子所有权和清理流程保持一致。

接口本身不存储拖尾历史，也不解析任何引擎配置。装饰脚本可以自行组织历史点、宽度/颜色曲线，再将结果交给原生提交路径；基础需求可继续使用已有 [Ribbon](/scripting/ribbon)。

## 验证与模板

这些接口已通过原生 ZIP 加载、独立数学/实际顶点缓冲、预算及资源释放测试。材质也有独立 shader/窗口验证记录；完整游戏画面、真实宿主与 Mixin 环境仍需实机验收，不能用离线结果替代。

后续装饰品模板将分别覆盖适用的新能力，包含源码、参数与 API 覆盖表；需要贴图时采用自绘 SVG 转 PNG 并附 SVG。模板不依赖特定模型、转换器输入或参考引擎密钥。

实现基线：[OmniMorph dev `573ec549`](https://github.com/AIH-Interactive/omni-morph/commit/573ec54930e508f3e80ee2f1aaebb83f709aa7bc)。

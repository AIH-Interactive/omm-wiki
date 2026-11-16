---
sidebar_position: 12
title: 装饰相机与纹理
---

# 装饰相机与纹理

> 适用版本：OmniMorph 1.0.0-alpha.12
> 可用范围：装饰脚本；普通模型继续使用原有完整接口。

装饰脚本的 `client` 仅提供当前本机相机的只读查询；`textures` 仅操作当前装饰实例的纹理。它们复用已有客户端接口和实例资源生命周期，适用于任意模型、粒子和装饰，不依赖特定格式。

| 调用 | 返回 | 说明 |
| --- | --- | --- |
| `client:getCameraPos()` | `Vector3` | 当前本机相机世界位置，单位为方块；返回独立向量 |
| `client:getCameraRot()` | `Vector3` | 与普通 `client` 相同的相机角度，单位为度，保留 Figura 的历史角度约定；返回独立向量 |
| `textures:getTextures()` | 纹理数组 | 当前实例加载的模型纹理；返回新的数组，纹理句柄仍属于当前实例 |
| `textures:newTexture(name, width, height)` | `Texture` | 创建当前实例自定义纹理；沿用已有纹理大小、数量、指令预算及清理规则 |

相机查询需在已加载客户端的渲染上下文使用。它们只读取相机，不控制相机，也不返回其他玩家或模型。装饰 `client` 不包含服务器信息或系统时间查询；装饰 `textures` 不包含 `fromVanilla`、`read`、`copy` 或任意资源路径读取。

## 示例：依相机位置放置装饰粒子

```lua
local image = textures:newTexture("camera-marker", 1, 1)
image:fill(0, 0, 1, 1, 1, 1, 1, 1)
local marker

function events.render()
    if not player:isLoaded() then return end
    if not marker or not marker:isAlive() then
        marker = particles:newCustomParticle(image, player:getPos())
        marker:setLifetime(20):setEmissive(true)
    end
    local camera = client:getCameraPos()
    local position = player:getPos() + vectors.vec3(0, 1.5, 0)
    local direction = camera - position
    if direction:length() > 0 then
        marker:setPos(position + direction:normalize() * 0.1)
    end
end
```

读取已有贴图时可遍历 `textures:getTextures()` 并调用纹理的 `getName()` / `getDimensions()`。创建的句柄可交给本实例粒子与材质；实例卸载时由既有资源路径清理。第一/第三人称和预览均读取各自当前本机相机。

实际生成 ZIP 已在真实装饰沙箱中加载，并验证原生模型、穿戴者、相机与纹理接口可被注册的 TICK/RENDER 消费；游戏画面为 MANUAL/PENDING。

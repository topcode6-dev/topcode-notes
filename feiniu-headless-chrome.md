```
# 飞牛NAS Docker 部署无头 Chrome（Headless Chrome）
适用：飞牛NAS，Docker 方式启动，用于网页抓取、JS渲染、截图、自动化采集。

## 镜像选择
使用官方 chromium 无头镜像：
`chromedp/headless-shell`

## 启动命令
```bash
docker run -d \
  --name headless-chrome \
  -p 9222:9222 \
  --shm-size=2g \
  --restart unless-stopped \
  chromedp/headless-shell:latest
```

- 9222：Chrome DevTools Protocol 端口
- --shm-size=2g：防止渲染页面崩溃（飞牛 NAS 必加，默认共享内存太小）

## 验证是否正常

NAS 内 SSH 执行：

```
curl http://127.0.0.1:9222/json/version
```

正常会返回 JSON，包含浏览器版本、websocket 地址。

## 常用注意事项（飞牛 NAS）

1. 防火墙放行 9222 端口；内网使用建议不要直接对公网暴露 9222，有安全风险。
2. 资源占用：渲染网页吃 CPU + 内存，低配置 NAS 不要并发太多任务。
3. 如需中文渲染：该镜像自带基础字体，如果中文乱码，需要自建镜像补充中文字体。
4. 访问本地局域网其他服务：Docker 默认桥接模式可访问 NAS 内网。

## 停止 / 重启

```
docker stop headless-chrome
docker start headless-chrome
docker rm headless-chrome
```

## 配套说明

对接方式：使用 CDP（Chrome DevTools Protocol），Node/Python/Go 都可以连接这个 ws 地址控制浏览器。
适合场景：网页采集、JS 动态页面渲染、商品页面截图、自动化脚本。

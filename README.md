# Xray 多协议配置模板 (xray-multiprotocol-config)

`RWxray` —— 一份 [Xray-core](https://github.com/XTLS/Xray-core) 的 `config.json` 配置模板（多协议入站 + DNS + 路由）。

> 注意：这是**配置文件模板**，不是可执行脚本。需要替换占位符后供 Xray 加载。

## 内容结构

- **DNS**：`1.1.1.1` / `1.0.0.1`，`UseIP` 策略
- **入站（inbounds）**：
  - 多个 **Shadowsocks**（`chacha20-ietf-poly1305`，tag 可多个、协议可重复）
  - **MIXED**（http + socks 混合入站）
  - **VLESS + REALITY**
  - **VLESS 高级**（可改）
  - 入站均开启 sniffing（http / tls / quic）
- **出站（outbounds）**：`DIRECT`（freedom，UseIPv4v6）、`BLOCK`（blackhole）、`DNS-OUT`（dns）
- **路由（routing）**：`domainStrategy: IPIfNonMatch`，含 DNS(53) 与协议分流规则

## 使用方法

1. 把文件中的占位符替换为实际值：
   - `设置端口` → 各入站要监听的端口
   - 各 Shadowsocks 的 `clients`、VLESS 的 `id` / REALITY 密钥等按需填写
   - 入站 `tag` 名称不能重复（协议可以相同）
2. 重命名为 `config.json` 并放到 Xray 配置路径（如 `/usr/local/etc/xray/config.json`）
3. 校验并重启：

```bash
xray run -test -c /usr/local/etc/xray/config.json   # 校验配置
systemctl restart xray
```

## 注意事项

- 模板中的中文占位符（如 `设置端口`）**必须全部替换**，否则不是合法 JSON，Xray 无法启动
- REALITY / VLESS 需要补全密钥、`shortIds`、`serverNames` 等参数后才能正常使用
- 多个同类入站需使用**不同端口与不同 tag**

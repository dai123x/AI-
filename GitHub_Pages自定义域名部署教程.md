# GitHub Pages 自定义域名部署完整教程（从买域名开始）

> 目标：从零开始，拥有一个属于自己的域名，并把它绑定到 GitHub Pages 上，得到一个**免费、自带 HTTPS、自动续期证书**的网站。
> 全程不购买服务器，零服务器费用，适合 HTML/CSS/JS 静态网站。
> 本教程所有个人敏感信息（域名、用户名等）均已用占位符 `<...>` 代替。

---

## 第 0 章 开始之前：先看懂全流程

| 阶段 | 做什么 | 花费 | 耗时 |
| ---- | ---- | ---- | ---- |
| 1 | 购买域名 + 实名认证 | 几十元/年 | 支付即时，实名审核约几分钟~1个工作日 |
| 2 | 注册 GitHub 账号 | 免费 | 几分钟 |
| 3 | 创建 Pages 专用仓库 | 免费 | 几分钟 |
| 4 | （推荐）域名所有权验证（TXT） | 免费 | 可提前完成；不是 DNS 指向与站点发布的替代步骤 |
| 5 | 配置 DNS 解析（A / CNAME） | 免费 | 几分钟 + DNS 生效 10~30 分钟 |
| 6 | 上传首页文件 index.html | 免费 | 几分钟 |
| 7 | 绑定域名 + 开启 HTTPS | 免费 | 证书签发几分钟~几十分钟 |
| 8 | 访问测试 | 免费 | 即时 |

**两个概念先分清（很重要）：**
- **TXT 验证记录**：用于 GitHub 账户级域名所有权验证，建议开启以降低域名被他人接管的风险；它是可选的安全步骤。
- **A / CNAME 解析记录**：把域名指向 GitHub Pages，用于访问网站；DNS 配置、仓库 Custom domain 设置与 Pages 发布配置仍需匹配。
- TXT 验证不是网站发布或 DNS 解析的前置条件；如启用验证，只需添加 GitHub 实际提供的记录，切勿自造记录。

---

## 第 1 章 购买域名（以腾讯云为例）

> 域名服务商可选腾讯云、阿里云、华为云等，流程大同小异。本教程以腾讯云（DNSPod）为例，因为其解析面板免费且操作简单。

### 1.1 注册 / 登录腾讯云账号
1. 浏览器打开：`https://cloud.tencent.com`
2. 点右上角【注册】/【登录】，支持微信扫码或手机号注册。
3. 首次使用建议顺手完成账号实名（个人即可），后面买域名、做域名实名认证都要用。

### 1.2 进入域名注册页面
1. 登录后，在顶部搜索框输入：**域名注册**
2. 点击搜索结果进入【域名注册】产品页（也可直接在浏览器输入 `https://dnspod.cloud.tencent.com` 进入 DNS 控制台，在左侧找"域名注册"）。

### 1.3 搜索你想要的域名
1. 在搜索框输入你想要的名称（如 `myname`），下方勾选想买的后缀。
2. 后缀说明：
   - `.com`：最主流，约 60~80 元/年
   - `.cn`：国内常用，需实名
   - `.cloud`、`.top`、`.xyz` 等新后缀：便宜（有的几元~几十元/年），适合个人
3. 点【查询】，查看结果：
   - ✅ 可注册：显示价格，可加入购物车
   - ❌ 已被注册：换名称或换后缀再搜

### 1.4 加入购物车并下单
1. 选中心仪域名 → 点【加入购物车】。
2. 在购物车选择购买时长（建议先买 1 年，后续可续费）。
3. 点【去结算】。

### 1.5 填写域名信息（重要）
下单页需要填写域名所有者信息（用于 WHOIS 登记）：
- 所有者类型：**个人**
- 姓名、联系电话、邮箱：如实填写
- 建议顺便勾选"开启隐私保护"（如服务商提供），避免个人信息公开

### 1.6 支付
支持微信 / 支付宝 / 银行卡等，支付后域名立即生效。

### 1.7 完成域名实名认证（必须做！）
国内注册局规则：**域名必须实名认证**，未实名可能被暂停解析或锁定，网站打不开。

1. 进入【控制台 → 域名注册 → 我的域名】，找到你的域名。
2. 点击域名，或右侧【更多操作 → 实名认证】。
3. 按提示创建/使用**信息模板**：
   - 个人：上传身份证正反面照片，填写姓名、身份证号
   - 企业：上传营业执照
4. 提交后等待审核：通常几分钟 ~ 1 个工作日，状态变为"正常 / 已实名"即可。

> ⚠️ 实名审核期间不影响域名解析配置，但建议**尽早提交**，防止网站上线后被关停解析。

### 1.8 确认域名已接入解析服务
- 腾讯云购买的域名**默认使用 DNSPod（腾讯云解析）**，无需额外开通。
- 验证方法：控制台 → 域名注册 → 我的域名，域名列表里"解析"状态显示正常即可。
- 下一步所有"添加解析记录"的操作，都在 **DNSPod 解析控制台** 完成。

---

## 第 2 章 确定托管方式：为什么选 GitHub Pages

| 需求 | 选择 |
| ---- | ---- |
| 纯静态网页（HTML/CSS/JS） | **GitHub Pages（免费）** ✅ |
| 需要 PHP、数据库、后端程序 | 云服务器 + Nginx（付费） |

- GitHub Pages 优点：**免费**、**自带 HTTPS 且自动续期**、**不用管服务器**。
- 缺点：只支持静态文件，不支持后端代码。
- 本教程走 GitHub Pages 方案；服务器方案要点见附录 B。

---

## 第 3 章 注册 GitHub 账号

1. 打开：`https://github.com`
2. 点【Sign up】（注册）：
   - **Username**（用户名）：全网唯一，**记牢**，后面仓库名和 CNAME 都要用它
   - **Email**：你的邮箱
   - **Password**：设置密码
3. 按提示完成验证码与邮箱验证（GitHub 会给注册邮箱发验证邮件，点邮件里的链接确认）。
4. 注册完成后登录 GitHub。

> 💡 用户名建议：全小写字母 + 数字（如 `myname123`），避免大小写混淆。

---

## 第 4 章 创建 GitHub Pages 专用仓库

### 4.1 新建仓库
1. 登录 GitHub 后，点击页面左上角绿色按钮【New】（新建仓库）。
2. 填写仓库信息。

### 4.2 仓库命名（必须严格遵守）
- **Repository name** 必须一字不差填：**`<你的GitHub用户名>.github.io`**
- 示例：用户名是 `myname123`，仓库名就是 `myname123.github.io`
- 这是个人站点专用命名，写错就无法正常启用 Pages。

### 4.3 可见性
- 按当前 GitHub 账户/组织方案与仓库策略选择可见性；不要把“必须公开”当作所有方案的通用要求。创建前请核对 GitHub Pages 对该方案的支持条件。
- 如果选择私有仓库，确认 Pages 发布权限和发布站点的访问范围；发布页面可能是公开可访问的。不要把 API 密钥、密码、个人隐私或其他不应公开的内容提交到仓库。

### 4.4 创建
- 点底部绿色【Create repository】。
- 创建成功后，浏览器地址栏显示：`github.com/<用户名>/<用户名>.github.io`

---

## 第 5 章 域名所有权验证（TXT 记录）

> 可选安全步骤：将域名添加到 GitHub 账户的 Verified domains，并按页面提示发布 TXT 记录，可降低他人将你的域名绑定到其 Pages 站点的风险。它与仓库中设置 Custom domain、配置 DNS 和发布站点是不同步骤。

### 账户级域名验证（建议但非必需）
1. 点击右上角头像 → 【Settings】→ 左侧菜单找 **Pages**。
2. 找到 **Custom domains（Verified domains）** 区域，点 **Add a domain**。
3. 输入你的域名（如 `example.com`），点 **Add domain**。
4. GitHub 会生成两条验证信息（先记下来）：
   - **主机记录**：形如 `_github-pages-challenge-<你的GitHub用户名>`
   - **记录值**：一长串随机文本

设置站点域名仍需单独进入仓库【Settings】→【Pages】→ **Custom domain**，填入域名并保存。TXT 验证不会代替此操作；未启用账户级验证时，按仓库页面显示的提示继续配置即可。

### 5.1 去 DNSPod 添加 TXT 记录
1. 打开 `https://dnspod.cloud.tencent.com`，登录腾讯云账号。
2. 在域名列表点击你的域名（如 `example.com`），进入**记录管理**页面。
3. 点蓝色按钮【添加记录】，按下表填写：

| 字段 | 填写内容 |
| ---- | ---- |
| 主机记录 | `_github-pages-challenge-<你的GitHub用户名>`（**不要**加域名后缀） |
| 记录类型 | **TXT** |
| 线路类型 | 默认 |
| 记录值 | **完整复制** GitHub 给的那一长串随机文本，不删减、不换行 |
| TTL | 600（10 分钟，默认即可） |

4. 点【确认】保存。

### 5.2 等待生效
- DNS 生效一般 **5～15 分钟**，个别网络最长 1 小时。
- 生效期间不要反复修改记录。

### 5.3 回到 GitHub 验证
- 回到 GitHub 的域名验证页面，点验证按钮。
- 状态变为 **Verified / DNS check successful** 即通过。

> ✅ 常见状态解读：
> - `DNS Check in Progress`：正在检测，等待
> - `DNS check successful`：验证成功
> - `DNS check failed`：先核对 GitHub 提供的 TXT 值、主机记录、DNS 查询结果和记录是否生效；如仍失败，再对照 GitHub 当前错误提示排查。
> - **验证成功 ≠ 网站可访问**，必须继续第 6 章配置 A/CNAME 解析。

---

## 第 6 章 配置 DNS 解析记录（让域名指向 GitHub）

> 目标：访问 `example.com` 时，DNS 把你的请求转到 GitHub Pages 服务器。

### 6.1 打开解析面板
DNSPod 控制台 → 点你的域名 → 【记录管理】页面（就是刚才加 TXT 的地方），点【添加记录】。

### 6.2 添加 A 记录（主域名 @）
GitHub Pages 官方建议为主域名配置以下 4 个 IPv4 A 记录：

| 主机记录 | 类型 | 记录值 |
| ---- | ---- | ---- |
| `@` | A | `185.199.108.153` |
| `@` | A | `185.199.109.153` |
| `@` | A | `185.199.110.153` |
| `@` | A | `185.199.111.153` |

DNS 服务商对同一主机记录的数量/负载均衡限制会随套餐和线路配置变化。若控制台拒绝添加，请查看当前套餐说明或联系服务商确认；不要假设“任选两条”与官方推荐设置完全等价。

### 6.3 添加 CNAME 记录（www 子域名）
| 字段 | 填写内容 |
| ---- | ---- |
| 主机记录 | `www` |
| 记录类型 | **CNAME** |
| 记录值 | `<你的GitHub用户名>.github.io.` |
| TTL | 600 |

> DNS 控制台可能自动补全域名末尾的根点 `.`，也可能不显示它。按控制台的主机名格式填写 `<你的GitHub用户名>.github.io` 即可；不要把“是否显示末尾点”单独当作解析失败原因。

### 6.4 添加完成后的清单核对

| 类型 | 主机记录 | 记录值 | 用途 |
| ---- | ---- | ---- | ---- |
| TXT | `_github-pages-challenge-...`（仅在主动启用 Verified domains 时添加） | GitHub 提供的随机文本 | 可选的账户级所有权验证 |
| A | `@` | `185.199.108.153` | 主域名解析 |
| A | `@` | `185.199.109.153` | 主域名解析 |
| A | `@` | `185.199.110.153` | 主域名解析 |
| A | `@` | `185.199.111.153` | 主域名解析 |
| CNAME | `www` | `<用户名>.github.io` | www 子域名解析 |

- TXT 账户级验证是可选安全措施；只在主动启用 GitHub Verified domains 且 GitHub 给出记录时添加。不要自行添加来源不明的 `_dnsauth` 记录。
- A 记录数量需符合当前 DNS 套餐设置；核对控制台显示，不把记录总数或免费套餐限制写死。

### 6.5 等待生效
- 新增记录生效：一般 **10～30 分钟**，最长 24 小时（与网络运营商缓存有关）。
- DNS 尚未传播时，可能暂时无法访问；若等待后仍失败，应同时检查 DNS 记录、Pages 部署状态、域名绑定与 HTTPS 状态，不要仅凭 `ERR_EMPTY_RESPONSE` 就判断为“正常等待”。

---

## 第 7 章 上传首页文件 index.html

> 仓库根目录必须有 `index.html`，否则即使解析通了也会 404。

### 7.1 进入仓库
浏览器打开：`https://github.com/<你的GitHub用户名>/<你的GitHub用户名>.github.io`

### 7.2 新建文件
1. 点【Add file】→【Create new file】。
2. **文件名**填写：`index.html`（**必须全小写**，`Index.html`、`INDEX.HTML` 都不行）。
3. 必须放在**仓库根目录**（不要创建文件夹再放进去）。

### 7.3 粘贴页面代码
基础版（够用）：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>example.com</title>
</head>
<body>
<h1>🎉 GitHub Pages 部署成功！</h1>
<p>域名：example.com</p>
</body>
</html>
```

进阶版（好看一点）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>example.com</title>
<style>
  body { font-family: system-ui, sans-serif; display: flex; align-items: center; justify-content: center; min-height: 100vh; margin: 0; background: #f5f7fa; }
  .card { background: #fff; padding: 48px 64px; border-radius: 16px; box-shadow: 0 4px 24px rgba(0,0,0,.08); text-align: center; }
  h1 { color: #24292f; margin-bottom: 8px; }
  p { color: #57606a; }
</style>
</head>
<body>
  <div class="card">
    <h1>🎉 部署成功！</h1>
    <p>example.com 已接入 GitHub Pages</p>
  </div>
</body>
</html>
```

### 7.4 提交文件
1. 滚动到页面最底部（或点右上角【Commit changes】）。
2. 提交信息保持默认即可，点绿色【Commit new file】保存。

### 7.5 配置发布来源并检查部署状态
1. 打开仓库【Settings】→【Pages】。
2. 在【Build and deployment】中选择发布来源：
   - 分支发布：Source 选 **Deploy from a branch**，再选择包含站点文件的分支（常见为 `main`）和目录（仓库根目录选 `/(root)`），点【Save】。
   - 工作流发布：若项目使用 GitHub Actions 构建，在 Source 中选择 **GitHub Actions**，并按提示创建/选择工作流。
3. 打开仓库顶部【Actions】检查 Pages 部署工作流：绿色 ✅ 表示该次部署成功；红色 ❌ 时查看失败步骤与日志。
4. 部署需要时间；如仍是 404，先核对 Pages 发布分支/目录、构建产物和 Actions 状态。

---

## 第 8 章 绑定自定义域名 + 开启 HTTPS

1. 进入仓库 → 点顶部【Settings】→ 左侧或页面中找 **Pages**。
2. 在 **Custom domain** 输入框填入你的域名（如 `example.com`），点【Save】。
3. 页面出现提示 **DNS Check in Progress**：GitHub 正在后台检测你的 DNS 解析。
   - ⚠️ 此时**不要反复点 Save**，每次保存都会重新触发校验，拉长等待。
4. 检测通过后，**Enforce HTTPS**（强制 HTTPS）复选框从灰色变为可勾选。
5. **勾选 Enforce HTTPS**。GitHub 会自动为域名申请 Let's Encrypt 免费证书并**自动续期**。

> 状态解读：
> - `DNS Check in Progress`：等待，属正常
> - `DNS check successful`：校验通过，继续等证书
> - `DNS check failed`：核对域名绑定、GitHub 推荐的 A/CNAME 记录、冲突记录、TXT 验证状态及 DNS 是否已传播；根点是否显示并非唯一诊断依据。
> - `Enforce HTTPS` 灰色：证书未签发，等待即可；成功后自动解锁

> ⚠️ 关于 SSL 证书的常见误区：
> - 不需要自己在服务商申请证书！GitHub 自动完成。
> - 之前在腾讯云申请/下载的免费 SSL 证书，**此方案完全用不上**，可放着不用。
> - 证书签发一般几分钟 ~ 几十分钟；即使未签发，`http://` 也可能已能访问，只是浏览器提示"不安全"。

---

## 第 9 章 访问测试

### 9.1 无痕窗口访问
- 用浏览器**无痕/隐私窗口**访问（避免本地缓存干扰）：
  - `https://example.com`
  - `https://www.example.com`

### 9.2 命令行诊断（Windows CMD / Mac 终端）
```
nslookup example.com
```
- 正常结果：返回 `185.199.108.x` 或 `185.199.109.x` 等 GitHub IP。
- 返回其他 IP 或超时：DNS 缓存未更新，继续等待或清缓存。

### 9.3 快速自检清单
- [ ] 域名实名认证已通过
- [ ] 仓库名是 `<用户名>.github.io`
- [ ] 仓库根目录有 `index.html`（全小写）
- [ ] DNS 记录符合 GitHub 与当前 DNS 服务商的要求（添加 GitHub 实际生成的验证 TXT 记录）
- [ ] Custom domain 已绑定，DNS check successful
- [ ] Enforce HTTPS 已勾选
- [ ] Actions 部署工作流绿色成功

---

## 第 10 章 常见问题排查表

| 现象 | 可能原因 | 解决办法 |
| ---- | ---- | ---- |
| `ERR_EMPTY_RESPONSE` / 无法访问 | DNS 记录不匹配/未生效、Pages 尚未部署，或 Custom domain 配置有误 | 核对四条 apex A 记录（或相应 CNAME）、Pages 发布状态和仓库域名设置；TXT 验证本身不是网站可访问的必要条件 |
| 404 页面 | 仓库没有 index.html；文件放进了子文件夹；文件名大小写不对 | 在仓库根目录建 `index.html`（全小写） |
| 404 持续 | Pages 首次构建未完成 | 进【Actions】看部署，等绿色成功（2~5 分钟） |
| 404 且 Actions 成功 | 分支/目录配置不对 | Pages 来源选 `Deploy from a branch`，分支 `main`，目录 `/(root)` |
| Enforce HTTPS 灰色不可勾选 | DNS 校验或证书签发未完成 | 等待，勿反复保存；通过后自动解锁 |
| CNAME 解析失效 | 记录值、主机记录、DNS 传播或冲突记录有误 | 核对目标主机名和 DNS 查询结果；末尾根点是否显示取决于控制台，不应单独当作故障原因 |
| 添加 4 条 A 记录被拒绝 | 当前套餐/线路配置限制该主机记录的数量 | 查看服务商当前限制或联系支持；不要把某个套餐限制推广为普遍规则，也不要默认两条与四条设置完全等价 |
| DNS check failed | 验证、绑定、记录或传播状态不匹配 | 按 GitHub 提示核验 Custom domain、实际 A/CNAME/TXT 记录、冲突记录和 DNS 查询结果 |
| 访问提示"不安全" | 证书尚未签发完成 | 等待；勾选 Enforce HTTPS 后自动签发 |
| 国内访问慢/打不开 | 网络问题或 DNS 缓存 | 换网络测试、清 DNS 缓存（CMD：`ipconfig /flushdns`） |

---

## 附录 A：关于域名与 SSL 证书的常见疑问

1. **GitHub Pages 需要自己在服务商买证书吗？**
   不需要。GitHub 自动签发 Let's Encrypt 免费证书并自动续期，零成本零维护。

2. **买域名送证书吗？**
   腾讯云等平台提供免费的 DV 证书（如 90 天期、需手动续期），但**只适用于服务器方案**。GitHub Pages 方案用不到。

3. **域名实名认证没做会怎样？**
   国内规则：未实名可能被暂停解析、域名被锁定，网站打不开。务必尽早完成。

4. **免费域名能绑 GitHub Pages 吗？**
   可以，只要你能控制该域名的 DNS 解析（能添加 TXT/A/CNAME 记录即可）。

5. **以后想换绑定怎么办？**
   仓库 Settings → Pages → Custom domain 改为新域名即可；旧解析记录在 DNSPod 删除。

---

## 附录 B：服务器方案要点速查（升级备用）

若以后网站需要动态功能（PHP、数据库），改用云服务器方案：

1. 购买轻量应用服务器（推荐香港地域，**免备案**），记下**公网 IP**。
2. SSH 登录服务器：
   ```bash
   systemctl status nginx        # 检查 Nginx 是否安装/运行
   apt update && apt install nginx -y   # 未安装则安装
   systemctl start nginx && systemctl enable nginx
   ```
3. 校验配置：`nginx -t`（输出 `test is successful` 才正常）。
4. 控制台防火墙放行 80 / 443 端口（来源 `0.0.0.0/0`）。
5. 网站根目录放 `index.html` 测试页。
6. 按域名结构配置 DNS：根域名通常设置 `@` 的 A/AAAA 记录，子域名可设置 CNAME；记录数量和可用类型以 DNS 服务商当前套餐、服务器和域名配置为准。
7. 在服务器配置 HTTPS（上传服务商签发的证书 + 配置 Nginx 443 站点）。

---

## 附录 C：日常维护清单

- **域名续费**：到期前在服务商续费，避免域名过期被回收。
- **域名实名**：保持已完成状态，续费不影响。
- **修改网站**：直接改仓库里的文件并 Commit，GitHub 自动重新构建发布。
- **证书**：GitHub 自动续期，无需任何操作。
- **解析/验证**：若启用了 GitHub Verified domains，保留 GitHub 提供的 TXT 验证记录；若未启用，则无需添加或保留来源不明的 TXT 记录。

---

*本教程已去除域名、GitHub 用户名等全部个人敏感信息，通用步骤可直接复用。*
#（注：内容由AI生成）

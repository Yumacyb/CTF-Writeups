# PortSwigger SQL Injection Lab 1 通关报告

## 1. 靶场信息
- **平台**: PortSwigger Web Security Academy
- **关卡**: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
- **目标**: 利用漏洞绕过原本的过滤条件，提取出数据库中未发布的隐藏商品（如 "Dancing In The Dark"）。

## 2. 漏洞原理与 Payload 拆解
该漏洞存在于商品分类的 URL 参数中。后台原 SQL 语句推测为：
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

我构造的 Payload 为:"' OR 1=1--"
**原理拆解**：
- `'`：提前闭合原本的单引号。
- `OR 1=1`：构造一个永远为真的条件，强行让数据库返回所有记录。
- `--`：SQL 注释符，将后面的 `AND released = 1` 注释掉，从而解除隐藏限制。

## 3. 手工抓包验证过程
使用 Burp Suite 拦截请求，修改 category 参数并放行。
<img width="2560" height="1600" alt="sqli_lab1_burp" src="https://github.com/user-attachments/assets/3b4c4748-83fd-44a0-b195-f0f3e6d19a2c" />
*（如上图所示，将参数修改为 `Gifts'%20OR%201=1--` 后，服务器返回 200 OK。）*

## 4. 网页端通关验证
注入生效后，成功在网页端看到隐藏商品，获得红色 `Solved` 标志。
<img width="2560" height="1600" alt="sqli_lab1_solved" src="https://github.com/user-attachments/assets/5344437a-3c30-4d6b-80e5-fbe181858999" />

## 5. Python 自动化脚本转化
为提升效率，我使用 `requests` 库编写了自动化脚本（位于我的 `Security-Scripts` 仓库），实现了从发包到结果判定的自动化。
<img width="2560" height="1600" alt="lab1_python_success" src="https://github.com/user-attachments/assets/be4a99c4-da62-493e-a189-2d16f4ce51fd" />
*（观察点：终端输出 `[+] SQL injection successful`，证明自动化脚本成功判断出 "Dancing In The Dark" 关键字存在，完美复现了手工渗透过程。）*


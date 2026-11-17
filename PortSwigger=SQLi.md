# PortSwigger SQLi Lab 01: WHERE 子句中的 SQL 注入漏洞允许检索隐藏数据
## 1. 靶场信息
- **平台**: PortSwigger Web Security Academy
- **模块**: SQL Injection
- **实验**: Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data
## 2. 漏洞原理
该实验的“产品类别筛选器”存在 SQL 注入漏洞。应用程序在执行查询时没有对用户输入进行过滤，直接拼接到 SQL 语句中。原始推测的 SQL 查询语句为：
`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
## 3. 抓包与修改思路 (Payload)
在浏览器中点击 `Gifts` 分类，使用 Burp Suite 拦截 HTTP 请求。
将 `category` 参数的值从 `Gifts` 修改为：
**`'+OR+1=1--`**
**Payload 作用拆解**：
- `'`：闭合原 SQL 语句中的单引号。
- `OR 1=1`：构造一个永真条件，使 WHERE 子句始终为 True。
- `--`：SQL 注释符，将原语句后面的 `AND released = 1` 注释掉，从而绕过对未发布产品的限制。
## 4. 最终结果
修改后的数据包发送后，成功绕过了限制，页面回显了所有产品（包括未发布的产品），并弹出了 **Congratulations, you solved the lab!** 提示。
![通关截图](你的截图文件名.png) 
*(记得把截图保存在同级目录下并修改这里的文件名)*

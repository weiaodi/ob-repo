---
type: summary
theme: 编程与技术
source: 豆包/raw/编程与技术/后端、Java与数据库.md
created: 2026-10-16
---

# 后端、Java与数据库

> 这组对话以 Java Web（JSP / Servlet / JavaBean / Tomcat / WAR 部署）与数据库基础（一对多外键、分布式一致性、乐观锁）为主，另有大量课程作业式问答（MVC scope、getColumnName 索引从 1 开始、web.xml 位置、Tomcat 端口）。还夹带几个实际工程问题：后端新增字段如何兼容旧数据、企业微信群定时推送小程序卡片。

## 核心要点
- 一对多关系的实现原则：在「多」的一方表里加外键指向「一」的一方主键，可配置 ON DELETE CASCADE / ON UPDATE CASCADE 控制联动。
- 多人同时填同一行数据的冲突本质是分布式一致性问题，可选方案包括行级/页面锁定、版本控制、时间戳排序、预分配编辑区域、冲突合并界面等。
- 乐观锁的基本假设是并发冲突少：读取不加锁，写入时校验版本号或时间戳是否与读取时一致，不一致则重试或走冲突处理；高并发频繁冲突场景下重试代价高。
- 表新增字段后旧数据该字段为 NULL，若新代码假定它一定有值，就会出现 NPE 或逻辑走偏——旧数据不是被"弄坏"，而是新代码不认识 NULL。
- 三种兼容方案：数据库层设默认值（旧数据自动填充，代码零改动，但大表 ALTER 会锁表且默认值需有业务含义）、业务层判空兼容、数据迁移补值。
- JSP/Servlet 侧要点：`request.getParameter` 取表单参数（缺失返回 null）；`getColumnName(i)` 返回的是第 i+1 列名（JDBC 列索引从 1 开始）；`web.xml` 放在 WEB-INF 下且客户端不可直接访问。
- JavaBean/Tomcat：Bean 生命周期按用途选 scope（request 请求期 / page 页面 / session 会话 / application 应用级）；Tomcat 默认 HTTP 端口 8080，HTTPS 8443，AJP 8009，Shutdown 8005。
- WAR 部署：把 target 下的 WAR 放进 webapps 会自动解压部署，访问地址为 `http://IP:8080/WAR文件名`；默认欢迎页通常是 index.jsp，但以 web.xml 的 welcome-file-list 为准。
- Express 本身不带脚手架命令行，可用 express-generator 生成骨架，也可手动 npm init + npm install express 后直接 `node app.js` 启动。

## 涉及概念
[[乐观锁]]、[[分布式一致性]]、[[外键]]

## 值得回看的问答
- **分布式的一致性问题，多人填写表格冲突案例**：同一行被两人先后提交后结果不确定，本质是缺少并发控制；解决手段有锁定、版本控制、时间戳排序、权限预分配等。
- **追问：乐观锁能解决吗？**：能在冲突不频繁时有效——读不加锁、写时对比版本号/时间戳，不一致就重试；但高并发冲突频繁时会带来大量重试，影响性能与体验。
- **我的一个后端服务在这更新的时候需要新增一个字段，那保有的旧数据需要在业务逻辑里边做一个兼容吗？**：旧数据该字段是 NULL，新代码若不认识 NULL 就等于旧数据不可用；优先考虑数据库层设带业务含义的默认值，其次业务层判空兼容或数据迁移补值。
- **数据库一对多怎么实现的**：在"多"的一方加外键指向"一"的一方主键，可用 ON DELETE CASCADE 等约束控制联动；Prisma 等 ORM 也遵循同一原则。
- **解释一下MVC模式中application、page、session的作用和区别**：application 全应用共享、生命周期最长；page 仅当前页面有效；session 基于单个用户会话，跨页面保存用户数据。
- **结果集getcolumnname（int i）方法返回的是第几个字段名称**：返回第 i+1 个列名——JDBC 的列索引从 1 开始而不是 0。

## 相关原始资料
[[豆包/raw/编程与技术/后端、Java与数据库]]

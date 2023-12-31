<p align="center">
   基于React+Spring Boot+RabbitMQ+AIGC的智能BI数据可视化平台，并加入了AI聊天功能。
</p>

<p align="center">
   <a target="_blank" href="https://github.com/ttkican/Blog">
      <img src="https://img.shields.io/badge/JDK-8-green"/>
      <img src="https://img.shields.io/badge/springboot-2.7.0-green"/>
      <img src="https://img.shields.io/badge/react-green"/>
      <img src="https://img.shields.io/badge/mysql-5.7-green"/>
      <img src="https://img.shields.io/badge/mybatis--plus-3.5.2-green"/>
      <img src="https://img.shields.io/badge/redis-6.2.6-green"/>
      <img src="https://img.shields.io/badge/nacos-2.2.1-green"/>
   </a>
</p>


> 作者：[Zilong](https://github.com/Xiezilonger)

## 项目介绍
本项目是基于React+Spring Boot+RabbitMQ+AIGC的智能BI数据分析平台，并加入了AI聊天功能。

访问地址：xxxxx
> AIGC ：Artificial Intelligence Generation Content(AI 生成内容)

区别于传统的BI，数据分析者只需要导入最原始的数据集，输入想要进行分析的目标，就能利用AI自动生成一个符合要求的图表以及分析结论。此外，还会有图表管理、异步生成、AI对话等功能。只需输入分析目标、原始数据和原始问题，利用AI就能一键生成可视化图表、分析结论和问题解答，大幅降低人工数据分析成本。

**优势：** 让不会数据分析的用户也可以通过输入目标快速完成数据分析，大幅节约人力成本，将会用到 AI 接口生成分析结果

## 项目架构图
### 基础架构
基础架构：客户端输入分析诉求和原始数据，向业务后端发送请求。业务后端利用AI服务处理客户端数据，保持到数据库，并生成图表。处理后的数据由业务后端发送给AI服务，AI服务生成结果并返回给后端，最终将结果返回给客户端展示。

![](https://user-images.githubusercontent.com/94662685/248857523-deff2de3-c370-4a9a-9628-723ace5ab4b3.png)
### 优化项目架构-异步化处理
优化流程（异步化）：客户端输入分析诉求和原始数据，向业务后端发送请求。业务后端将请求事件放入消息队列，并为客户端生成取餐号，让要生成图表的客户端去排队，消息队列根据I服务负载情况，定期检查进度，如果AI服务还能处理更多的图表生成请求，就向任务处理模块发送消息。

任务处理模块调用AI服务处理客户端数据，AI 服务异步生成结果返回给后端并保存到数据库，当后端的AI工服务生成完毕后，可以通过向前端发送通知的方式，或者通过业务后端监控数据库中图表生成服务的状态，来确定生成结果是否可用。若生成结果可用，前端即可获取并处理相应的数据，最终将结果返回给客户端展示。在此期间，用户可以去做自己的事情。
![image](https://user-images.githubusercontent.com/94662685/248858431-6dbf41e0-adfe-40cf-94da-f3db6c73b69d.png)

## 项目技术栈和特点
### 后端
1. Spring Boot 2.7.2
2. Spring MVC
3. MyBatis + MyBatis Plus 数据访问（开启分页）
4. Spring Boot 调试工具和项目处理器
5. Spring AOP 切面编程
6. Spring Scheduler 定时任务
7. Spring 事务注解
8. Redis：Redisson限流控制
9. MyBatis-Plus 数据库访问结构
10. IDEA插件 MyBatisX ： 根据数据库表自动生成
11. **RabbitMQ：消息队列**
12. AI SDK：鱼聪明AI接口开发
13. JDK 线程池及异步化
15. Swagger + Knife4j 项目文档
16. Easy Excel：表格数据处理、Hutool工具库 、Apache Common Utils、Gson 解析库、Lombok 注解

### 前端
1. React 18
2. Umi 4 前端框架
3. Ant Design Pro 5.x 脚手架
4. Ant Design 组件库 
5. OpenAPI 代码生成：自动生成后端调用代码
6. EChart 图表生成

### 数据存储
- MySQL 数据库
- 阿里云 OSS 对象存储

### 项目特性
- Spring Session Redis 分布式登录
- 全局请求响应拦截器（记录日志）
- 全局异常处理器
- 自定义错误码
- 封装通用响应类
- Swagger + Knife4j 接口文档
- 自定义权限注解 + 全局校验
- 全局跨域处理
- 长整数丢失精度解决
- 多环境配置

### 项目功能
- 用户登录、注册、注销、更新、检索、权限管理、头像上传
- 图表创建、删除、查询、查看愿数据
- AI对话创建、删除、查询
- 订单创建、删除、修改、支付、查询
- 沙箱支付创建、查询
- 使用次数修改、新增、充值

### 单元测试
- JUnit5 单元测试、业务功能单元测试

## 项目功能
1. 用户登录
2. 智能分析（同步）。调用AI根据用户上传csv文件生成对应的 JSON 数据，并使用 ECharts图表 将分析结果可视化展示
3. 智能分析（异步）。使用了线程池异步生成图表，最后将线程池改造成使用 RabbitMQ消息队列 保证消息的可靠性，实现消息重试机制
4. 用户限流。本项目使用到令牌桶限流算法，使用Redisson实现简单且高效分布式限流，限制用户每秒只能调用一次数据分析接口，防止用户恶意占用系统资源
5. 调用AI进行数据分析，并控制AI的输出
6. 由于AIGC的输入 Token 限制，使用 Easy Excel 解析用户上传的 XLSX 表格数据文件并压缩为CSV，实测提高了20%的单次输入数据量、并节约了成本。
7. 后端自定义 Prompt 预设模板并封装用户输入的数据和分析诉求，通过对接 AIGC 接口生成可视化图表 JSON 配置和分析结论，返回给前端渲染。
### 新增功能
1. 新增用户注册功能
2. 新增用户调用次数表，用户注册同时完成插入次数表，当使用AI生成接口时分布式锁对使用次数锁定，次数扣减才释放锁
3. 新增删除图表/对话结果功能
4. 新增死信队列，将处理图表的队列绑定到死信队列上，保证消息可靠性，若分析失败，则进入死信，将补偿用户次数
5. 添加支付宝沙箱支付功能，下单充值AI使用次数完成，使用沙箱进行支付，完成支付功能，支付完成得到对应的使用次数
6. 新增延迟队列，当下单完成10分钟，还没有支付订单，则会标记为超时订单
7. 新增用户上传头像功能，使用阿里云对象存储OSS存储图片
8. 修改前端登录/注册界面
9. 优化前端显示，效果如下展示
10. 用户查看原始数据
11. 新增AI对话，用户提交问题，AI分析解答

## 项目截图
###登陆界面
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/001.png)
###注册界面
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/002.png)
###首页
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/003.png)
###智能分析（同步）
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/004.png)
###能分析（异步）
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/005.png)
###图表中心
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/006.png)
###猫咪AI
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/007.png)
###猫咪解答
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/008.png)
###个人信息
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/009.png)
###个人订单
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/010.png)
###订单付款
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/011.png)
###支付信息查询
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/012.png)
###用户管理（仅管理员可见）
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/013.png)
###添加用户（仅管理员可见）
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/014.png)
###所有用户图表集中管理（仅管理员可见）
![](https://living-10086.oss-cn-chengdu.aliyuncs.com/bi-oss/%E6%96%B0%E5%BB%BA%E6%96%87%E4%BB%B6%E5%A4%B9/015.png)

## 项目BUG
- AI生成的内容导致查询图表出现报错，由于AIGC得出的结果不一定是JSON数据，导致前端JSON数据格式解析失败
- 下单了无法支付，目前是使用沙箱环境，只有我手中的沙箱账号才能支付

## 后续项目改造
- 加入广告
- 将项目改造成微服务

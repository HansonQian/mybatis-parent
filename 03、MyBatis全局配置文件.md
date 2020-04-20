### 1、MyBatis全局配置文件

>  MyBatis 的配置文件包含了影响 MyBatis 行为甚深的设置（settings）和属性（properties）信息.

#### 1.1、全局配置文件结构

- configuration

  - properties 属性
  - setting 设置
  - typeAliases 类型命名
  - typeHandlers 类型处理器
  - objectFactory 对象工厂
  - plugins 插件
  - environment 环境
    - environment 环境变量
      - transactionManager 事务管理器
      - dataSource 数据源
  - databaseIdProvider 数据库厂商标识
  - mappers 映射器

  ​
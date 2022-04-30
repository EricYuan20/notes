# MyBatis-plus逻辑删除

1、配置文件application.properties设置逻辑删除

```properties
#逻辑删除
mybatis-plus.global-config.db-config.logic-delete-field=flag
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

2、在实体类上加上索引

```java
@ApiModelProperty(value = "逻辑删除 1（true）已删除， 0（false）未删除")
@TableLogic
private Integer isDeleted;
```


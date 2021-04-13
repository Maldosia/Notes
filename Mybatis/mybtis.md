# Mybatis

# 1. #和$有什么区别

- #{}是预编译处理，是占位符；${}是字符串替换，是拼接符
- Mybatis处理时，把sql中的#{}替换为?号，调用PreparedStatement来赋值
- Mybatis处理时，把sql中的${}替换为变量的值，调用Statement来赋值
- #{}的变量替换是在DBMS中，变量替换后，#{}对应的变量会自动加上单引号
- ${}的变量替换是在DBMS外，变量替换后，${}对应的变量不会自动家上单引号
- 使用#{}可以有效的防止SQL注入

# 2.常用标签

## 2.1 SQL语句标签

```xml
<select id="" parameterType="" resultMap=""></select>
<insert id="" parameterType=""></insert>
<update id="" parameterType=""></update>
<delete id="" parameterType=""></delete>

<!-- id 表示此段SQL执行语句的唯一标识，也是接口的方法名称(必须一致) -->
<!-- parameterType 表示该SQL语句中需要传入的参数，类型要与对应接口方法的类型一致 -->
<!-- resultMap 表示调用已定义的<resultMap>映射管理器的id值-->
<!-- resultType 表示匹配普通java类或自定义的pojo -->
```

## 2.2 SQL片段标签

```xml
<!-- 自定义标签 -->
<sql id=""></sql>

<!-- 引用标签 -->
<include refid=""></include>
```

## 2.3 映射管理器

```xml
<resultMap id="" type=""><!-- id 表示这个映射管理器的唯一标识，type 表示需要映射的实体类 -->
    <id column="" property="" /><!-- 结果集唯一的列，column是数据库中的列，property是实体属性 -->
    <relust column="" property="" />
</resultMap>

<association property= "" javaType=""></association><!-- 一对多关系 -->


<collection property="" ofType=""></collection><!-- 一对多关系 -->
```

## 2.4 常用动态语句标签

```xml
<where/><if/><set/><choose/><foreach/>
```

# 3. 嵌套查询和嵌套结果的区别

- 嵌套查询：多条SQL语句分开写并配置
- 嵌套结果：一条SQL语句关联查询并配置

实质效果是一样的，嵌套语句的查询会导致数据库访问次数不一定，进而有可能影响性能。

todo:没搞明白



# 4. 插入数据获得返回值

```xml
<settings>
    <setting name="useGeneratedKeys" value="true"></setting><!-- 只针对insert语句生效，默认为false -->
</settings>
```

# 5. 二级缓存




















































































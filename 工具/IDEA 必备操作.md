
# IDEA提示忽略大小写
如下图所示，将 Match case 不要勾选即可

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462605578-d7e7fcf3-fb60-43d7-9f3f-9fd70770f6a7.png)

# 鼠标滚动调节字体

当我们进行了设置之后，就可以使用 **ctrl + 鼠标滚轮**，进行字体的调节

将下面箭头所指的选项勾选即可

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462724959-c529b45d-3c5b-46bb-ac3d-822d5629abc6.png)

# 自动导包与排除无用的 import
打开下面的两个选项即可

第一个选项，表示会自动导包，当我们创建对象时，会自动在代码中添加 import

第二个选项，表示去除无用的 import ，当我们在代码中没有使用相关的对象时，IDEA会自动去掉该对象的 import

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462772021-32a23223-5ed4-4c22-9b62-45fd16234ca3.png)

# 设置多行标签页
直接看效果

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462881751-46833702-6367-43cd-99e4-14df7d413830.png)

当我们打开了很多的类时，IDEA 默认是在一行的，这样会显示不完全

只需勾选下面的选项即可实现多行显示标签页

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462893024-2ceef8bb-87de-45f0-85d5-511cae06e506.png)

# 查看当前方法被那些方法调用
首先我们双击选中当前的方法名

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462938843-027236ac-4eed-4503-9a3f-8b37ee3c9b13.png)

使用 ctrl + alt + h

此时可以看到当前的 isValidUrl 方法被 UrlUtil 中的 checkUrl 方法引用

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462963789-dc6f6926-045b-410e-8b0b-7e2df15f0257.png)

# 方法分隔符
使得方法与方法之间出现一条竖线， 方便进行代码预览，打开下面箭头所指即可

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739462983618-a596cb5b-fa2b-472e-b2f8-fc9213b0b4c2.png)

# JRebel and XRebel 插件
热部署插件，能够让我们在不重启项目的情况下跟新代码，此插件需要激活，请自行搜索

之后我们启动项目就可以使用 JRebel 进行启动（如下图），当我们更改了代码时，使用 ctrl + F9，即可实现项目的热跟新，而不需要重新启动项目

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739463115700-1c536f34-755e-4745-91ec-ab5fb8e38f5e.png)

但是，当我们跟新 mapper.xml 文件时， JRebel 并不会感知到，所以我们又要下载另一个插件

**JRebel mybatisPlus extension**，当我们跟新完 mapper.xml 后， 同样使用 ctrl + F9 即可实现项目跟新

# MybatisX 插件
当我们安装此插件后我们的 Mapper 接口会自动与相应的 mapper.xml 文件进行绑定

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739463128115-b06020b3-816a-46c8-8e64-750b59224da9.png)

点击图标旁边的小鸟即可实现 Mapper接口到 mapper.xml 的相互跳转

# MyBatis Log Free 插件
Mybatis 默认的日志输出是使用 ？来进行参数的占位的，如下

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739463223775-68e132c3-40ac-41b0-82b6-25809e5a4391.png)

那我们想要能直接执行的 sql 怎么办呢？

当我们安装完成 MyBatis Log Free 插件后，**打开它的 sql 记录功能然后启动项目**，即可获得完整的 sql 语句

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739463198247-9564001a-3631-4105-bdae-56a95482ae9d.png)

此时，在控制台会出现 MyBatis Log Free 的窗口，此时启动项目，感知到 sql 执行时，会将完整 sql 语句打印出来

![](https://cdn.nlark.com/yuque/0/2025/png/40756221/1739463212922-8f869156-f300-4173-b3be-de829b6e2763.png)





# MyBatisLogFormatter 插件
MyBatisLogFormatter，用于提取MyBatis日志并将参数填充到SQL中、压缩、美化SQL、一键打开Query Console。 在控制台中，你只需要选中MyBatis日志如下，

==>  Preparing: SELECT ID,phone,avatar,nickname,active,use_work_space_id,created_at,created_by,updated_at,updated_by FROM sys_user WHERE ID=?

==> Parameters: 1837781314710069250(Long)

然后点击鼠标右键弹出菜单选项，选择MyBatisLogFormatter，即可完成SQL格式化并复制到剪贴板，或者复制这段日志到右侧工具窗口进行格式化；选择MyBatisLogFormatter To SQL QueryConsole选项则可以实现MyBatisLog日志的sql提取、格式化并打开SQL Query Console



# Rainbow Brackets Lite 插件
鼠标点击后，对应的括号会高亮显示，方便我们进行代码阅读



# CamelCase 插件
进行单词大小写驼峰转换

Use Edit / Toggle Camelel Case or the default shortcut SHIFT + ALT + U



# POJO to JSON 插件
实体类转换为 json




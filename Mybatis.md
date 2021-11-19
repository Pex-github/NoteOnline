##### 5.2.4 Mybatis 是如何进⾏分⻚的？分⻚插件的原理是什么？ 

答：Mybatis 使⽤ RowBounds 对象进⾏分⻚，它是针对 ResultSet 结果集执⾏的内存分⻚，⽽ ⾮物理分⻚，可以在 sql 内直接书写带有物理分⻚的参数来完成物理分⻚功能，也可以使⽤分⻚ 插件来完成物理分⻚。 分⻚插件的基本原理是使⽤ Mybatis 提供的插件接⼝，实现⾃定义插件，在插件的拦截⽅法内拦 截待执⾏的 sql，然后重写 sql，根据 dialect ⽅⾔，添加对应的物理分⻚语句和物理分⻚参数。 举例： select _ from student ，拦截 sql 后重写为： select t._ from select \* from studentt limit 010 

##### 5.2.5 简述 Mybatis 的插件运⾏原理，以及如何编写⼀个插件。 

 答：Mybatis 仅可以编写针对 ParameterHandler 、 ResultSetHandler 、 StatementHandler 、 Executor 这 4 种接⼝的插件， Mybatis 使⽤ JDK 的动态代理，为需要拦截的接⼝⽣成代理对象以实现接⼝⽅法拦截功能，每当 执⾏这 4 种接⼝对象的⽅法时，就会进⼊拦截⽅法，具体就是 InvocationHandler 的 invoke() ⽅ 法，当然，只会拦截那些你指定需要拦截的⽅法。 实现 Mybatis 的 Interceptor 接⼝并复写 intercept() ⽅法，然后在给插件编写注解，指定要拦截哪 ⼀个接⼝的哪些⽅法即可，记住，别忘了在配置⽂件中配置你编写的插件。

##### 为什么说 Mybatis 是半⾃动 ORM 映射⼯具？它与全⾃动的 区别在哪⾥？

Hibernate 属于全⾃动 ORM 映射⼯具，使⽤ Hibernate 查询关联对象或者关联集合对象时， 可以根据对象关系模型直接获取，所以它是全⾃动的。⽽ Mybatis 在查询关联对象或关联集合对 象时，需要⼿动编写 sql 来完成，所以，称之为半⾃动 ORM 映射⼯具。


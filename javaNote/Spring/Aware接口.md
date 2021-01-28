Spring中有很多继承于`Aware`中的接口，这些接口到底是做什么用到的。
![Aware](./Aware.webp) 

`Aware`,翻译过来是知道的，已感知的，意识到的，所以这些接口从字面意思应该是能感知到所有`Aware`前面的含义。

先举个`BeanNameAware`的例子，实现`BeanNameAware`接口，可以让该Bean感知到自身的`BeanName`（对应Spring容器的BeanId属性）属性,举个例子：

- `BeanNameAware`接口的定义
```
public interface BeanNameAware extends Aware {
      void setBeanName(String name);
}
```

- 定义两个`User`,一个实现`BeanNameAware`,一个不实现。
```
import org.springframework.beans.factory.BeanNameAware;

public class User implements BeanNameAware{

    private String id;

    private String name;

    private String address;

    public void setBeanName(String beanName) {
        //ID保存BeanName的值
        id=beanName;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

```
import org.springframework.beans.factory.BeanNameAware;

public class User2 {

    private String id;

    private String name;

    private String address;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "User{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }
}
```

- 在Spring配置文件中初始化两个对象。
```
    <bean id="zhangsan"  class="com.github.jettyrun.springinterface.demo.aware.beannameaware.User">
        <property name="name" value="zhangsan"></property>
        <property name="address" value="火星"></property>
    </bean>

    <bean id="lisi"  class="com.github.jettyrun.springinterface.demo.aware.beannameaware.User2">
        <property name="name" value="lisi"></property>
        <property name="address" value="火星"></property>
    </bean>
```
- main方法测试一下`BeanNameAware`接口所起的作用。
```
 public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("classpath:application-beanaware.xml");

        User user=context.getBean(User.class);
        System.out.println(String.format("实现了BeanNameAware接口的信息BeanId=%s,所有信息=%s",user.getId(),user.toString()));

        User2 user2=context.getBean(User2.class);
        System.out.println(String.format("未实现BeanNameAware接口的信息BeanId=%s,所有信息=%s",user2.getId(),user2.toString()));
    }
```
- 运行结果
```
实现了BeanNameAware接口的信息BeanId=zhangsan,所有信息=User{id='zhangsan', name='zhangsan', address='火星'}
未实现BeanNameAware接口的信息BeanId=null,所有信息=User{id='null', name='lisi', address='火星'}
```
能够看到，我们在实现了`BeanNameAware`的 `User`中，获取到了Spring容器中的`BeanId`（对应`spring配置文件`中的`id`属性），而没有实现`BeanNameAware`的`User2`，则不能获取到Spring容器中的Id属性。

所以`BeanNameAware`接口是为了让自身`Bean`能够感知到，获取到自身在Spring容器中的id属性。

同理，其他的`Aware`接口也是为了能够感知到自身的一些属性。
比如实现了`ApplicationContextAware`接口的类，能够获取到`ApplicationContext`，实现了`BeanFactoryAware`接口的类，能够获取到`BeanFactory`对象。



**作者：一滴水的坚持  
链接：https://www.jianshu.com/p/c5c61c31080b  
来源：简书  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。**


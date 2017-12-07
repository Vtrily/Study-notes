title: Prepare For Interview  
tags: [Interview]  
date: 2017-11-11  

---
### Prepare For Interview ###

### Java Basic ###  





### 设计模式 ###  

##### 单例模式 #####  

单例类只能有一个实例  
单例类必须自己创建自己的唯一实例  
单例类必须给所有其他对象提供这一实例  

保证一个类仅有一个实例，并提供一个访问他的全局访问点  

单例模式的实现：  
1.懒汉式，线程不安全  

```
public class Singleton{  
    private static Singleton instance;  
    private Singleton(){

    }
    public static Singleton getinstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```
2.懒汉式，线程安全  
缺点：必须加锁synchronized才能保证单例，但加锁会影响效率  

```
public class Singleton{
    private static Singleton instance;
    private Singleton(){

    }
    public static synchronized Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }

}
```
3.饿汉式  
缺点：类加载时就初始化，浪费内存  
优点：没有加锁，执行效率会提高  
```
public class Singleton{
    private static Singleton instance = new Singleton();
    private Singleton(){

    }
    public static Singleton getInstance(){
        return instance;
    }
}
```

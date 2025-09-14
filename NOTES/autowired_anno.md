# @Autowired and `<context:annotation-config/>`

## What is the use of `<context:annotation-config/>`?

- If you are using annotations, you need to know about the corresponding **BeanPostProcessor** class name and configure it in the Spring bean configuration file.  
- It is very difficult to remember the **BeanPostProcessor** classes for annotations. To overcome this problem, Spring provides one tag called:  

  ```xml
  <context:annotation-config/>


* Whenever we declare the `<context:annotation-config/>` tag, the Spring IOC container automatically reads the **BeanPostProcessor** and **BeanFactoryPostProcessor** and injects the corresponding classes as beans.


## 1. What is Bean Autowiring?
If we don’t want to manage the dependency between beans manually, and instead want the IoC container to automatically identify and inject those dependencies, this is called **Bean autowiring**.

---

## 2. Two approaches
There are two main approaches to autowiring:
1. **Configuration-driven (XML) approach**  
2. **Annotation-driven approach**

---

## 3. Configuration-driven approach (XML)
> By default bean autowiring is not enabled. To enable it you add an `autowire="mode"` attribute to the `<bean>` tag.

**Step-by-step**
1. In your `<bean>` definition add the attribute `autowire="mode"`.
2. `mode` tells the IoC container how to identify the dependency.

**Possible modes**
- `byName` — matches a bean property name with a bean id in the context.  
- `byType` — matches a bean property type with a bean definition by type.  
- `constructor` — resolves dependencies using constructor argument matching.

**Example**
```xml
<!-- byName -->
<bean id="myService" class="com.example.MyService" autowire="byName" />

<!-- byType -->
<bean id="myService" class="com.example.MyService" autowire="byType" />

<!-- constructor -->
<bean id="myService" class="com.example.MyService" autowire="constructor" />
````

---

## 4. Annotation-driven approach

Unlike the XML approach, annotation-driven autowiring does not use modes — it injects dependencies by **type** using annotations.

**Step-by-step**

1. Annotate the target injection point with `@Autowired`.
2. Spring will resolve the dependency by type and inject the matching bean.

**Places you can declare `@Autowired`**

1. **Field (attribute) level**
   Spring uses reflection to inject the dependency directly into the field.

   ```java
   public class MyService {
       @Autowired
       private MyRepository repo;
   }
   ```
2. **Constructor level**

   ```java
   public class MyService {
       private final MyRepository repo;

       @Autowired
       public MyService(MyRepository repo) {
           this.repo = repo;
       }
   }
   ```
3. **Setter level**

   ```java
   public class MyService {
       private MyRepository repo;

       @Autowired
       public void setRepo(MyRepository repo) {
           this.repo = repo;
       }
   }
   ```
4. **Arbitrary method level**
   Annotate any method (commonly a public void method) and Spring will call it and supply the dependencies:

   ```java
   public class MyService {
       private MyRepository repo;

       @Autowired
       public void injectDependencies(MyRepository repo) {
           this.repo = repo;
       }
   }
   ```

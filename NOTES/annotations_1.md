# @AnnotationContextApplicationContext Annotation

---

- Generally, when I am using `ClassPathXmlApplicationContext`:
  - I need to enable annotations in Spring bean configuration file in **2 ways**:
    ```xml
    <context:annotation-config>
    <context:component-scan base-package=""/>
    ```
- If I use `AnnotationConfigApplicationContext`, then:
  - IOC container automatically registers the `BeanPostProcessor`.
  - No need to register explicitly.
- If I want to use annotations for the **entire application**, then:
  - Instantiate IOC with `AnnotationConfigApplicationContext`.
  - No need to write any configuration file to register `BeanPostProcessor`.

---

## ApplicationContext (I)

| ClassPathXmlApplicationContext | FileSystemApplicationContext | AnnotationConfigApplicationContext |
|--------------------------------|-----------------------------|-----------------------------------|
| C                              | C                           | C                                 |

---

## Example

### Class

```java
@Component
@Lazy
public class Employee {
    public Employee() {
        System.out.println("Employee object initialized");
    }
}
````

### Test

```java
public class AnnotationConfigApplicationContextTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext("com.spring.annotations.stereotype");
        Employee emp = context.getBean("employee", Employee.class);
    }
}
```

### Output

```
Employee object initialized
```



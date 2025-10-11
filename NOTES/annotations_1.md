# @AnnotationConfigApplicationContext Annotation

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


# @Value Annotation
--------------------

- When we are using `@Autowired` we can’t inject primitive values.  
- So `@Value` comes into picture.  

## Example

### Dependent class
```java
@Component
public class Chip {
    @Value("c123")
    private String chipId;

    @Value("Model101")
    private String modelNo;

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

### Target class

```java
@Component
public class Robot {

    private Chip chip;

    @Autowired
    public void setChip(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robot{" +
                "chip=" + chip +
                '}';
    }
}
```

### `application-value-context.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
    
    <context:component-scan base-package="com.spring.annotations.value"/>
</beans>
```

### Test Class

```java
public class ValueAnnotationTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application-value-context.xml");
        Robot robot = context.getBean("robot", Robot.class);
        System.out.println(robot);
    }
}
```

### Output

```
Robot{chip=Chip{chipId=c123, modelNo='Model101'}}
```


# PropertySource

## Hardcoded the values in @Value Annotation

- If we are writing primitive values directly inside class, then the value will become hard coded.
- So our class needs to read data from a properties file.

### Example

#### Dependent class

```java
@Component
public class Chip {
    @Value("#{props.chipId}")
    private String chipId;
    @Value("#{props.modelNo}")
    private String modelNo;

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

#### Target class

```java
@Component
public class Robot {

    private Chip chip;

    @Autowired
    public void setChip(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robot{" +
                "chip=" + chip +
                '}';
    }
}
```

#### application-value-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">

    <context:component-scan base-package="com.spring.annotations.value"/>
    <util:properties id="props" location="classpath:chip.properties"/>
</beans>
```

#### chip.properties

```properties
chipId=c123
modelNo=Model101
```

#### Test

```java
public class ValueAnnotationTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application-value-context.xml");
        Robot robot = context.getBean("robot", Robot.class);
        System.out.println(robot);
    }
}
```

#### Output

```
Robot{chip=Chip{chipId=c123, modelNo='Model101'}}
```

---

* Whenever we are reading the data from properties file we have to configure `util:properties` as a bean.
* To avoid `util:properties` tag in configuration file, Spring has provided annotation called `@PropertySource` from Spring 3.2 onwards.
* When I use `@PropertySource`, the whole properties file will be read and dumped into the IOC container as key-value pairs.
* By using EL Expression (Spring 4.3.5+), we can use the value, or alternatively, use the `Environment` object.

---

### Example Using @PropertySource

#### Dependent class

```java
@Component
@PropertySource("classpath:sensor.properties")
public class Sensor {

    @Value("${sensorId}")
    private String sensorId;
    @Value("${sensorType}")
    private String sensorType;

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

#### Target class

```java
@Component("automaticRobo")
public class AutomaticRobo {

    private Sensor sensor;

    @Autowired
    public void setSensor(Sensor sensor) {
        this.sensor = sensor;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "sensor=" + sensor +
                '}';
    }
}
```

#### Test

```java
public class PropertySourceAnnotationTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext("com.spring.annotations.value");
        AutomaticRobo automaticRobo = context.getBean("automaticRobo", AutomaticRobo.class);
        System.out.println(automaticRobo);
    }
}
```

#### Output

```
AutomaticRobo{sensor=Sensor{sensorId='sen123', sensorType='silicon s145'}}
```

---

* When we use `@PropertySource`, Spring IOC container creates an `Environment` object (like system properties) to hold key-value pairs. This is a feature added in Spring 4.x onwards.

### Example Using Environment

#### Dependent class

```java
@Component
@PropertySource("classpath:sensor.properties")
public class Sensor {

    Environment environment;

    //@Value("${sensorId}")
    @Value("#{environment.sensorId}")
    private String sensorId;
    //@Value("${sensorType}")
    @Value("#{environment.sensorType}")
    private String sensorType;

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

#### Target class

```java
@Component("automaticRobo")
public class AutomaticRobo {

    private Sensor sensor;

    @Autowired
    public void setSensor(Sensor sensor) {
        this.sensor = sensor;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "sensor=" + sensor +
                '}';
    }
}
```

#### Test

```java
public class PropertySourceAnnotationTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext("com.spring.annotations.value");
        AutomaticRobo automaticRobo = context.getBean("automaticRobo", AutomaticRobo.class);
        System.out.println(automaticRobo);
    }
}
```

#### Output

```
AutomaticRobo{sensor=Sensor{sensorId='sen123', sensorType='silicon s145'}}
```

# @DependsOn Annotation

## Spring Bean Configuration File

- By default, the IOC container creates the objects based on the declaration order (top-to-bottom) in the configuration approach (Spring bean configuration file).  
- By using `dependson tag`, we can manage the order of creation of objects.

## Annotation Approach

- In the annotation approach, we can’t control the order of object creation.

# Example

## Account Class

```java
@Component
@DependsOn("customer")
public class Account {

    public Account() {
        System.out.println("Account object initialization");
    }
}
````

## Customer Class

```java
@Component
public class Customer {

    public Customer() {
        System.out.println("Customer object initialization");
    }
}
```

## Test Class

```java
public class DependsOnTest {

    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext("com.spring.annotations.dependson");
    }
}
```

## Output

```
Customer object initialization
Account object initialization
```




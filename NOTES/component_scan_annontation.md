# @ComponentScan Annotation

## Example Program 1

### Toy.java
```java
// Source Code Present
@Component
public class Toy {

    private String toyName;
    private Motor motor;

    @Value("${toyName}")
    public void setToyName(String toyName) {
        this.toyName = toyName;
    }

    @Autowired
    public void setMotor(Motor motor) {
        this.motor = motor;
    }

    @Override
    public String toString() {
        return "Toy{" +
                "toyName='" + toyName + '\'' +
                ", motor=" + motor +
                '}';
    }
}
````

---

### Motor.java

```java
// No Source Code
public class Motor {

    private int capacity;
    private String type;

    public void setCapacity(int capacity) {
        this.capacity = capacity;
    }

    public void setType(String type) {
        this.type = type;
    }

    @Override
    public String toString() {
        return "Motor{" +
                "capacity=" + capacity +
                ", type='" + type + '\'' +
                '}';
    }
}
```

---

### JavaConfig.java

```java
@Configuration
@PropertySource("classpath:streo_java_config.properties")
@ComponentScan(basePackages = {"com.spring.annotations.javaconfig.streotype.combination"})
public class StreoJavaComConfig {

    @Autowired
    private Environment env;

    @Bean
    public Motor motor() {
        Motor motor = new Motor();
        motor.setCapacity(Integer.parseInt(env.getProperty("capacity")));
        motor.setType(env.getProperty("type"));
        return motor;
    }
}
```

---

### Test.java

```java
public class StreoTypeJavaConfigTest {
    public static void main(String[] args) {

        ApplicationContext context = new AnnotationConfigApplicationContext(StreoJavaComConfig.class);
        Toy toy = context.getBean("toy", Toy.class);
        System.out.println(toy);
    }
}
```

---

### streo_java_config.properties

```
capacity=20
type=aluminium
toyName=Train Toy
```

---

### Output

```
Toy{toyName='Train Toy', motor=Motor{capacity=20, type='aluminium'}}
```

# Example Program 2

## Motor.java
```java
// Source Code
@Component
public class Motor {

    private int capacity;
    private String type;

    @Value("${capacity}")
    public void setCapacity(int capacity) {
        this.capacity = capacity;
    }

    @Value("${type}")
    public void setType(String type) {
        this.type = type;
    }

    @Override
    public String toString() {
        return "Motor{" +
                "capacity=" + capacity +
                ", type='" + type + '\'' +
                '}';
    }
}
````

---

## Toy.java

```java
// No Source Code Present
public class Toy {

    private String toyName;
    private Motor motor;

    public void setToyName(String toyName) {
        this.toyName = toyName;
    }

    public void setMotor(Motor motor) {
        this.motor = motor;
    }

    @Override
    public String toString() {
        return "Toy{" +
                "toyName='" + toyName + '\'' +
                ", motor=" + motor +
                '}';
    }
}
```

---

## JavaConfig.java

```java
@Configuration
@PropertySource("classpath:streo_java_config.properties")
@ComponentScan(basePackages = {"com.spring.annotations.javaconfig.streotype.combination.reversed"})
public class StreoJavaComConfig {

    @Autowired
    private Environment env;

    @Bean
    public Toy toy(Motor motor) {
        Toy toy = new Toy();
        toy.setToyName(env.getProperty("toyName"));
        toy.setMotor(motor);
        return toy;
    }
}
```

---

## Test.java

```java
public class StreoTypeJavaConfigTest {
    public static void main(String[] args) {

        ApplicationContext context = new AnnotationConfigApplicationContext(StreoJavaComConfig.class);
        Toy toy = context.getBean("toy", Toy.class);
        System.out.println(toy);
    }
}
```

---

## streo_java_config.properties

```
capacity=20
type=aluminium
toyName=Train Toy
```

---

## Output

```
Toy{toyName='Train Toy', motor=Motor{capacity=20, type='aluminium'}}
```

## Note
- When you define the `@PropertySource` annotation in a Java configuration class, an internal `Environment` object is automatically created.  
- This `Environment` object can be accessed and used throughout the entire application.  
- There is no need to declare the `@PropertySource` annotation again in stereotype or component classes containing source code.  
- These classes can directly utilize the existing `Environment` object.
```



```
```

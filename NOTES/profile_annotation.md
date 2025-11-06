# @Profile Annotation

## Overview
The `@Profile` annotation in Spring is used to address challenges when switching between different environments (e.g., development, QA, production). It allows environment-specific configurations to be managed cleanly and efficiently.

---

## Problems Without `@Profile`
1. **Manual Configuration Changes:**  
   Each time you switch between environments, you must manually update the database configuration, making maintenance difficult.

2. **Risk of Errors:**  
   Frequent manual modifications increase the chances of mistakes in configuration changes.

---

## Solution Using `@Profile`
To solve the above issues, define separate configuration files such as:

- `dev_db.properties`  
- `qa_db.properties`

Pass both configurations to the IoC container, and specify which environment (`dev` or `qa`) to use.  
**Advantage:** This enables **parallel deployments** of the same application into multiple environments.

---

## Ways to Write Configuration Information
There are **three main approaches** to define configurations:

### 1. XML Configuration  
*Note: This approach is outdated and generally no longer recommended.*

---

### 2. Stereotype Annotations  
Use this approach **when you have access to the source code.**

Key annotations:
- `@Autowired` – For autowiring dependencies  
- `@Value` – For injecting primitive dependencies  
  - Avoid writing `@Value` on the class level; instead, use **Java Configuration with `@PropertySource`**  
- `@Lazy`, `@DependsOn`, `@PropertySource`, `@Scope`  
- `@PostConstruct`, `@PreDestroy`, `@Required`, `@Qualifier`

#### `@PropertySource`
If only property values change between environments, continue using stereotype annotations and define the `@PropertySource` annotation in a Java configuration class.

#### `@Lazy`, `@DependsOn`, `@Scope`
If these configurations differ between environments (e.g., `dev` to `qa`), you should use the **Java Configuration** approach.

#### `@PostConstruct`, `@PreDestroy`, `@Required`
These annotations remain constant regardless of the environment (`dev`, `qa`, `prod`).

---

### 3. Java Configuration Class
You cannot maintain all environment configurations (dev, QA, prod) within a single Java configuration class, as it becomes difficult to manage.  
Instead, create **separate Java configuration classes** for each environment:
- `DevJavaConfig`
- `QAJavaConfig`
- `ProdJavaConfig`

#### Avoiding Code Duplication
To prevent redundant code across these configuration classes:
1. Create an **abstract base configuration class** that contains common configuration code.
2. Let each environment-specific configuration class (`DevJavaConfig`, `QAJavaConfig`, etc.) **extend** this base configuration.

---

## Summary
The `@Profile` annotation provides a clean and scalable solution for managing multiple environments by:
- Eliminating manual configuration switching
- Reducing maintenance complexity
- Supporting parallel deployments
- Promoting modular and reusable configuration design

## Pictorial Representation - 1
<img width="1356" height="681" alt="Screenshot 2025-11-05 124642" src="https://github.com/user-attachments/assets/3f7ac4ec-90cc-4663-b273-5ba2a775abf6" />

## Pictorial Representation - 2

<img width="3163" height="4116" alt="SPRING-PROFILE" src="https://github.com/user-attachments/assets/b480b7f9-f064-408d-ada4-5035d5513116" />

## Pictorial Representation - 3
<img width="1907" height="324" alt="Screenshot 2025-11-06 100611" src="https://github.com/user-attachments/assets/f1860cc7-421a-42c0-81a3-71552150d47e" />

## Pictorial Representation - 4

<img width="1711" height="599" alt="Screenshot 2025-11-06 102448" src="https://github.com/user-attachments/assets/539d5249-821b-4385-b4a4-afda73fa096a" />


# Example Program with @Profile Annotation (Stereotype Annotation)

---

## Radio.java
```java
// Source Code Present
@Component
public class Radio {

    @Value("${bandType}")
    private String bandType;
    @Value("${frequencyRange}")
    private String frequencyRange;

    private ITuner tuner;

    @Autowired
    public void setTuner(ITuner tuner) {
        this.tuner = tuner;
    }

    public void on(int frequency) {
        tuner.tune(frequency);
        System.out.println("Listening...");
    }

    @Override
    public String toString() {
        return "Radio{" +
                "bandType='" + bandType + '\'' +
                ", frequencyRange=" + frequencyRange +
                ", tuner=" + tuner +
                '}';
    }
}
````

---

## ITuner.java

```java
public interface ITuner {
    public void tune(int frequency);
}
```

---

## AnalogTuner.java

```java
// Source Code Present
@Component
@Profile("dev")
public class AnalogTuner implements ITuner {
    @Override
    public void tune(int frequency) {
        System.out.println("tunning frequency " + frequency);
    }
}
```

---

## DigitalTuner.java

```java
// Digital Source Code Present
@Component
@Profile("qa")
public class DigitalTuner implements ITuner {
    @Override
    public void tune(int frequency) {
        System.out.println("tune frequency " + frequency);
    }
}
```

---

## JavaConfigDev.java

```java
@Configuration
@PropertySource("classpath:app_dev.properties")
@ComponentScan(basePackages = {"com.spring.annotations.streotype.profiles"})
@Profile("dev")
public class JavaConfigDev {
}
```

---

## JavaConfigQA.java

```java
@Configuration
@PropertySource("classpath:app_qa.properties")
@ComponentScan(basePackages = {"com.spring.annotations.streotype.profiles"})
@Profile("qa")
public class JavaConfigQA {
}
```

---

## app_dev.properties

```
bandType=AM
frequencyRange=100-200
```

---

## app_qa.properties

```
bandType=FM
frequencyRange=1000-2000
```

---

## ProfileTest.java

```java
public class ProfileTest {

    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext();
        ((AnnotationConfigApplicationContext) context).getEnvironment().setActiveProfiles("qa");
        ((AnnotationConfigApplicationContext) context).register(JavaConfigDev.class);
        ((AnnotationConfigApplicationContext) context).register(JavaConfigQA.class);
        // After register will not read the configuration, it just holds references of Java config classes
        ((AnnotationConfigApplicationContext) context).refresh(); // Once refresh happens, then only configuration is read

        Radio radio = context.getBean("radio", Radio.class);
        radio.on(98);
        System.out.println(radio);
    }
}
```

---

## Output

### Profile is `qa`

```
tune frequency 98
Listening...
Radio{bandType='FM', frequencyRange=1000-2000, tuner=com.spring.annotations.streotype.profiles.DigitalTuner@2a266d09}
```

---

### Profile is `dev`

```
tunning frequency 98
Listening...
Radio{bandType='AM', frequencyRange=100-200, tuner=com.spring.annotations.streotype.profiles.AnalogTuner@2a266d09}
```



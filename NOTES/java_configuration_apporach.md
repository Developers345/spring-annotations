# Stereotype Annotations — Limitations

When we use **stereotype annotations**, if we have the **source code of a class**, then in order to make it a bean definition, we can directly write stereotype annotations on the source code.

However, stereotype annotations have certain limitations that may need to be avoided in some cases:

---

### 1. Limited Bean Configuration
A given class can be configured as **one bean definition only**.  
If we want the same class to be instantiated as multiple objects using different values, this **cannot be achieved** using stereotype annotations.

---

### 2. Loss of Non-Invasiveness
The class loses **non-invasiveness**.  
Throughout the code, we end up writing **Spring Framework annotations**, so our code will not work without **Spring dependencies**.  
Even if we want to remove Spring Framework later, we would need to **rewrite almost all parts of the application**.

---

If you **don’t have source code**, or if you have faced the above challenges and want to **avoid XML-based configuration**, then use **Java Configuration Class**.

# Java Class

```java
@Component
class FuelTank {
    @Value("10")
    int capacity;

    @Value("diesel")
    String fuelType;

    // setter
}
````

---

# a-c.xml

```xml
<bean id="fuelTank" class="FuelTank" scope="singleton">
    <!-- For one bean definition level, the same set of values will be preserved.
         Due to that, by default, the scope is singleton -->
    <property name="capacity" value="10"/>
    <property name="fuelType" value="diesel"/>
</bean>

<bean id="fuelTank1" class="FuelTank">
    <property name="capacity" value="12"/>
    <property name="fuelType" value="petrol"/>
</bean>
```

---

# Test.java

```java
ApplicationContext context = new ClassPathXmlApplicationContext("a-c.xml");
FuelTank ft = context.getBean("fuelTank", FuelTank.class);
```

<img width="1293" height="631" alt="image" src="https://github.com/user-attachments/assets/4025414c-5f85-4ecc-9023-4f3c670968ba" />


# Java Configuration Approach Example

// Object instantiated with values that are placed in the IoC container

---

```java
/**
 * Instantiate the objects of your classes in methods
 * and return them to the IoC container,
 * asking it to keep the object as a bean definition in the container.
 */
````

---

## Java Configuration Class

```java
@Configuration
class JavaConfig {
  
    @Bean
    public Vehicle vehicle() {
        return new Vehicle();
    }
}
```

---

## Vehicle Java Class

```java
public class Vehicle {

    public void drive() {
        System.out.println("driving..");
    }
}
```

---

## Test Class

```java
public class JavaConfigTest {

    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(JavaConfig.class);
        Vehicle vehicle = context.getBean("vehicle", Vehicle.class);
        vehicle.drive();
    }
}
```

---

## Output

```
driving..
```

# Explanation

- First, the **IoC container** checks whether the given **Java configuration class** has the correct syntax.  
  It verifies:
  - The class is annotated with `@Configuration`.
  - All methods have the `public` access modifier.
  - Each method that returns a bean is annotated with `@Bean`.

---

- After validation, the container **scans each method one by one**, creates objects of all the classes configured with the `@Bean` annotation, and **places them into the IoC container** as a **map (key–value pair)**, where:
  - **Key:** method name (bean ID)  
  - **Value:** corresponding object instance

---

- By default, the **method name acts as the `bean_id`**.  
  You **cannot specify anything directly** in the `@Bean` annotation without parameters.

- If you want to provide a **custom bean ID**, you can use:

  ```java
  @Bean(name = "vehicle")
  

# Java Configuration Autowired Example


## Dependent Class

```java
// no source code
public class Rocket {

    public void ignite() {
        System.out.println("ignited");
    }
}
````


## Target Class

```java
// no source code
public class Launcher {

    private Rocket rocket;

    public void launch() {
        rocket.ignite();
        System.out.println("Launched");
    }

    public void setRocket(Rocket rocket) {
        this.rocket = rocket;
    }
}
```

---

## Java Configuration Class

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AutowiredJavaConfig {

    @Bean
    public Rocket rocket() {
        Rocket rocket = new Rocket();
        return rocket;
    }

    // @Bean(autowire = Autowire.BY_NAME)
    // -> This was deprecated from Spring 5.x

    // Instead of using the above, use method parameter injection.

    // @Autowired
    // -> Not possible here because @Autowired injects the dependency by type only,
    //    and depending on where it's written the @Autowired annotation, it behaves differently.

    @Bean
    public Launcher launcher(Rocket rocket) {
        Launcher launcher = new Launcher();

        /*
        //#1 Approach
        Rocket rocket = new Rocket();
        launcher.setRocket(rocket);

        //#2 Approach
        Rocket rocket = rocket();
        launcher.setRocket(rocket);

        The above two approaches are NOT recommended because
        in both cases, the developer manually manages the dependencies.
        Example: Launcher -> Rocket -> Signal
        (This complexity should be handled by the Spring container)
        */

        launcher.setRocket(rocket);
        return launcher;
    }
}
```

---

## Test Class

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class AutowiredJavaConfigTest {

    public static void main(String[] args) {
        ApplicationContext context =
            new AnnotationConfigApplicationContext(AutowiredJavaConfig.class);

        Launcher launcher = context.getBean("launcher", Launcher.class);
        launcher.launch();
    }
}
```

---

## Output

```
ignited
Launched
```

---

## 🔍 Explanation

* The `@Configuration` class defines two beans: `Rocket` and `Launcher`.
* Spring automatically injects the `Rocket` bean into the `Launcher` bean through the **method parameter** in `launcher(Rocket rocket)`.
* This is known as **method parameter injection** in **Java-based Spring configuration**.
* It eliminates the need for deprecated `autowire` attributes or manual dependency management.

---

✅ **Best Practice:**
Always prefer **method parameter injection** when wiring beans in **Java configuration**, as it’s cleaner, modern, and fully supported in **Spring 5.x and above**.

# @Autowired in Spring (Java Configuration Approach)

## What is `@Autowired`?
- `@Autowired` should be written **inside the class** for which we want to perform **autowiring**.  
- It allows Spring to automatically inject **dependent beans** into a class without manual configuration.

---

## 1. How Dependencies Are Identified
- Dependencies are identified **by type only** (`byType`).

---

## 2. How Dependencies Are Injected
- Dependencies are injected **based on the level** at which we wrote the annotation:
  - **Field level:** Injects directly into the variable.
  - **Constructor level:** Injects through constructor parameters.
  - **Setter/method level:** Injects through setter or custom methods.

---

## Why `@Qualifier` Is Not Used in Java Configuration Approach

```java
/* 
@Qualifier("rocket1") -> It will not be used in Java Configuration approach.

Reason:
In XML-based Spring configuration, beans are defined at the **bean level**, so the IoC container can easily identify which bean to inject.

However, in Java-based configuration, beans are defined at the **method level** (inside @Configuration classes). 
Due to this, the IoC container may get confused about which specific bean to inject.
*/
````

---

## Alternative: Using `@Primary`

```java
@Primary 
// Tells the IoC container to consider this bean as the default choice
// and ignore other beans for autowiring 
// (equivalent to setting autowire-candidate="false" for other beans in XML configuration)
```

# Primitive Injection in Java Configuration Approach

## Example Program

### Player.java
```java
public class Player {
    private int playerNo;
    private String playerName;
    private String team;
    private int rank;

    public int getPlayerNo() {
        return playerNo;
    }

    public void setPlayerNo(int playerNo) {
        this.playerNo = playerNo;
    }

    public String getPlayerName() {
        return playerName;
    }

    public void setPlayerName(String playerName) {
        this.playerName = playerName;
    }

    public String getTeam() {
        return team;
    }

    public void setTeam(String team) {
        this.team = team;
    }

    public int getRank() {
        return rank;
    }

    public void setRank(int rank) {
        this.rank = rank;
    }

    @Override
    public String toString() {
        return "Player{" +
                "playerNo=" + playerNo +
                ", playerName='" + playerName + '\'' +
                ", team='" + team + '\'' +
                ", rank=" + rank +
                '}';
    }
}
````

---

### EnvConfig.java

```java
@Configuration
// @PropertySources({@PropertySource("classpath:application.properties"), @PropertySource("classpath:errors.properties")})
// -> How to read multiple properties files
@PropertySource("classpath:application_env.properties")
public class EnvConfig {

    /*
    Whenever you write the @PropertySource annotation in JavaConfig class,
    an Environment object is automatically created inside the IoC container.
    This Environment object holds references of multiple PropertySource objects like a chain.
    Whenever you call env.getProperty("key"), the Environment object internally
    looks up the corresponding PropertySource object and retrieves the value.
    */
    @Autowired
    private Environment env;
    
    @Bean
    public Player player() throws IOException {
        Player player = new Player();
        System.out.println(this.getClass().getName());

        player.setPlayerNo(Integer.parseInt(env.getProperty("playerNo")));
        player.setPlayerName(env.getProperty("playerName"));
        player.setTeam(env.getProperty("team"));
        player.setRank(Integer.parseInt(env.getProperty("rank")));

        /*
        Here, the problem is we are reading data manually from the properties file.
        If the same properties are needed in another method, we would have to write
        the same code again. Creating a separate method and calling it is also unnecessary
        because Spring provides the @PropertySource annotation for this purpose.

        Example (manual way):
        Properties props = new Properties();
        props.load(this.getClass().getResourceAsStream("/application_env.properties"));
        player.setPlayerNo(Integer.parseInt(props.getProperty("playerNo")));
        player.setPlayerName(props.getProperty("playerName"));
        player.setTeam(props.getProperty("team"));
        player.setRank(Integer.parseInt(props.getProperty("rank")));
       */

        /*
        Hardcoded values example (not recommended):
        player.setPlayerNo(101);
        player.setPlayerName("Sachin");
        player.setTeam("India");
        player.setRank(1);
        */

        return player;
    }
}
```

---

### application_env.properties

```
playerNo=101
playerName=Sachin
team=india
rank=1
```

---

### EnvTest.java

```java
public class EnvTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(EnvConfig.class);
        Player player = context.getBean("player", Player.class);
        System.out.println(player);
    }
}
```

### Output

com.spring.annotations.javaconfig.primitive.env.EnvConfig$$SpringCGLIB$$0
Player{playerNo=101, playerName='Sachin', team='india', rank=1}



<img width="902" height="486" alt="Screenshot 2025-11-01 183054" src="https://github.com/user-attachments/assets/2b46975d-006b-4497-b9a2-73531c69161d" />


### Explanation

Whenever you write the `@PropertySource` annotation in a Java configuration class,
an **Environment** object is created inside the **IoC container**.
This `Environment` object holds references of multiple `PropertySource` objects like a chain.
When you call `env.getProperty("key")`, the `Environment` object internally goes to the respective
`PropertySource` and retrieves the value.




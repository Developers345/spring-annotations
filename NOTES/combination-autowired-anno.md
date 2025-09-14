# Combinations and Permutations

## Case - 1

➡️ If target class has **two `@Autowired` constructors**, then Spring gets confused about which one to inject and throws the following error:

---

## Example

### Dependent Class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent Class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target Class

```java
public class Robo {

    private Chip chip;
    private Sensor sensor;

    @Autowired
    public Robo(Sensor sensor) {
        this.sensor = sensor;
    }

    @Autowired
    public Robo(Chip chip) {
        super();
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                ", sensor=" + sensor +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="sensor" class="com.spring.annotations.Sensor">
        <property name="sensorId" value="s1"/>
        <property name="sensorType" value="silicon"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

### Test Class

```java
public class AutoWiredTest {

    public static void main(String[] args) {

        ApplicationContext context = new ClassPathXmlApplicationContext("application-context.xml");
        Robo robo = context.getBean("robo",Robo.class);
        System.out.println(robo);
    }
}
```

---

### Output

```
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: 
Error creating bean with name 'robo': Invalid autowire-marked constructor: 
public com.spring.annotations.Robo(com.spring.annotations.Chip). 
Found constructor with 'required' Autowired annotation already: 
public com.spring.annotations.Robo(com.spring.annotations.Sensor)
```


Case - 2
--------
-> Target class has 2 constructor dependents and for both dependents we have `@Autowired(required=false)`.  
   In this case, Spring always injects the first written dependent only.

---

Example
--------

### Dependent class 1
```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target class

```java
public class Robo {

    private Chip chip;
    private Sensor sensor;

    @Autowired(required = false)
    public Robo(Chip chip) {
        super();
        this.chip = chip;
    }

    @Autowired(required = false)
    public Robo(Sensor sensor) {
        this.sensor = sensor;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                ", sensor=" + sensor +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="sensor" class="com.spring.annotations.Sensor">
        <property name="sensorId" value="s1"/>
        <property name="sensorType" value="silicon"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```
### Output

Robo{chip=Chip{chipId=101, modelNo='model102'}, sensor=null}


# Case - 3

Target class has **2 constructor dependencies**, and:  
- For one dependency we use `@Autowired`  
- For another we use `@Autowired(required = false)`  

👉 Then **Spring throws an error**.  

> **Note:** For Constructor injection all `@Autowired(required = false)` are not valid.

---

## Example

### Dependent class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target class

```java
public class Robo {

    private Chip chip;
    private Sensor sensor;

    @Autowired
    public Robo(Chip chip) {
        super();
        this.chip = chip;
    }

    @Autowired(required = false)
    public Robo(Sensor sensor) {
        this.sensor = sensor;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                ", sensor=" + sensor +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="sensor" class="com.spring.annotations.Sensor">
        <property name="sensorId" value="s1"/>
        <property name="sensorType" value="silicon"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

## Output

```
Exception in thread "main" org.springframework.beans.factory.BeanCreationException: 
Error creating bean with name 'robo': Invalid autowire-marked constructor: 
public com.spring.annotations.Robo(com.spring.annotations.Sensor). 
Found constructor with 'required' Autowired annotation already: 
public com.spring.annotations.Robo(com.spring.annotations.Chip)
```

Case - 4
--------
Target class have the 3 constructor depedent's and two depedent's have the single argument constructor and one have the 2 argument constructor then always invoke max no of parameter constructor only.

Example
-------

### Dependent class 1
```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

### Target class

```java
public class Robo {

    private Chip chip;
    private Sensor sensor;

    @Autowired(required = false)
    public Robo(Chip chip) {
        super();
        this.chip = chip;
    }

    @Autowired(required = false)
    public Robo(Sensor sensor) {
        this.sensor = sensor;
    }

    @Autowired(required = false)
    public Robo(Chip chip, Sensor sensor) {
        this.chip = chip;
        this.sensor = sensor;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                ", sensor=" + sensor +
                '}';
    }
}
```

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="sensor" class="com.spring.annotations.Sensor">
       <property name="sensorId" value="s1"/>
        <property name="sensorType" value="silicon"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

### Output

```
Robo{chip=Chip{chipId=101, modelNo='model102'}, sensor=Sensor{sensorId='s1', sensorType='silicon'}}
```

# Case - 5

---

➡️ **Target class has 1 dependent injected through setter injection.**  
If the corresponding Spring bean configuration file does **not** have the bean configured, then Spring throws the below error.

---

## Example

### Dependent Class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent Class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target Class

```java
public class Robo {

    private Chip chip;
    
    @Autowired
    public void setChip(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context 
           http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!--<bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>--> 
    <!-- Bean definition is commented -->

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

### Output

```
Exception in thread "main" 
org.springframework.beans.factory.UnsatisfiedDependencyException: 
Error creating bean with name 'robo': 
Unsatisfied dependency expressed through method 'setChip' parameter 0: 
No qualifying bean of type 'com.spring.annotations.Chip' available: 
expected at least 1 bean which qualifies as autowire candidate. 
Dependency annotations: {}
```

---

### Note

➡️ By default, the `@Autowired` annotation has its parameter **required = true**.
This means Spring expects the dependency bean to be available, otherwise it throws an exception.


# Case - 6

Target class has 1 dependent injected through **setter injection**.  
The setter injection uses `@Autowired(required=false)`.  
If the corresponding Spring bean configuration file does not have the bean defined, then the dependency is **optional**, so Spring will not throw an error.

---

## Example

### Dependent class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target class

```java
public class Robo {

    private Chip chip;
    
    @Autowired(required=false)
    public void setChip(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!--<bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>--> <!-- bean definition commented -->

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

### Output

```
Robo{chip=null}
```


# Case 7

Target class has **1 dependent injected through constructor injection**.  
(Constructor injection has `@Autowired(required = false)`).  

If the corresponding Spring bean configuration file does **not** have the bean configured,  
then **Spring will throw an error** because `@Autowired` is optional but **constructor injection is mandatory**.

---

## Example

### Dependent class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target class

```java
public class Robo {

    private Chip chip;
    
    @Autowired(required = false)
    public Robo(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!--<bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>--> <!-- commented the bean definition -->

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

## Output

```
Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException: 
Error creating bean with name 'robo' defined in class path resource [application-context.xml]: 
Unsatisfied dependency expressed through constructor parameter 0: 
No qualifying bean of type 'com.spring.annotations.Chip' available: 
expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}
```


# Case - 8

-> Target class has **1 dependent** injected through **constructor injection** (constructor injection has `@Autowired(required=false)`) and target class has one **no-argument constructor**.  

If the corresponding Spring bean configuration file does not define the dependency, then Spring will **invoke the no-argument constructor** and will **not throw an error**.

---

## Example

### Dependent class 1

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Dependent class 2

```java
public class Sensor {
    private String sensorId;
    private String sensorType;

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    @Override
    public String toString() {
        return "Sensor{" +
                "sensorId='" + sensorId + '\'' +
                ", sensorType='" + sensorType + '\'' +
                '}';
    }
}
```

---

### Target class

```java
public class Robo {

    private Chip chip;

    public Robo() {
        System.out.println("no argument constructor");
    }

    @Autowired(required = false)
    public Robo(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                '}';
    }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!--<bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>--> <!-- commented the bean definition -->

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

### Output

```
no argument constructor
Robo{chip=null}
```

# Case - 9  

Target class has 1 dependent injected through **setter injection**.  

When there are **two bean definitions for the same class / bean type**, Spring will get confused because `@Autowired` always matches through **class / bean type only**.  
So, Spring will throw an exception.  

---

## Example  

### Dependent class  

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

### Target class

```java
public class Robo {

   private Chip chip;
    
   @Autowired
   public void setChip(Chip chip) {
       this.chip = chip;
   }

   @Override
   public String toString() {
       return "Robo{" +
               "chip=" + chip +
               '}';
   }
}
```

---

### application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip1" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="chip2" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />
</beans>
```

---

## Output

```
Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException: 
Error creating bean with name 'robo': Unsatisfied dependency expressed through method 'setChip' parameter 0: 
No qualifying bean of type 'com.spring.annotations.Chip' available: expected single matching bean but found 2: chip1,chip2
```


# @Qualifier  

- `@Autowired` will perform injection through **“byType”** mode.  
  So, if we have more than one bean of type `Chip` in the configuration,  
  then it will not be able to make the decision of which bean has to be injected,  
  and will raise an **ambiguity error**.  

- To resolve this, we have to perform **“byName”** injection.  
  This can be done using **`@Qualifier`** as shown below:  

---

# Case 10  

## Dependent class  

```java
public class Chip {

    private String chipId;
    private String modelNo;

    public void setChipId(String chipId) {
        this.chipId = chipId;
    }

    public void setModelNo(String modelNo) {
        this.modelNo = modelNo;
    }

    public String getModelNo() {
        return modelNo;
    }

    public String getChipId() {
        return chipId;
    }

    @Override
    public String toString() {
        return "Chip{" +
                "chipId=" + chipId +
                ", modelNo='" + modelNo + '\'' +
                '}';
    }
}
````

---

## Target class

```java
public class Robo {

    private Chip chip;
    private Sensor sensor;

    @Autowired
    @Qualifier("my-chip")
    public void setChip(Chip chip) {
        this.chip = chip;
    }

    @Override
    public String toString() {
        return "Robo{" +
                "chip=" + chip +
                '}';
    }
}
```

---

## application-context.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!-- We can use bean id as qualifier name. 
         We need to specify the same in target class -->
    <bean id="chip" class="com.spring.annotations.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
        <qualifier value="my-chip"></qualifier>
    </bean>

    <bean id="chip1" class="com.spring.annotations.Chip">
        <property name="chipId" value="102"/>
        <property name="modelNo" value="model103"/>
    </bean>

    <bean id="robo" class="com.spring.annotations.Robo"/>

    <context:annotation-config />

</beans>
```

---

## Output

```
Robo{chip=Chip{chipId=101, modelNo='model102'}}
```

---

## Note

* In this way, indirectly by using `@Qualifier`, we are able to perform **“byName” injection**.
* If we do not configure any qualifier value, then by default the qualifier value will be the **attribute name**.

```






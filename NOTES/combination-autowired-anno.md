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



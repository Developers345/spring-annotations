# @Import Annotation

## Overview
- If you have multiple Java configuration classes, using the `@Import` annotation allows you to create a single IoC (Inversion of Control) container.  
- Generally, when you go for multiple Java configuration classes — for example, when you have different roles of classes (like service and persistence classes) — you can compartmentalize them by placing them into separate Java configuration classes.

---

## Example Program

### `BarChart.java`
```java
// No Source Code
public class BarChart {
    public void prepared() {
        System.out.println("data prepared");
    }
}
````

---

### `BarChartViewer.java`

```java
// No Source Code
public class BarChartViewer {

    private BarChart barChart;

    public void renderChart() {
        barChart.prepared();
        System.out.println("rendering the barchart data ");
    }

    public void setBarChart(BarChart barChart) {
        this.barChart = barChart;
    }
}
```

---

### `UIConfig.java`

```java
@Configuration
public class UIConfig {

    @Bean
    public BarChartViewer barChartViewer(BarChart barChart) {
        BarChartViewer barChartViewer = new BarChartViewer();
        barChartViewer.setBarChart(barChart);
        return barChartViewer;
    }
}
```

---

### `ServiceConfig.java`

```java
@Configuration
public class ServiceConfig {

    @Bean
    public BarChart barChart() {
        BarChart barChart = new BarChart();
        return barChart;
    }
}
```

---

### `RootConfig.java`

```java
@Configuration
@Import({ServiceConfig.class, UIConfig.class})
public class RootConfig {
}
```

---

### `Test.java`

```java
public class ImportJavaConfigTest {

    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(RootConfig.class);
        BarChartViewer barChartViewer = context.getBean("barChartViewer", BarChartViewer.class);
        barChartViewer.renderChart();
    }
}
```

---

## Output

```
data prepared
rendering the barchart data
```

# @ImportResource Annotation

## Overview
- If part of the Spring configuration is defined in an XML configuration file and another part is defined in a Java configuration class, you can use the `@ImportResource` annotation to load the XML-based bean definitions into the Java configuration context.  
- The order between Java configuration classes and XML configuration files is **not important**.

---

## Example Program

### `AccountManager.java`
```java
// No Source Code
public class AccountManager {

    private UsageMonitor usageMonitor;

    public void desposite(String accountNo, double amount) {
        System.out.println("deposite the amount");
        usageMonitor.captureData(accountNo, "deposite");
    }

    public void setUsageMonitor(UsageMonitor usageMonitor) {
        this.usageMonitor = usageMonitor;
    }
}
````

---

### `UsageMonitor.java`

```java
// No Source Code
public class UsageMonitor {

    public void captureData(String accountNo, String operationType) {
        System.out.println("data is capuring .." + accountNo + " " + operationType);
    }
}
```

---

### `JavaConfig.java`

```java
@Configuration
public class JavaConfig {

    @Bean
    public AccountManager accountManager(UsageMonitor usageMonitor) {
        AccountManager accountManager = new AccountManager();
        accountManager.setUsageMonitor(usageMonitor);
        return accountManager;
    }
}
```

---

### `monitoring_bean.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
           http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <bean id="chip" class="com.spring.annotations.streotype.Chip">
        <property name="chipId" value="101"/>
        <property name="modelNo" value="model102"/>
    </bean>

    <bean id="usageMonitor" class="com.spring.annotations.javaconfig.importresources.anno.UsageMonitor"/>

</beans>
```

---

### `RootConfig.java`

```java
@Configuration
@ImportResource("classpath:monitoring_bean.xml")
@Import(JavaConfig.class)
public class RootConfig {
}
```

---

### `Test.java`

```java
public class ImportResourcesTest {

    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(RootConfig.class);
        AccountManager accountManager = context.getBean("accountManager", AccountManager.class);
        accountManager.desposite("12rt45", 3000.00);
    }
}
```

---

## Output

```
deposite the amount
data is capuring ..12rt45 deposite
```


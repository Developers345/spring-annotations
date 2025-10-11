# Ways to Work with Annotations

There are 4 ways to work with annotations:

1. **Configuration Approach** — Spring bean configuration approach (only XML)
2. **Stereotype Annotations** — Only annotations
3. **Stereotype with XML** — Combination of annotations with XML
4. **Stereotype with Java Configuration Approach** — Annotations with using java configuration classes alternative to XML.

# Combination of Annotations (XML + Annotation) — Example 1

A small, step-by-step example showing how to mix Spring **annotation-based** components with **XML** bean definitions.

---

## Project structure (suggested)

```
src/
└─ main/
   ├─ java/
   │  └─ com/spring/xml/anno/
   │     ├─ MessageWriter.java
   │     ├─ HTMLMessageConverter.java
   │     ├─ IMessageConverter.java
   │     └─ AnnotationXMLTest.java
   └─ resources/
      └─ application-context.xml
```

---

## 1. Target class (source code present) — `MessageWriter.java`

```java
package com.spring.xml.anno;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MessageWriter {

    @Autowired
    private IMessageConverter messageConverter;

    public void writeMessage(String message) {
        messageConverter.convertMessage(message);
    }
}
```

---

## 2. Dependent class (defined in XML - source code not present) — `HTMLMessageConverter.java`

```java
package com.spring.xml.anno;

public class HTMLMessageConverter implements IMessageConverter {

    @Override
    public void convertMessage(String message) {
        System.out.println("<html>Hello" + message + "</html>");
    }
}
```

> **Note:** This class is **not** annotated with `@Component` — it will be declared as a bean in the XML file.

---

## 3. Interface — `IMessageConverter.java`

```java
package com.spring.xml.anno;

public interface IMessageConverter {
    void convertMessage(String message);
}
```

---

## 4. `application-context.xml` (in `src/main/resources`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
         http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">

    <!-- XML-defined bean for the converter -->
    <bean id="messageConverter" class="com.spring.xml.anno.HTMLMessageConverter"/>

    <!-- component-scan to pick up @Component classes like MessageWriter -->
    <context:component-scan base-package="com.spring.xml.anno"/>
</beans>
```

---

## 5. Test / Runner — `AnnotationXMLTest.java`

```java
package com.spring.xml.anno;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationXMLTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application-context.xml");
        MessageWriter messageWriter = context.getBean("messageWriter", MessageWriter.class);
        messageWriter.writeMessage("Gireesh");
    }
}
```

---

## 6. Run & Output

Run `AnnotationXMLTest.main(...)` (from your IDE or via `java -cp ...` / Maven/Gradle).
Expected console output:

```
<html>HelloGireesh</html>
```

---

## 7. How it works (step-by-step)

1. `context:component-scan` finds `MessageWriter` because it's annotated with `@Component`. Spring creates a bean named `messageWriter` (default bean name).
2. `application-context.xml` defines a bean with id `messageConverter` whose class is `HTMLMessageConverter`.
3. `MessageWriter` has a field `@Autowired private IMessageConverter messageConverter;`

   * Spring autowires by type: it finds the XML-defined `HTMLMessageConverter` (which implements `IMessageConverter`) and injects it into `MessageWriter`.
4. Calling `messageWriter.writeMessage("Gireesh")` delegates to `messageConverter.convertMessage(...)`, which prints the HTML string.

---

## 8. Notes & common pitfalls

* Autowiring is by **type** by default. If multiple `IMessageConverter` beans exist, use `@Qualifier` or set `@Primary` to disambiguate.
* Ensure `application-context.xml` is on the classpath (e.g., `src/main/resources`).
* Bean names: `@Component` without explicit name produces a bean id like `messageWriter` (class name with lower-case first letter).
* You can mix XML bean definitions and annotations freely — XML can define beans that are injected into annotation-scanned components and vice versa.

---

# Example 2

---

## Dependent class
```java
//Source code not present
public class Payment {
    private String paymentId;
    private double amount;
    private double balance;

    public String getPaymentId() {
        return paymentId;
    }

    public void setPaymentId(String paymentId) {
        this.paymentId = paymentId;
    }

    public double getAmount() {
        return amount;
    }

    public void setAmount(double amount) {
        this.amount = amount;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "Payment{" +
                "paymentId='" + paymentId + '\'' +
                ", amount=" + amount +
                ", balance=" + balance +
                '}';
    }
}
````

---

## Target class

```java
//Source code present
@Controller
public class PaymentSystem {

    @Autowired
    private Payment payment;

    public void processPayment() {
        System.out.println("payment processing..");
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
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="
          http://www.springframework.org/schema/beans 
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context 
          http://www.springframework.org/schema/context/spring-context-4.3.xsd
          http://www.springframework.org/schema/util 
          http://www.springframework.org/schema/util/spring-util-4.3.xsd">

    <bean id="payment" class="com.spring.xml.anno.Payment">
        <property name="paymentId" value="s123"/>
        <property name="amount" value="40000.00"/>
        <property name="balance" value="500000.00"/>
    </bean>

</beans>
```

---

## Test

```java
public class AnnotationXMLTest {
    public static void main(String[] args) {
        ApplicationContext context =
            new ClassPathXmlApplicationContext("application-context.xml");

        PaymentSystem paymentSystem =
            context.getBean("paymentSystem", PaymentSystem.class);

        paymentSystem.processPayment();
    }
}
```

---

## Output

```
payment processing..
```

# Example 3

---

## Dependent class

**Source code present**

```java
@Component
public class Address {
    @Value("#{addprops.addressLine1}")
    private String addressLine1;
    @Value("#{addprops.addressLine2}")
    private String addressLine2;
    @Value("#{addprops.pincode}")
    private int pincode;
    @Value("#{addprops.city}")
    private String city;
    @Value("#{addprops.state}")
    private String state;
    @Value("#{addprops.country}")
    private String country;

    @Override
    public String toString() {
        return "Address{" +
                "addressLine1='" + addressLine1 + '\'' +
                ", addressLine2='" + addressLine2 + '\'' +
                ", pincode=" + pincode +
                ", city='" + city + '\'' +
                ", state='" + state + '\'' +
                ", country='" + country + '\'' +
                '}';
    }
}
````

---

## Target class

**No source code**

```java
public class Person {

    @Autowired
    private Address address;

    @Override
    public String toString() {
        return "Person{" +
                "address=" + address +
                '}';
    }
}
```

---

## `address.properties`

```properties
addressLine1=FlatNo:G7
addressLine2=PVR ENCLAVE APP
pincode=500090
city=Hyderabad
state=Telangana
country=India
```

---

## `application-context.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:util="http://www.springframework.org/schema/util"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">

    <bean id="person" class="com.spring.xml.anno.Person"/>

    <context:component-scan base-package="com.spring.xml.anno"/>

    <util:properties id="addprops" location="classpath:address.properties"/>
</beans>
```

---

## Test Class

```java
public class AnnotationXMLTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("application-context.xml");

        Person person = context.getBean("person", Person.class);
        System.out.println(person);
    }
}
```

---

## Output

```text
Person{address=Address{addressLine1='FlatNo:G7', addressLine2='PVR ENCLAVE APP', pincode=500090, city='Hyderabad', state='Telangana', country='India'}}
```

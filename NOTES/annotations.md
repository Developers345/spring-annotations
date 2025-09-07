## 1. Why Annotations Come Into Picture?

- Annotations are meant to provide information about our classes to the Spring IoC container.  
- Both XML and Annotations are used to provide information about our classes to the container.  
- Annotations are an alternate way to provide information to the container.  
- Annotations came into picture due to the disadvantages of using XML.

## 2. XML Disadvantages

- XML is verbose (lengthy configuration). To define information for one class, we may need to write a minimum of 3 lines.  
- Developers need to remember the tags and their correct order.  
- XML does not support rapid application development.  
- XML has limited support from IDEs (Eclipse, IntelliJ).  
- Developers find it difficult to debug the application when any exception occurs in the configuration.


## 3. Advantages and Disadvantages of Annotations

## Advantages of Annotations
- Annotations are very short in nature.
- Annotations support rapid application development.
- Annotations are Java code and have IDE support.
- Developers can write annotations easily along with Java code.

## Disadvantages of Annotations
- Annotations are written along with Java source code, so if any changes happen, recompilation and redeployment are required. This increases maintenance and testing costs.
- Developers may find it difficult to understand the project because they need to navigate through all classes in the application.

# Advantages of XML
- XML is an external configuration file, so any changes do not require recompilation or redeployment. A simple restart is sufficient, reducing maintenance and testing costs.
- Developers can get a clear picture of relationships and dependencies of all classes in the application in one place.
- XML encourages POJO class development without including third-party classes.

# Levels of Annotations
We can declare annotations at four levels:
1. Class level
2. Method level
3. Attribute level
4. Parameter level

# Retention Policy of Java Annotations
- Retention policy defines at what point annotations are discarded.
- Java defines three types of retention policies in `java.lang.annotation.RetentionPolicy` enumeration:
  1. **SOURCE**: Annotation is retained only at the source level and discarded at compile time.
  2. **CLASS**: Annotation is retained till compile time (default policy) and discarded at runtime.
  3. **RUNTIME**: Annotation is retained till runtime.

# Types of Annotations
There are three types of annotations:

## 1. Source Code Assister
- Name indicates the purpose and does not impact functionality.
- Helps in better understanding of source code.
- Available until compile time.
- Example: `@Override`

## 2. Compile Assister
- Generally used to suppress warnings at compile time.
- Example: `@SuppressWarnings`
- Usage: To avoid compile-time warnings for deprecated classes or other issues.

## 3. Runtime Assister
- Annotations written in the source code that are retained until runtime.

## 4. How Annotation Works Internally?

- Spring initially did not support annotations. Later, from Spring 2.0 onwards, Spring supports annotations.
- Enabling annotations in Spring internally uses **BeanPostProcessor**.
- **BeanPostProcessor** is used to perform initialization and customization **after an object is created in the IOC container but before it is used**.

## 5. @Required Annotation: What Happens Internally?

- **Background:**
  - Before the introduction of the `@Required` annotation, Spring used a concept called **"dependency-check"**.
  - The purpose of **dependency-check** was to enforce **mandatory setter injection**, ensuring that certain properties must be set for a bean.

- **Drawbacks of dependency-check:**
  - If a bean had multiple attributes (e.g., three attributes injected via setter in the XML configuration), you could only specify at the bean definition level:
    ```xml
    <bean id="exampleBean" class="com.example.Example" dependency-check="simple"/>
    ```
    - This meant all three attributes were mandatory.
    - However, if you wanted only **one attribute** to be mandatory while others were optional, dependency-check could not support this scenario.
  - Due to this limitation, Spring removed **dependency-check** starting from **Spring 2.5**.

- **Introduction of @Required annotation:**
  - To replace dependency-check, Spring introduced the `@Required` annotation.
  - **How it works internally:**
    - When a bean with `@Required` annotation is created:
      1. Spring first creates the object of the target class.
      2. It performs setter injection **after the post-construction of the bean**, but **before placing the bean into the IOC container**.
      3. Spring scans the class to check which setters have been marked with `@Required`.
      4. For each `@Required` setter, Spring verifies if a value is provided via the `<property>` tag in the XML configuration.
      5. If a required property is missing, Spring throws a **`BeanInitializationException`** to indicate that mandatory properties are not set.

- **Deprecation:**
  - The `@Required` annotation was **deprecated in Spring 5.1**.
  - The recommended alternative is **constructor injection** for mandatory properties, as it provides better immutability and clearer enforcement of required dependencies.

- **Example:**
  ```java
  public class ExampleBean {
      private String name;

      @Required
      public void setName(String name) {
          this.name = name;
      }
  }

```xml
<bean id="exampleBean" class="com.example.ExampleBean">
    <property name="name" value="Spring Example"/>
</bean>
```

* **Key Points to Remember:**

  * `@Required` is a replacement for the limited `dependency-check`.
  * It enforces mandatory setter injection at the framework level.
  * Deprecated in favor of constructor injection for better design and clarity.

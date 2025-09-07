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


# @Autowired and `<context:annotation-config/>`

## What is the use of `<context:annotation-config/>`?

- If you are using annotations, you need to know about the corresponding **BeanPostProcessor** class name and configure it in the Spring bean configuration file.  
- It is very difficult to remember the **BeanPostProcessor** classes for annotations. To overcome this problem, Spring provides one tag called:  

  ```xml
  <context:annotation-config/>
````

* Whenever we declare the `<context:annotation-config/>` tag, the Spring IOC container automatically reads the **BeanPostProcessor** and **BeanFactoryPostProcessor** and injects the corresponding classes as beans.


# Stereotype Annotations (@Component, @Service, @Repository)

---

## Step 1 — Overview
Spring provides *stereotype annotations* to mark classes as Spring-managed beans without writing XML configuration. These annotations are specializations of `@Component` and are used to clarify the role of a class in the application.

## Step 2 — The four common stereotype annotations
1. `@Component`  
2. `@Service`  
3. `@Repository`  
4. `@Controller`  

## Step 3 — Why multiple annotations for the same purpose?
Spring offers multiple stereotype annotations so you can **separate responsibilities** and make the role of each class explicit. In a typical web application you will commonly see:
- Presentation layer classes  
- Service (business) layer classes  
- DAO / persistence layer classes

Using different annotations improves readability, tooling, and can enable layer-specific behavior (for example, exception translation on `@Repository`).

## Step 4 — Short definitions / roles
- `@Component` — general-purpose POJO bean. Use when no other stereotype fits.  
- `@Controller` — handles HTTP requests (presentation / MVC controllers).  
- `@Service` — contains business logic, orchestration, or delegate logic.  
- `@Repository` — persistence / DAO layer; interacts with the database.

## Step 5 — Mapping table
| Annotation   | Layer / Role              | Notes |
|--------------|---------------------------|-------|
| `@Component` | General POJO              | Generic stereotype |
| `@Service`   | Service / business logic  | Semantic clarity; optional AOP targets |
| `@Repository`| Persistence / DAO         | Exception translation; DAO specific behavior |
| `@Controller`| Presentation / MVC        | Handles web requests (use `@RestController` for REST APIs) |

## Step 6 — Example usages

### `@Component`
```java
import org.springframework.stereotype.Component;

@Component
public class PaymentUtils {
    public boolean validateCard(String cardNumber) {
        // validation logic
        return true;
    }
}
````

### `@Service`

```java
import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;

@Service
public class OrderService {
    @Autowired
    private OrderRepository orderRepository;

    public void processOrder(Order order) {
        // business logic
        orderRepository.save(order);
    }
}
```

### `@Repository`

```java
import org.springframework.stereotype.Repository;
import org.springframework.data.jpa.repository.JpaRepository;

@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    // query methods
}
```

### `@Controller`

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/orders")
public class OrderController {
    @Autowired
    private OrderService orderService;

    @GetMapping("/{id}")
    public String getOrder(@PathVariable Long id, Model model) {
        // handle request and return view name
        return "orderView";
    }
}
```

## Notes

* All four annotations are detected by component scanning and register beans automatically.
* `@Repository`, `@Service`, and `@Controller` are semantic specializations of `@Component`.
* For REST APIs prefer `@RestController` (combines `@Controller` + `@ResponseBody`).

---

# `@Component` as the Base Annotation

---

## Step 1 — Statement
`@Component` is the base annotation for the other three Spring stereotype annotations.

## Step 2 — Visual (ASCII) diagram
```

```
                             @Component
                      |           |          | 
                 @Controller  @Service   @Repository
```

````

## Step 3 — What this means (short)
- `@Controller`, `@Service`, and `@Repository` are *specializations* of `@Component`.  
- They are conceptually annotated with `@Component`, so all are discovered by Spring's component scanning and registered as beans.

## Step 4 — How it looks in code (conceptual)
Spring's specializations are simple meta-annotations. Conceptually they look like this:

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {
    String value() default "";
}
````

(The real Spring source follows this pattern for `@Service`, `@Repository`, and `@Controller`.)

## Step 5 — Why use the specializations?

* **Clarity**: makes the role of the class explicit (presentation, business, persistence).
* **Tooling**: IDEs and frameworks can offer different support based on the stereotype.
* **Behavior**: some stereotypes (e.g., `@Repository`) enable layer-specific features like exception translation.

## Step 6 — Quick example mapping

* `@Controller` → presentation / web layer
* `@Service` → business / service layer
* `@Repository` → data access / persistence layer

---

## Step 7 — One-line summary

`@Component` is the generic bean marker; `@Controller`, `@Service`, and `@Repository` extend that idea semantically so your code is clearer and Spring can apply layer-specific behavior.



## Title

**Using stereotype annotations and common BeanPostProcessor**

---

## Steps

1. **Goal — post-processing for business delegates**
   If you want to apply some post-processing logic after a business-delegate object is created, you should implement that logic in a `BeanPostProcessor`.

2. **Why not mark everything `@Component`?**
   If you mark all classes with `@Component` you lose the ability to classify related groups of classes (role-based grouping). You also can’t easily target BeanPostProcessor logic specifically to business delegates.

3. **What happens if classes are not annotated?**
   If classes are not annotated with stereotypes, you can’t make them role-based (you can’t classify them by role such as service/delegate/repository).

4. **Solution — use `@Service` for business delegates**
   Annotating business delegates with `@Service` lets you:

   * classify those classes clearly, and
   * apply BeanPostProcessor logic specifically to those service/delegate beans.

   ```java
   @Service
   public class MyBusinessDelegate {
       // business methods
   }
   ```

5. **Register beans with the IoC container**
   To use stereotype annotations, the beans must be registered with the IoC container when the container is created. One common way is component-scanning.

   Example (XML):

   ```xml
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="...">

       <context:component-scan base-package="com.example.yourpackage" />
   </beans>
   ```

6. **Common BeanPostProcessor**
   Rather than providing multiple post-processors, Spring provides a single `BeanPostProcessor` called `CommonAnnotationApplicationBeanPostProcessor` (as described in your notes), which you can enable via component-scan.

7. **Enable with component-scan**
   Enable scanning (and thereby allow stereotype handling and the post-processor) with:

   ```xml
   <context:component-scan base-package="com.example.yourpackage" />
   ```


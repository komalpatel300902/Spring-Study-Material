# Q1. Tell me all about Exception Handling in the Spring Boot.

## **Exception Handling in Spring Boot** 🚀

Exception handling in **Spring Boot** is a crucial aspect of building robust applications. Spring provides multiple ways to handle exceptions gracefully, ensuring a better user experience and maintainable code.

---

## **1️⃣ Exception Handling Methods in Spring Boot**
### **1. Using `@ExceptionHandler` (Method-Level)**
- Used inside a **Controller** or `@RestControllerAdvice` to handle specific exceptions.

#### ✅ **Example**
```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id)
                .orElseThrow(() -> new UserNotFoundException("User not found with ID: " + id));
    }

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```
✅ **When to Use?**  
✔ When handling specific exceptions inside a single controller.

---

### **2. Using `@ControllerAdvice` (Global Exception Handling)**
- Used to handle exceptions globally across multiple controllers.

#### ✅ **Example**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFoundException(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)  // Catch all other exceptions
    public ResponseEntity<String> handleGlobalException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Something went wrong: " + ex.getMessage());
    }
}
```
✅ **When to Use?**  
✔ When handling exceptions **globally** for multiple controllers.

---

### **3. Using `@ResponseStatus`**
- Used to define the **HTTP status code** for an exception class.

#### ✅ **Example**
```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```
✅ **When to Use?**  
✔ When you need a simple way to **associate exceptions with status codes**.

---

### **4. Using `HandlerExceptionResolver` (Advanced Custom Handling)**
- **More control** over exception handling logic.

#### ✅ **Example**
```java
@Component
public class CustomExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        response.setStatus(HttpStatus.INTERNAL_SERVER_ERROR.value());
        ModelAndView mav = new ModelAndView();
        mav.addObject("error", ex.getMessage());
        mav.setViewName("error");
        return mav;
    }
}
```
✅ **When to Use?**  
✔ When needing advanced **custom exception handling logic**.

---

### **5. Using `ResponseEntityExceptionHandler` (Spring's Default Exception Handling)**
- Used to **extend** and customize Spring's default error handling.

#### ✅ **Example**
```java
@RestControllerAdvice
public class CustomExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<Object> handleUserNotFound(UserNotFoundException ex, WebRequest request) {
        ApiError error = new ApiError(HttpStatus.NOT_FOUND, ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
}
```
✅ **When to Use?**  
✔ When customizing **Spring's built-in exception handling**.

---

## **2️⃣ Spring Boot's Default Exception Handling**
Spring Boot provides **built-in exception handling** for common exceptions:

| Exception | Default HTTP Status |
|-----------|---------------------|
| `HttpMessageNotReadableException` | `400 BAD REQUEST` |
| `MethodArgumentNotValidException` | `400 BAD REQUEST` |
| `NoHandlerFoundException` | `404 NOT FOUND` |
| `HttpRequestMethodNotSupportedException` | `405 METHOD NOT ALLOWED` |

📌 **To enable `NoHandlerFoundException` handling**, add this in `application.properties`:
```properties
spring.mvc.throw-exception-if-no-handler-found=true
spring.web.resources.add-mappings=false
```

---

## **3️⃣ Best Practices for Exception Handling**
✔ **Use `@ControllerAdvice`** for global exception handling.  
✔ **Create custom exception classes** to improve readability.  
✔ **Avoid exposing sensitive data** in error messages.  
✔ **Use meaningful HTTP status codes** (e.g., `400`, `404`, `500`).  
✔ **Log exceptions properly** (`Logger.error()`) to track errors.  

---

## **4️⃣ Example: Custom Error Response Structure**
For API responses, a **structured error response** is recommended:

#### ✅ **Custom Exception Response DTO**
```java
public class ApiError {
    private HttpStatus status;
    private String message;
    private LocalDateTime timestamp;

    public ApiError(HttpStatus status, String message) {
        this.status = status;
        this.message = message;
        this.timestamp = LocalDateTime.now();
    }

    // Getters and Setters
}
```

#### ✅ **Example Response**
```json
{
    "status": "NOT_FOUND",
    "message": "User not found with ID: 10",
    "timestamp": "2024-01-30T10:30:45"
}
```

---

## **5️⃣ Summary Table**
| Exception Handling Approach | Usage | Best For |
|----------------------------|-------|----------|
| `@ExceptionHandler` | Local method exception handling | Handling specific exceptions within a single controller |
| `@ControllerAdvice` | Global exception handling | Managing exceptions across multiple controllers |
| `@ResponseStatus` | Custom HTTP status for exceptions | Simple status-code-based exception handling |
| `HandlerExceptionResolver` | Advanced custom handling | Complex exception handling logic |
| `ResponseEntityExceptionHandler` | Extending Spring's exception handling | Customizing default Spring behavior |

---

## **Final Thoughts**
Spring Boot offers **powerful exception handling mechanisms** to enhance application reliability. The best approach depends on your project needs. **Using `@ControllerAdvice` with custom DTOs is often the best practice!** 🚀🔥  


---

# Q2. For DTO I had placed many checks. When the input is wrong the error is handled by the method which I created in the global exception. how does message and status code is passed to that Exception handler and what are all the data that passes when Exception is thrown.

### **How Exception Data is Passed to Global Exception Handler in Spring Boot?**

When an exception is thrown inside a Spring Boot application, **Spring propagates it** to the appropriate handler. If you've defined a **global exception handler** using `@RestControllerAdvice`, Spring automatically calls it and passes the **exception object** and relevant request details.

---

## **1️⃣ Flow of Exception Handling**
1. **A validation error or other exception occurs.**
2. **Spring intercepts the exception** and checks if an `@ExceptionHandler` exists for it.
3. If an `@ExceptionHandler` is present, Spring passes the **exception object** and request details to it.
4. The handler processes the exception and **returns a structured response** (e.g., HTTP status code + error message).

---

## **2️⃣ Example: DTO Validation & Global Exception Handling**
Let's assume you have a **DTO with validation annotations**:

### ✅ **User DTO with Validation**
```java
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;

public class UserDTO {

    @NotBlank(message = "Name cannot be empty")
    @Size(min = 3, max = 50, message = "Name must be between 3 and 50 characters")
    private String name;

    @Email(message = "Invalid email format")
    private String email;

    @Size(min = 10, max = 10, message = "Mobile number must be exactly 10 digits")
    private String mobile;

    // Getters and Setters
}
```

---

### ✅ **Controller Using DTO**
```java
@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping("/create")
    public ResponseEntity<String> createUser(@Valid @RequestBody UserDTO userDto) {
        // Simulating saving user (actual DB call would be here)
        return ResponseEntity.ok("User created successfully");
    }
}
```
⏩ If **invalid data** is sent, **Spring automatically throws `MethodArgumentNotValidException`**.

---

### ✅ **Global Exception Handler for DTO Validation Errors**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    // Handling DTO validation errors
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, Object>> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, Object> errors = new HashMap<>();
        errors.put("status", HttpStatus.BAD_REQUEST.value());
        errors.put("timestamp", LocalDateTime.now());

        // Extracting validation errors
        List<String> errorMessages = ex.getBindingResult()
                                       .getFieldErrors()
                                       .stream()
                                       .map(error -> error.getField() + ": " + error.getDefaultMessage())
                                       .collect(Collectors.toList());

        errors.put("errors", errorMessages);
        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }
}
```
---

## **3️⃣ How Exception Data is Passed?**
When an exception like `MethodArgumentNotValidException` is thrown:
- Spring **automatically calls** the `@ExceptionHandler` method.
- The `ex` (exception object) contains:
  - `ex.getBindingResult().getFieldErrors()` → **List of validation errors**
  - `error.getField()` → **Field that caused the error**
  - `error.getDefaultMessage()` → **Message from the DTO validation annotation**
  - `HttpStatus.BAD_REQUEST.value()` → **HTTP status (400)**

---

### ✅ **Example Request & Error Response**
📌 **Request (Invalid Input)**
```json
{
  "name": "",
  "email": "invalid-email",
  "mobile": "123"
}
```

📌 **Response from Global Exception Handler**
```json
{
  "status": 400,
  "timestamp": "2024-01-30T14:00:00",
  "errors": [
    "name: Name cannot be empty",
    "email: Invalid email format",
    "mobile: Mobile number must be exactly 10 digits"
  ]
}
```
🚀 Now the API returns a **structured and meaningful** error response!

---

## **4️⃣ Other Exception Data That Can Be Passed**
Besides validation errors, when an exception is thrown, Spring **passes multiple pieces of information** to the handler:

| Data | How to Access It? | Description |
|------|------------------|-------------|
| Exception Message | `ex.getMessage()` | The error message |
| Exception Cause | `ex.getCause()` | Root cause of the error |
| HTTP Status | `HttpStatus.BAD_REQUEST.value()` | Status code (e.g., 400, 404, 500) |
| Exception Class | `ex.getClass().getSimpleName()` | The type of exception |
| Field Errors (DTO) | `ex.getBindingResult().getFieldErrors()` | List of validation errors |
| Request Path | `request.getRequestURI()` | Endpoint where the error occurred |

---

## **5️⃣ Handling Custom Exceptions with Detailed Response**
Sometimes, we may want to **throw custom exceptions** instead of using built-in ones.

### ✅ **Custom Exception**
```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```

### ✅ **Global Exception Handler for Custom Exceptions**
```java
@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<Map<String, Object>> handleUserNotFound(UserNotFoundException ex, HttpServletRequest request) {
    Map<String, Object> response = new HashMap<>();
    response.put("status", HttpStatus.NOT_FOUND.value());
    response.put("error", "User Not Found");
    response.put("message", ex.getMessage());
    response.put("path", request.getRequestURI());
    response.put("timestamp", LocalDateTime.now());
    return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
}
```

📌 **Example Response**
```json
{
  "status": 404,
  "error": "User Not Found",
  "message": "User with ID 10 not found",
  "path": "/users/10",
  "timestamp": "2024-01-30T14:05:00"
}
```

---

## **6️⃣ Summary**
✔ **Exception Data Passed to `@ExceptionHandler`**
- `Exception message` → `ex.getMessage()`
- `List of errors` (DTO validation) → `ex.getBindingResult().getFieldErrors()`
- `Status Code` → `HttpStatus.BAD_REQUEST.value()`
- `Exception Type` → `ex.getClass().getSimpleName()`
- `Request Path` → `request.getRequestURI()`
  
✔ **Best Practices**
- Always **return structured error responses** (use DTOs if needed).
- Use `@RestControllerAdvice` to handle multiple exceptions globally.
- For validation errors, extract **field-specific** messages.
- Always include a **timestamp & HTTP status** in the response.

Would you like a **customized error-handling response format** for your project? 🚀😊


# Q3. If want to call same function for 2 different exception how I will do ?

### **Handling Multiple Exceptions with the Same Function in Spring Boot**
If you want to **call the same exception handler for multiple exception types**, you can **do it in three ways**:

---

## **1️⃣ Using Multiple `@ExceptionHandler` Parameters**
You can specify multiple exception types inside the `@ExceptionHandler` annotation.

### ✅ **Example**
```java
@ExceptionHandler({UserNotFoundException.class, ProductNotFoundException.class})
public ResponseEntity<Map<String, Object>> handleNotFoundExceptions(RuntimeException ex, HttpServletRequest request) {
    Map<String, Object> response = new HashMap<>();
    response.put("status", HttpStatus.NOT_FOUND.value());
    response.put("error", "Resource Not Found");
    response.put("message", ex.getMessage());
    response.put("path", request.getRequestURI());
    response.put("timestamp", LocalDateTime.now());
    
    return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
}
```
📌 **How it works?**
- If **`UserNotFoundException`** or **`ProductNotFoundException`** is thrown, this function will handle it.
- It returns a **consistent response format**.

---

## **2️⃣ Using a Common Parent Exception Class**
If multiple exceptions share a **common parent class**, you can catch that parent class instead.

### ✅ **Example**
```java
@ExceptionHandler(NotFoundException.class) // Parent Exception
public ResponseEntity<Map<String, Object>> handleNotFoundException(NotFoundException ex, HttpServletRequest request) {
    Map<String, Object> response = new HashMap<>();
    response.put("status", HttpStatus.NOT_FOUND.value());
    response.put("error", "Resource Not Found");
    response.put("message", ex.getMessage());
    response.put("path", request.getRequestURI());
    response.put("timestamp", LocalDateTime.now());

    return new ResponseEntity<>(response, HttpStatus.NOT_FOUND);
}
```
📌 **How it works?**
- Define a **custom parent exception class**:
```java
public class NotFoundException extends RuntimeException {
    public NotFoundException(String message) {
        super(message);
    }
}
```
- Then, make **other exceptions extend `NotFoundException`**:
```java
public class UserNotFoundException extends NotFoundException {
    public UserNotFoundException(String message) {
        super(message);
    }
}
```
- Now, **all subclasses** (e.g., `UserNotFoundException`, `ProductNotFoundException`) will be handled by the **same method**.

---

## **3️⃣ Using `Exception` as a Generic Catch-All**
If you want to **handle all exceptions in one method**, you can catch `Exception` or `RuntimeException`.

### ✅ **Example**
```java
@ExceptionHandler(Exception.class)
public ResponseEntity<Map<String, Object>> handleAllExceptions(Exception ex, HttpServletRequest request) {
    Map<String, Object> response = new HashMap<>();
    response.put("status", HttpStatus.INTERNAL_SERVER_ERROR.value());
    response.put("error", "Internal Server Error");
    response.put("message", ex.getMessage());
    response.put("path", request.getRequestURI());
    response.put("timestamp", LocalDateTime.now());

    return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR);
}
```
📌 **How it works?**
- This will **catch all exceptions**, but it's **not recommended** for specific cases.
- It’s useful for handling **unexpected errors**.

---

## **🔹 Which Approach Should You Use?**
| Approach | When to Use? | Example |
|----------|-------------|---------|
| **Multiple `@ExceptionHandler` Parameters** | When handling a **few specific exceptions** | `@ExceptionHandler({UserNotFoundException.class, ProductNotFoundException.class})` |
| **Common Parent Exception** | When multiple exceptions share a **common meaning** | `UserNotFoundException extends NotFoundException` |
| **Generic Catch-All (`Exception.class`)** | As a **fallback** for unhandled errors | `@ExceptionHandler(Exception.class)` |


# Q4. does Exception class is parent of all exception class. What are its most useful method and attribute?

### **📌 Is `Exception` the Parent of All Exceptions?**
Yes! ✅ In **Java**, the `Exception` class is a **subclass of `Throwable`**, which means:
- It is the **parent class** of all **checked exceptions**.
- It inherits from `Throwable`, which is the **root class for all exceptions and errors**.

📌 **Hierarchy of Exceptions in Java**:
```
java.lang.Object
 └── java.lang.Throwable  <-- The root class of all exceptions & errors
      ├── java.lang.Exception  <-- Parent of all checked exceptions
      │    ├── IOException
      │    ├── SQLException
      │    ├── ParseException
      │    ├── CustomException (User-defined)
      │    └── ...
      └── java.lang.Error  <-- Used for system-level failures (e.g., OutOfMemoryError)
           ├── StackOverflowError
           ├── OutOfMemoryError
           └── ...
```
---
### **📌 Important Methods of `Exception` Class**
The `Exception` class **inherits methods from `Throwable`**, which provides useful functionalities for debugging errors.

| Method | Description | Example |
|--------|------------|---------|
| `getMessage()` | Returns the **exception message** | `ex.getMessage()` |
| `getCause()` | Returns the **cause of the exception** (if any) | `ex.getCause()` |
| `toString()` | Returns a **detailed string** containing class name and message | `ex.toString()` |
| `printStackTrace()` | Prints the **stack trace** of where the exception occurred | `ex.printStackTrace()` |
| `fillInStackTrace()` | Updates the **stack trace** for rethrowing the exception | `ex.fillInStackTrace()` |
| `getStackTrace()` | Returns an array of **stack trace elements** | `StackTraceElement[] trace = ex.getStackTrace();` |

---
### **📌 Example Usage of `Exception` Methods**
```java
try {
    int result = 10 / 0; // Throws ArithmeticException
} catch (Exception ex) {
    System.out.println("Exception Message: " + ex.getMessage());  // / by zero
    System.out.println("Exception Cause: " + ex.getCause());      // null (no cause)
    System.out.println("Exception Class: " + ex.toString());      // java.lang.ArithmeticException: / by zero
    ex.printStackTrace();  // Prints stack trace
}
```

---
### **📌 Attributes (Instance Variables) of `Throwable`**
Since `Exception` extends `Throwable`, it inherits these important **private attributes**:
1. **`String detailMessage`** → Stores the exception message.
2. **`Throwable cause`** → Stores the cause of the exception.
3. **`StackTraceElement[] stackTrace`** → Stores stack trace information.

These are accessed via getter methods like `getMessage()`, `getCause()`, and `getStackTrace()`.

---
### **📌 Checked vs. Unchecked Exceptions**
| Type | Parent Class | Examples | Checked? |
|------|-------------|----------|---------|
| **Checked Exceptions** | `Exception` | `IOException`, `SQLException`, `FileNotFoundException` | ✅ Yes |
| **Unchecked Exceptions** | `RuntimeException` | `NullPointerException`, `ArithmeticException`, `IndexOutOfBoundsException` | ❌ No |

---
### **📌 Key Takeaways**
✅ `Exception` is the **parent class of all checked exceptions**.  
✅ `Throwable` is the **root class** of all exceptions & errors.  
✅ Useful methods: `getMessage()`, `getCause()`, `printStackTrace()`, `getStackTrace()`.  
✅ You should use `Exception` for checked exceptions and `RuntimeException` for unchecked exceptions.  

Would you like help with a custom exception class? 🚀😊


# Q5. what are the types of exception I can come across in spring boot project development



### **📌 Types of Exceptions in a Spring Boot Project**  
In a **Spring Boot** project, exceptions can occur at various levels, such as **database access, HTTP requests, business logic, and configuration issues**. Here’s a categorized breakdown of exceptions you might encounter:  

---

## **1️⃣ Spring Framework Exceptions**
These exceptions are related to the **Spring container, dependency injection, and bean management**.

| Exception | Cause |
|-----------|-------|
| **`BeanCreationException`** | When a bean fails to be created due to misconfiguration |
| **`NoSuchBeanDefinitionException`** | When a required bean is not found in the Spring context |
| **`BeanInitializationException`** | Issues in bean initialization |
| **`UnsatisfiedDependencyException`** | Missing or incompatible dependencies in the application |

🔹 **Example**  
```java
@Bean
public MyService myService() {
    return new MyService();  // If MyService has dependencies, they must be resolved
}
```

---

## **2️⃣ Web & REST API Exceptions (Spring MVC & Spring Web)**
These exceptions occur when handling **HTTP requests and responses**.

| Exception | Cause |
|-----------|-------|
| **`HttpRequestMethodNotSupportedException`** | Calling an unsupported HTTP method (e.g., `POST` instead of `GET`) |
| **`HttpMediaTypeNotSupportedException`** | Sending a request with an unsupported content type |
| **`MissingServletRequestParameterException`** | A required request parameter is missing |
| **`MethodArgumentNotValidException`** | Validation fails on method arguments (e.g., `@Valid`) |
| **`BindException`** | Data binding failure in request parameters |
| **`ResponseStatusException`** | Custom exception for setting HTTP status code |

🔹 **Example**  
```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody UserDTO user) {
    // If validation fails, it throws MethodArgumentNotValidException
}
```

---

## **3️⃣ Database & JPA Exceptions**
These exceptions occur when interacting with **JPA, Hibernate, and databases**.

| Exception | Cause |
|-----------|-------|
| **`DataIntegrityViolationException`** | Violation of database constraints (e.g., unique constraint, null constraint) |
| **`EmptyResultDataAccessException`** | When no result is found in a query but one was expected |
| **`TransactionSystemException`** | Errors related to transactions, like rollback failures |
| **`JpaSystemException`** | Low-level JPA errors (e.g., entity mapping issues) |
| **`EntityNotFoundException`** | When an entity does not exist but was expected |

🔹 **Example**  
```java
Optional<User> user = userRepository.findById(userId);
if (!user.isPresent()) {
    throw new EntityNotFoundException("User not found with ID: " + userId);
}
```

---

## **4️⃣ Security & Authentication Exceptions**
These exceptions occur when dealing with **Spring Security & authentication mechanisms**.

| Exception | Cause |
|-----------|-------|
| **`AccessDeniedException`** | Unauthorized access to a protected resource |
| **`AuthenticationException`** | Failure in authentication (invalid username/password) |
| **`UsernameNotFoundException`** | When a user is not found in the authentication system |
| **`BadCredentialsException`** | Incorrect username or password |
| **`ExpiredJwtException`** | JWT token has expired |

🔹 **Example**  
```java
if (!passwordEncoder.matches(password, user.getPassword())) {
    throw new BadCredentialsException("Invalid credentials!");
}
```

---

## **5️⃣ Configuration & Property Exceptions**
These exceptions occur due to issues in **Spring Boot configuration files (`application.properties` or `application.yml`)**.

| Exception | Cause |
|-----------|-------|
| **`ConversionFailedException`** | Type mismatch in property conversion |
| **`InvalidPropertyException`** | Accessing a property that does not exist in a configuration file |
| **`ConfigurationPropertiesBindingException`** | Issues in `@ConfigurationProperties` binding |
| **`IllegalStateException`** | Invalid application state (e.g., missing required configuration) |

🔹 **Example**  
```properties
server.port=ABC   # Invalid port, causes ConversionFailedException
```

---

## **6️⃣ File Handling Exceptions**
These exceptions occur when dealing with **file uploads, downloads, and storage**.

| Exception | Cause |
|-----------|-------|
| **`MultipartException`** | Issues in handling multipart file uploads |
| **`FileNotFoundException`** | When the requested file does not exist |
| **`MaxUploadSizeExceededException`** | Uploading a file that exceeds the maximum allowed size |

🔹 **Example**  
```java
if (file.getSize() > MAX_SIZE) {
    throw new MaxUploadSizeExceededException(MAX_SIZE);
}
```

---

## **7️⃣ External API & Networking Exceptions**
These exceptions occur when calling **external REST APIs or network services**.

| Exception | Cause |
|-----------|-------|
| **`RestClientException`** | Generic exception for REST client failures |
| **`ResourceAccessException`** | Failure to access a remote service |
| **`HttpClientErrorException`** | HTTP 4xx errors from an external API |
| **`HttpServerErrorException`** | HTTP 5xx errors from an external API |

🔹 **Example (Using RestTemplate)**  
```java
try {
    restTemplate.getForObject("https://example.com/api", String.class);
} catch (RestClientException e) {
    throw new RuntimeException("Failed to call external API", e);
}
```

---

## **8️⃣ General Java Exceptions**
Apart from Spring Boot exceptions, **general Java exceptions** are also common.

| Exception | Cause |
|-----------|-------|
| **`NullPointerException`** | Accessing a null object reference |
| **`IllegalArgumentException`** | Passing an invalid argument |
| **`NumberFormatException`** | Converting a string to a number fails |
| **`ArrayIndexOutOfBoundsException`** | Accessing an array index that does not exist |

🔹 **Example**  
```java
String str = null;
System.out.println(str.length());  // Throws NullPointerException
```

---

## **📌 How to Handle Exceptions in Spring Boot?**
1️⃣ **Using `@ExceptionHandler` (Local Exception Handling)**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<String> handleEntityNotFound(EntityNotFoundException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
    }
}
```

2️⃣ **Using `@ControllerAdvice` (Global Exception Handling)**
```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneralException(Exception ex) {
        return new ResponseEntity<>("Something went wrong!", HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

3️⃣ **Using `@ResponseStatus`**
```java
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

## **🔥 Summary**
Spring Boot projects encounter exceptions at **various levels**:  
✅ **Spring Framework Exceptions** (e.g., `BeanCreationException`)  
✅ **Web & REST API Exceptions** (e.g., `HttpRequestMethodNotSupportedException`)  
✅ **Database & JPA Exceptions** (e.g., `DataIntegrityViolationException`)  
✅ **Security & Authentication Exceptions** (e.g., `AccessDeniedException`)  
✅ **File Handling Exceptions** (e.g., `MaxUploadSizeExceededException`)  
✅ **External API & Network Exceptions** (e.g., `RestClientException`)  
✅ **General Java Exceptions** (e.g., `NullPointerException`)  

Would you like a **custom exception handling strategy** for your Spring Boot project? 🚀

# Q6. There are 4 custom exception in the java project AccessDenialException , InvalidRequestExcetipn, ResourceNotFoundException , ResourceConflictException. When to throw each one of them ?

Here’s when you should throw each of the custom exceptions in your Java project:

### 1. **AccessDenialException**  
   - **When to throw:**  
     - When a user tries to access a resource or perform an action they don’t have permission for.  
     - When authentication fails (e.g., invalid credentials).  
     - When authorization fails (e.g., insufficient roles or privileges).
   - **Example Scenario:**  
     - A regular user attempts to access an admin-only endpoint.  
   - **Example Code:**  
     ```java
     if (!user.hasPermission("ADMIN")) {
         throw new AccessDenialException("You do not have permission to access this resource.");
     }
     ```

### 2. **InvalidRequestException** (Typo: should be `InvalidRequestException`)  
   - **When to throw:**  
     - When the request has incorrect, malformed, or missing parameters.  
     - When required fields are missing in an API request.  
     - When validation on input data fails.
   - **Example Scenario:**  
     - A user submits a form without filling in the required fields.  
   - **Example Code:**  
     ```java
     if (request.getUsername() == null || request.getUsername().isEmpty()) {
         throw new InvalidRequestException("Username cannot be empty.");
     }
     ```

### 3. **ResourceNotFoundException**  
   - **When to throw:**  
     - When trying to retrieve a resource (e.g., user, file, product) that does not exist.  
     - When an ID or reference to a resource is invalid.  
   - **Example Scenario:**  
     - A user searches for a product that does not exist in the database.  
   - **Example Code:**  
     ```java
     Optional<User> user = userRepository.findById(userId);
     if (!user.isPresent()) {
         throw new ResourceNotFoundException("User with ID " + userId + " not found.");
     }
     ```

### 4. **ResourceConflictException**  
   - **When to throw:**  
     - When an action results in a conflict with existing data.  
     - When trying to create a resource that already exists (e.g., duplicate usernames, emails).  
     - When an update operation causes a conflict (e.g., optimistic locking).  
   - **Example Scenario:**  
     - A user attempts to register with an email that is already in use.  
   - **Example Code:**  
     ```java
     if (userRepository.existsByEmail(email)) {
         throw new ResourceConflictException("Email " + email + " is already registered.");
     }
     ```

### **Summary Table**
| Exception | When to Throw |
|-----------|--------------|
| `AccessDenialException` | User lacks permission or authentication fails. |
| `InvalidRequestException` | Request contains invalid/missing parameters. |
| `ResourceNotFoundException` | Requested resource does not exist. |
| `ResourceConflictException` | Operation conflicts with existing data. |

Let me know if you need any refinements! 🚀
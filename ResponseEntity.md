# Response Entity

📌 ResponseEntity represents the whole HTTP response: `status code`, `headers`, and `body`. As a result, we can use it to fully configure the HTTP response.

If we want to use it, we have to return it from the endpoint; Spring takes care of the rest.

1️⃣ ResponseEntity is a generic type. Consequently, we can use any type as the response body:

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return new ResponseEntity<>("Hello World!", HttpStatus.OK);
}
```
2️⃣ Since we specify the response status programmatically, we can return with different status codes for different scenarios:

```java

@GetMapping("/age")
ResponseEntity<String> age(
  @RequestParam("yearOfBirth") int yearOfBirth) {
 
    if (isInFuture(yearOfBirth)) {
        return new ResponseEntity<>(
          "Year of birth cannot be in the future", 
          HttpStatus.BAD_REQUEST);
    }

    return new ResponseEntity<>(
      "Your age is " + calculateAge(yearOfBirth), 
      HttpStatus.OK);
}
```

3️⃣ Additionally, we can set HTTP headers:
```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    HttpHeaders headers = new HttpHeaders();
    headers.add("Custom-Header", "foo");
        
    return new ResponseEntity<>(
      "Custom header set", headers, HttpStatus.OK);
}
```
4️⃣ Furthermore, ResponseEntity provides two nested builder interfaces: HeadersBuilder and its subinterface, BodyBuilder. Therefore, we can access their capabilities through the static methods of ResponseEntity.

The simplest case is a response with a body and HTTP 200 response code:

```java
@GetMapping("/hello")
ResponseEntity<String> hello() {
    return ResponseEntity.ok("Hello World!");
}
```
5️⃣ For the most popular HTTP status codes we get static methods:

```java
BodyBuilder accepted();
BodyBuilder badRequest();
BodyBuilder created(java.net.URI location);
HeadersBuilder<?> noContent();
HeadersBuilder<?> notFound();
BodyBuilder ok();
```
6️⃣ In addition, we can use the BodyBuilder status(HttpStatus status) and the BodyBuilder status(int status) methods to set any HTTP status.

Finally, with ResponseEntity<T> BodyBuilder.body(T body) we can set the HTTP response body:

```java

@GetMapping("/age")
ResponseEntity<String> age(@RequestParam("yearOfBirth") int yearOfBirth) {
    if (isInFuture(yearOfBirth)) {
        return ResponseEntity.badRequest()
            .body("Year of birth cannot be in the future");
    }

    return ResponseEntity.status(HttpStatus.OK)
        .body("Your age is " + calculateAge(yearOfBirth));
}
```

7️⃣ We can also set custom headers:

```java
@GetMapping("/customHeader")
ResponseEntity<String> customHeader() {
    return ResponseEntity.ok()
        .header("Custom-Header", "foo")
        .body("Custom header set");
}
```

Since BodyBuilder.body() returns a ResponseEntity instead of BodyBuilder, it should be the last call.

Note that with HeaderBuilder we can’t set any properties of the response body.

While returning ResponseEntity<T> object from the controller, we might get an exception or error while processing the request and would like to return error-related information to the user represented as some other type, let’s say E.

Spring 3.2 brings support for a global @ExceptionHandler with the new @ControllerAdvice annotation, which handles these kinds of scenarios. For in-depth details, refer to our existing article here.

While ResponseEntity is very powerful, we shouldn’t overuse it. In simple cases, there are other options that satisfy our needs and they result in much cleaner code.
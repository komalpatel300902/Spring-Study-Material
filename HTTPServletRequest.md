# Q1. What are the useful function HTTPServletRequest has ?
`HttpServletRequest` in Java provides several useful methods for handling HTTP requests in a servlet. Here are some of the most commonly used methods:

### **1. Request Information**
- `getMethod()` – Returns the HTTP method (e.g., `GET`, `POST`, `PUT`, `DELETE`).
- `getRequestURI()` – Returns the part of the request URL after the domain name.
- `getRequestURL()` – Returns the full URL of the request.
- `getContextPath()` – Returns the context path of the web application.
- `getServletPath()` – Returns the servlet path.
- `getProtocol()` – Returns the protocol (e.g., `HTTP/1.1`).
- `getScheme()` – Returns the scheme (`http` or `https`).
- `getServerName()` – Returns the server name.
- `getServerPort()` – Returns the port number.
- `getRemoteAddr()` – Returns the IP address of the client.
- `getRemoteHost()` – Returns the hostname of the client.

### **2. Parameter Handling**
- `getParameter(String name)` – Retrieves a request parameter.
- `getParameterMap()` – Returns a `Map` of parameter names and values.
- `getParameterNames()` – Returns an `Enumeration` of parameter names.
- `getParameterValues(String name)` – Returns an array of values for a parameter (useful for checkboxes or multiple selections).

**Example:**
```java
String username = request.getParameter("username");
String[] roles = request.getParameterValues("roles");
```

### **3. Header Handling**
- `getHeader(String name)` – Retrieves the value of a specific header.
- `getHeaders(String name)` – Returns all values of a specific header.
- `getHeaderNames()` – Returns an `Enumeration` of all header names.
- `getIntHeader(String name)` – Returns an integer value of a header.
- `getDateHeader(String name)` – Returns a date value of a header.

**Example:**
```java
String userAgent = request.getHeader("User-Agent");
```

### **4. Session Management**
- `getSession()` – Returns the current session or creates a new one.
- `getSession(boolean create)` – Returns the current session, or `null` if `create` is `false` and no session exists.
- `isRequestedSessionIdValid()` – Checks if the session ID is still valid.

**Example:**
```java
HttpSession session = request.getSession();
session.setAttribute("username", "JohnDoe");
```

### **5. Request Body and Input Stream**
- `getReader()` – Returns a `BufferedReader` to read the request body.
- `getInputStream()` – Returns a `ServletInputStream` to read binary data (e.g., file uploads).

**Example (reading request body):**
```java
BufferedReader reader = request.getReader();
String line;
while ((line = reader.readLine()) != null) {
    System.out.println(line);
}
```

### **6. Cookies Handling**
- `getCookies()` – Returns an array of `Cookie` objects.

**Example:**
```java
Cookie[] cookies = request.getCookies();
if (cookies != null) {
    for (Cookie cookie : cookies) {
        System.out.println(cookie.getName() + ": " + cookie.getValue());
    }
}
```

### **7. Request Attributes**
- `getAttribute(String name)` – Retrieves an attribute set by a servlet.
- `setAttribute(String name, Object value)` – Stores an attribute for later use.
- `removeAttribute(String name)` – Removes an attribute.

**Example:**
```java
request.setAttribute("message", "Welcome!");
String message = (String) request.getAttribute("message");
```

### **8. Request Forwarding & Redirection**
- `getRequestDispatcher(String path)` – Returns a `RequestDispatcher` for forwarding requests.
- `getServletContext()` – Returns the `ServletContext`.

**Example (forwarding request):**
```java
RequestDispatcher dispatcher = request.getRequestDispatcher("/home.jsp");
dispatcher.forward(request, response);
```

### **9. Authentication & Security**
- `getRemoteUser()` – Returns the name of the authenticated user.
- `getUserPrincipal()` – Returns the `Principal` object of the logged-in user.
- `isUserInRole(String role)` – Checks if the user has a specific role.
- `getAuthType()` – Returns the authentication type (e.g., `BASIC`, `DIGEST`, `FORM`).

**Example:**
```java
if (request.isUserInRole("ADMIN")) {
    System.out.println("User is an admin.");
}
```

### **10. Encoding & Character Handling**
- `getCharacterEncoding()` – Returns the request character encoding.
- `setCharacterEncoding(String encoding)` – Sets the character encoding for the request.
- `getContentType()` – Returns the MIME type of the request body.

**Example:**
```java
request.setCharacterEncoding("UTF-8");
```

---

### **Summary Table**

| Category | Method |
|----------|--------|
| Request Info | `getMethod()`, `getRequestURI()`, `getRequestURL()`, `getContextPath()`, `getRemoteAddr()` |
| Parameters | `getParameter()`, `getParameterMap()`, `getParameterValues()` |
| Headers | `getHeader()`, `getHeaderNames()`, `getIntHeader()`, `getDateHeader()` |
| Session | `getSession()`, `getSession(boolean create)`, `isRequestedSessionIdValid()` |
| Request Body | `getReader()`, `getInputStream()` |
| Cookies | `getCookies()` |
| Attributes | `getAttribute()`, `setAttribute()`, `removeAttribute()` |
| Forwarding | `getRequestDispatcher()` |
| Authentication | `getRemoteUser()`, `isUserInRole()`, `getAuthType()` |
| Encoding | `getCharacterEncoding()`, `setCharacterEncoding()`, `getContentType()` |

Would you like examples for specific use cases? 🚀
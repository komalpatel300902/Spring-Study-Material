## Q. If JSON input is send what data types can accept it ?

If you want to use `@RequestBody` to take JSON input, the data type of the method parameter can be one of the following:

---

## **✅ Acceptable Data Types for `@RequestBody`**
Here’s your updated table with the **Preference** column added:  

---

## **✅ Acceptable Data Types for `@RequestBody`**
| Data Type | Description | Example | Preference |
|-----------|------------|---------|------------|
| **Entity Class (`User`)** | Maps JSON to a Java object using Jackson | `@RequestBody User user` | ❌ Least Preferred |
| **DTO Class (`UserUpdateDTO`)** | Custom object used for updates to avoid exposing the full entity | `@RequestBody UserUpdateDTO dto` | ✅ Highly Preferred |
| **Map (`Map<String, Object>`)** | Allows dynamic updates with key-value pairs | `@RequestBody Map<String, Object> updates` | ⚠️ Moderately Preferred |
| **List (`List<User>`)** | Accepts a list of objects | `@RequestBody List<User> users` | ⚠️ Moderately Preferred |
| **String (`String`)** | Accepts raw JSON as a string | `@RequestBody String json` | ❌ Least Preferred |
| **JsonNode (`JsonNode`)** | Handles flexible JSON structures dynamically | `@RequestBody JsonNode jsonNode` | ⚠️ Moderately Preferred |
| **List (`List<Map<String, Object>>`)** | Accepts a list of dictionaries for dynamic updates | `@RequestBody List<Map<String, Object>> users` | ⚠️ Moderately Preferred |
| **List (`List<SampleDTO>`)** | Accepts a list of DTO objects | `@RequestBody List<SampleDTO> users` | ✅ Highly Preferred |


---

## **1️⃣ Using an Entity Class (`User`)**
- **Best for structured input where all fields are known.**
```java
@PutMapping("/{id}")
public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User updatedUser) {
    Optional<User> optionalUser = userRepository.findById(id);
    if (optionalUser.isPresent()) {
        User existingUser = optionalUser.get();
        existingUser.setName(updatedUser.getName());
        existingUser.setEmail(updatedUser.getEmail());
        return ResponseEntity.ok(userRepository.save(existingUser));
    }
    return ResponseEntity.notFound().build();
}
```
📌 **Example JSON Input Using PostMan**
```json
{
  "name": "John Doe",
  "email": "john@example.com"
}
```

---

## **2️⃣ Using a DTO (Data Transfer Object)**
- **Best for updating specific fields without exposing full entity.**
```java

public class SampleDTO {
    int id;
    String name;
    String mobile;
    String email;

    // Add getter setter for above variable  
}

```
```java
@PostMapping("/Checking/RestBodyInputType")
void checkingInput(@RequestBody @NotNull SampleDTO data){
    System.out.println(data);
    System.out.println(data.getClass());
    System.out.println(data.getId());
    System.out.println(data.getName());
    System.out.println(data.getEmail());
    System.out.println(data.getMobile());

}
```
📌 **Example JSON Input Using PostMan**
```json
{
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

}
```
`Output`
```cmd
com.example.Restaurant.DTO.SampleDTO@951c94
class com.example.Restaurant.DTO.SampleDTO
12
Komal
qwerty14march@gmail.com
1232324354
```
---

## **3️⃣ Using `Map<String, Object>` for Partial Updates**
- **Best for flexible, dynamic updates where any field can be changed.**
```java
@PostMapping("/Checking/RestBodyInputType")
void checkingInput(@RequestBody @NotNull Map<String, String> data){
    System.out.println(data);
    System.out.println(data.getClass());
    System.out.println(data.get("id"));
    System.out.println(data.get("name"));
    System.out.println(data.get("email"));
    System.out.println(data.get("mobile"));

}
```
📌 **Example JSON Input Using PostMan**
```json
{
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

}
```
`Output`
```cmd
com.example.Restaurant.DTO.SampleDTO@951c94
class com.example.Restaurant.DTO.SampleDTO
12
Komal
qwerty14march@gmail.com
1232324354
```

---

## **4️⃣ Using a List of Objects (`List<User>`)**
- **Best for bulk updates.**
```java
@PutMapping("/bulk-update")
public ResponseEntity<List<User>> updateUsers(@RequestBody List<User> users) {
    List<User> updatedUsers = userRepository.saveAll(users);
    return ResponseEntity.ok(updatedUsers);
}
```
📌 **Example JSON Input Using PostMan**
```json
[
  { "id": 1, "name": "Alice" },
  { "id": 2, "email": "bob@example.com" }
]
```

---

## **5️⃣ Using `String` to Accept Raw JSON**
- **Best when you want to process raw JSON manually.**
```java
@PostMapping("/Checking/RestBodyInputType")
    void checkingInput(@RequestBody @NotNull String records){
        System.out.println(records);
    }
```
📌 **Example JSON Input Using PostMan**
```json
[
    {
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

    },
    {
    "id": 13,
    "name": "Raven",
    "mobile": "9087243513",
    "email": "raven14march@gmail.com"
    }
]
```
`Output`
```cmd
[
    {
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

    },
    {
    "id": 13,
    "name": "Raven",
    "mobile": "9087243513",
    "email": "raven14march@gmail.com"
    }
]

```
---

## **6️⃣ Using `JsonNode` (Jackson)**
- **Best for handling nested JSON structures dynamically.**

`Case I` : Input is List of Json.

```java
@PostMapping("/Checking/RestBodyInputType")
    void checkingInput(@RequestBody @NotNull JsonNode records){
        System.out.println(records);
        System.out.println(records.getClass());
        System.out.println(records.size());
        System.out.println(records.get(0));
        System.out.println(records.get(1));
        System.out.println(records.get(0).get("id"));
        System.out.println(records.get(0).get("name"));
        System.out.println(records.get(1).get("id"));
        System.out.println(records.get(1).get("name"));
        System.out.println(records.get(0).get(0));
        System.out.println(records.get(1).get(0));
    }
```
📌 **Example JSON Input Using PostMan**
```json

[
    {
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

    },
    {
    "id": 13,
    "name": "Raven",
    "mobile": "9087243513",
    "email": "raven14march@gmail.com"
    }
]
```
---
`Output`
```cmd
class com.fasterxml.jackson.databind.node.ArrayNode
2
{"id":12,"name":"Komal","mobile":"1232324354","email":"qwerty14march@gmail.com"}
{"id":13,"name":"Raven","mobile":"9087243513","email":"raven14march@gmail.com"}
12
"Komal"
13
"Raven"
null
null
```
`Case 2` : Input is Json
```java
    @PostMapping("/Checking/RestBodyInputType")
    void checkingInput(@RequestBody @NotNull JsonNode records){
        System.out.println(records);
        System.out.println(records.getClass());
        System.out.println(records.size());
        System.out.println(records.get("id"));
        System.out.println(records.get("name"));
        System.out.println(records.get(0));
        System.out.println(records.get(1));
    }
```
📌 **Example JSON Input Using PostMan**
```json
{
"id": 12,
"name": "Komal",
"mobile": "1232324354",
"email": "qwerty14march@gmail.com"

}
```
---
`Output`
```cmd
{"id":12,"name":"Komal","mobile":"1232324354","email":"qwerty14march@gmail.com"}
class com.fasterxml.jackson.databind.node.ObjectNode
4
12
"Komal"
12
"Komal"
null
null
```

---
## **7️⃣ Using List<Map<String, Object>>**
- **Best for handling the multiple inputs**
```java
@PostMapping("/Checking/RestBodyInputType")
void checkingInput(@RequestBody @NotNull List<Map<String, String>> records){
    for (int i = 0; i < records.size(); i++) {
        Map<String, String> data = records.get(i);

        System.out.println(data);
        System.out.println(data.getClass());
        System.out.println(data.get("id"));
        System.out.println(data.get("name"));
        System.out.println(data.get("email"));
        System.out.println(data.get("mobile"));
    }
}
```
📌 **Example JSON Input Using PostMan**
```json
[
    {
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

    },
    {
    "id": 13,
    "name": "Raven",
    "mobile": "9087243513",
    "email": "raven14march@gmail.com"
    }
]
```
`Output`
```cmd
{id=12, name=Komal, mobile=1232324354, email=qwerty14march@gmail.com}
class java.util.LinkedHashMap
12
Komal
qwerty14march@gmail.com
1232324354
{id=13, name=Raven, mobile=9087243513, email=raven14march@gmail.com}
class java.util.LinkedHashMap
13
Raven
raven14march@gmail.com
9087243513
```
---

## **8️⃣ Using `List<SampleDTO>`**
- **Best for handling the multiple inputs**
```java
public class SampleDTO {
    int id;
    String name;
    String mobile;
    String email;

    // Add getter setter for above variable  
}
```
```java
@PostMapping("/Checking/RestBodyInputType")
    void checkingInput(@RequestBody @NotNull List<SampleDTO> records){
        for (int i = 0; i < records.size(); i++) {
            SampleDTO data = records.get(i);

            System.out.println(data);
            System.out.println(data.getClass());
            System.out.println(data.getId());
            System.out.println(data.getName());
            System.out.println(data.getEmail());
            System.out.println(data.getMobile());
        }
    }
```
📌 **Example JSON Input Using PostMan**
```json
[
    {
    "id": 12,
    "name": "Komal",
    "mobile": "1232324354",
    "email": "qwerty14march@gmail.com"

    },
    {
    "id": 13,
    "name": "Raven",
    "mobile": "9087243513",
    "email": "raven14march@gmail.com"
    }
]
```
`Output`
```cmd
com.example.Restaurant.DTO.SampleDTO@b95014
class com.example.Restaurant.DTO.SampleDTO
12
Komal
qwerty14march@gmail.com
1232324354
com.example.Restaurant.DTO.SampleDTO@1374678
class com.example.Restaurant.DTO.SampleDTO
13
Raven
raven14march@gmail.com
9087243513
```
## **✅ Summary: Best Data Type for Your Use Case**
| Use Case | Data Type |
|----------|----------|
| Full object update | `@RequestBody User` |
| Partial update (specific fields) | `@RequestBody UserUpdateDTO` |
| Dynamic fields update | `@RequestBody Map<String, Object>` |
| Bulk update | `@RequestBody List<User>` |
| Raw JSON string processing | `@RequestBody String` |
| Handling complex/nested JSON | `@RequestBody JsonNode` |

---

Would you like help adding **field validation** (e.g., checking email format, phone length)? 🚀
# Java
- [final keyword in java | final variable, final method and final class in java](https://youtu.be/i9llT4A43hg?si=4hrmTmqhZ3ddqk3E)
- [Optionals In Java - Simple Tutorial](https://youtu.be/vKVzRbsMnTQ?si=g07vx6TdqSgxye4G)
- 
# Spring-Study-Material
### Exception Handling 
- [Exception Handling in Spring Boot REST API Explained With Demonstration](https://youtu.be/L2o485T70Do?si=UYyw2zP38lbfbuau)
  
Q. if we want to home/AllRestaurant and home/AllRestaurant/{variable} we have to create 2 diffrent get method  ? or we can do it in same path    
ChatGPT said:
You don't need to create two separate methods. You can handle both /home/AllRestaurant and /home/AllRestaurant/{restaurantName} in the same method by making the @PathVariable optional.

`Solution:`

Use @RequestMapping with a flexible path and @PathVariable(required = false):

```java

@GetMapping({"/home/AllRestaurant", "/home/AllRestaurant/{restaurantName}"})
public List<RestaurantOutDTO> fetchRestaurantData(@PathVariable(required = false) String restaurantName) {
    if (restaurantName == null || restaurantName.isEmpty()) {
        // Fetch all restaurants
        return restaurantService.getAllRestaurants();
    } else {
        // Fetch restaurant by name
        return restaurantService.getRestaurantByName(restaurantName);
    }
}
```

Explanation:
The { "/home/AllRestaurant", "/home/AllRestaurant/{restaurantName}" } syntax allows both URLs to be handled in the same method.
@PathVariable(required = false) makes restaurantName optional.
If restaurantName is null or empty, return all restaurants.
Otherwise, return the data for the specified restaurant.
This way, your API remains clean and efficient with a single method. 🚀

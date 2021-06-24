## web application
For this learning outcome I have helped creating our group project application. You can find a link to the repositories [here](https://github.com/Digital-Menu-FHICT-S3)
### Prove
I will show my proof by giving a sample for test, frontend and backend.
#### Tests
This is an integration tests I have made for the Menu service:
```java
@Test
  void shouldGetDishesByCategoryName() throws Exception {
    mvc.perform(get("/menu/dishes/category/Vlees")
      .contentType(MediaType.APPLICATION_JSON)
      .accept(MediaType.APPLICATION_JSON))
      .andExpect(status().isOk())
      .andExpect(jsonPath("$[0].dishId").value(1L))
      .andExpect(jsonPath("$[0].categoryId").value(1L))
      .andExpect(jsonPath("$[0].name").value("Biefstuk"))
      .andExpect(jsonPath("$[0].price").value(7.00))
      .andExpect(jsonPath("$[0].description").value("Een stuk biefstuk"))
      .andExpect(jsonPath("$[0].imageUrl").value("https://fontys.nl/upload/50716580-70d3-4c39-86dd-4237e0166f38_image6483968316988970112.png"))
      .andExpect(jsonPath("$[1].dishId").value(2L))
      .andExpect(jsonPath("$[1].categoryId").value(1L))
      .andExpect(jsonPath("$[1].name").value("Kip"))
      .andExpect(jsonPath("$[1].price").value(5.00))
      .andExpect(jsonPath("$[1].description").value("Een stuk kip"))
      .andExpect(jsonPath("$[1].imageUrl").value("https://fontys.nl/upload/50716580-70d3-4c39-86dd-4237e0166f38_image6483968316988970112.png"));
  }
```
This test checks if the correct dishes are being pulled from the database by category name "Vlees"
#### frontend
This is an function I made in our Online-menu-view:
```javascript
const changeAmount = (index, dishId, amount) => {
  updateSession(dishId, amount)
  dishes[index].amount = amount
  setDishes(dishes.filter(dish => dish.amount > 0));
}
```
This function takes an index(the iteration of the array), an dishId(the id of the current dish) and an amount(the amount of the current dish). First it call upon the updateSession function which will update the local storage, After that it wil set the new amount to the current dish and last it will filter the array by filtering out all dishes with an amount of 0 or less.

#### backend
This an function I made in our Menu-service:
```java
@PostMapping("/all-in-shopping-cart")
    public List<Dish> getAllDishes(@RequestBody List<Long> dishIds) {
        return dishService.getAllDishes(dishIds);
    }
```
This function returns a `JSON` array of dishes. It gets a List of longs out of the RequestBody called "dishIds". dishIds is then given with a function call which will make a call onto the database and return all data that corresponds to the dishIds given. The idea is that a request from the frontend is being send towards this function and this request is from the shoppingcart which has a list of dishIds that they send over to get all the data from.

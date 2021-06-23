## Software quality
### What is it?
With software quality we mean the assurance of the quality of the software. This is the process of testing your code so that if something would break you will be able to see it fast by checking the test instead of having to debug for the bug or even worse, not finding the bug until after deployment. This ofcourse would be very troublesome. Speaking of tests, there are a lot different sort of tests. To name a few:

* Unit tests
* Acceptation tests
* Integration tests
* static code analysis
* front to back testing

As you can see there are a lot of different types of tests and this is just a fraction. In this document we are mainly going to focus on: `Integration tests`, `Unit tests` and `static code analysis`.

## Personal development
I have learned a lot about tests this semester. I have learned how to create integration tests and how to implement static code analysis and I leaned how to create tests for Spring applications. I have learned how to implement `inegration tests` because of my group project and I learned how to use `static code analysis` from a fellow classmate. Now I will show some examples of integration tests I have implemented:
### React
Sadly there wasn't much I could test in the `React` frontend because almost all functions were related to http request. So I will like to to show you a example from thr group project instead which I tried to implement in my own project but wouldn't work because of how I implemented `Keycloak` in my application. I tried to create a test to see if the application loads without failing. This is how whe did it in the group project:

```javascript
test('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  ReactDOM.unmountComponentAtNode(div);
});
```

This will inject a div in the app class and see if it still renders. This would check if the application would render without crashing. I tried to implement this in my own project aswell but it wouldn't work because my app class has a dependency on `keycloak` which won't be able to load in in a test enviroment.

I did implement `static code analysis` into my react front-end which looks like this:
![front-end-sonar-cload](https://github.com/KevinOomenTheDeveloper/Fontys-semester-3-portolio/blob/main/Individual-project/Images/front-end-sonar-cloud.png)

I implemented this by using `Sonarcload` and linking my Github account with the service, so now I can very easily select repositories which I want to be analyzed on `Sonarcloud`

### Spring
Ofcourse I will have to test my `Spring` back-end aswell. But first I had to configure my testing environment for my `Spring` back-end. The first thing we will have to do is add some dependecies to my pom.xml file like so:
```java
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>test</scope>
</dependency>
```
After I added the dependecies I had to create a mock database for testing purposes like so(application.propperties):
```java
spring.datasource.url=jdbc:h2:~/chat-service-db;DB_CLOSE_DELAY=-1
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.generate-ddl=true
spring.jpa.hibernate.ddl-auto=create-drop
spring.datasource.initialization-mode=always
```
This code will create a mocked `h2 database` that will be created when tests are run. This is still an empty database so to create some data for the database we can create a `SQL` file that will be executed automatically like so(data.sql):
```sql
INSERT INTO MESSAGE (MESSAGE_ID, DESCRIPTION, USER_ID) VALUES
(1L,'test1','1'),
(2L,'test2','2'),
(3L,'test3','1');
```
The file hierarchy will look like this:

![back-end-file-hierarchy](https://github.com/KevinOomenTheDeveloper/Fontys-semester-3-portolio/blob/main/Individual-project/Images/back-end-file-hierarchy.png)

Now that I have created the testing environment I can start creating some `unit tests` for my `Spring` backend like so:
```java
@Test
public void shouldGetLogs(){
  List<Message > expectedMessages = new ArrayList<Message>();
  expectedMessages.add(new Message(1L, "test1", "1"));
  expectedMessages.add(new Message(3L, "test3", "1"));
  assertThat(messageService.getLogs("1")).isEqualTo(expectedMessages);
}
```

This `unit test` checks if the correct data is being pulled from the mocked `h2 database`. If this is the case it will succeed if not it will fail.

Up next I wanted to implement `integration tests` into the project but because of how I implemented `Keycloak` in the project it was proven difficult to `integration test`. In the code I try to get the `Keycloak` user id token from the `HttpServletRequest` which is not a problem at first glance but when you try to test it it's a different story. First of i tried to mock the `HttpServletRequest` which succeeded, but i couldn't find a way to add a `Keycloak` token to the HttpServletRequest. I tried to mock the `Keycloak` token which worked but the data which is supposed to be in it wouldn't generate nor would I have been able to set this into the keycloak token. so after sometime I gave up and stopped wasting my time with it. For the sake of an example i'll show the way I implemented such tests in the group project:
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
This test tests if the correct dishes are given whenever dishes are requested by category name. If the mocked database data that is being pulled isn't the same as expected test results the test will fail. But if this is the case, the test will succeed.

lastly I have also implemented `static code analysis` in my `Spring` back-end which looks like this:

![back-end-sonar-cloud](https://github.com/KevinOomenTheDeveloper/Fontys-semester-3-portolio/blob/main/Individual-project/Images/back-end-sonar-cloud.png)

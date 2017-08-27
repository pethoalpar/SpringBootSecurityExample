<h1>Spring boot security example with thymeleaf</h1>

<h3>Add dependencies to pom.xml</h3>

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
            <version>1.5.6.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>1.5.6.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
            <version>1.5.6.RELEASE</version>
        </dependency>
    </dependencies>
``` 

<h3>Main.java<h3>

```java
@SpringBootApplication
public class Main implements CommandLineRunner{

    public static void main(String [] args){
        SpringApplication.run(Main.class, args);
    }

    public void run(String... strings) throws Exception {

    }
}
```

<h3>Config</h3>

```java
@Controller
public class SecurityConfig extends WebSecurityConfigurerAdapter{

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .authorizeRequests()
                .antMatchers("/","/home","/about").permitAll()
                .antMatchers("/admin/**").hasAnyRole("ADMIN")
                .antMatchers("/user/**").hasAnyRole("USER","ADMIN")
                .anyRequest().authenticated()
                .and()
                .formLogin().loginPage("/login").permitAll()
                .and()
                .logout().permitAll()
                .and()
                .exceptionHandling().accessDeniedPage("/403");
    }

    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception{
        auth.inMemoryAuthentication()
                .withUser("user").password("password").roles("USER")
                .and()
                .withUser("admin").password("password").roles("ADMIN");
    }
}
```

<h3>Controller</h3>

```java
@Controller
public class DefaultController {

    @GetMapping("/")
    public String home(){
        return "/home";
    }

    @GetMapping("/home")
    public String home1(){
        return "/home";
    }

    @GetMapping("/admin")
    public String admin(){
        return "/admin";
    }

    @GetMapping("/user")
    public String user (){
        return "/user";
    }

    @GetMapping("/about")
    public String about(){
        return "/about";
    }

    @GetMapping("/login")
    public String login(){
        return "/login";
    }

    @GetMapping("/403")
    public String invalidUser(){
        return "/invalidUser";
    }
}
```

<h2>resources/templates</h2>

<h3>About page</h3>

```html
<!DOCTYPE html>
<html>

<body>
<div class="starter-template">
    <h1>Normal page (No login need)</h1>
</div>

</body>
</html>
```

<h3>Admin page</h3>

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="starter-template">
    <h1>Admin page (Login need)</h1>
    <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
    <form th:action="@{/logout}" method="post">
        <input type="submit" value="Sign Out"/>
    </form>
</div>
</body>
</html>
```

<h3>Home page</h3>

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="starter-template">
    <h1>Spring security</h1>
    <h2>1. <a th:href="@{/admin}">Admin page</a></h2>
    <h2>1. <a th:href="@{/user}">User page</a></h2>
    <h2>1. <a th:href="@{/login}">Login page</a></h2>
    <h2>1. <a th:href="@{/about}">Not protected page</a></h2>
</div>
</body>
</html>
```

<h3>User not authenticated page</h3>

```html
<!DOCTYPE html>
<html>
<body>
<div class="starter-template">
    <h1>You do not have access</h1>
</div>
</body>
</html>
```

<h3>Login page</h3>

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
<body>
    <form th:action="@{/login}" method="post">
        <fieldset>
            <h1>Please sign in</h1>

            <div th:if="${param.error}">
                <div class="alert alert-danger">
                    Invalid user name and password!
                </div>
            </div>

            <div th:if="${param.logout}">
                <div class="alert alert-info">
                    You have been logged out!
                </div>
            </div>

            <div class="form-group">
                <input type="text" name="username" id="username" class="form-control input-lg"
                    placeholder="UserName" required="true" autofocus="true"/>
            </div>

            <div class="form-group">
                <input type="password" name="password" id="password" class="form-control input-lg"
                       placeholder="Password" required="true"/>
            </div>

            <div class="row">
                <input type="submit" class="btn btn-lg btn-primary btn-block" value="Sign In"/>
            </div>
        </fieldset>
    </form>
</body>
</html>
```

<h3>User page</h3>

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
<div class="starter-template">
    <h1>User page (Login need)</h1>
    <h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
    <form th:action="@{/logout}" method="post">
        <input type="submit" value="Sign Out"/>
    </form>
</div>
</body>
</html>
```
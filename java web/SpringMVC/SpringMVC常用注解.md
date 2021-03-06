###### 20160425  

## SpringMVC常用注解  

##### 一、@RequestMapping  
RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。@RequestMapping注解有六个属性。  
###### （1）value, method    
* value ：   指定请求的实际地址，指定的地址可以是URI Template 模式    
* method ： 指定请求的method类型    
```java  
@RequestMapping(value="/login", method = RequestMethod.GET)
public String login() {
    return "login";
}
```  

###### (2) consumes, produces  
* consumes ：指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;  
* produces : 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回    

###### POST请求   
```java  
@RequestMapping(value="/login", method = RequestMethod.POST,
                consumes = "application/json")
public String add() {
    return "login";
}
```  
###### GET请求   
```java  
@RequestMapping(value="/login", method = RequestMethod.GET,
                produces = "application/json")
public String add() {
    return "login";
}
```  

###### （3） params, headers  
* params ： 指定request中必须包含某些参数值是，才让该方法处理。  
* headers ： 指定request中必须包含某些指定的header值，才能让该方法处理请求。   

###### 请求参数限制  
```java  
@RequestMapping（value="/add", method = RequestMethod.POST,
           params={"empolyee", "confirm=true"）
public String add() {•••}
```  
###### 请求头限制  
```java  
@RequestMapping(value="/add", method = RequestMethod.GET,
               header={"X-Client","content-type=text/*"})
public String add() {•••}
```     

##### 二、@PathVariable   
将请求URL中的模板变量的值映射到功能处理方法的参数上。   
```java  
@RequestMapping(value = "/user/{userId}", method = RequestMethod.POST)   
public void delete(@PathVariable(value = "userId") Integer userId) {
	//operation
}  
```  
###### 请求URL：` http://localhost:8080/user/123 `  


##### 三、@RequestParam   
该注解可以用来获取请求参数   
###### 方式一：  
###### 前端请求     
```java  
<a href="/test/user?username=hh&address=123">testParam</a>  
```   
###### 后台Controller   
```java   
@Controller
@RequestMapping("/test")
public class TestController {

    @RequestMapping(value = "/user", method = RequestMethod.GET)
    public void testParam (@RequestParam("username") String username,
                           @RequestParam("address") String address) {
        System.out.println("username:"+username+";address:"+address);

    }
}
```   
###### 前端请求   
```html  
<form action="/test/user" method="post" accept-charset="UTF-8">
	用户名：<input type="text" name="username"/><br>
	地址：<input type="text" name="address"/> <br>
	<button type="submit">提交</button>
</form>  
```  
###### 后台Controller  
```java  
@Controller
@RequestMapping("/test")
public class TestController {

    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public void testParam (@RequestParam("username") String username,
                           @RequestParam("address") String address) {
        System.out.println("username:"+username+";address:"+address);

    }
}  
```  
###### @RequestParam常用参数   
* value ： 请求参数的名字，如 ` @RequestParam(value = "username") `  
* required : 标注请求参数是否为必须，默认是true    
* defaultValue ：默认值，表示如果请求中没有同名参数时的默认值   

###### 注意：建议用包装类型代替原子类型，当允许参数为空时使用包装类型可以减少出错情况   

##### 四、@RequestBody   
 将HTTP请求正文转换为适合的HttpMessageConverter对象。   
 使用@RequestBody和@ResponseBody时一定需要注意，因为一不小心就会出现415错误，即请求实体无法正确映射，具体内容参考上一节。    
在处理请求参数时，使用@RequestBody比使用@RequestParam更具有优势，更加灵活。尤其是在传多个参数而且与实体相对应时，使用@RequestBody更加方便。  
###### 前端JS   
```java  
 var data = {
     username : username,
     password : password
 };

 data = JSON.stringify(data);

 $.ajax({
     type: "POST",
     contentType: "application/json",
     dataType: "json",
     url: "/test/user",
     data: data,
     success: function (data) {
         alert("成功");
     },
     error: function (data) {
         alert("失败");
     }
 });
```   
###### 后台Controller接受   
```java  
@Controller
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public void addUser (@RequestBody User user) {
        System.out.println(user);
    }
}
```  
##### 五、@ResponseBody   
将内容或对象作为 HTTP 响应正文返回，并调用适合HttpMessageConverter的Adapter转换对象，写入输出流。     
###### 前端JS  
```java  
var data = {
    username : username,
    password : password
};

data = JSON.stringify(data);

$.ajax({
    type: "POST",
    contentType: "application/json",
    dataType: "json",
    url: "/test/user",
    data: data,
    success: function (data) {
        alert("succeed");
    },
    error: function (data) {
        alert("failed");
    }
});
```   
###### 后台Controller  
```java  
@Controller
@RequestMapping("/test")
public class TestController {

    @RequestMapping(value = "/user", method = RequestMethod.POST)
    public @ResponseBody String addUser (@RequestBody User user) {

        if (null != user) {
            return "{\"SUCCESS\":true}";
        } else {
            return "{\"SUCCESS\":false}";
        }
    }
}
```  




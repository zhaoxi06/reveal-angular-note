大多数前端应用都是通过HTTP协议与后端服务器通讯。现代浏览器支持使用两种不同的api发起http请求：XMLHttpRequest接口和fetch()api。
## 从服务器获取数据
### 请求带类型的响应
可以将HttpClient请求结构化，以声明响应对象的类型。要指定响应对象类型，首先要定义一个具有所需属性的接口（使用接口，而不是类，因为响应不能自动转换为类的实例）。
```
export interface Config {
    heroesUrl: string;
    textfile: string;
}

// 使用
this.http.get<Config>(this.configUrl);
```
要访问接口中定义的属性，必须将获得的对象显式转换为所需的响应类型。例如：
```
.subscribe(data => this.config = {
    heroesUrl: (data as any).heroesUrl,
    textfile: (data as any).textfile,
});
```

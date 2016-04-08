# Gbeta ![build status](https://travis-ci.org/yyrdl/Gbeta.svg?branch=master)
Gbeta是一个Express 风格的web框架，性能上比martini好
```
BenchmarkgbetaSingleRoute   2000000      710 ns/op      41 B/op    3 allocs/op
BenchmarkMartiniSingleRoute    100000    12038 ns/op     464 B/op   13 allocs/op
BenchmarkGbetaMutipleRoute     3000   410271 ns/op   26051 B/op  824 allocs/op
BenchmarkMartiniMutipleRoute     1000  2373968 ns/op  101615 B/op 2266 allocs/op
```
## Install/Update
```shell
 go get -u github.com/yyrdl/gbeta
```
## Hello World
```go
   package main

   import(
	"github/yyrdl/gbeta"
	"fmt"
   )

   func main(){
	 app:=gbeta.App()
	 app.Get("/",func(ctx *gbeta.Context,res gbeta.Res,req gbeta.Req){
	     res.Write([]byte("Hello World!"))	
	 })
	 app.Listen("8080",func(err error){
		if err!=nil{
			//do something
		}else{
			fmt.Println("Server is running at port 8080.")
		}
	})
	
}
```
 
#### app.Use(path string,middleware gbeta.Middlewares)
##### middleware Interface
```go
   type Middlewares Interface{
	Do(ctx *Context,res Res,req Req,next Next)
   }
```
##### example
```go
  type My_Middleware struct{}
  func (m*My_Middle)Do(ctx *gbeta.Context,res gbeta.Res,req gbeta.Req,next gbeta.Next){
	c:=make(chan bool,1)
	const done bool=true
	go func(){
		//do something
		ctx.Set("name","jason")
		c<-done
	}()
	<-c
	next(true)
}
func param(ctx *Context, key string) (bool, string) {
	v := ctx.Get(key)
	if v != nil {
		if vv, ok := v.(string); ok {
			return true, vv
		}
	}
	return false, ""
}
func main(){
	app:=gbeta.App()
	app.Get("/profile/:user",func(ctx *gbeta.Context,res gbeta.Res,req gbeta.Req){
		if found,name:=param(ctx,"name");found{
			fmt.Println("something wrong ! I should not find 'name' here!")
		}
		if found,name:=param(ctx,"user");!found{
			fmt.Println("something wrong ! I should not find 'user' here!")
		}
		res.Write([]byte("Hello world!"))
	})
	app.Use("/v1",new(My_Middleware))
	app.Post("/v1/admin",func(ctx *gbeta.Context,res gbeta.Res,req gbeta.Req){
		if found,name:=param(ctx,"name");!found{
			fmt.Println("something wrong ! I should  find 'name' here!")
		}
		res.Write([]byte("Request recieved!"))
	})
	app.Listen("8080",func(err error){
		if err!=nil{
			// do something
		}else{
			//
			fmt.Println("Server is running! :)")
		}
	})
 }

```
#### app.WrapServeHTTP(original_func gbeta.ServeHTTPFunc)gbeta.ServeHTTPFunc
你可以使用这个接口编写一些特殊的中间件，比如logger
##### gbeta.ServeHTTPFunc
```go
  type ServeHTTPFunc func(w http.ResponseWriter,req *http.Request)
```

#### app.UseSubRouter(path string ,router *gbeta.Router)
example
```go
     app:=gbeta.App()
	 subrouter:=gbeta.NewRouter()
	 subrouter.Get("/article/:id",func(ctx *gbeta.Context,res gbeta.Res,req gbeta.Req){
		res.Write([]byte("Hello world"))
	})
	app.Use("/service1",subrouter)
	app.Listen("8080",func(err error){
		//do something
	})
```
####  gbeta.App()*gbeta._App
####  app.Get(string,gbeta.ReqHandler)
####  app.Post(string,gbeta.ReqHandler)
####  app.Put(string,gbeta.ReqHandler)
####  app.Patch(string,gbeta.ReqHandler)
####  app.Delete(string,gbeta.ReqHandler)
####  app.Options(string,gbeta.ReqHandler)
####  app.Listen(port string,handler gbeta.ListenHandler)
####  app.ListenTLS(port string,certFile string, keyFile string,handler gbeta.ListenHandler)
####  app.HandlePanic(handler gbeta.PanicHandler)
####  app.HandleNotFound(handler gbeta.NotFoundHandler)
####  app.DefaultOptions(cmd bool)
enable or disable the default options support
####  app.ServeHTTP(w http.ResponseWriter,req *http.Request)

####  gbeta.NewRouter()*gbeta.Router
####  router.Use(string,gbeta.Middlewares)
####  router.UseSubRouter(string,*gbeta.Router)
####  router.Get(string,gbeta.ReqHandler)
####  router.Post(string,gbeta.ReqHandler)
####  router.Put(string,gbeta.ReqHandler)
####  router.Patch(string,gbeta.ReqHandler)
####  router.Delete(string,gbeta.ReqHandler)
####  router.Options(string,gbeta.ReqHandler)

#### License
MIT License
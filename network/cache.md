```flow
  st=>start: 用户请求资源
  op1=>operation: 直接使用缓存
  op2=>operation: 向服务器请求 If-None-Match
  op3=>operation: 向服务器请求 If-Modified-Since
  op4=>operation: 服务器返回304，读取本地缓存
  op5=>operation: 向服务器请求
  op6=>operation: 请求响应，协商缓存
  cond1=>condition: 是否存在缓存?
  cond2=>condition: 缓存是否过期?
  cond3=>condition: 判断 ETag?
  cond4=>condition: 判断 Last-Modified?
  cond5=>condition: 服务器返回200还是304?
  e=>end 返回展示资源
  st->cond1
  cond1(no)->op5
  cond1(yes)->cond2
```
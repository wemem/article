```go
//bad
package post

var client *grpc.ClientConn

func init() {
    var err error
    client, err = grpc.Dial(...）
    if err != nil {
        panic(err)
    }
}

func ListPosts() ([]*Post, error) {
    posts, err := client.ListPosts(...)
    if err != nil {
        return []*Post{}, err
    }
    
    return posts, nil
}
                            
//good
package post

type Service interface {
    ListPosts() ([]*Post, error)
}

type service struct {
    conn *grpc.ClientConn
}

func NewService(conn *grpc.ClientConn) Service {
    return &service{
        conn: conn,
    }
}

func (s *service) ListPosts() ([]*Post, error) {
    posts, err := s.conn.ListPosts(...)
    if err != nil {
        return []*Post{}, err
    }
    
    return posts, nil
}

```

1. 通过接口 Service 暴露对外的 ListPosts 方法;
2. 使用 NewService 函数初始化 Service 接口的实现并通过私有的结构体 service 持有 grpc 连接；
3. ListPosts 不再依赖全局变量，而是依赖接口体 service 持有的连接；


```go
package main

import ...

func main() {
    conn, err = grpc.Dial(...）
    if err != nil {
        panic(err)
    }
    
    svc := post.NewService(conn)
    posts, err := svc.ListPosts()
    if err != nil {
        panic(err)
    }
    
    fmt.Println(posts)
}
```
**总结**

- 使用大写的 Service 对外暴露方法；
- 使用小写的 service 实现接口中定义的方法；
- 通过 NewService 函数初始化 Service 接口；
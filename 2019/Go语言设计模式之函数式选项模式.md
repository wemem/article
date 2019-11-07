```go
package main

import "fmt"

type Option struct {
	A string
	B string
	C int
}

func newOption(a, b string, c int) *Option {
	return &Option{
		A: a,
		B: b,
		C: 0,
	}
}

// 函数类型
type OptionFunc func(*Option)

func WithA(a string) OptionFunc {
	return func(option *Option) {
		option.A = a
	}
}

func WithB(b string) OptionFunc {
	return func(o *Option) {
		o.B = b
	}
}

func WithC(c int) OptionFunc {
	return func(o *Option) {
		o.C = c
	}
}

var (
	defaultOption = &Option{
		A: "A",
		B: "B",
		C: 100,
	}
)

func newOption2(opts ...OptionFunc) (opt *Option) {
	opt = defaultOption
	for _, o := range opts {
		o(opt)
	}
	return
}

func main(){
	x := newOption2()
	fmt.Println(x)
	x = newOption2(
		WithA("测试"),
		WithC(250),
	)
	fmt.Println(x)
}

```

优点：
> 对初始化参数优化的扩展性
> 很好的处理了默认参数

# Go生产者和消费者模式

## 单一生产者和单一消费者
```go
package main

import (
	"fmt"
)

var messages = []string{
	"The world itself's",
	"just one big hoax.",
	"Spamming each other with our",
	"running commentary of bullshit,",
	"masquerading as insight, our social media",
	"faking as intimacy.",
	"Or is it that we voted for this?",
	"Not with our rigged elections,",
	"but with our things, our property, our money.",
	"I'm not saying anything new.",
	"We all know why we do this,",
	"not because Hunger Games",
	"books make us happy,",
	"but because we wanna be sedated.",
	"Because it's painful not to pretend,",
	"because we're cowards.",
	"- Elliot Alderson",
	"Mr. Robot",
}

func producer(link chan<- string) {
	for _, m := range messages {
		link <- m
	}
	close(link)
}

func consumer(link <-chan string, done chan<- bool) {
	for b := range link {
		fmt.Println(b)
	}
	done <- true
}

func main() {
	link := make(chan string)
	done := make(chan bool)
	go producer(link)
	go consumer(link, done)
	<-done
}
```

## 单一生产者和多个消费者
```go
package main

import "fmt"

var messages = []string{
	"The world itself's",
	"just one big hoax.",
	"Spamming each other with our",
	"running commentary of bullshit,",
	"masquerading as insight, our social media",
	"faking as intimacy.",
	"Or is it that we voted for this?",
	"Not with our rigged elections,",
	"but with our things, our property, our money.",
	"I'm not saying anything new.",
	"We all know why we do this,",
	"not because Hunger Games",
	"books make us happy,",
	"but because we wanna be sedated.",
	"Because it's painful not to pretend,",
	"because we're cowards.",
	"- Elliot Alderson",
	"Mr. Robot",
}

const consumerCount int = 3

func produce(jobs chan<- string) {
	for _, msg := range messages {
		jobs <- msg
	}
	close(jobs)
}

func consume(worker int, jobs <-chan string, done chan<- bool) {
	for msg := range jobs {
		fmt.Printf("Message %v is consumed by worker %v.\n", msg, worker)
	}
	done <- true
}

func main() {
	jobs := make(chan string)
	done := make(chan bool)

	go produce(jobs)

	for i := 1; i <= consumerCount; i++ {
		go consume(i, jobs, done)
	}
	<-done
}
```

## 多个生产者和单一消费者
```go
package main

import (
	"fmt"
	"sync"
)

const producerCount int = 4

var messages = [][]string{
	{
		"The world itself's",
		"just one big hoax.",
		"Spamming each other with our",
		"running commentary of bullshit,",
	},
	{
		"but with our things, our property, our money.",
		"I'm not saying anything new.",
		"We all know why we do this,",
		"not because Hunger Games",
		"books make us happy,",
	},
	{
		"masquerading as insight, our social media",
		"faking as intimacy.",
		"Or is it that we voted for this?",
		"Not with our rigged elections,",
	},
	{
		"but because we wanna be sedated.",
		"Because it's painful not to pretend,",
		"because we're cowards.",
		"- Elliot Alderson",
		"Mr. Robot",
	},
}

func produce(jobs chan<- string, idx int, wg *sync.WaitGroup) {
	defer wg.Done()
	for _, msg := range messages[idx] {
		fmt.Printf("Producer %v sending message \"%v\"\n", idx, msg)
		jobs <- msg
	}
}

func consume(jobs <-chan string, done chan<- bool) {
	for msg := range jobs {
		fmt.Printf("Consumed message \"%v\"\n", msg)
	}
	done <- true
}

func main() {
	jobs := make(chan string)
	done := make(chan bool)
	wg := sync.WaitGroup{}

	for i := 0; i < producerCount; i++ {
		wg.Add(1)
		go produce(jobs, i, &wg)
	}

	go consume(jobs, done)

	wg.Wait()
	close(jobs)
	<-done
}
```
## 多个生产者和多个消费者
```go
package main

import (
	"fmt"
	"sync"
)

var messages = [][]string{
	{
		"The world itself's",
		"just one big hoax.",
		"Spamming each other with our",
		"running commentary of bullshit,",
	},
	{
		"but with our things, our property, our money.",
		"I'm not saying anything new.",
		"We all know why we do this,",
		"not because Hunger Games",
		"books make us happy,",
	},
	{
		"masquerading as insight, our social media",
		"faking as intimacy.",
		"Or is it that we voted for this?",
		"Not with our rigged elections,",
	},
	{
		"but because we wanna be sedated.",
		"Because it's painful not to pretend,",
		"because we're cowards.",
		"- Elliot Alderson",
		"Mr. Robot",
	},
}

const producerCount int = 4
const consumerCount int = 3

func produce(link chan<- string, id int, wg *sync.WaitGroup) {
	defer wg.Done()
	for _, msg := range messages[id] {
		link <- msg
	}
}

func consume(link <-chan string, id int, wg *sync.WaitGroup) {
	defer wg.Done()
	for msg := range link {
		fmt.Printf("Message \"%v\" is consumed by consumer %v\n", msg, id)
	}
}

func main() {
	link := make(chan string)
	wp := &sync.WaitGroup{}
	wc := &sync.WaitGroup{}

	wp.Add(producerCount)
	wc.Add(consumerCount)

	for i := 0; i < producerCount; i++ {
		go produce(link, i, wp)
	}

	for i := 0; i < consumerCount; i++ {
		go consume(link, i, wc)
	}

	wp.Wait()
	close(link)
	wc.Wait()
}
```

## 最终版
```go
package main

import "fmt"

var messages = []string{
	"The world itself's",
	"just one big hoax.",
	"Spamming each other with our",
	"running commentary of bullshit,",
	"masquerading as insight, our social media",
	"faking as intimacy.",
	"Or is it that we voted for this?",
	"Not with our rigged elections,",
	"but with our things, our property, our money.",
	"I'm not saying anything new.",
	"We all know why we do this,",
	"not because Hunger Games",
	"books make us happy,",
	"but because we wanna be sedated.",
	"Because it's painful not to pretend,",
	"because we're cowards.",
	"- Elliot Alderson",
	"Mr. Robot",
}

const producerCount int = 3
const consumerCount int = 3

var workers []*producers

type producers struct {
	myQ  chan string
	quit chan bool
	id   int
}

func execute(jobQ chan<- string, workerPool chan *producers, allDone chan<- bool) {
	for _, j := range messages {
		jobQ <- j
	}
	close(jobQ)
	for _, w := range workers {
		w.quit <- true
	}
	close(workerPool)
	allDone <- true
}

func produce(jobQ <-chan string, p *producers, workerPool chan *producers) {
	for {
		select {
		case msg := <-jobQ:
			{
				workerPool <- p
				if len(msg) > 0 {
					fmt.Printf("Job \"%v\" produced by worker %v\n", msg, p.id)
				}
				p.myQ <- msg
			}
		case <-p.quit:
			return
		}
	}
}

func consume(cIdx int, workerPool <-chan *producers) {
	for {
		worker := <-workerPool
		if msg, ok := <-worker.myQ; ok {
			if len(msg) > 0 {
				fmt.Printf("Message \"%v\" is consumed by consumer %v from worker %v\n", msg, cIdx, worker.id)
			}
		}
	}
}

func main() {
	jobQ := make(chan string)
	allDone := make(chan bool)
	workerPool := make(chan *producers)

	for i := 0; i < producerCount; i++ {
		workers = append(workers, &producers{
			myQ:  make(chan string),
			quit: make(chan bool),
			id:   i,
		})
		go produce(jobQ, workers[i], workerPool)
	}

	go execute(jobQ, workerPool, allDone)

	for i := 0; i < consumerCount; i++ {
		go consume(i, workerPool)
	}
	<-allDone
}
```
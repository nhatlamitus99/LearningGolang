# LearningGolang

### 1. Collections
### 2. Reference
### 3. Defer
### 4. Concurrency: Goroutine, Channel
### 5. Synchronize: Mutex
### 6. Atomic
### 7. WaitGroup
### 8. Context
### 9. Go Scheduler 
### 10. Worker Pool 



1. Collection: Array, Slice, List, Map:
 * Array
    + Đặc điểm:
      - mảng kích thước cố định
      - xác định bằng kiểu dữ liệu và kích thước mảng
    ```golang
    [5]int != [5]string
    [5]int != [4]int
    ```
 * Slice
    + Đặc điểm:
      - mảng kích thước động, tương tự ArrayList của ngôn ngữ khác
      - nó là một struct chứa con trỏ trỏ đến array gốc, length, capacity
      - nếu append khi len > cap => cap := cap*2, re-allocate. 
    ```golang
    type slice struct {
        ptr unsafe.Pointer
        len int
        cap int
    }
    ```
    ![pic_0](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-22-10-37-06-13.jpg)
 * List
    + Đặc điểm:
      - kích thước động, triển khai cấu trúc danh sách liên kết đôi => chi phí truy cập tuyến tính.
 * Map
    + Đặc điểm:
      - tương tự cấu trúc HashMap ở các ngôn ngữ khác
      - nó là con trỏ trỏ đến một loại struct
      - not goroutine safe => sử dụng mutex (Mutex, RWMutex) để synchronize (xem ví dụ ở phần 5: Synchonize: Mutex).
      
2. Reference
 * Go không truyền params kiểu tham chiếu vào hàm, chỉ có truyền tham trị.
 * Khi truyền params vào trong hàm, thực chất là truyền 1 bản copy của các params đó.
   + Truyền array vào func, những thay đổi của array ở trong func ko ảnh hướng đến array đó ở bên ngoài.
   + Truyền slice, map vào func, những thay đổi trên data của chúng sẽ ảnh hướng đến slice, map đó ở bên ngoài vì slice chứa biến con trỏ, map là một con trỏ (bản gốc và bản copy đều có con trỏ trỏ đến cùng 1 vùng nhớ trong memory).
   
3. Defer:
 * Đặc điểm: 
    + Câu lệnh sau defer sẽ được gọi ngay trước khi hàm chứa nó kết thúc.
    + Giá trị params truyền vào hàm defer được lưu lại tại thời điểm truyền vào chứ không phải lúc surrounding func kết thúc.
    + Khi nhiều defer được dùng thì lưu trữ theo cơ chế stack.
 ```golang
 func main() {
    i := 0
    defer fmt.Println(i)
    i++
    fmt.Println(i)
    return
}
// output: 1 0
 ```
 
   
4. Concurrency: Goroutine, Channel
 * Goroutine:
    + Goroutine gọn nhẹ hơn Thread, chiếm ít tài nguyên bộ nhớ (2KB)
    + Chi phí switch context thấp hơn so với OS Thead
    + Được quản lí bởi Go runtime
    + Các trạng thái của goroutine:
        - Waiting: trạng thái chờ lock từ goroutine khác (khi sử dụng atomic, mutex)
        - Runable: sẵn sàng để được thực thi
        - Executing: đang được thực thi
 * Channel:
    + Là một biến con trỏ đặc biệt để các Goroutine giao tiếp với nhau trong quá trình concurrency.
    + Cơ chế: khi 1 goroutine gởi data vào channel thì goroutine đó sẽ chờ khi nào có 1 goroutine khác lấy data này đi mới có thể cho ghi data mới vào channel.
    + Khi close channel thì nó không nhận data nữa, đọc data sẽ trả về zero-value.
    + Khi goroutine read hoặc write vào channel thì bị block nhưng close channel thì goroutine không bị block.
    + Unidirectional channel chỉ cho phép 1 chiều (đọc hoặc ghi) dữ liệu vào channel để kiểm soát chương trình tốt hơn. Có thể convert channel 2 chiều thành channel 1 chiều, trường hợp ngược lại không đúng.
    + Code Demo :
    ```golang
    // param là channel 1 chiều
    func greet(roc <-chan string) {
	    fmt.Println("Hello " + <-roc + "!")
    }

    func main() {
        fmt.Println("main() started")
        // tạo channel 2 chiều
        c := make(chan string)     
        
        // auto convert để truyền vào hàm greet hợp lệ
        go greet(c)
        
        c <- "John"
        fmt.Println("main() stopped")
    }
    ```
 * Các trường hợp dẫn đến Deadlock khi sử dụng channel:
    + Select không có case nào được thực thi => sử dụng default case để tránh deadlock vì nó không bị block. Giải pháp khác là set timeout: time.After()
    ```golang
    select{
    case <- ch1: 
        //
    case <- ch2: 
        //
    case <- time.After(2*time.Second) 
        //
    }
    ```
    + Nếu không có goroutine nào ghi data vào channel mà có lệnh đọc data thì chương trình sẽ rơi vào trạng thái deadlock
    ```golang
    func main() {
        ch := make(chan int)
        data := <-ch
    }
    ```
    + Trường hợp ghi và đọc data từ channel được thực hiện ở main goroutine sẽ dẫn đến deadlock
    Vì khi 1 goroutine ghi data vào channel thì goroutine đó sẽ bị block để chờ goroutine khác lấy data ra khỏi channel mới tiếp tục thực thi, nếu main goroutine bị block thì chương trình sẽ rơi vào deadlock.
    ```golang
    func main() {
        // init
        ch := make(chan int) 
        // send data
        ch <- 10
        // receive data
        data := <- ch
        // sleep
        time.Sleep(time.Second)
    }
    ```
    + Giải quyết các vấn đề trên => Buffered Channel cho phép một lượng nhất định goroutines gởi data vào channel sau đó lấy data đó ra, block xảy ra khi đầy buffer nên hạn chế tình trạng deadlock.
    
    Demo goroutine vs channel:
    ```golang
    func main()  {
        ch := make(chan int)

        go func(ch chan int) {
            for i := 0; i < 10; i++ {
                ch <- i
            }
            close(ch)
        }(ch)

        for value := range ch {
            fmt.Println(value)
        }

        time.Sleep(time.Second)
    }
    ```
5. Synchronize:
 * Mutex: dùng để lock 1 đoạn code, đảm bảo chỉ có 1 goroutine (read hoặc write) được phép truy cập vào resource dùng chung.
 * RWMutex: nhiều goroutine read được truy cập cùng lúc, các goroutine khác (read - write hay write - write) phải chờ lock được release.
   => Performance: RWMutex > Mutex
   + Code Demo:
    ```golang
        func write(hashMap map[int]string, mutex *sync.RWMutex) {
            mutex.Lock()
            hashMap[2] = "write"
            mutex.Unlock()
        }

        func read(hashMap map[int]string, mutex *sync.RWMutex) {
            mutex.RLock()
            read := hashMap[1]
            mutex.RUnlock()
            fmt.Println("read: ", read)
        }

        func main() {
            mutex := &sync.RWMutex{}
            hashMap := make(map[int]string)
            hashMap[1] = "a"
            hashMap[2] = "b"
            go write(hashMap, mutex)
            go read(hashMap, mutex)
            time.Sleep(time.Second)
            fmt.Println("Golang")
        }       
    ```
6. Atomic:
  * Đặc điểm:
    + Tránh tình trạng race condition, khi các goroutine truy cập vào biến chung khi biến đó chưa cập nhập xong giá trị.
    + Dùng để lock biến dùng chung giữa các goroutine, đảm bảo ở 1 thời điểm chỉ có 1 goroutine thay đổi giá trị của biến số nguyên dùng chung.
  * Code Demo:
  ```golang
  // without atomic
  count++   => có thể sai lệch trong quá trình nhiều goroutine cập nhật biến count (đọc ra -> sửa -> ghi lại)
  // atomic
  atomic.AddInt32(&count,1)
  ```
   
7. WaitGroup:
  * Đặc điểm: Dùng để đảm bảo một goroutine chính chờ các goroutine con chạy xong mới tiếp tục chạy.
  * Code Demo:
  ```golang
  // init
  wg := sync.WaitGroup
  // set counter = 1
  wg.Add(1)
  // giảm counter xuống 1
  wg.Done()
  // goroutine chờ khi counter = 0 mới thực thi tiếp, nếu countert > 0 thì block goroutine chính.
  wg.Wait()  // goroutine bị block tại đây
  ```
8. Context:
  * Đặc điểm:
    + Mỗi request gởi đến server sẽ được một goroutine đảm nhận xử lý. Trường hợp client tắt browser hoặc offline nếu server vẫn xử lý request để trả về response sẽ gây lãng phí => Context cung cấp các phương thức để xử lý request khi bị cancel hoặc timeout một cách hiệu quả.
    + Ví dụ minh họa:
    
        - Server nhận request, sau đó query xuống database lấy data để xử lý rồi trả response về cho client:
    ![pic1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-22-09-28-20-25.jpg)
    
        - Luồng xử lý ở server:
    ![pic2](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-22-09-28-33-41.jpg)
    
        - Khi request bij cancel, nếu không xử lý request cancel hoặc timeout thì sẽ lãng phí, giảm performance:
    ![pic3](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-22-09-28-45-52.jpg)
    
        - Context giúp handle hiệu quả hơn:
    ![pic4](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-22-09-28-55-44.jpg)

    + Code Demo:
    ```golang
    // Tạo ra 1 context mới, đây là top-level context nên không bị cancel
    ctx_1 := context.Background()
    
    // Tạo ra 1 context mới từ root context (ctx_1)
        // set timeout là 2s => request sẽ cancel sau 2s
    ctx_2, err := context.WithTimeOut(ctx_1, 2*time.Second)
        // set deadline => request sẽ cancel sau thời điểm này.
    ctx_2, cancel := context.WithDeadline(ctx_1, time.Date(2020, time.November, 10, 23, 0, 0, 0, time.UTC))
      
    // Phát ra sự kiện cancel bằng context.WithCancel()
    ctx_3, err := context.WithCancel(ctx_1)
    
    // Lắng nghe sự kiện cancel, context.Done() trả về 1 channel
    ch := <- context.Done()
    
    ```
9. Go Scheduler:
  + package jasonlvhit/gocron: dùng để run tasks theo định kỳ.
  + Code Demo:
  ```golang
    import (
        "fmt"
        "github.com/jasonlvhit/gocron"
        "time"
    )
    // task cần schedule
    func myTask() {
        fmt.Println("This task will run periodically")
    }
    
    // run myTask sau mỗi 1 giây
    func executeCronJob() {
        gocron.Every(1).Second().Do(myTask)
        <- gocron.Start()  // run tasks đã được scheduled
    }
    
    func main() {
        go executeCronJob()
        time.Sleep(10*time.Second)
    }
```
10. Worker Pool:
  + Đặc điểm: pool dùng để quản lý các jobs từ các resources được đưa vào và giao jobs cho các goroutines xử lý. 
  + package: dmora/workerpool

  => Tái sử dụng lại các goroutines (workers), tránh việc tạo quá nhiều goroutines gây lãng phí. 
  ![pic_1](https://github.com/nhatlamitus99/LearningGolang/blob/main/image/Screenshot_2020-10-28-11-21-34-10.jpg)
  
  + Code Demo:
  ```golang
  type Job struct {
  	id int
	resource interface{}
  }
  
  type Result struct {
  	Job Job
	Err error
  }
  
  type Pool struct {
	numRoutines int
	Jobs chan Job
	Results chan Result
	done chan bool
	complete bool
  }
  ```
  ```golang
  // tạo worker pool chứa 3 goroutine để xử lý các resources
  pool := wokerpool.NewPool(3)
  
  // đưa resources vào pool và thực thi các job
  pool.Start(resources[] interface{}, procFunc ProcessorFunc, resFunc ResultProcessorFunc) 
  	// resources: danh sách các việc cần làm
  	// procFunc: hàm xử lý công việc
 	// resFunc: hàm xử lý khi nhận kết quả
     
  // kiểm tra đã hoàn thành all jobs
  pool.IsCompleted() 
  
  	  // Logic phía trong hàm pool.Start():
	  pool.allocate() // allocate jobs dựa vào array resources nhận được.
	  pool.work() 	  // gọi hàm procFunc để xử lý các jobs.
	  pool.collect()  // gọi hàm resFunc để xử lý kết quả trả về, báo completed all jobs.
  ```
  

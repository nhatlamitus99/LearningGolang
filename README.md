# LearningGolang
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
      - nó là một struct chứa con trỏ trỏ đến array gốc, len, cap
      - nếu append khi len > cap => cap := cap*2, re-allocate. 
 * List
    + Đặc điểm:
      - kích thước động, triển khai cấu trúc danh sách liên kết đôi => chi phí truy cập tuyến tính.
 * Map
    + Đặc điểm:
      - tương tự cấu trúc HashMap ở các ngôn ngữ khác
      - nó là con trỏ trỏ đến một loại struct
      - not goroutine safe => sử dụng mutex (Mutex, RWMutex) để synchronize.
      
2. Reference in Go
 * Go không có biến tham chiếu hay truyền params kiểu tham chiếu vào hàm, chỉ có truyền tham trị.
 * Khi truyền params vào trong hàm, thực chất là truyền 1 bản copy của các params đó.
   + Truyền array vào func, những thay đổi của array ở trong func ko ảnh hướng đến array đó bên ngoài.
   + Truyền slice, map vào func, những thay đổi trên data của chúng sẽ ảnh hướng đến slice, map đó ở bên ngoài vì slice chứa biến con trỏ, map là một con trỏ (bản gốc và bản copy đều có con trỏ trỏ đến cùng 1 vùng nhớ trong memory).
3. Concurrency: Goroutine, Channel
 * Goroutine:
    + Goroutine gọn nhẹ, chiếm ít tài nguyên bộ nhớ (2KB)
    + Chi phí switch context thấp
    + Được quản lí bởi Go runtime
 * Channel:
    + Là một biến đặc biệt để các Goroutine giao tiếp với nhau khi truy cập vào shared memory
    + Cơ chế: khi 1 goroutine gởi data vào channel thì chương trình sẽ chờ khi nào có 1 goroutine khác lấy data này đi mới có thể cho goroutine khác ghi data mới vào channel.
 * Các trường hợp dẫn đến Deadlock khi sử dụng channel:
 
    + Nếu không có goroutine nào send data đến channel mà có lệnh đọc data thì chương trình sẽ rơi vào trạng thái deadlock
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
    + Giải quyết vấn đề trên => Buffered Channel cho phép một lượng nhất định goroutines gởi data vào channel sau đó lấy data đó ra, thay vì chỉ có 1 goroutine dễ dẫn đến tình trạng deadlock.
    
    Demo goroutine vs channel:
    ```golang
    // demo
    func send(ch chan int) {
        ch <- 10
    }

    func receive(ch chan int) {
        data := <-ch
        fmt.Println(data)
    }

    func main() {
        ch := make(chan int)
        go send(ch)
        go receive(ch)
        time.Sleep(time.Second)
    }
    ```
4. Sync
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
5. Atomic:
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
   
6. WaitGroup:
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
  wg.Wait()
6. Context:
7. Go Scheduler:

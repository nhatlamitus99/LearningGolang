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
3. Concurrency: Goroutines, channels
 * Goroutine:
    + Goroutine gọn nhẹ, chiếm ít tài nguyên bộ nhớ (2KB)
    + Chi phí switch context thấp
    + Được quản lí bởi Go runtime
 * Channel:
    + Là một biến đặc biệt để các Goroutine giao tiếp với nhau khi truy cập vào shared memory
    + Cơ chế: khi 1 goroutine gởi data vào channel thì chương trình sẽ chờ khi nào có 1 goroutine khác lấy data này đi mới có thể cho goroutine khác ghi data mới vào channel.
    + Mở rộng: Channel Buffer: cho phép số lượng nhất định goroutines gởi data vào channel sau đó lấy data đó ra, thay vì chỉ có 1 goroutine.
 * Code Demo:
    ```golang
    // init
    channel := make(chan int)
    // send data to channel
    channel <- data_1
    // receive data from channel
    data_2 := <-channel  
    ```
    Nếu không có goroutine nào send data đến channel mà có lệnh đọc data thì chương trình sẽ rơi vào trạng thái deadlock
    ```golang
    func main() {
        ch := make(chan int)
        data := <-ch
    }
    ```
    Demo channel:
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
 * Mutex: chỉ có 1 goroutine (read hoặc write) được phép truy cập vào resoure dùng chung.
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
5. Async, Atomic, Context

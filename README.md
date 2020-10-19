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
      - not goroutine safe => sử dụng mutex (Mutex, RWMutex) để synchronize
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
      
2. Reference in Go
 * Go không có biến tham chiếu hay truyền params kiểu tham chiếu vào hàm, chỉ có truyền tham trị.
3. Concurrency: Goroutines, channels
4. Mutex, RWMutex
 * Mutex: chỉ có 1 goroutine (read hoặc write) được phép truy cập vào resoure dùng chung.
 * RWMutex: nhiều goroutine read được truy cập cùng lúc, các goroutine khác (read - write hay write - write) phải chờ có lock được release mới vào được resource chung.
    Performance: RWMutex > Mutex
5. Async, Atomic, Context

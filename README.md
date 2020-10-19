# LearningGolang
1. Collection: Array, Slice, List, Map:
 * Array
    + Feature:
      - data type and length 
      - fixed length
      - value type
    
 * Slice
    + Feature:
      - flexible length, like arraylist in other languages
      - it's a struct which contains pointer to base array, len, capacity
      - if len > cap => cap = cap*2
      - it's a struct contain pointer and lens
      - when pass into func, slice pass by value so slice in func is a copy of original slice and both ref the same base array, so change is visible
 * List
    + Feature:
      - flexible length, implements linked-list 
 
 * Map
    + Feature:
      - like hashMap in other languages
      - it's a pointer to struct
      - not goroutine safe => should use mutex to sync
      
2. Reference in Go
 * Go has not ref variables, just value variables
3. Concurrency: Goroutines, channels
4. Async, Atomic, Context

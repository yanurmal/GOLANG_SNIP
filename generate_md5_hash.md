```
package main

import (
	"crypto/md5"
	"fmt"
)

func main() {
	data := []byte("hellodrm")
	fmt.Printf("%x", md5.Sum(data))
}
```

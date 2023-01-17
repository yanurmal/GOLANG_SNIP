```
package main

import (
	"fmt"
	"os/exec"
)

func main() {
	// File yang akan di-set password
	file := "document.pdf"
	// Password yang akan digunakan
	password := "password123"

	// Menjalankan perintah untuk menambahkan password pada file PDF
	cmd := exec.Command("pdftk", file, "output", "-", "user_pw", password)
	out, err := cmd.CombinedOutput()
	if err != nil {
		fmt.Println(err)
		return
	}

	// Menyimpan hasil ke file yang sama
	cmd = exec.Command("pdftk", "-", "output", file)
	cmd.Stdin = bytes.NewReader(out)
	cmd.Run()

	fmt.Println("Password berhasil ditambahkan pada file", file)
}
```

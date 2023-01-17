```
package main

import (
	"bytes"
	"fmt"
	"os/exec"
)

func main() {
	// File yang akan di-set password
	input := "test.pdf"
	outout := "test_pwd.pdf"
	// Password yang akan digunakan
	password := "password123"

	// Menjalankan perintah untuk menambahkan password pada file PDF
	// pdftk test.pdf output test_pwd.pdf owner_pw 123 user_pw 456
	cmd := exec.Command("pdftk", input, "output", outout, "user_pw", password)
	out, err := cmd.CombinedOutput()
	if err != nil {
		fmt.Println(err)
		return
	}

	// Menyimpan hasil ke file yang sama
	// cmd = exec.Command("pdftk", "-", "output", outout)
	cmd.Stdin = bytes.NewReader(out)
	cmd.Run()

	fmt.Println("Password berhasil ditambahkan pada file", outout)
}

```

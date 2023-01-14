Berikut adalah contoh kode untuk mengenkripsi file PDF menggunakan AES-256 di Golang:
```
package main

import (
    "crypto/aes"
    "crypto/cipher"
    "crypto/rand"
    "encoding/hex"
    "fmt"
    "io"
    "os"
)

func EncryptFile(fileName string, password string) error {
    // Open the original file
    originalFile, err := os.Open(fileName)
    if err != nil {
        return err
    }
    defer originalFile.Close()

    // Create a new file for the encrypted data
    encryptedFile, err := os.Create(fileName + ".enc")
    if err != nil {
        return err
    }
    defer encryptedFile.Close()

    // Derive the key from the password
    key := []byte(password)
    derivedKey := make([]byte, 32)
    copy(derivedKey, key)

    // Create a new AES cipher block
    block, err := aes.NewCipher(derivedKey)
    if err != nil {
        return err
    }

    // Create a new random initialization vector
    iv := make([]byte, aes.BlockSize)
    if _, err := io.ReadFull(rand.Reader, iv); err != nil {
        return err
    }

    // Encrypt the data using the AES block in CTR mode
    stream := cipher.NewCTR(block, iv)
    writer := &cipher.StreamWriter{S: stream, W: encryptedFile}
    if _, err := io.Copy(writer, originalFile); err != nil {
        return err
    }

    // Print the hex-encoded initialization vector
    fmt.Println("Initialization Vector:", hex.EncodeToString(iv))

    return nil
}

func main() {
    fileName := "example.pdf"
    password := "your_password"

    if err := EncryptFile(fileName, password); err != nil {
        fmt.Println("Error encrypting file:", err)
    } else {
        fmt.Println("File encrypted successfully!")
    }
}
```

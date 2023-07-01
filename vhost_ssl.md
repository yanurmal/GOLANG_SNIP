```
package main

import (
	"github.com/gin-contrib/secure"
	"github.com/gin-gonic/gin"
	"github.com/foolin/goview"
	"github.com/foolin/goview/supports/ginview"
	"github.com/gin-contrib/redirect"
	"net/http"
)

type User struct {
	ID   string `json:"id"`
	Name string `json:"name"`
}

var users []User

func main() {
	router := gin.Default()

	// Set view engine
	router.HTMLRender = ginview.Default()

	// Middleware for secure headers
	router.Use(secure.New(secure.Config{
		// Set SecureMode to true to enable HTTPS redirect
		SecureMode: true,
	}))

	// Virtual host example
	vhost := router.Group("myvirtualhost.com") // Ubah dengan nama host virtual yang diinginkan

	vhost.Use(func(c *gin.Context) {
		// Middleware untuk host virtual
		c.Next()
	})

	vhost.GET("/", func(c *gin.Context) {
		// Handler untuk rute pada host virtual
		c.HTML(http.StatusOK, "index.html", gin.H{
			"message": "Ini halaman host virtual!",
		})
	})

	vhost.GET("/users", func(c *gin.Context) {
		// Handler untuk GET /users
		c.JSON(http.StatusOK, users)
	})

	vhost.POST("/users", func(c *gin.Context) {
		// Handler untuk POST /users
		var newUser User
		if err := c.ShouldBindJSON(&newUser); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}

		users = append(users, newUser)
		c.Status(http.StatusCreated)
	})

	vhost.PUT("/users/:id", func(c *gin.Context) {
		// Handler untuk PUT /users/:id
		id := c.Param("id")

		var updatedUser User
		if err := c.ShouldBindJSON(&updatedUser); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return
		}

		var foundUser *User
		for i, u := range users {
			if u.ID == id {
				foundUser = &users[i]
				break
			}
		}

		if foundUser == nil {
			c.Status(http.StatusNotFound)
			return
		}

		foundUser.Name = updatedUser.Name

		c.Status(http.StatusOK)
	})

	vhost.DELETE("/users/:id", func(c *gin.Context) {
		// Handler untuk DELETE /users/:id
		id := c.Param("id")

		for i, u := range users {
			if u.ID == id {
				users = append(users[:i], users[i+1:]...)
				c.Status(http.StatusOK)
				return
			}
		}

		c.Status(http.StatusNotFound)
	})

	// Set default redirect
	router.Use(redirect.RedirectTo("https://myvirtualhost.com"))

	// Jalankan server dengan SSL
	router.RunTLS(":443", "path/to/certificate.pem", "path/to/private-key.pem") // Ubah path/to/certificate.pem dan path/to/private-key.pem sesuai dengan lokasi berkas sertifikat dan kunci pribadi Anda
}
```

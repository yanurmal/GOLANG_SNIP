```
package main

import (
	"math"
)

func trilateration(p1, p2, p3 [3]float64, r1, r2, r3 float64) (float64, float64) {
	ex := (p2[0]-p1[0])/distance(p1,p2)
	ey := (p2[1]-p1[1])/distance(p1,p2)
	ez := (p2[2]-p1[2])/distance(p1,p2)
	
	px := p1[0] + ex*r1
	py := p1[1] + ey*r1
	pz := p1[2] + ez*r1
	
	var i float64 = ey*(px-p3[0]) - ex*(py-p3[1])
	var j float64 = ex*(pz-p3[2]) - ez*(px-p3[0])
	var k float64 = ez*(py-p3[1]) - ey*(pz-p3[2])
	
	d := distance(p1,p3)
	
	x := (p3[0]+ (i/(i+j+k)))
	y := (p3[1]+ (j/(i+j+k)))
	
	return x, y
}

func distance(p1, p2 [3]float64) float64 {
	return math.Sqrt(math.Pow(p2[0]-p1[0], 2) + math.Pow(p2[1]-p1[1], 2) + math.Pow(p2[2]-p1[2], 2))
}

func main() {
	p1 := [3]float64{1, 1, 1}
	p2 := [3]float64{2, 2, 2}
	p3 := [3]float64{3, 3, 3}
	r1 := 1.0
	r2 := 2.0
	r3 := 3.0

	x, y := trilateration(p1, p2, p3, r1, r2, r3)
	fmt.Println("Coordinate: ", x, y)
}
```

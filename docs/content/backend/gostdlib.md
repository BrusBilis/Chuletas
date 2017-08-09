# GOLANG STANDARD LIBRARY  

---

## FMT

`import "fmt"`

`fmt.Print()` - imprime  
`fmt.Println()` - imprime y salta de linea   
`fmt.Printf()` - imprime con un determinado formato  

```go
type point struct {
    x, y int
}
p := point{1, 2}

fmt.Printf("%v\n", p)                   // {1 2}

// en una struct, `%+v` incluye los nombres de los campos de la struct
fmt.Printf("%+v\n", p)                  // {x:1 y:2}

// Imprimir el tipo de un valor
fmt.Printf("%T\n", p)                   // main.point

// `%d` para enteros standard
fmt.Printf("%d\n", 123)                 // 123

// Imprime el caracter que corresponde al entero
fmt.Printf("%c\n", 65)                  // a

// Imprime floats
fmt.Printf("%f\n", 78.9)                // 78.90000

// Imprime string basicas `%s`.
fmt.Printf("%s\n", "\"string\"")        // "string"

// Imprimir Booleano
fmt.Printf("%t\n", a ==b)               // true o false

// Imprime un puntero`%p`.
fmt.Printf("%p\n", &p)                  // 0x42135100
```

`fmt.Sprint()` - devuelve el resultado a una string    
`fmt.Sprintln()` - devuelve el resultado con salto de linea a una string    
`fmt.Sprintf()` - devuelve el resultado con un determinado formato a una string  

```go
// las Sxxx() son como las normales en vez de imprimir el resultado
// lo devuelven como un string
s := fmt.Sprintf("Hi, my name is %s and I'm %d years old.", "Bob", 23)
// s vale "Hi, my name is Bob and I'm 23 years old."
```

`fmt.Scan()` - para leer una palabra del teclado , almacena sucesivos valores
separados por un espacio en sucesivos argumentos. Saltos de linea cuentan como
espacio   
`fmt.Scanln()` - para leer una palabra del teclado , almacena sucesivos valores
separados por un espacio en sucesivos argumentos. Saltos de linea acaban con la
lectura de datos  

* **verbos**

\- General  
`%v` - valor en formato por defecto. En structs `%+v` añade los nombres de los campos  
`%T` - tipo del valor  
`%#v` - representacion del tipo del valor con sintaxis de golang  
\- Booleano   
`%t` - booleano, devuelve palabra true o false   
\- Integer   
`%b` - devuelve en base 2  
`%c` - devuelve caracter representado por el correspondiente codigo Unicode    
`%d` - devuelve en base 10   
`%U` - formato Unicode  
\- Floating point   
`f%` - notacion decimal sin exponentes    
`e%` - notacion decimal con exponentes
\- Strings y []byte
`%s` -  cadenas normales  
`%q` -  para escapar comillas dobles  
`%x` - convierte a base hexadecimal    

---

## STRINGS

`import "strings"`  

`strings.Contains("test", "es")` = true - Contiene "test" a "es"      
`strings.Count("test", "t")` = 2 - Cuantas "t" hay en "test"  
`strings.HasPrefix("test", "te")` = true - Comienza "test" por "te"    
`strings.HasSuffix("test", "st")` = True -  Acaba "test" en "st"    
`strings.Index("test", "e")` = 1 - Posicion de string "e" dentro de string
"test", si no esta devuelve -1  
`strings.Join([]string{"a","b"}, "-")` = "a-b" - Coge una lista de strings y
las junta en una separadas por otra string ("-" en el ejemplo)   
`strings.Repeat("a", 5)` = aaaaa -  Repite una string n veces  
`strings.Replace("aaaa", "a", "b", 2)` = "bbaa" - reemplaza en una cadena una
parte por otra n veces (o todas las que se pueda si pasamos -1)  
`strings.Split("a-b-c-d-e", "-")` = []string{"a","b","c","d","e"} - Parte una
string en un array de strings usando otra string como separador  
`strings.ToLower("test")` = "TEST "- convierte la cadena a minusculas  
`strings.ToUpper("TEST")` = "test" - convierte la cadena a mayusculas  
`strings.Fields("cadena que sea)` = como split usando espacios en blanco. es equivalente a si usaramos `strings.Split(text, " ")`  

Convertir string en slice of bytes y viceversa  
`arr := []byte("test")`  
`str := string([]byte{'t','e','s','t'})`

Fuera del paquete string

`len("aquiunacadena")` - nos da la longitud de la string     
`"cadena"[3]` - nos da el codigo ASCII del caracter de indice 3, "e" = 101  
`string(cadena[n])` - nos da el caracter de la cadena en la posicion n  

---

## STRCONV

`import "strconv"` - conversiones entre numeros y strings  

`s := strconv.Itoa(-42)` - int to string  
`i, err := strconv.Atoi("-42")` - string to int  
`b, err := strconv.ParseBool("true")` - string to boolean  
`f, err := strconv.ParseFloat("3.1415", 64)` - string to float  
`i, err := strconv.ParseInt("-42", 10, 64)` - string to int  
`u, err := strconv.ParseUint("42", 10, 64)` - string to uint  
`s := strconv.FormatBool(true)` - boolean value to string  
`s := strconv.FormatFloat(3.1415, 'E', -1, 64)` - float to string  
`s := strconv.FormatInt(-42, 16)` - int to string  
`s := strconv.FormatUint(42, 16)` - uint to string  

---

## APPEND 

[Slice tricks](https://github.com/golang/go/wiki/SliceTricks) 

[More](https://www.reddit.com/r/golang/comments/283vpk/help_with_slices_and_passbyreference/)

`func append(slice []T, elements...T) []T.`

---

## IO

`import "io"`    

Tiene dos interfaces principales

* **Reader**  
soporta leer a a traves del metodo Read  

* **Writer**  
soporta escribir a traves del metodo Write

### io/ioutil

`import io/ioutil`    

* **leer y escribir un archivo**  

Mas control a traves de un `File struct` del paquete OS

```go
data := []byte("Hello World!\n")

// write 
err := ioutil.WriteFile("data1", data, 0644)
if err != nil {
    panic(err)
}

//read
read, err := ioutil.ReadFile("data1")
if err != nil {
    return
}
fmt.Print(string(read1))
```

* **Limitar tamaño io**

```go
defer resp.Body.Close()
limitReader := &io.LimitedReader{R: resp.Body, N: 2e6} // (2mb)
body, err := ioutil.ReadAll(limitReader)
```

---

## OS

`import "os"`  

### Archivos

* **Saber donde estamos**

```go
os.Getwd()
```

* **leer escribir un archivo**  

```go
// Una forma
file, err := os.Open("test.txt")
if err != nil {
    // handle the error here
}
defer file.Close()
stat, err := file.Stat()              // get the file size
if err != nil {
    return
}
bs := make([]byte, stat.Size())       // read the file
_, err = file.Read(bs)
if err != nil {
    return
}
str := string(bs)
fmt.Println(str)
```

```go
// otra forma
data := []byte("Hello World!\n")

// write to file and read from file using the File struct
file1, _ := os.Create("data2")
defer file1.Close()

bytes, _ := file1.Write(data)
fmt.Printf("Wrote %d bytes to file\n", bytes)

file2, _ := os.Open("data2")
defer file2.Close()

read2 := make([]byte, len(data))
bytes, _ = file2.Read(read2)
fmt.Printf("Read %d bytes from file\n", bytes)
fmt.Println(string(read2))
```

* **crear un archivo**  

```go
func main() {
  file, err := os.Create("test.txt")
  if err != nil {
      return
  }
  defer file.Close()
  file.WriteString("test")
}
```

* **Leer el contenido de un directorio**  

`Readdir` - coge un argumento que es el numero de entradas que devuelve. Con -1
devuelve todas  

```go
func main() {
    dir, err := os.Open(".")
    if err != nil {
        return
    }
    defer dir.Close()
    fileInfos, err := dir.Readdir(-1)
    if err != nil {
        return
    }
    for _, fi := range fileInfos {
        fmt.Println(fi.Name())
    }
}
```

`Walk` - para recorrer recursivamente un directorio. Pertenece al paquete
[path/filepath](#pathfilepath)  

### Argumentos en comandos

* **Command line arguments**

el primer valor del slice de argumentos es el nombre del comando path incluido

`argsWithProg := os.Args`- slice completo con comando nombre path incluido  
`argsWithoutProg := os.Args[1:]` - slice solo de argumentos  
`arg := os.Args[x]` - devuelve argumento de posicion X  

### Variables de Entorno

* **environment variables**  

`os.Setenv("nombreVariable", "valor")` - establece un par clave/valor para una variable de entorno  
`os.Getenv("nombreVariable")` - devuelve el valor de esa clave  

```go
// os.Environ es una lista de todas las variables de entorno
for _, e := range os.Environ() {
    pair := strings.Split(e, "=")
		fmt.Println(pair[0], "-->", pair[1])
}
```

---

## PATH/FILEPATH  

`import path/filepath`    

* **Recorrer recursivamente un directorio**

`Walk`  

```go
func main() {
    filepath.Walk(".", func(path string, info os.FileInfo, err error)
                                                              error {
        fmt.Println(path)
        return nil
    })
}
```
## REGEXP  

`import "regexp"`  

```go
// Comprueba si es una cadena
patron := "loquequeremoscomprobar"
match, _ := regexp.MatchString("p([a-z]+)ch", patron)
fmt.Println(match)

// o compilamos primero una struct optimizada para regexp
patron := "loquequeremoscomprobar"
r, _ := regexp.Compile("p([a-z]+)ch")
fmt.Println(r.MatchString(patron))
```

```go
// Por ejemplo cambiar en la cadena s los guiones bajos por guiones
r := regexp.MustCompile("_")
s = r.ReplaceAllString(s, `-`)
```

---

## JSON

`import "encoding/json"`  

[Golang JSON](https://blog.golang.org/json-and-go)  

`dataJson, err := json.Marshal(structObject)` - Go struct data to JSON data    
`dataJson, err:= json.MarshalIndent(strObj, "", "  ")` - bien preformateado  


`err := json.Unmarshal(dataJson, &structObject)` - JSON data to Go struct data  

```go
urlDir := "https://brusbilis.com/api/que/queramos"
resp, err := http.Get(urlDir)
if err != nil {
    log.Fatal(err)
}
defer resp.Body.Close()

body, err := ioutil.ReadAll(resp.Body)
if err != nil {
    log.Fatal(err)
}
body = body[5 : len(body)-6] // quitar mis pegatinas de <pre></pre>

var s structObject
err = json.Unmarshal(body, &s)
fmt.Println(s)
```

* **Convertir json to go struct**  

[JSON-to-Go online](https://mholt.github.io/json-to-go/)

`json:"-"` ignora ese campo tanto al convertir a json o al convertir desde json  
`json:"nombreCampo,omitempy` - no se incluye el campo al convertir a json si ese campo ya tiene un valor por defecto.

* **Decoder**

Ademas de `Unmarshal/Marshal` existe `Decoder/Encoder`, que se debe usar si los datos vienen de una stream io.Reader como por ejemplo el Body de una http.Request.  
Si los datos estan en una string o en memoria mejor usar Unmarshal

```go
type configuration struct { lo que sea }

file, _ := os.Open("conf.json")
defer file.Close()
decoder := json.NewDecoder(file)
conf := configuration{}
err := decoder.Decode(&conf)
if err != nil {
    fmt.Println("Error:", err)
}
fmt.Println(conf)
```

* **Parsing Interfaces**

Parsing into an Interface  
If you have truly no idea what your JSON might look like, you can parse it into a generic interface{}. If you’re not familiar an empty interface{} is a way of defining a variable in Go as “this could be anything”. At runtime Go will then allocate the appropriate memory to fit whatever you decide to store in it.  
This is what it looks like:  

```go
var parsed interface{}
err := json.Unmarshal(data, &parsed)
``` 

Actually using parsed is a bit laborious, as Go can’t use it without knowing what type it is. You can end up with code which tries the kitchen sink:

```go
switch parsed.(type) {
    case int:
        someGreatIntFunction(parsed.(int))
    case map:
        someMapThing(parsed.(map))
    default:
        panic("JSON type not understood")
}
```

You can also do similar type assertions inline:

```go
intVal, ok := parsed.(int)
if !ok {
    panic("JSON value must be an int")
}
```

Fortunately however, it’s rare to truly have no idea what a value might be. If, for example, you know your JSON value is an object, you can parse it into a map[string]interface{}. This gives you the advantage of being able to refer to specific keys. An example:

```go
var parsed map[string]interface{}
data := []byte(`
    {
        "id": "k34rAT4",
        "age": 24
    }
`)
err := json.Unmarshal(data, &parsed)
```

You can then refer to specific keys without a problem:

```go
parsed["id"]
````

You still have interfaces as the value of your map however, so you must do type assertions to use them:

```go
idString := parsed["id"].(string)
```

Go uses these six types for all values parsed into interfaces:

```go
bool, for JSON booleans
float64, for JSON numbers
string, for JSON strings
[]interface{}, for JSON arrays
map[string]interface{}, for JSON objects
nil for JSON null
```

Meaning, your numbers will always be of typefloat64, and will need to be casted to int, for example. If you have a particular need to get ints directly, you can use the UseNumber method. Which gives you an object which can be converted to either a float64 or an int at your discretion.

Similarly, all objects decoded into an interface will be map[string]interface{}, and will need to be manually mapped to whatever struct you may wish to place them in.

[Sobre JSON](https://eager.io/blog/go-and-json/)

---

## TIME

`import "time"`  

`now := time.Now()` - nos da la hora actual 2012-10-31 15:50:13.793654 +0000 UTC

`then := time.Date(2015, 10, 10, 18, 30, 08, 0, time.UTC)` - Creamos un struct de tiempo asociado a una localizacion (time zone)   

```go
then.Year()
then.Month()
then.Day()
then.Hour()
then.Minute()
then.Second()
then.Nanosecond()
then.Location()
then.Weekday()
then.Before(now)
then.After(now)
then.Equal(now)
```

`diff := now.Sub(then)` - metodo Sub devuelve duracion del intervalo entre dos tiempos  

```go
diff.Hours()
diff.Minutes()
diff.Seconds()
diff.Nanoseconds()
// avanzar o retroceder en el tiempo
then.Add(diff)
then.Add(-diff)

// sumar tiempo
tiempoQueSea.Add( 1000 * time.Hours)
```

* **String to Time**


```go
// pasar una fecha a string segun un determinado formato
layout := "2006-01-02 15:04:05"  
t, err := time.Parse(layout1, start)
if err != nil {
    fmt.Prinltln(err)
}

// Hora actual a string con determinado formato  
horaActual = time.Now().Format(layout)
```

### Timestamp

`Unix epoch`

```go
now := time.Now()
secs := now.Unix()
nanos := now.UnixNano()
millis := nanos / 1000000

time.Unix(secs, 0)
time.Unix(0, nanos)
```

### Intervalos

`timers` - para hacer algo una vez dentro de un tiempo  

```go
timer1 := time.NewTimer(time.Second * 2)
<-timer1.C
fmt.Println("Timer 1 expired")
timer2 := time.NewTimer(time.Second)
go func() {
    <-timer2.C
    fmt.Println("Timer 2 expired")
}()
stop2 := timer2.Stop()
if stop2 {
    fmt.Println("Timer 2 stopped")
}
// Timer 1 expired
// Timer 2 stopped
// Timer 2 expired NUNCA APARECE, se cancela antes
```

`tickers` -  para hacer algo repetidamente a intervalos regulares  

```go
ticker := time.NewTicker(time.Millisecond * 500)
go func() {
    for t := range ticker.C {
        fmt.Println("Tick at", t)
    }
}()
time.Sleep(time.Millisecond * 1600)
ticker.Stop()
fmt.Println("Ticker stopped")
```

---

## MATH

`import "math"`  

`math.Floor(x float64) float64` - devuelve el entero (int) mas grande poisble menor o igual que x  
`math.Pow(x,y float64) float64` - x elevado a y    

---

## MATH/RAND

`import "math/rand"`  

`rand.Intn(10)`- genera un numero aleatorio entero >= 0 y < 10  
`rand.Float64()`- genera un numero aleatorio >= 0.0 y < 1.0  
`(rand.Float64()*5)+5`- genera entre >= 5.0 y < 10.0   

`s1 := rand.NewSource(time.Now().UnixNano())`- semilla para que no sea siempre igual  
`r1 := rand.New(s1)`- para ir cambiando la semilla  

`rand.Seed(time.Now().UTC().UnixNano())` - otra forma de cambiar la semilla para que no siempre sea igual  

```go
func init() {
	//fmt.Println(`Init from package tracker`)
	r = rand.New(rand.NewSource(time.Now().UnixNano()))
}
var r *rand.Rand
func createRandomString() string {
	const chars = "abcdefghijklmnopqrstuvwxyz0123456789"
	result := ""
	for i := 0; i < lenID; i++ {
		result += string(chars[r.Intn(len(chars))])
	}
	return result
}
```

---

## DATABASE/SQL

[database/sql](http://go-database-sql.org/index.html)

---

## FLAG

`import "flag"`  

Para enviar argumentos a un comando  

[Ejemplos](https://gobyexample.com/command-line-flags)  

---

## CONTAINERS/LIST  

`import containers/list`    

Este paquete contiene una doubly linked list  

>> Linked List --> ![go](/z-static/images/go/linkedList.png)

Cada node la de lista contiene un valor y un puntero al siguiente nodo. Como
esta lista es doblemente enlazada cada nodo tiene tambien un puntero al nodo
anterior

---

## SORT  

`import sort`    


Contiene funciones para ordenar datos arbitrario de slices de ints y floats y
structs definidas por el usuario  

* **s = []strings**

`sort.strings(s)` -  De menor a mayor alfabeticamente  

* ** n = []ints || float32 || float64**

`sort.Ints(n)` -  Ordena los numeros de menor a mayor  
`sort.IntsAreSorted(n)` -  booleano que devuelve si estan ordenados  

* **custom sorting**

```go
package main

import "sort"
import "fmt"

// If I have an array/slice of structs in Go and want to sort them
// using the sort package it seems to me that I need to implement
// the whole sort interface which contains 3 methods:
// https://golang.org/pkg/sort/#Interface  

type ByLength []string

func (s ByLength) Len() int {
    return len(s)
}

func (s ByLength) Swap(i, j int) {
    s[i], s[j] = s[j], s[i]
}

func (s ByLength) Less(i, j int) bool {
    return len(s[i]) < len(s[j])
}

func main() {
    fruits := []string{"peach", "banana", "kiwi"}
    sort.Sort(ByLength(fruits))
    fmt.Println(fruits)
}
```

---

## HASH/CRC32

`import hash/crc32`    


1B-85

---

## CRYPTO/SHA1

`import crypto/sha1`    

[SHA1 ejemplo](https://gobyexample.com/sha1-hashes)


1B-86

---
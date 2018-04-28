---
title: Sorting Structs in Golang
date: 2017-06-01
---

We can not directly use the sorting library `Sort` for sorting structs in Golang. 
In order to sort a struct the underlying struct should implement a special interface called `sort.Interface`

This interface requires three methods 

`Len`, `Swap` and `Less`

Let's try to understand this using an example.

#### Define a struct

```go
type EnvVar struct {
	Name  string
	value string
}
```

Here we have a struct `struct` named `EnvVar` with two fields `Name` and `value`

Next we have to implement the interface.

#### Implementing the interface
 
```go

func (e EnvVar) Len() int {
	return len(e)
}

func (e EnvVar) Less(i, j int) bool {
	return e[i].Name < e[j].Name
}

func (e EnvVar) Swap(i, j int) {
	e[i], e[j] = e[j],es[i]
}
```

The first method returns the how many elements are there in our struct.
We are using the default golang `len()` function for this.

The second method helps us to determine which element in our struct comes first.
Here we are doing a simple string comparision which will give us a lexicographic sort order of the elements.

The third method shuffles the element around.

##### Lets sort some data
```go
func main() {
	envs := EnvSort{
		{Name: "MYSQL_ROOT_PASSWORD", value: "openshift"},
		{Name: "MYSQL_DATABASE", value: "openshift"},
		{Name: "MYSQL_PASSWORD", value: "openshift"},
		{Name: "MYSQL_USER", value: "openshift"},
		{Name: "DB_HOST", value: "openshift"},
		{Name: "DB_DBID", value: "openshift"},
		{Name: "DB_PASS", value: "openshift"},
		{Name: "DB_PORT", value: "openshift"},
		{Name: "DB_USER", value: "openshift"},
	}

	sort.Sort(envs)

	for _, v := range envs {
		fmt.Println(v.Name, v.value)
	}

}
```
##### Output
```
DB_DBID openshift
DB_HOST openshift
DB_PASS openshift
DB_PORT openshift
DB_USER openshift
MYSQL_DATABASE openshift
MYSQL_PASSWORD openshift
MYSQL_ROOT_PASSWORD openshift
MYSQL_USER openshift
```

That's how we can sort any `struct` in golang.

#### Full Code
```go
package main

import (
	"fmt"
	"sort"
)

type EnvVar struct {
	Name  string
	value string
}

type EnvSort []EnvVar

func (slice EnvSort) Len() int {
	return len(slice)
}

func (slice EnvSort) Less(i, j int) bool {
	return slice[i].Name < slice[j].Name
}

func (slice EnvSort) Swap(i, j int) {
	slice[i], slice[j] = slice[j], slice[i]
}

func main() {
	envs := EnvSort{
		{Name: "MYSQL_ROOT_PASSWORD", value: "openshift"},
		{Name: "MYSQL_DATABASE", value: "openshift"},
		{Name: "MYSQL_PASSWORD", value: "openshift"},
		{Name: "MYSQL_USER", value: "openshift"},
		{Name: "DB_HOST", value: "openshift"},
		{Name: "DB_DBID", value: "openshift"},
		{Name: "DB_PASS", value: "openshift"},
		{Name: "DB_PORT", value: "openshift"},
		{Name: "DB_USER", value: "openshift"},
	}

	sort.Sort(envs)

	for _, v := range envs {
		fmt.Println(v.Name, v.value)
	}

}
```

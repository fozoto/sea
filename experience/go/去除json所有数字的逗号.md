# 去除json所有数字的逗号

```shell
package main

import (
	"fmt"
	"regexp"
)

func main() {
	re := regexp.MustCompile(`([0-9]+),`)
	fmt.Println(re.ReplaceAllString(`{"a": "123,451,662.34562", "b": "123,346,897.123435"}`, "$1"))
}
```

---
{"dg-publish":true,"permalink":"/cool-stuff-for-coding/go/better-error-handling/","dg-note-properties":{}}
---

this simply makes go error handling less carpal tunnely

# Code
```go
package must

import (
    "fmt"
    "strings"
)

type mustErr struct{ err error }

func Check[T any](val T, err error, msg string) T {
    if err != nil {
        wrapped := strings.ReplaceAll(msg, "{err}", err.Error())
        panic(mustErr{fmt.Errorf("%s", wrapped)})
    }
    
    return val
}

func Try(fn func() error) (err error) {
    defer func() {
        if r := recover(); r != nil {
            if me, ok := r.(mustErr); ok {
                err = me.err
            } else {
                panic(r)
            }
        }
    }()
    
    return fn()
}
```
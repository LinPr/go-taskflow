# Go-Taskflow
A static DAG Task Computing Framework with taskflow-styled api.

## Feature
- User-friendly programming interface
- Static Tasking and Subflow Tasking supported
- Taskflow visualization

## Example
```go
package main

import (
	"context"
	"fmt"
	"log"
	"os"
	"runtime"

	gotaskflow "github.com/noneback/go-taskflow"
)

func main() {
	executor := gotaskflow.NewExecutor(runtime.NumCPU() - 1)
	tf := gotaskflow.NewTaskFlow("G")
	A, B, C :=
		gotaskflow.NewTask("A", func(ctx *context.Context) {
			fmt.Println("A")
		}),
		gotaskflow.NewTask("B", func(ctx *context.Context) {
			fmt.Println("B")
		}),
		gotaskflow.NewTask("C", func(ctx *context.Context) {
			fmt.Println("C")
		})

	A1, B1, C1 :=
		gotaskflow.NewTask("A1", func(ctx *context.Context) {
			fmt.Println("A1")
		}),
		gotaskflow.NewTask("B1", func(ctx *context.Context) {
			fmt.Println("B1")
		}),
		gotaskflow.NewTask("C1", func(ctx *context.Context) {
			fmt.Println("C1")
		})
	A.Precede(B)
	C.Precede(B)
	A1.Precede(B)
	C.Succeed(A1)
	C.Succeed(B1)

	subflow := gotaskflow.NewSubflow("sub1", func(sf *gotaskflow.Subflow) {
		A2, B2, C2 :=
			gotaskflow.NewTask("A2", func(ctx *context.Context) {
				fmt.Println("A2")
			}),
			gotaskflow.NewTask("B2", func(ctx *context.Context) {
				fmt.Println("B2")
			}),
			gotaskflow.NewTask("C2", func(ctx *context.Context) {
				fmt.Println("C2")
			})
		A2.Precede(B2)
		C2.Precede(B2)
		sf.Push(A2, B2, C2)
	})

	subflow2 := gotaskflow.NewSubflow("sub2", func(sf *gotaskflow.Subflow) {
		A3, B3, C3 :=
			gotaskflow.NewTask("A3", func(ctx *context.Context) {
				fmt.Println("A3")
			}),
			gotaskflow.NewTask("B3", func(ctx *context.Context) {
				fmt.Println("B3")
			}),
			gotaskflow.NewTask("C3", func(ctx *context.Context) {
				fmt.Println("C3")
				// time.Sleep(10 * time.Second)
			})
		A3.Precede(B3)
		C3.Precede(B3)
		sf.Push(A3, B3, C3)
	})

	subflow.Precede(B)
	subflow.Precede(subflow2)

	tf := gotaskflow.NewTaskFlow("G")
	tf.Push(A, B, C)
	tf.Push(A1, B1, C1, subflow, subflow2)
	exector.Run(tf)
	exector.Wait()
	if err := gotaskflow.Visualizer.Visualize(tf, os.Stdout); err != nil {
		log.Fatal(err)
	}
}
```
## What's next
- [ ] Taskflow Composition
- [ ] Taskflow Profiler

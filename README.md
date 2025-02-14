# Go 1.24

Messing around with new features.

## `go tool`

Using https://github.com/air-verse/air as an example of a tool that I'd like to have when developing a project but not
as a dependency of a project.  We used to use `tools.go` as a workaround for this.

References:

- https://go.dev/doc/modules/managing-dependencies#tools
- https://antonz.org/go-1-24/#tool-dependencies
- https://www.jvt.me/posts/2025/01/27/go-tools-124/
- https://github.com/air-verse/air

To add `air` to a project with `go tool`:

1. Add a tool dependency:

    ```sh
    go get -tool github.com/air-verse/air@latest
    ```

1. Test it out:

    ```sh
    go tool air

      __    _   ___
     / /\  | | | |_)
    /_/--\ |_| |_| \_ v1.61.7, built with Go go1.24.0

    mkdir ~/code/go-1.24/tmp
    watching .
    !exclude tmp
    building...
    running...
    hey!Process Exit with Code 0
    ```

1. Get a list of all currently available tools:

    ```sh
    go tool
    ...
    trace
    vet
    github.com/air-verse/air
    ```

1. Tidy up.  Before doing this the `go.sum` file was minimal:

    ```sh
    go mod tidy
    ```

1. Configure `air` with `go tool`:

    ```sh
    go tool air init
    ```

1. Update `.air.toml` to watch and run tests:

   ```diff
   --- /tmp/.air.toml      2025-02-14 13:52:27
   +++ .air.toml   2025-02-14 13:44:01
   @@ -4,12 +4,12 @@

    [build]
    args_bin = []
   -bin = "./tmp/main"
   -cmd = "go build -o ./tmp/main ."
   +bin = "./tmp/main_test -test.v"
   +cmd = "go test -c -o ./tmp/main_test"
    delay = 1000
    exclude_dir = ["assets", "tmp", "vendor", "testdata"]
    exclude_file = []
   -exclude_regex = ["_test.go"]
   +exclude_regex = []
    exclude_unchanged = false
    follow_symlink = false
    full_bin = ""
   ```

1. Then `go run air` will run tests verbosely and watch for changes:

    ```sh
    # go tool air

      __    _   ___
     / /\  | | | |_)
    /_/--\ |_| |_| \_ v1.61.7, built with Go go1.24.0

    watching .
    !exclude tmp
    building...
    running...
    === RUN   TestMain
        main_test.go:6: TODO
    --- FAIL: TestMain (0.00s)
    FAIL
    Process Exit with Code: 1

    ```

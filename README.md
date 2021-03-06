# Task - Simple "Make" alternative

Task is a simple tool that allows you to easily run development and build
tasks. Task is written in Go, but can be used to develop any language.
It aims to be simpler and easier to use then [GNU Make][make].

## Installation

If you have a Go environment setup, you can simply run:

```bash
go get -u -v github.com/go-task/task/cmd/task
```

Or you can download from the [releases][releases] page and add to your `PATH`.

## Usage

Create a file called `Taskfile.yml`. The `cmds` key should contains the
commands of a task:

```yml
build:
  cmds:
    - go build -v -i main.go

assets:
  cmds:
    - gulp
```

Running the tasks is as simple as running:

```bash
task assets build
```

If Bash is available (Linux and Windows while on Git Bash), the commands will
run in Bash, otherwise will run on `cmd` (Windows).

### Task dependencies

You may have tasks that depends on others. Just point them on `deps` will run
them automatically:

```yml
build:
  deps: [assets]
  cmds:
    - go build -v -i main.go

assets:
  cmds:
    - gulp
```

In the above example, `assets` will always run right before `build` if you run
`task build`.

A task can have only dependencies and no commands to group tasks together:

```yml
assets:
  deps: [js, css]

js:
  cmds:
    - npm run buildjs

css:
  cmds:
    - npm run buildcss
```

### Prevent task from running when not necessary

If a task generates something, you can inform Task the source and generated
files, to Task will prevent to run them if not necessary.

```yml
build:
  deps: [js, css]
  cmds:
    - go build -v -i main.go

js:
  cmds:
    - npm run buildjs
  sources:
    - js/src/**/*.js
  generates:
    - public/bundle.js

css:
  cmds:
    - npm run buildcss
  sources:
    - css/src/*.css
  generates:
    - public/bundle.css
```

`sources` and `generates` should be file patterns. When both are given, Task
will compare the modification date/time of the files to determine if it's
necessary to run the task. If not, it will just print
`Task "js" is up to date`.

[make]: https://www.gnu.org/software/make/
[releases]: https://github.com/go-task/task/releases

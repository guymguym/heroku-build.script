# Heroku Buildpack: build.script

Use it if you need a custom buildpack that will call a build.script in the app`s dir.

## Usage

Create a directory for our Heroku app:

```bash
$ mkdir -p myapp/bin
$ cd myapp
```

Here is a simple hello world example of a build.script:

```bash
$ cat - > build.script << 'EOF'
#!/usr/bin/env bash
set -e
case "$1" in
"detect")
    echo "build.script"
    exit 0
    ;;
"release")
    echo "build.script"
    exit 0
    ;;
"compile")
    shift
    BLD="$1"
    echo "-----> Compilation Starting: $*"
    mkdir -p ${BLD}/bin/
    echo "#!/usr/bin/env bash" > ${BLD}/bin/program
    echo "echo hello world" >> ${BLD}/bin/program
    chmod +x ${BLD}/bin/program
    echo "-----> Compilation Finished"
    exit 0
    ;;
*)
    echo "Unknown command: $1"
    exit 1
    ;;
esac
}
EOF

$ chmod +x build.script
$ echo -e "program: bin/program" > Procfile
```

Push the app to Heroku and run our executable:

```bash
$ git init; git add .; git commit -am 'init'
$ heroku create --buildpack http://github.com/guymguym/null-buildpack.git
$ git push heroku master
$ heroku run program
Running `program` attached to terminal... up, run.8663
hello world
```

## Motivation

TODO - I can explain


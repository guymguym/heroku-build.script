# Heroku Buildpack: build.script

Use it if you need a custom buildpack that will call a build.script in the app dir, instead of using a predefined buildpack sequence.

## Motivation

This allows creating a custom script which is part of your source tree, that contains a customized build sequence for your heroku app.

Since the build script is located inside the app you push to heroku, you can actually change the build system without a need to create a specific buildpack.

This was forked from the original null-buildpack by ryandotsmith.

## Usage

Create a directory for our Heroku app:

```bash
$ mkdir myapp
$ cd myapp
```

Now create an executable build.script file in your app`s root dir. 

This file will be executed with the arguments from heroku buildpack: build.script detect|compile|release \<build-dir\> [cache-dir]. 

See https://devcenter.heroku.com/articles/buildpack-api for more info.

Notice that the build.script file can be implemented using any executable/interpreted format that heroku stack will be able to run.

If you do not like or cannot use the name build.script, you can provide a file called .build.script in the app root dir which will contain a different name (for example try: echo "shala/lala/lala/ding.dong" > .build.script and make sure to put your build.script content in that path).

Here is a simple hello world example of a build.script written in bash:

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
$ heroku create --buildpack https://github.com/guymguym/heroku-build.script.git
$ git push heroku master
$ heroku run program
Running `program` attached to terminal... up, run.8663
hello world
```


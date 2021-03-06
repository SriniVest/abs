# System (shell) commands

Executing system commands is one of the most important features
of ABS, as it allows the mixing of conveniency of the shell with
the syntax of a modern programming language.

Commands are executed either with `$(command)` or `` `command` ``,
which resemble Bash's syntax to execute commands in a subshell:

``` bash
date = $(date) # "Sun Apr 1 04:30:59 +01 1995"
date = `date` # "Sun Apr 1 04:30:59 +01 1995"
```

As you can see, the return value of a command is a simple
string -- the output of the program. If the program was to
encounter an error, the same string would hold the error
message:

``` bash
date = $(dat) # "bash: dat: command not found"
```

It would be fairly painful to have to parse strings
manually to understand if a command executed without errors;
in ABS, the returned string has a special property `ok` that
checks whether the command was successful:

``` bash
ls = $(ls -la)

if ls.ok {
    echo("hello world")
}

# or

if `ls -la`.ok {
    echo("hello world")
}
```

## Executing commands in background

Sometimes you might want to execute a command in
background, so that the script keeps executing
while the command is running. In order to do so,
you can simply add an `&` at the end of your script:

``` bash
`sleep 10 &`
echo("This will be printed right away!")
```

You might also want to check whether a command
is "done", by checking the boolean `.done` property:

``` bash
cmd = `sleep 10 &`
cmd.done # false
`sleep 11`
cmd.done # true
```

If, at some point, you want to wait for the command
to finish before running additional code, you can
use the `wait` method:

``` bash
cmd = `sleep 10 &`
echo("This will be printed right away!")
cmd.wait()
echo("This will be printed after 10s")
```

## Interpolation

You can also replace parts of the command with variables
declared within your program using the `$` symbol:

``` bash
file = "cpuinfo"
x = $(cat /proc/$file)
echo(x) # processor: 0\nvendor_id: GenuineIntel...
```

and if you need `$` literals in your command, you
simply need to escape them with a `\`:

``` bash
$(echo $PWD) # "" since the ABS variable PWD doesn't exist
$(echo \$PWD) # "/go/src/github.com/abs-lang/abs"
```

## Limitations

Currently, commands that use the `$()` syntax need to be
on their own line, meaning that you will not
be able to have additional code on the same line.
This will throw an error:

``` bash
$(sleep 10); echo("hello world")
```

Note that this is currently a limitation that will likely
be removed in the future (see [#41](https://github.com/abs-lang/abs/issues/41)).

Also note that, currently, the implementation of system commands
requires the `bash` executable to [be available on the system](https://github.com/abs-lang/abs/blob/5b5b0abf3115a5dd4dfe8485501f8765985ad0db/evaluator/evaluator.go#L696-L722).
On Windows, commands are executed through [cmd.exe](https://github.com/abs-lang/abs/blob/ee793641be09ad8572c3e913fef8468f69b0c0a2/evaluator/evaluator.go#L1101-L1103).
Future work will make it possible to select which shell to use,
as well as bypassing the shell altogether (see [#73](https://github.com/abs-lang/abs/issues/73)).

## Next

That's about it for this section!

You can now head over to read about [operators](/syntax/operators).
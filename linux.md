## Nohup and background processes

To make it a background process for whatever reason, you can simply do `nohup [command & parameters] > /dev/null 2>&1 &`. 

> The `&` at the end will run whatever the supplied command is in the background of the current shell session, so it's a child process that will be killed at the end of the current session. 
>
> `nohup` changes the parent of the child process to `1` when the session ends, which is the `init` process (which is the first process to start when a Linux machine boots up, and runs until the system shuts down), so the specified command will continue to run in the background.
>
> `> /dev/null 2>&1` redirects output of the command you specified to /dev/null, which discards all data sent to it, so it discards the command output. 
>
> - The `2` is the file descriptor for `stderr` (standard error), and `1` is the file descriptor for `stdout` (standard output). 
> - `2>&1` redirects `stderr` to the location of the first file descriptor (`stdout`), and then the `> /dev/null` at the beginning redirects `stdout` to `/dev/null`, which effectively silences all output.

To kill a background process, you can use `pgrep [command/process name]` to get the process ID, and then use that with `kill -9 [PID]`, to kill the background process (where PID is process ID), i.e:

```bash
killID=$(pgrep [command/process name]) #assigns the PID returned from pgrep to the killID variable
kill -9 $killID #the program with PID 

```

#### Named Pipes

You can use these to send commands to background processes, but you can also use it to send data between programs, as an example do this in across 2 terminal windows:

```bash
cd $HOME #make sure you're in a common directory in both terminals

#Terminal 1 only
mkfifo test_pipe #a pipe with name test_pipe is created in the current directory, actually displayed as a file by ls commands
ls -l > test_pipe #output of ls -l is piped through test_pipe, this command waits until you do the thing below in Terminal 2

#Terminal 2 only
cat < test_pipe #this directs stuff from the pipe to the cat command, at which point the `ls -l > test_pipe` command stops hanging

```

The reason why the command in Terminal 1 'hangs', is due to blocking - the pipe needs one end to be outputting to something and one to input to something to work.

Note that, as the pipe is effectively connecting 2 locations, it doesn't work for multiple output locations. i.e, if you tried to do the `cat < test_pipe` line in another terminal, Terminal 3, it wouldn't necessarily print anything anything, as the output data has already left the pipe.

I mentioned that a pipe is displayed as basically a file, and it is, except there are some differences, such as that the file name has a `|` after it, to indicate it's a pipe, and `ls -l` shows that it's a pipe by also having a `p` as the left most column of the row of the pipe.

You can also use named pipes to pass data to background processes:

```bash
mkfifo /tmp/srv-input #makes a pipe at the specified location with the name srv-input
tail -f /tmp/srv-input | nohup [command/process name] > /dev/null 2>&1 &
#pipes the newest addition of /tmp/srv-input to the background process

```

`tail -f` essentially watches a file for changes, and would output the newest addition, and then `|` pipes the output to the background process.

This means we can do the following to pass data to the background process:

```bash
echo "[some command]" > /tmp/srv-input #will pass the command to the background process

```
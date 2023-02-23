# Flux Commands

A proposal for how the flux cli should be designed. This proposal places
the most commonly used or needed commands at the top, and strives for an intuitive,
simple form. 

## flux

The flux command group is intended for the user submitting jobs. It should be simple,
intuitive, and predictable.

```console
admin              Administrative commands
alloc              Interactively allocate a new flux instance and run a command/script within
attach             Interactively attach stdin/stdout/stderr to job
batch              Allocate a new flux instance and run a command/script within
cancel             Cancel one or more jobs
id                 Convert jobid(s) to another form
info               Display info for one or more jobs
kill               Forcefully kill one or more running jobs
signal             Send a signal (e.g., raise exception) on one or more jobs
cron               Schedule tasks on timers and events
exec               Execute processes across flux ranks in a job
jobs               Show queued, running, and historical flux jobs
prune              Purge the oldest inactive jobs
resource           List/manipulate Flux resource status
run                Interactively run a command/script in the existing Flux instance
ssh                Create proxy environment for Flux instance
submit             Run a command/script in the existing Flux instance
system             System commands
version            Display flux version information
wait               Wait for job(s) to complete  
```

### Submit/Run a Flux Job

#### Interactively

Run a flux job in an existing flux instance

```console
# Submit a job directly to get back an id
$ flux run echo 'hello world'
hello world

# Submit a flux job file
$ flux run -f mybatch.sh
hello world
```

Allocate and run a command within a **new flux instance**

```console
# Allocate a new flux instance and run a command
$ flux alloc echo 'hello world'
hello world

# Allocate a new flux instance with a flux job file
$ flux alloc -f mybatch.sh
hello world
```

#### Non-interactively

Run a flux job in an existing flux instance

```console
# Submit a job directly to get back an id
$ flux submit echo 'hello world'
ƒhejTu4KTFm

# Submit a flux job file
$ flux submit -f mybatch.sh
ƒhejTu4KTFm
```

Allocate and run a command within a **new flux instance**

```console
# Submit a job directly to get back an id
$ flux batch echo 'hello world'
ƒhejTu4KTFm

# Submit a flux job file
$ flux batch -f mybatch.sh
ƒhejTu4KTFm
```

### flux cancel

Cancel flux jobs

```console
# Cancel a job with a particular id
$ flux cancel <jobid>

# Cancel all flux jobs (with confirmation, limited to user namespace)
$ flux cancel --all

# Cancel all flux jobs (with force)
$ flux cancel --all --force

# Cancel all flux jobs (with confirmation, all users)
$ sudo flux cancel --all --users all
```

### flux kill

Kill flux jobs, forcefully cancelling if in the queue,
if running send a SIGTERM. This is the "not nice way" to cancel.

```console
# Kill a job with a particular id
$ flux kill <jobid>

# Kill all flux jobs (with confirmation, limited to user namespace)
$ flux kill --all

# Kill all flux jobs (with force)
$ flux kill --all --force

# Kill all flux jobs (with confirmation, all users)
$ sudo flux kill --all --users all
```

### flux jobs

Flux jobs queue listing

```console
# Show all jobs
$ flux jobs

# Show the last job submitted
$ flux jobs --last

# Filter based on a regular expression (previously pgrep)
$ flux jobs --filter <regex>
```


### flux signal

Send a custom signal to a job

```console
# Send signal 9 to a job
$ flux signal <jobid> 9

# Send signal 9 to all jobs
$ flux signal 9 --all
```

### flux info

Get information about jobs.

```console
# Get info about a specific job
$ flux info <jobid>

# Get job events
$ flux info <jobid> --events

# Get job time remaining (or just have this display in job info?)
$ flux info <jobid> --timeleft
```

### flux admin

The flux admin command group is for flux debugging or administrator usage,
usually making a change.

```console
config             Manage/query Flux configuration
dump               Write KVS snapshot to portable archive
dmesg              manipulate broker log ring buffer
{get,set,ls}attr   Access, modify, and list broker attributes
namespace          Convert job ids to job guest kvs namespace names
ping               measure round-trip latency to Flux services
restore            Read KVS snapshot from portable archive
wait-event         Wait for an event 
memo               Post an RFC 21 memo to a job
taskmap            Utility function for working with job task maps
queue              Manipulate flux queues
```

#### Example
```console
# Restore KVS snapshot from a portable archive
$ flux admin restore archive.tar.gz
```

## flux system

Flux system displays information about a system.

```console
env                Print the flux environment or execute a command inside it
overlay            Show flux overlay network status
kvs                Flux key-value store utility
pstree             display job hierarchies
shutdown           Shut down a Flux instance
start              bootstrap a local Flux instance
logs               Show Flux instance start and stop times
top                Display Running Flux Jobs
uptime             Tell how long Flux has been up and running
```

#### Example
```
# Start flux instance
$ flux system start
```

### flux system logs

This should allow display of timing / log information (originally was `startlog`)

```console
# Show flux instance start and stop times
$ flux system logs
```

We only have one group under here but it could be expanded.

## flux

The root is relevant to submitting or showing information about jobs. This design makes sense
because most users will see this.


## unknown

I don't understand these commands enough to make a suggestion

Is this like scp?

```
filemap            Map files into a Flux instance
```

Why would I want to do this? Shouldn't I define urgency when I submit?
(Should this be an admin command not accessible to standard users?)

```
urgency         Set job urgency (0-31, HOLD, EXPEDITE, DEFAULT)
```

I don't understand use cases for this:

```
status          Wait for job(s) to complete and exit with largest exit code
```

## flux batch spec

We need to design a spec file that can handle submitting multiple jobs, or batch,
and maybe have directives that users are asking for (either as directives or another format).


## Notes

- bulksubmit is removed, it's confusing to have submit, batch, and bulksubmit
- instead of bulksubmit, submit either takes a file (with `-f` or not) 
- I don't understand the difference between "enqueue in bulk" and "submit" but I would rather have one command to submit a job.
- flux stats and info are merged into just flux info
- timeleft should be a part of job info
- last should be part of jobs
- "purge" might be prune or gc (garbage collect)
- "proxy" renamed to ssh so more intuitive
- `eventlog` should be `flux info <jobid> --events`
- pgrep should be a part of flux jobs `--filter`
- startlog renamed to logs

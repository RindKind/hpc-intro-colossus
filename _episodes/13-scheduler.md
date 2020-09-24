---
title: "Scheduling jobs"
teaching: 45
exercises: 30
questions:
- "What is a scheduler and why are they used?"
- "How do I launch a program to run on any one node in the cluster?"
- "How do I capture the output of a program that is run on a node in the cluster?"
objectives:
- "Run a simple Hello World style program on the cluster."
- "Submit a simple Hello World style script to the cluster."
- "Use the batch system command line tools to monitor the execution of your job."
- "Inspect the output and error files of your jobs."
keypoints:
- "The scheduler handles how compute resources are shared between users."
- "Everything you do should be run through the scheduler."
- "A job is just a shell script."
- "If in doubt, request more resources than you will need."
---

## Job scheduler

An HPC system might have thousands of nodes and thousands of users. How do we decide who gets what
and when? How do we ensure that a task is run with the resources it needs? This job is handled by a
special piece of software called the scheduler. On an HPC system, the scheduler manages which jobs
run where and when.

The following illustration compares these tasks of a job scheduler to a waiter in a restaurant.
If you can relate to an instance where you had to wait for a while in a queue to get in to a 
popular restaurant, then you may now understand why sometimes your job do not start instantly
as in your laptop.

{% include figure.html max-width="75%" file="/fig/restaurant_queue_manager.svg"
alt="Compare a job scheduler to a waiter in a restaurant" caption="" %}

> ## Job scheduling roleplay (optional)
> 
> Your instructor will divide you into groups taking on different roles in the cluster (users,
> compute nodes and the scheduler). Follow their instructions as they lead you through this
> exercise. You will be emulating how a job scheduling system works on the cluster.
> 
> [*notes for the instructor here*](../guide)
{: .discussion}

The scheduler used in this lesson is {{ site.sched.name }}. Although {{ site.sched.name }} is not
used everywhere, running jobs is quite similar regardless of what software is being used. The exact
syntax might change, but the concepts remain the same.

## Job scripts should not contain any sensitive information 
Job scripts should not contain any sensitive information. Especially the job name should not have personal ids

## Running a batch job

The most basic use of the scheduler is to run a command non-interactively. Any command (or series 
of commands) that you want to run on the cluster is called a *job*, and the process of using a
scheduler to run the job is called *batch job submission*.

In this case, the job we want to run is just a shell script. Let's create a demo shell script to
run as a test. The landing pad will have a number of terminal-based text editors installed. Use
whichever you prefer. Unsure? `nano` is a pretty good, basic choice.

```
{{ site.remote.prompt }} nano example-job.sh
{{ site.remote.prompt }} chmod +x example-job.sh
{{ site.remote.prompt }} cat example-job.sh
```
{: .bash}

```
#!/bin/bash

# Project:
#SBATCH --account=p33_norment

# Memory:
#SBATCH --mem-per-cpu=1

# Wall clock limit:
#SBATCH --time=00:10:00

echo -n "This script is running on "
hostname
```
{: .output}

> ## Creating our test job
> 
> Run the script. Does it execute on the cluster or just our login node?
>
> > ## Solution
> >
> > ```
> > {{ site.remote.prompt }} ./example-job.sh
> > ```
> > {: .bash}
> > ```
> > This script is running on {{ site.remote.host }}
> > ```
> > {: .output}
> > 
> > This job runs on the login node.
> {: .solution}
{: .challenge}

If you completed the previous challenge successfully, you probably realise that there is a
distinction between running the job through the scheduler and just "running it". To submit this job
to the scheduler, we use the `{{ site.sched.submit.name }}` command.

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {{ site.sched.submit.options }} example-job.sh
```
{: .bash}

{% include {{ site.snippets }}/13/basic-job-script.snip %}

And that's all we need to do to submit a job. Our work is done -- now the scheduler takes over and
tries to run the job for us. While the job is waiting to run, it goes into a list of jobs called 
the *queue*. To check on our job's status, we check the queue using the command
`{{ site.sched.status }} {{ site.sched.flag.user }}`.

```
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```
{: .bash}

{% include {{ site.snippets }}/13/basic-job-status.snip %}

The best way to check our job's status is with `{{ site.sched.status }}`. Of course, running 
`{{ site.sched.status }}` repeatedly to check on things can be a little tiresome. To see a real-time
view of our jobs, we can use the `watch` command. `watch` reruns a given command at 2-second
intervals. This is too frequent, and will likely upset your system administrator. You can change
the interval to a more reasonable value, for example 15 seconds, with the `-n 15` parameter. Let's
try using it to monitor another job.

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {{ site.sched.submit.options }} example-job.sh
{{ site.remote.prompt }} watch -n 15 {{ site.sched.status }} {{ site.sched.flag.user }}
```
{: .bash}

You should see an auto-updating display of your job's status. When it finishes, it will disappear
from the queue. Press `Ctrl-C` when you want to stop the `watch` command.

> ## Where's the output?
>
> On the login node, this script printed output to the terminal -- but when we exit `watch`,
> there's nothing. Where'd it go?
>
> Cluster job output is typically redirected to a file in the directory you launched it from.
> Use `ls` to find and read the file.
{: .discussion}

## Customising a job

The job we just ran used all of the scheduler's default options. In a real-world scenario, that's
probably not what we want. The default options represent a reasonable minimum. Chances are, we will
need more cores, more memory, more time, among other special considerations. To get access to these
resources we must customize our job script.

Comments in UNIX shell scripts (denoted by `#`) are typically ignored, but there are exceptions.
For instance the special `#!` comment at the beginning of scripts specifies what program should be
used to run it (you'll typically see `#!/bin/bash`). Schedulers like {{ site.sched.name }} also
have a special comment used to denote special scheduler-specific options. Though these comments
differ from scheduler to scheduler, {{ site.sched.name }}'s special comment is 
`{{ site.sched.comment }}`. Anything following the `{{ site.sched.comment }}` comment is interpreted
as an instruction to the scheduler.

Let's illustrate this by example. By default, a job's name is the name of the script, but the 
`{{ site.sched.flag.name }}` option can be used to change the name of a job. Add an option to the
script:

```
{{ site.remote.prompt }} cat example-job.sh
```
{: .bash}

```
#!/bin/bash

# Job name:
#SBATCH --job-name=Testjob
# Project:
#SBATCH --account=p33_norment
# Nodes:
#SBATCH --nodes=1
# Cores:
#SBATCH --ntasks=1
# Wall clock limit:
#SBATCH --time=00:10:00
# Max memory usage:
#SBATCH --mem-per-cpu=4G

## Set up job environment:
module purge   # clear any inherited modules
set -o errexit # exit on errors

echo -n "This script is running on "
hostname

```
{: .output}

Submit the job (using `{{ site.sched.submit.name }} {{ site.sched.submit.options }} example-job.sh`)
and monitor it:

```
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```
{: .bash}

{% include {{ site.snippets }}/13/job-with-name-status.snip %}

Fantastic, we've successfully changed the name of our job!


## Cancelling a job

Sometimes we'll make a mistake and need to cancel a job. This can be done with the 
`{{ site.sched.del }}` command. Let's submit a job and then cancel it using its job number (remember
to change the walltime so that it runs long enough for you to cancel it before it is killed!).

```
{{ site.remote.prompt }} {{ site.sched.submit.name }} {{ site.sched.submit.options }} example-job.sh
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```
{: .bash}

{% include {{ site.snippets }}/13/terminate-job-begin.snip %}

Now cancel the job with its job number (printed in your terminal). A clean return of your command
prompt indicates that the request to cancel the job was successful.

```
{{ site.remote.prompt }} {{site.sched.del }} 38759
# ... Note that it might take a minute for the job to disappear from the queue ...
{{ site.remote.prompt }} {{ site.sched.status }} {{ site.sched.flag.user }}
```
{: .bash}

{% include {{ site.snippets }}/13/terminate-job-cancel.snip %}

{% include {{ site.snippets }}/13/terminate-multiple-jobs.snip %}

## Other types of jobs

* accel 
* dragen

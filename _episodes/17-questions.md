---
title: "Questions"
teaching: 10
exercises: 5
questions:
- "How do we execute a task in parallel?"
objectives:
- "Understand how to run a parallel job on a cluster."
keypoints:
- "Parallelism is an important feature of HPC clusters."
- "MPI parallelism is a common case."
- "The queuing system facilitates executing parallel tasks."
---

We now have the tools we need to run a multi-processor job. This
is a very important aspect of HPC systems, as parallelism is 
one of the primary tools we have to improve the performance of
computationnal tasks.

## Running the Parallel Job

We will run an example that uses the Message Passing Interface (MPI)
for parallelism -- this is a common tool on HPC systems.


Is it what you expected? How good is the value for pi?

##  How and when we need to use "kinit" and "module init" commands.  

[Managing Data on Colossus](Managing Data on Colossus)
For security reasons the /cluster/project/pX and /cluster/software filesystems require additional
Kerberos authentication. Whenever you ssh into one of the submit hosts, you'll need to authenticate
using your password. You're then given a ticket for the duration of 10 hours. Using this ticket
you can access the filesystems without having to provide your password again for the duration
of the ticket, when it is automatically renewed up to a week. You can list your current ticket
status using:

```
klist

```
{: .bash}


```
-bash-4.2$ klist
Ticket cache: FILE:/tmp/krb5cc_1555
Default principal: xx-user@TSD.USIT.NO

Valid starting       Expires              Service principal
09/23/2020 09:43:36  09/23/2020 19:43:36  krbtgt/TSD.USIT.NO@TSD.USIT.NO
        renew until 09/30/2020 09:43:28
09/23/2020 09:43:40  09/23/2020 19:43:36  nfs/ess01.tsd.usit.no@
        renew until 09/30/2020 09:43:28
09/23/2020 09:43:40  09/23/2020 19:43:36  nfs/ess01.tsd.usit.no@TSD.USIT.NO
        renew until 09/30/2020 09:43:28

```
{: .output}


```
-bash-4.2$ cd /cluster/project/p33

```
{: .bash}

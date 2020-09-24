---
title: "Questions"
teaching: 10
exercises: 5
questions:
- "Q"
objectives:
- "A"
keypoints:
- "Q"
---


##  How and when we need to use "kinit" and "module init" commands.  

[Managing Data on Colossus](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/data.html)
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


```
-bash: cd: /cluster/project/p33: No such file or directory

```
{: .output}

The above outputs indicates that you have not obtained a key or your Kerberos key has expired.
If you face the error use the **knit** command to obtain a key and refresh with **module init**

```
-bash-4.2$ knit
Password for xx-user@TSD.USIT.NO:

-bash-4.2$ module init

```
{: .bash}


### How to interpret output of "squeue", "qsumm" and "cost" commands

**squeue**

[Queue System on Colossus](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/queue-system.html)

```
-bash-4.2$ squeue

```
{: .bash}


```
           JOBID PARTITION     NAME     USER ST         TIME  NODES NODELIST(REASON)
           4015649    normal MPsf     p33-ddd  PD         0:00      1 (AssocGrpBillingMinutes)
           4132215    normal 20efef   p33-yttt PD       0:00      1 (Resources)
           4132216    normal ertre    p33-yuuu PD       0:00      1 (Priority)
           .............
           .............
           4121987    normal convertx p33-mmm  PD       0:00      1 (launch failed requeued held)
           .............
           .............
           4131791    normal 2sfs7-0. p33-yyyy  R    2:34:55      1 c2-3
           4131785    normal 20fsd50. p33-yxxx  R    2:35:28      1 c2-3
           .............
           .............

```
{: .output}

 - AssocGrpBillingMinutes: When a project has exceeded the PE hour (cpu hour) limit,
                           jobs will be left pending with the reason "AssocGrpBillingMinutes".
 - Resources: The requested resources are not disposed at the moment
 - Priotiry : Dipending on the resources you had requested, it was better to give chance 
              to someone else the chance before you.  So the overall queue is better managed. 
 - launch failed requeued held :  Node fault, problem is on our side  



State Codes

| Status        | Code  | Explaination                                                                  |
| ------------- | :---: | ----------------------------------------------------------------------        |
| COMPLETED	| `CD`	| The job has completed successfully.                                           |
| COMPLETING	| `CG`	| The job is finishing but some processes are still active (e.g.copying files). |
| FAILED	| `F`	| The job terminated with a non-zero exit code and failed to execute.           |
| PENDING	| `PD`	| The job is waiting for resource allocation. It will eventually run.           |
| PREEMPTED	| `PR`	| The job was terminated because of preemption by another job [Preempt](https://slurm.schedmd.com/preempt.html).                  |
| RUNNING	| `R`	| The job currently is allocated to a node and is running.                      |
| SUSPENDED	| `S`	| A running job has been stopped with its cores released to other jobs.         |
| STOPPED	| `ST`	| A running job has been stopped with its cores retained.                       |


**qsumm**
[Dedicated Resources](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/dedicated-resources.html)

```
-bash-4.2$ qsumm

```
{: .bash}


```

-bash-4.2$ qsumm
----------------------------------
Account          Limit  nRun nPend
----------------------------------
p33               1824   430  3356
Total             1824   754  3715
----------------------------------
Reservation aihub:
p33                128     0     0
p33_aihub          128     0     0
Total              128     9     0
----------------------------------
Reservation norment:
p33_norment        432   432  1434
Total              432   432  1434
----------------------------------
Reservation norment_dev:
p33_norment_dev     16     0     0
Total               16     0     0
----------------------------------

```
{: .output}

qsumm displays the current usage of CPUs (or rather Processor
Equivalents - PEs) on Colossus.  It is updated every 5 minutes.

- "Limit": how many PEs there are in total
- "nRun":  how many PEs are currently used for running jobs in total and
           for your project
- "nPend": how many PEs worth of pending jobs there are in total and
           for your project



**cost**

```
-bash-4.2$ cost --details

```
{: .bash}


```
Report for account p33 on Colossus
Allocation period 2020.1 (2020-04-01 -- 2020-10-01)
Last updated on Thu Sep 24 10:46:33 2020
===========================================================
Account  Description              Billing hours  % of limit
===========================================================
p33      Used (finished)             1998599.34      62.5 %
p33      Reserved (running)            22857.87       0.7 %
p33      Pending (waiting)            773605.00      24.2 %
p33      Available                    404937.80      12.7 %
p33      Limit                       3200000.00     100.0 %
===========================================================
Account  User                Used billing hours  % of limit
===========================================================
p33      p33-iv                   916239.00      28.6 %
p33      p33-to                   379347.62      11.9 %
p33      p33-ma                   215387.20       6.7 %
p33      p33-mam                   95010.36       3.0 %
p33      p33-of                        0.14       0.0 %
p33      p33-sa                        0.00       0.0 %
===========================================================

```
{: .output}


### p33-specific queues

```

-  ReservationName=aihub StartTime=2019-06-11T23:15:18 EndTime=2024-04-01T00:00:00 Duration=1755-00:44:42
   Nodes=gpu-[2-3] NodeCnt=2 CoreCnt=128 Features=(null) PartitionName=(null) Flags=SPEC_NODES  TRES=cpu=128
   Users=(null) Accounts=....p33,p33_aihub...
-  ReservationName=norment_dev StartTime=2020-08-11T15:43:53 EndTime=2023-04-01T00:00:00 Duration=962-08:16:07
   Nodes=c1-5 NodeCnt=1 CoreCnt=16 Features=(null) PartitionName=normal Flags=IGNORE_JOBS,SPEC_NODES
     NodeName=c1-5 CoreIDs=48-63  TRES=cpu=32
   Users=(null) Accounts=p33_norment_dev,p697_norment_dev Licenses=(null) State=ACTIVE BurstBuffer=(null) Watts=n/a
-  ReservationName=norment StartTime=2020-09-02T13:14:30 EndTime=2023-04-01T00:00:00 Duration=940-10:45:30
   Nodes=c1-[5-7,12],c2-[1-3] NodeCnt=7 CoreCnt=432 Features=(null) PartitionName=normal Flags=IGNORE_JOBS,SPEC_NODES
     NodeName=c1-5 CoreIDs=0-47
     NodeName=c1-6 CoreIDs=0-63
     NodeName=c1-7 CoreIDs=0-63
     NodeName=c1-12 CoreIDs=0-63
     NodeName=c2-1 CoreIDs=0-63
     NodeName=c2-2 CoreIDs=0-63
     NodeName=c2-3 CoreIDs=0-63
   TRES=cpu=864
   Users=(null) Accounts=p33_norment,p697_norment Licenses=(null) State=ACTIVE BurstBuffer=(null) Watts=n/a

```
{: .output}


###  Would it be possible to make a short demo on to use a singularity container on TSD​?

Sorry not today, there is no point in showing a hello world, need to show real use case.


### When there are many jobs submitted 
 -  I feel that we often face a problem when one user submits many jobs, and this causes long delays for
    all other users. It would be great if the user  who need to consume a lot of Colossus resources can
    run his jobs with low-priority or low QoS. Is there a way to do this?
 -  Can you elaborate on how priority of jobs, between users and between batches of jobs, is determined?
    Which factors play along, and how are these weighted?

[Colossus queue system](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/queue-system.html)
To run a job on the cluster, you submit a job script into the job queue, and the job is started when
one or more suitable compute nodes are available. The job queue is managed by a queue system called 
[Slurm](https://slurm.schedmd.com/).

**Queue position, waiting time**
 - Resource requested, (e.g. number of cores)
 - Availability of the requested resource, (did you ask for a full node, GPU)
   - Is there resource you can share with project members
 - Whether have access to a reservation.
 - How many jobs each user has pending in the queue
 - How many jobs the project has pending in the queue
 - Time requested (time waited)

[Colossus queue system](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/queue-system.html)
Colossus starts jobs by priority + backfilling, so small, short jobs can start earlier than jobs with higher priority, 
as long as they do not delay the higher priority jobs. In addition, we have added a limit on how many jobs belonging 
to a user increase in priority over time, to avoid a single user preventing all other users from getting jobs run by 
submitting a large number of jobs at the same time. In this way, the priority will in effect increase for users 
running few jobs relative to users running many jobs. This is a trade-off, and we will adjust the limit (currently 10)
if we see that the effect is too large/small.

**nice**
--nice[=adjustment]
    Run the job with an adjusted scheduling priority within Slurm. With no adjustment value the scheduling priority is 
    decreased by 100. A negative nice value increases the priority, otherwise decreases it. The adjustment range is 
    +/- 2147483645. Only privileged users can specify a negative adjustment. 

```
sbatch --nice=100 low-priority-job.slurm 

```
{: .output}


###  Tips and tricks for optimizing choice
- Tips and tricks for optimizing choice of mem-per-cpu, and cpus-per-task 
  (also wrt balance between risk that it stands long in queue and how quick it runs).

- Ask what your program can use. Can your program use multiple threads. Providing multiple cores to a
   program that can not run in parallel does not speed up the run time

- Several ways to request cores.
  - ntasks
  - cpus-per-task
  - nodes + ntasks
  - nodes + cpus-per-task


### Tips and tricks for selecting the right queue(?)
[job-scripts](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/job-scripts.html)


### How can we best check disk space available, particularly for /cluster?
[Data on colossus] (https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/data.html)
-  The most reliable is to use du, but this will take a lot of time if the content is big

```
df -h /cluster/projects/p33
```
{: .bash}

- Diskusage file [Resources](https://www.uio.no/english/services/it/research/sensitive-data/use-tsd/hpc/resources.html)
 - /cluster/projects/p33/cluster_disk_usage.txt  (nt updated anymore)


###  What is the role of $scratch in the cluster nowadays, still useful to copy for certain jobs I guess, but when?
  -  /cluster/project/p33 is as fast as $SCRATCH
  - If you use $SCRATCH you must mkae sure to copy the needed outputs back to submit folder 
    - chkfile OutputFile
  - This provides good housekeeping
    - Less stress on backup
    - Temporary files do not take space
    - Disk quota not affected
 - If you have large number of files or big files copying to $SCRATCH will slow things down
 - If there are millions of files involved, use local /tmp
   - #SBATCH --gres=localtmp:20
   - cd  $LOCALTMP
 

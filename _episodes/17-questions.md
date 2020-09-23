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

#### squeue

```
-bash-4.2$ squeue

```
{: .bash}


```
           JOBID PARTITION     NAME     USER ST         TIME  NODES NODELIST(REASON)
           4015649    normal MPsf     nobody PD         0:00      1 (AssocGrpBillingMinutes)
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


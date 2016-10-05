Purpose
=======

Scheduler is key feature of modern multi user os. With scheduler, multiple user can run multiple processes on computer. Morevoer interactive system is based on scheduler. But, scheduling costs. Choosing what process to run and how much to learn comsumes processor. And during switching between processes, context must be saved and retrieved. This processor incur cache miss or page fault. For me, those overhead seems great burden to processor. So, I want to check the actual overhead of scheduling.

Plan
====

There are many linux tools that can trace things that happens on computer, from the hardware to software. Those tools can count branch miss, cache miss, page fault, system call, intructions, almost everything. With this tool, I can trace the what's happening on kernel and overhead of scheduler.

Tools
-----

There are many tools that serve my purpose. There are 2 method. Profiling and Tracing. At this point, it's hard for me to distiguish between two methods, but I'll work on tracing because it seems Tracing can record timestamp which is important point to this project. There are tool like Perf, SystemTap, ktap, ftrace. It seems **Perf** is suitable tool that works on linux.(Not sure at this point).

Colleague recommended the dtrace which is the famous tracer in Solaris. Dtrace seems great option, but I want to work on familiar linux enviroment.

Enviroment
----------

I'm not sure about the what kind of enviroment should I set to get proper data. I'll make workloads by calling system call constantly or running many application concurrently. Using cron might be good option.

What to know
============

How to use tracing tool
-----------------------

Great guide about perf <http://www.brendangregg.com/perf.html>

How linux scheduler works
-------------------------

Informative link <http://iloveriver.egloos.com/category/cfs%20%EC%8A%A4%EC%BC%80%EC%A5%B4%EB%9F%AC> <https://www.ibm.com/developerworks/library/l-completely-fair-scheduler/> <https://www.quora.com/How-is-schedule-function-called-in-linux> <https://doc.opensuse.org/documentation/html/openSUSE_121/opensuse-tuning/cha.tuning.taskscheduler.html> <http://stackoverflow.com/questions/22900469/debugging-linux-scheduler> <https://www.quora.com/Linux-Kernel-How-much-processor-time-does-a-process-switching-cost-to-the-process-scheduler>

### Flow of words in linux kernel book

nice, priority, cfs, sleep, find next entity, schedule, redblacktree, schedule class, sched<sub>fifo</sub>, sched<sub>normal</sub>, sched<sub>rr</sub>, ready queue, wake up, switch<sub>mm</sub>, switch<sub>to</sub>, need<sub>resched</sub>, kernel cannot be interupted, preempt<sub>count</sub>, update<sub>rqclock</sub>, shced<sub>setstoptask</sub>, check<sub>preemptcurr</sub>,

### Important properties of CFS Scheduler

-   vruntime
-   group scheduling
-   load balancing

### Scheduler related system<sub>call</sub>

-   nice()
-   sched<sub>setscheduler</sub>()
-   sched<sub>getscheduler</sub>()
-   sched<sub>setparam</sub>()
-   sched<sub>getparam</sub>()
-   sched<sub>getprioritymax</sub>()
-   sched<sub>getprioritymax</sub>()
-   sched<sub>rrgetinterval</sub>()
-   sched<sub>setaffinity</sub>()
-   sched<sub>getaffinity</sub>()
-   sched<sub>yield</sub>()

sched<sub>entity</sub> has cfs<sub>rq</sub> schedule at kernel/sched/core.c 3420 \_<sub>schedule</sub> at kernel/sched/core.c3318

What kind of kernel action should I trace
-----------------------------------------

check the effort to preempt? For example, check<sub>preempttick</sub>(), calc<sub>deltfair</sub>(), sched<sub>slice</sub>() cpu affinity? Or should I check the timer IRQ, ISR such as updating the clock? I'm not sure at this point

How to?
-------

At this point, I found a way to track the scheduler by ftrace. This way I can check exact time of each kernel function's execution time. With this data, I expect to get some clue about overhead. And more over, I'm getting familiar with linux scheduler, so I have positive expectation to get project done. Key point is schedule() function at kernel/sched/core.c. And I should also check the condition when schedule function called.(when kernel enter user)

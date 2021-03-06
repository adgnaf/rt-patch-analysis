cr以linux 4.11-rt为参考对象
1 分析 mainline Linux v.s. Linux-rt-full locl/mutex/sync api
在SMP下，理解每对api的含义，使用场景，特征


| API name                                 | kernel  | critical section | uninterrupt | nopreempt | nomigrate | nosfotirq | sleep/sched |
| :--------------------------------------- | :------ | :--------------: | :---------: | :-------: | :-------: | :-------: | :---------: |
| local_irq_disable/enable                 | vanilla |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
| local_irq_save/restore                   | vanilla |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
| local_bh_disable/enable                  | vanilla |        N         |      N      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        N         |      N      |     N     |     Y     |     Y     |     Y       |
| spin_lock/unlock                         | vanilla |        Y         |      N      |     Y     |     Y     |     N     |     N       |
|                                          | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| spin_lock/unlock_irqsave/restore         | vanilla |        Y         |      Y      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| spin_lock/unlock_bh                      | vanilla |        Y         |      N      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        Y         |      N      |     N     |     Y     |     Y     |     Y       |
| preempt_disable/enable                   | vanilla |        N         |      N      |     Y     |     Y     |     N     |     N       |
|                                          | rt-full |        N         |      N      |     Y     |     Y     |     N     |     N       |
| mutex_lock/unlock                        | vanilla |        Y         |      N      |     N     |     N     |     N     |     Y       |
|                                          | rt-full |        Y         |      N      |     N     |     N     |     N     |     Y       |
| rcu_read_lock/unlock                     | vanilla |        N         |      N      |     Y     |     Y     |     N     |     N       |
|                                          | rt-full |        N         |      N      |     Y     |     Y     |     N     |     N       |
| get/put_cpu                              | vanilla |        N         |      N      |     Y     |     Y     |     N     |     N       |
|                                          | rt-full |        N         |      N      |     Y     |     Y     |     N     |     N       |
| local_irq_disable/enable_nort            | vanilla |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        N         |      N      |     N     |     N     |     N     |     Y       |
| local_irq_disable/enable_rt              | vanilla |        N         |      N      |     N     |     N     |     N     |     Y       |
|                                          | rt-full |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
| local_irq_save/restore_nort              | vanilla |        N         |      Y      |     Y     |     Y     |     Y     |     N       |
|                                          | rt-full |        N         |      N      |     N     |     N     |     N     |     Y       |
| local_irq_save/restore_rt                | vanilla |                  |             |           |           |           |             |  (无此API)
|                                          | rt-full |                  |             |           |           |           |             |  (无此API)
| local_lock/unlock[_irq]                  | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| cpu_relax                                | vanilla |                  |             |           |           |           |             |
| cpu_chill                                | rt-full |                  |             |           |           |           |     Y       |
| get/put_cpu_light                        | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| migrate_disable/enable                   | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| spin_lock/unlock_ _no_mg                 | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| futex_wake                               | vanilla |                  |             |           |           |           |             |
| WAKE_Q\|wake_up_q\|wake_q_add\|mark_wake_futex | rt-full |                  |             |           |           |           |             |
| bit_spin_lock/unlock                     | vanilla |        Y         |      N      |     Y     |     Y     |     N     |     N       | 避免该API用在RT中
| pagefault_disabled_inc/dec               | rt-full |                  |             |           |           |           |             |
| pagefault_disable/enable\|pagefault_disabled\|might_fault | rt-full |                  |             |           |           |           |             |
| in_atomic                                | vanilla |                  |             |           |           |           |             |检测当前上下文状态（是否原子上下文）
| faulthandler_disabled                    | rt-full |                  |             |           |           |           |             |
| get/put_cpu_var                          | vanilla |        N         |      N      |     Y     |     Y     |     N     |     N       |
| lock/unlock_fpu_owner                    | rt-full |        N         |      N      |     Y     |     Y     |     N     |     N       |
| cpu_lock/unlock_irqsave/restore          | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| swait_event|swait_wake_all|swait_event_interruptible | rt-full |                 |             |           |           |           |             |
| get/put_locked_var                       | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| get/put_local_var                        | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| local_lock/unlock_cpu                    | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| bh_uptodate_lock/unlock_irqsave          | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |
| io_schedule                              | rt-full |                  |             |           |           |           |             |
| yield()/msleep(1)                        | rt-full |                  |             |           |           |           |     Y       |
| get/put_online_cpus                      | rt-full |                  |             |           |           |           |             |
| cpu_hotplug_disable/enable               | rt-full |                  |             |           |           |           |             |
| local_lock                               | rt-full |        N         |      N      |     N     |     Y     |     N     |     Y       |
| hotplug_lock                             | rt-full |        Y         |      N      |     N     |     Y     |     N     |     Y       |

* "*" determined by CONFIG_PREEMPT_RCU
* uninterruptible > no preempt
* Preemption (along with some other context info) are maintained by the preempt_count(). Possible
* contexts include task, hardirq, softirq and nmi.
* Mainline Linux has no specific way to disable migration while enable preemption.
* With preemption enabled in the configuration, do not allow preemption = unsleepable.
* Softirq handlers are always invoked in a separate thread with preemption disabled.

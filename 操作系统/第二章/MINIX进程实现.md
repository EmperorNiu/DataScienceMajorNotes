# MINIX进程实现

### MINIX3源码组织

#### 头文件

- 公共头文件
  - src/include
  - src/

#### 进程表

在minix中，进程表被分为三个部分：内核，进程管理器和文件系统服务器

- 内核进程表
  - src/kernel/proc.h
  - memory map（T,D,S): 指针数组，指向物理起始地址和终止地址
  - accounting：使用了多少CPU时间
  - p_rts_flags 消息
  - 

```
struct proc {
  struct stackframe_s p_reg;	/* 存储寄存器 */
  struct segframe p_seg;	/* segment descriptors */
  proc_nr_t p_nr;		/* 进程号 */
  struct priv *p_priv;		/* system privileges structure */
  volatile u32_t p_rts_flags;	/* process is runnable only if zero */
  volatile u32_t p_misc_flags;	/* flags that do not suspend the process */

  char p_priority;		/* 当前优先级y */
  u64_t p_cpu_time_left;	/*剩余时间片 */
  unsigned p_quantum_size_ms;	/* assigned time quantum in ms
				   FIXME remove this */
  struct proc *p_scheduler;	/* who should get out of quantum msg */
  unsigned p_cpu;		/* what CPU is the process running on */
#ifdef CONFIG_SMP
  bitchunk_t p_cpu_mask[BITMAP_CHUNKS(CONFIG_MAX_CPUS)]; /* what CPUs is the
							    process allowed to
							    run on */
  bitchunk_t p_stale_tlb[BITMAP_CHUNKS(CONFIG_MAX_CPUS)]; /* On which cpu are
				possibly stale entries from this process and has
				to be fresed the next kernel touches this
				processes memory
				 */
#endif

  /* Accounting statistics that get passed to the process' scheduler */
  struct {
	u64_t enter_queue;	/* time when enqueued (cycles) */
	u64_t time_in_queue;	/* time spent in queue */
	unsigned long dequeues;
	unsigned long ipc_sync;
	unsigned long ipc_async;
	unsigned long preempted;
  } p_accounting;

  clock_t p_user_time;		/* user time in ticks */
  clock_t p_sys_time;		/* sys time in ticks */

  clock_t p_virt_left;		/* number of ticks left on virtual timer */
  clock_t p_prof_left;		/* number of ticks left on profile timer */

  u64_t p_cycles;		/* how many cycles did the process use */
  u64_t p_kcall_cycles;		/* kernel cycles caused by this proc (kcall) */
  u64_t p_kipc_cycles;		/* cycles caused by this proc (ipc) */

  struct proc *p_nextready;	/* pointer to next ready process */
  struct proc *p_caller_q;	/* head of list of procs wishing to send */
  struct proc *p_q_link;	/* link to next proc wishing to send */
  endpoint_t p_getfrom_e;	/* from whom does process want to receive? */
  endpoint_t p_sendto_e;	/* to whom does process want to send? */

  sigset_t p_pending;		/* bit map for pending kernel signals */

  char p_name[PROC_NAME_LEN];	/* name of the process, including \0 */

  endpoint_t p_endpoint;	/* endpoint number, generation-aware */

  message p_sendmsg;		/* Message from this process if SENDING */
  message p_delivermsg;		/* Message for this process if MF_DELIVERMSG */
  vir_bytes p_delivermsg_vir;	/* Virtual addr this proc wants message at */

  /* If handler functions detect a process wants to do something with
   * memory that isn't present, VM has to fix it. Until it has asked
   * what needs to be done and fixed it, save necessary state here.
   *
   * The requester gets a copy of its request message in reqmsg and gets
   * VMREQUEST set.
   */
  struct {
	struct proc	*nextrestart;	/* next in vmrestart chain */
	struct proc	*nextrequestor;	/* next in vmrequest chain */
#define VMSTYPE_SYS_NONE	0
#define VMSTYPE_KERNELCALL	1
#define VMSTYPE_DELIVERMSG	2
#define VMSTYPE_MAP		3

	int		type;		/* suspended operation */
	union {
		/* VMSTYPE_SYS_MESSAGE */
		message		reqmsg;	/* suspended request message */
	} saved;

	/* Parameters of request to VM */
	int		req_type;
	endpoint_t	target;
	union {
		struct {
			vir_bytes 	start, length;	/* memory range */
			u8_t		writeflag;	/* nonzero for write access */
		} check;
	} params;
	/* VM result when available */
	int		vmresult;

	/* If the suspended operation is a sys_call, its details are
	 * stored here.
	 */
  } p_vmrequest;

  int p_found;	/* consistency checking variables */
  int p_magic;		/* check validity of proc pointers */

  /* if MF_SC_DEFER is set, this struct is valid and contains the
   * do_ipc() arguments that are still to be executed
   */
  struct { reg_t r1, r2, r3; } p_defer;

  u64_t p_signal_received;
  timer_t deadline;
#if DEBUG_TRACE
  int p_schedules;
#endif
};
```

#### 进程的特权级

三个特权级：

- Kernel and interrupt handles
- kernal tasks(linux没有此特权级)
- servers and user processes

#### Boot与bootblock

boot在开机时启动，装入操作系统【09，关机时启动

#### main

cstart函数初始化全局描述符，中断描述符等，再跳转至mian

- 初始化流程
  - 初始化中断控制器
  - 初始化进程表与特权控制表
  - 初始化boot image中的进程
  - 设置IDLE为第一个运行的进程
  - 显示欢迎信息
  - 调用mpx386.s文件中的_restart函数开始进程调度等
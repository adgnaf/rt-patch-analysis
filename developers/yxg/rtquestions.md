## Q1 
preempt_disable: ͨ����preempt������һ���ر���ռ������ý��̱�����  

### ��ʱ��Ҫ��ӻ�ȡ��preempt_disable/disable[_rt]
[2.6.26]ppc-tlbflush-preempt.patch
[2.6.26]send-nmi-all-preempt-disable.patch
[2.6.29]preempt-realtime-x86_64.patch
[2.6.29]preempt-realtime-ipc.patch
[2.6.29]x86-pae-preempt-realtime-fix.patch
[4.1]0011-arm-futex-UP-futex_atomic_op_inuser-relies-on-disabl.patch
[4.9]fs-namespace-preemption-fix.patch
- �������ʻ��޸�per cpu var�Ĵ���Σ�����`write_cr3()`
- ����ͬ�����⣬����`[4.1]0011-arm-futex-UP-futex_atomic_op_inuser-relies-on-disabl.patch`����Ȼ�����ַ����ǲ���ʵʱ�����ֵ����ȶ
- ���һ��patchǡ���෴���Ǵ���ռ��������ѭ������ռһֱ�رգ�ʹ�ò���ѭ������ռ

### ��ʱ��Ҫ��XXX�滻Ϊpreempt_disable/disable[_rt]
��preempt_disable�滻��ֻ��barrier����ֻ��һ��
- barrier() -> preempt_disable()   
[4.9]peterz-percpu-rwsem-rt.patch

��С�˹ر���ռ������ע����˵����preempt_disable�����ٽ������룬������޸Ļ��������ع�

### ��ʱ��Ҫ��preempt_enable�滻Ϊpreempt_enable_nort
_nort��ʵ�־���һ��barrier()������ִ��˳��

[4.8]lglocks-rt.patch
[4.8]lockinglglocks_Use_preempt_enabledisable_nort.patch
[4.9]mm--rt--Fix-generic-kmap_atomic-for-RT.patch
[4.9]arm-enable-highmem-for-rt.patch

- �������еĺ����Լ��ر���ռ�����߿��Ա�����per cpu var���滻������������С�ٽ���������1�У�ʹ��per_cpu_ptr�ر���ռ���ֵ���Ͳ���Ҫ���ⲿʹ��preempt_disable�ر���ռ�ˡ�
- ���е����޸��˲��ֺ�����ʹ��ĳЩ������pagefault_disable)�Դ�preempt_disable/enable����ô�����preempt_disable������Ч�ʡ������޸ľ���pagefault_disable��ء�

## Q2
### ��ʱ��Ҫ��ӻ�ȡ��migrate_disable/disable
�ر�Ǩ�Ƶ�����Ȼ������ռ�����Ա���per cpu var����ռ�����Ľ��̲����޸ĸý��̵�per cpu var�����Ըý���ֻ��Ҫ��֤����Ǩ�Ƴ�ȥ���� 
�д����Ĺر�Ǩ����������ر���ռ������patch��ֻ�ҵ�����������
[3.0]console-make-rt-friendly.patch
[3.18]printk-rt-aware.patch

- ������Ӧ���Ǳ���per cpu var��con����Ϊcon���������cpu��ռ�ģ�����Ǩ�Ʊ�֤����ȷ��

### migrate_disable() -> local_bh_disable()
[3.10]net-netif-rx-ni-use-local-bh-disable.patch

�������Ϊ�ڲ��ĵ��õĺ�����ɾȥ��������Ҫmigrate_disable

### ��ʱ��Ҫ��XXX�滻Ϊmigrate_disable/disable
- preempt_disable() -> migrate_disable()
�ر���ռ��Ϊ�ر�Ǩ�ƣ�ʹ�ñ�����á���������ͬʱ���������ȼ���ת�Ŀ���

֮���patch�����϶���ֻ��Ҫ��thread����cpu�ϣ���֤��ȷ�Լ��ɣ�����Ҫ��ȫ�ر���ռ

���ֻ��Ϊ�˱���per CPU var �Ĵ�ȡ����ôֻ��Ҫ�ر�Ǩ�ƣ���ռ��Ȼ�ǿ�������ġ������������ʵʱ�ԡ�
[3.0]hotplug-use-migrate-disable.patch
[3.10]upstream-net-rt-remove-preemption-disabling-in-netif_rx.patch
[3.18]block-mq-drop-preempt-disable.patch
[3.18]printk-rt-aware.patch
[4.4]dump-stack-don-t-disable-preemption-during-trace.patch
[4.6]KVM-arm-arm64-downgrade-preempt_disable-d-region-to-.patch

## Q3
### ��ʱ��Ҫ��ӻ�ȡ��local_irq_disable/enable, local_irq_save/restore[_nort]
�ر��жϣ����߽�FLAG�����ر��ж�

���µ�patch�����жϣ�ʹ����һ���ִ�������ʱ���տ��ܵ�IPI�ж�

����ͺ��������ʵ����أ���û�����������
[2.6.24]sched-enable-irqs-in-preempt-in-notifier-call.patch

### Enclose with irq_save
[2.6.25]radix-percpu-hack-fix.patch

��patch���ж���Ϊ�˻��Ᵽ����ע����˵Ҳ����ʹ��һ��rt sleeping spinlock���ﵽ��ͬĿ��

[2.6.26]rwlock-protect-reader_lock_count.patch
ͬ��Ҳ�ǻ��Ᵽ�����ڸ�����½���һ��spinlock����ʵ�Ͼ���һ��spinlock_irqsave


### Remove irq-save
��rt�ں��£��ú�������һ���߳��У��Ͳ�����Ҫ�ر��ж���
[3.10]mmci-remove-bogus-irq-save.patch

### local_irq_save() -> raw_local_irq_save()
[2.6.22]arm-fix-atomic-cmpxchg.patch
[2.6.25]i386-mark-atomic-irq-ops-raw.patch
[2.6.26]generic-cmpxchg-use-raw-local-irq-variant.patch

### local_irq_disable() -> local_irq_save(flags)
[2.6.25]rcu-hrt-fixups.patch

### local_irq_save() -> local_irq_save_nort()
�ر��жϱ�Ϊ�ղ�����

�кܶ��������޸ģ������ǲ�����Ҫ�ر��ж�����֤ԭ���ԣ���������Ϊ�ˡ�sleeping���ģ������ڲ����������ֶα�֤ԭ���ԣ���������ĺ�����rt�п��Ա���ռ��������Ҫ����Ĺ��жϣ�����ĺ���������crash���

[2.6.24]local_irq_save_nort-in-swap.patch
[2.6.24]user-no-irq-disable.patch
[3.0]ata-disable-interrupts-if-non-rt.patch
[3.0]drivers-net-gianfar-make-rt-aware.patch
[3.0]drivers-net-vortex-fix-locking-issues.patch
[3.0]fs-ntfs-disable-interrupt-non-rt.patch
[3.0]ide-use-nort-local-irq-variants.patch
[3.0]infiniband-mellanox-ib-use-nort-irq.patch
[3.0]inpt-gameport-use-local-irq-nort.patch
[3.0]mm-scatterlist-dont-disable-irqs-on-RT.patch
[3.0]signal-fix-up-rcu-wreckage.patch
[3.14]user-use-local-irq-nort.patch
[4.11]mm-bounce-local-irq-save-nort.patch

���µ��滻����bugfix
[2.6.24]ntfs-local-irq-save-nort.patch
[3.0]resource-counters-use-localirq-nort.patch
[4.1]sas-ata-isci-dont-t-disable-interrupts-in-qc_issue-h.patch
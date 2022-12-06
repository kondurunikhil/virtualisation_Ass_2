# virtualisation_Ass_2
CMPE 283 Assignment 2


1) Went to https://github.com/torvalds/linux, forked the repo to my account, and cloned it on our GCP VM:
 
2) cd ~ <br />
  git clone https://github.com/kondurunikhil/linux.git<br />
  cd linux<br />
  git status<br />
 git remote -v <br />
![git remote -v](https://github.com/kondurunikhil/virtualisation_Ass_2/blob/main/images/git_remote_v.png)


3) cp /boot/config-5.15.0-1025-gcp ~/linux/.config

4) Install necessary Tools
```
sudo apt-get install build-essential
sudo apt-get install kernel-package
sudo apt-get install ccache 
sudo apt-get install flex
sudo apt-get install bison
sudo apt-get install libssl-dev
sudo apt-get install libelf-dev
```
5) prepare the linux using make prepare command: <br />
```
make prepare
```
6) build all the modules that goes into the linux kernel:<br />
```
make -j 8 modules
```
7) build the linux kernel itself:  <br />
```
make -j 8
```

8) After the modules and kernel is built, we can package them into a format that suitable for booting inside our VM <br />
```
sudo make -j 8 INSTALL_MOD_STRIP=1 modules_install
```
9) Install linux kernal 
```
sudo make -j 8 install
```
10)   for Assignment 2 let's modify ~/linux/arch/x86/kvm/vmx/vmx.c  : <br />
```
    extern atomic_t total_exits_counter;
    extern atomic64_t total_cup_cycles_counter;

    static int vmx_handle_exit(struct kvm_vcpu *vcpu, fastpath_t exit_fastpath)
    {
        uint64_t start_time_counter, end_time_counter;
        int ret;
        arch_atomic_inc(&total_exits_counter);
        start_time_counter = rdtsc();
        ret = __vmx_handle_exit(vcpu, exit_fastpath);
        end_time_stamp_counter = rdtsc();
        arch_atomic64_add((end_time_counter - start_time_counter), &total_cup_cycles_counter);
    }
 ```


--------------------------------------------------

11)  for A2 requirement modify ~/linux/arch/x86/kvm/cpuid.c :
    ```
    atomic_t total_exits_counter = ATOMIC_INIT(0);
    EXPORT_SYMBOL(total_exits_counter);

    atomic64_t total_cup_cycles_counter = ATOMIC64_INIT(0);
    EXPORT_SYMBOL(total_cup_cycles_counter);

    int kvm_emulate_cpuid(struct kvm_vcpu *vcpu)
    {
        u32 eax, ebx, ecx, edx;

        if (cpuid_fault_enabled(vcpu) && !kvm_require_cpl(vcpu, 0))
            return 1;

        eax = kvm_rax_read(vcpu);
        ecx = kvm_rcx_read(vcpu);

        switch(eax) {
            case 0x4FFFFFFC:
                eax = arch_atomic_read(&total_exits_counter);
                break;
            case 0x4FFFFFFD:
                ebx = (atomic64_read(&total_cup_cycles_counter) >> 32);;
                ecx = (atomic64_read(&total_cup_cycles_counter) & 0xFFFFFFFF);
                //printk(KERN_INFO "### Total CPU Exit Cycle Time(hi) in EBX = %u", ebx);
                //printk(KERN_INFO "### Total CPU Exit Cycle Time(lo) in ECX = %u", ecx);
                break;
```


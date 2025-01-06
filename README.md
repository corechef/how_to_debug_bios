# how_to_debug_bios
My notes on debugging firmware on my Laptop. Operating environment is Ubuntu 24.04.1 LTS x86_64. Hardware is a Lenovo ideapad 320-15IKB. I have not updated the firmware ever since I bought this machine at around 2018.


## How to get the firmware flash image
run `sudo flashrom -p internal -r bios_backup.rom`. You may need to disable secureboot feature in your BIOS/UEFI.

## How to Set up Qemu for this
- Make a file called `target.xml` containing
  ```
  <?xml version="1.0"?><!DOCTYPE target SYSTEM "gdb-target.dtd">
  <target>
    <architecture>i8086</architecture>
    <xi:include href="i386-32bit.xml"/>
  </target>
  ```
- Get `i386-32bit.xml` file from this URL: `https://raw.githubusercontent.com/qemu/qemu/master/gdb-xml/i386-32bit.xml`
  - In ubuntu, I get it with the command: `wget https://raw.githubusercontent.com/qemu/qemu/master/gdb-xml/i386-32bit.xml`
 
## How to navigate gdb using this
normal gdb commands like `info registers` work. But we have more in additional scripts provided.
### Stepping through an instruction
use `stepi` gdb command to step a single instruction
### Show Real Mode Context
use `context` script command to print: stack, DS:SI, ES:DI, CPU registers, IP, and EFLAGS.
### All default registers
use `info registers` for info about all registers including control registers, xmm registers, efer, segment registers etc.

- Open qemu with this command: `qemu-system-i386 -bios bios_backup.rom -s -S`
- Open gdb connection with this command: `gdb -ix ./gdb_init_real_mode.txt -ex 'set tdesc filename target.xml' -ex 'target remote localhost:1234'`

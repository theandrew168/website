---
date: 2020-02-27
title: "Device Ownership 001: Longan Nano - Getting Started"
slug: "device-ownership-001"
tags: ["risc-v", "firmware", "longan nano"]
draft: true
---
# Introduction
The [Sipeed Longan Nano](https://www.seeedstudio.com/Sipeed-Longan-Nano-RISC-V-GD32VF103CBT6-Development-Board-p-4205.html) is a small, affordable, 32-bit [RISC-V](https://en.wikipedia.org/wiki/RISC-V) chip.
Despite its minimalism, the Longan Nano provides enough power and peripherals to learn RISC-V assembly and build exciting programs along the way.

The Longan Nano comes with the following components:

* 108 MHz GD32VF103[CBT6] 32-bit CPU
* 128 KB flash storage
* 32 KB sram memory
* 3 LEDs (red, green, and blue)
* 1 USB Type-C port
* 1 microSD card slot
* 160x80 pixel LCD (0.96 inches)
* 2 Buttons (RESET and BOOT)

With the specs out of the way, we can begin the journey of figuring out how to go from a blank text file to controlling all aspects of our hardware.
Get ready to discuss registers, design clean programs, and dive into some datasheets.

# The world's smallest RISC-V program
Seen below is a valid RISC-V program that does absolutely nothing!
```
addi zero, zero, 0
```
This particular combination of words and numbers tells the computer to add 0 to a special register named `zero`.
More specifically, `addi` (shorthand for "add immediate") is an instruction that tells the CPU to add the literal number 0 to the value currently stored in register `zero` and then store the resulting sum back into register `zero`.

What is a register, though?

One analogy for explaining the relationship between a CPU and its registers is to consider a librarian.
The librarian's job is to organize and repair books.
Within arm's reach of their desk, the librarian has a small book shelf capable of holding only 32 books.
If the librarian needs to work on a book that is already in their shelf, then they can instantly pick it up and get to work.
If the book is _not_ on their shelf, however, then they need to stop working, walk deeper into the library, grab the book, and bring it back.
In order to make room for this new book, an old book must be taken back and stored somewhere in the library.

When it comes to computers, we deal with numbers instead of books.
All RISC-V CPUs come with 32 general-purpose registers for holding temporary values to be used by the program.
In the case of the Longan Nano, these registers are 32 bits long meaning that they can store any integer value from -2,147,483,648 up to 2,147,483,647.

All 32 of RISC-V's registers behave the same except for one: the `zero` register.
This register is special because it always holds the integer value 0.
If any other value attempts to get placed there, it effectively disappears.
This register is useful for discarding unwanted values and for when 0 is used in computation.

Knowing what we now know, we can look at our simple program in a bit more detail.
```
addi zero, zero, 0
```
The `addi` instruction takes three parameters: the destination register, the source register, and an immediate integer value.
These parameters are arranged as follows:
```
addi rd, rs1, imm
```

To recap, our simple program is adding the value currently stored in the `zero` register (which is always 0) to the value immediate integer value 0.
This sum (0 + 0 = 0) is then stored back into register `zero`.
Even though the program appears to write 0 to the `zero` register, it doesn't affect it at all.
As we learned with the special `zero` register, all values placed in it disappear.
Even if the result of the sum had been non-zero, the instruction would not have changed the `zero` register.

Now that our simple program is written and understood, how do we convert it to something that CPUs understand?

# Ready, set, assemble!
This section details the steps necessary to "assemble" our simple program.
Assembling is the process of converting human-readable assembly language text to a specific binary representation that a CPU can understand.

To start, we create a directory to hold the our project's development files.
```
mkdir device_ownership
cd device_ownership/
```

Then, since the [assembler](https://en.wikipedia.org/wiki/Assembly_language#Assembler) we are going to use is written in [Python](https://www.python.org), we will install all of the system packages necessary to install additional Python modules.
```
sudo apt install python3-pip python3-venv
```

To keep things organized, we will use a [virtual environment](https://docs.python.org/3/library/venv.html) to isolate our project-specific Python modules from the rest of our system.
```
python3 -m venv venv/
source venv/bin/activate
```
**Note that that `source` command above will be required any time you come back to work on the project!**

With a virtual environment in place, we can install the RISC-V assembler used in this series: [simpleriscv](https://pypi.org/project/simpleriscv/).
```
pip install simpleriscv
```

Last but not least, we can write our simple program to a file and assemble it. 
```
echo "addi zero, zero, 0" > smallest.asm
simpleriscv asm smallest.asm
```

By default, simpleriscv places its output in a file named with a `.bin` extension in place of the original `.asm`.

Congratulations!
You just wrote and assembled the world's smallest RISC-V program!

# Uploading firmware with DFU
clean this up  
explain udev to avoid the sudo? :'(  
explain how upload / download is nutters in DFU  
link that DFU spec like a boss  

```

sudo apt install build-essential git libusb-1.0-0-dev

# build dfu-util from source and install
git clone git://git.code.sf.net/p/dfu-util/dfu-util
cd dfu-util
./autogen.sh
./configure
make -j4
sudo make install
cd ..
rm -r dfu-util/

udev magic
dfu-util -l
dfu-util -a 0 -D smallest.bin -s 0x08000000:0x00020000
dfu-util -a 0 -U firmware.bin -s 0x08000000:0x00020000
```

# Conclusion
did it even work? did it do anything? we can't really know  
in the next post, we will write a program that does something we can see!  
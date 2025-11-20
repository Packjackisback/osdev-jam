---
part: "part-2"
batch: 'osdev'
totalParts: 'total number of parts in the jam'
title: 'Operating Systems Development'
description: 'From 0 to an operating system.'
contributor: 'packjackisback'
contributorSlackID: 'your Slack ID'
thumbnail: 'thumbnail image link'
timeEstimate: ''
difficulty: 'Difficult'
keywords: 'a, list, of, keywords separated by comma space'
presentation: 'link to figma slides'
presentationPlay: 'link to figma slides in presentation mode'
presentationPDF: 'link to pdf of slides'
notes: 'link to notes (optional)'
poster: 'link to poster (optional)'
video: 'link to video (optional)'
---

# Part 1 - Make it boot!

Before we even learn how computers work, what a bootloader is, or what assembly looks like, we are going to make something boot on a computer! Lets do it!

## Step 1 - Make a boot sector:

First, switch to wherever you are doing the development in. I recommend a git repo, but any directory should be fine!

Make a file, 

```
boot.asm
```

inside it, include the following word for word:

```asm
[org 0x7c00]

mov ah, 0x0e
mov al 'H'
int 0x10

jmp $

times 510-($-$$) db 0
dw 0xaa55
```

This is short, but very confusing. Please don't worry about not knowing any line, you will :D.

## Step 2 - Build it:

Run:

```bash
nasm -f bin boot.asm -o boot.bin
```

This will build our boot sector. That's it!

Run it with 
```bash
qemu-system-x86_64 -drive format=raw,file=boot.bin
```

If everything goes well, we should see a black screen, with the letter `H` sitting alone.

Congrats! You just told an (admittedly virtual) cpu to do something, right after booting up!

This is OS development, at its most basic.

## Step 3 - Woah, Woah, WOAH. What did we just make?

Now that we have our running example, lets talk about what just happend.

`[org 0x7c00]`

org tells the assembler what memory address will have the code when the CPU runs.

When the computer starts, it loads the boot sector at 0x7c00 (the reason for this particular number is [complicated](https://halcyondream.org/2025/01/21/Why-does-BIOS-load-boot-code-from-0x7c00.html)), but we are basically telling the assembler to add this magic number to each address we use.

`mov ah, 0x0e`

We need a little explanation of what `mov` is first:

The way I like to think of `mov` is "copy this to here", so in this case `0x0e` is getting copied to the `ah` register. 

Registers are fast storage locations in the CPU. They get used constantly in ASM. Think of them like the variables you are used to in other languages. The cool thing about these registers is they often have special uses!

Going back to the example here, `mov ah, 0x0e`, we are using the `ah` register. 

The `ah` (and `al`) registers are part of the `ax` register. They both hold 8 bit values (`al` is the upper 8 bits, `ah` the lower), and together create a 16 bit register, `ax`. In this case, `ah` holds the bios function number (common ones [here](https://wiki.osdev.org/BIOS#Common_functions)), so when we call the interrupt `0x10`, (we'll get to that in a second) the cpu knows to display whatever is in the `al` register to the screen (in this case `H`!)

`int 0x10`

This is our first interrupt, and is usually associated with displaying things to the screen. Most resources call this the video interrupt, so I will be using this term going forward :D . Nothing can explain this as well as looking at https://wiki.osdev.org/BIOS#INT_0x10 , so I highly recommend reading that now. As a whole the osdev wiki is an amazing resource, and I'd definitely look there first if you don't understand something or are having trouble with something osdev related.

When an interrupt is called, the cpu looks at the `ah` register to figure out what to do (associated with the interrupt code), so calling `int 0x10` on `ah` = `0x0e` tells it to display a character.

As you can see, assembly is a lot of looking up registers and things at first, especially on the osdev side, but don't worry, we'll move away from assembly pretty soon!

`jmp $`

This is a fairly simple instruction, as `$` just means the current instruction address, so it is continously jumping to itself, basically infinitely spinning so the user has time to read the printed text, and it keeps the cpu busy so it doesn't keep going and read random memory.

```asm
times 510-($-$$) db 0
dw 0xaa55
```

These are pretty complicated, and to understand them we must understand how the cpu determines a proper boot sector. In order to be proper, the boot sector must meet 2 criteria:

- it must be 512 bytes in size
This is why we pad the bootloader with 0s after the code.
(`times 510-($-$$) db 0` is super complicated, so lemme break it down real quick. I think it is pretty self explanatory that `times n db 0` would fill n bytes with 0s, and `$` is the current position, and `$$` is the start of the current section. This means that we end up padding the rest of the file with zeros, leaving two spaces for the next requirement)

- it must end in 55 AA
`dw 0xaa55` this writes the signature in little endian, which just flips it.

That's it! I know it is still pretty confusing, but if we were to run this on a live pc, we would be directly telling the cpu what to do! Of course, this 512 byte limit is very strict, so in the future we will work to increase this.


### Optional Challenge

If you want a little challenge, look up the common interrupt functions from the osdev wiki, the si register, and the db directive, and try to get it to print a string to the string, and print out "Hello from " your name.

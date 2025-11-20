---
part: "part-4"
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

# Part 3 - Understanding the boot sector.

Now that we have followed along the last 2 parts, we should be pretty familiar with assembly, and somewhat aquainted with how the cpu loads the bootloader.

Now, we will learn:

- How the bios loads the boot sector into memory
- How to map addresses to segment:offset registers
- How to improve our boot sector to expand later!


## Step 1 - How memory is laid out after boot.

When the BIOS loads the boot sector, it loads the first 512 bytes of the disk to the physical address `0x00007c00`. This is that magic number at the start of our boot.asm, `[org 0x7c00]`.

Our CPU is in real mode right now though, so the addresses are specified by **segment:offset**.

To understand segments and offsets, we need to think a little bit about how our jumping works from before. When we had `jmp loop`, the cpu jumped from its place in the file back up to the associated address. Just like our `si` on a string, the cpu holds a pointer walking along the addresses of the instructions, and through editing this (like with `jmp`!) we can move around the instructions. This pointer is known as `IP`, and this holds the offset from `CS` where the instruction is stored. `CS` holds the base address for the instructions, in this case, `0x0000`. There are similar segments for data `DS`, extra stuff `ES`, and the stack `SS`.

TODO: Make diagram. Reword.

By default when we boot right now, `CS:IP` = `0x0000:0x7c00`, and `DS = ES = SS = 0x0000`

This is hard to understand, but lets just move on for now to update our bootloader to be safer and setup for the next part.

## Step 2 - A better bootloader

Rather than lead you to how to do this, I'm going to show the code, and explain what each does because it won't make much sense for now. We'll keep the printing string example from before, but make it safer and better set up. 

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

start:
	cli               ; disable interrupts while we initialize
	xor ax, ax        ; xoring something by itself creates 0, temporary to clean other segment registers
	mov ds, ax		  ; set data segment to zero
	mov es, ax		  ; set extra segment to zero
	mov ss, ax		  ; set stack to zero
	mov sp, 0x7c00    ; set the stack pointer to 0x7c00, it will grow down from there.
	sti               ; enable interrupts again


	mov si, message       ; move the pointer to the string into source index

loop:
	lodsb             ; set al to [si], and increment si + 1
	cmp al, 0         ; al - 0, then set flags, including zf
	jz end			  ; if zf, then jump to the end
	mov ah, 0x0E	  ; set ah to print character mode
	int 0x10		  ; call video interrupt
	jmp loop		  ; jump back to the start of the loop

end:
    jmp $			  ; spinning spinning spinning

message: db "Hello world from Jackson", 0 ; don't forget to zero pad!

times 510-($-$$) db 0 ; pad to 510 with 0s, leaving 2 spaces for below
dw 0xaa55             ; end file with AA 55
```

Now, instead of just printing the string, we are cleaning and setting everything up. We don't want `es`, `ds`, or `ss` to point to any random address, so we are setting ax to zero, and using it to clean each value. While we do this though, we don't want the cpu to try and do anything with these values, as they haven't been setup right, so we disable and reenable interrupts.

Currently, the memory at 0x7C00 looks like this

TODO make diagram

I'm sure you are tired of using assembly, so in the next step we will make the jump to C!

We learned a little bit about how sectors and offsets work here, but we will go into a lot more detail in the future.


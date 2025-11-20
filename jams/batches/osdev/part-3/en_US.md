---
part: "part-3"
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

# Part 2 - Lets get more comfortable

The last section was a lot, especially if you have never been introduced to assembly. We are going to do a few little things, just to clear some things up and increase our assembly strength. 

First, lets do the optional practice from last time! If you haven't attempted it yet, I recommend trying it, because a lot of Osdev is being willing to learn on your own! 

## Write a string in the boot sector

First, lets open up our example from last time:

```bash
[org 0x7c00]

mov ah, 0x0e
mov al, 'H'
int 0x10

jmp $

times 510-($-$$) db 0
dw 0xaa55
```

We won't really be touching the first and last two lines, but we can go ahead and remove from `mov ah, 0x0e` to `jmp $`.

There *is* an instruction to print a string, but that requires a bunch of things to be setup, and would require a lot of explanation. For now, we will just be using a little bit of programming logic!

If we can only print one character at once, then we obviously could do something like this:

```asm
mov ah, 0x0e
mov al, 'H'
int 0x10
mov al, 'e'
int 0x10
mov al, 'l'
int 0x10
int 0x10
mov al, 'o'
int 0x10
```

but this is long, tedious, and looks stupid.

Instead, lets think: if we can only print one character at a time, and want to print a string, how would we approach this?

That's right! We iterate over the string, printing one character at a time. While in programming languages we typically would use structures like for and while loops, all we have in assembly is jumps. We can define a point to jump to, and through some clever "index" register usage we can loop through our strings!

Lets figure out how to implement this.

First, we should figure out where to put our string. Down at the bottom, right before our padding code, lets type 
```asm
message: db "Hello world from (your name)", 0
```

This loads our string into memory with a nice name to refer to it, and the 0 at the end pads the string with 0. This will be important later!

Next, lets move this message into the source index register, which is a commonly used general purpose register that will store the address of `message`. To do this, we will move message into `si`. Do you remember how to do this? 


We use the `mov` directive, so now our code should look like this:

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

mov si, message       ; move the pointer to the string into source index


message: db "Hello world from Jackson", 0

times 510-($-$$) db 0 ; pad to 510 with 0s, leaving 2 spaces for below
dw 0xaa55             ; end file with AA 55
```

Next, we will need to create our little for loop. In c, it would look like this:

```c
#include <stdio.h>

int main() {
    char message[] = "Hello world from Jackson";
    int i = 0;

    while (message[i] != 0) {
        putchar(message[i]);
        i++;
    }

    return 0;
}
```

But we lack the pretty for loops, so we will instead need to define two addresses: the loop address and the end address. Just as we did for the string, we will create them like this:

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

mov si, message       ; move the pointer to the string into source index

loop:


end:

message: db "Hello world from Jackson", 0

times 510-($-$$) db 0 ; pad to 510 with 0s, leaving 2 spaces for below
dw 0xaa55             ; end file with AA 55
```

Lets also include the spinning code from last time at the end address:

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

mov si, message       ; move the pointer to the string into source index

loop:


end:
    jmp $

message: db "Hello world from Jackson", 0

times 510-($-$$) db 0 ; pad to 510 with 0s, leaving 2 spaces for below
dw 0xaa55             ; end file with AA 55
```

Okay! Now, lets walk through what we need to do for each loop!

First, lets remember that si holds the pointer to message, so if we wanted to access the next character, all we would need to do is increment the pointer by one. This means that the flow to print a string is simply:

- Move the current character that `si` points at to a register (we are using `al` for this so we can print it easily)
- Check if that character is 0, so we can stop at the 0 we put at the end and not print a bunch of zeros
- If that check is true, and `al` is 0, then jump to the end address (with the spinning)
- Make sure `ah` is the display character code from before, `0x0E`
- Run the display character interrupt, `0x10`
- Increment the address on the string, (`si`) by one
- Jump back to the start of this loop and run it again

We know how to do most of this, but we haven't learned conditionals yet.

There are multiple conditionals, but there are really only two that could apply in this case: `cmp` and `and`.

`cmp x y` subtracts the second operand from the first (x-y), but doesn't store the result. Instead, it will set flags based on the result, so if we wanted to check if a register held zero, we would do 

```asm
cmp al, 0
jz end
```

`jz` in this case jumps if the `zf` flag is zero, which is set by cmp. There are also a lot of different flags, and I highly recommend you [research it more](https://en.wikipedia.org/wiki/FLAGS_register).

Now, you just need one last peice and you can create this on your own!

To access the item at an address, use [], so doing [si] would get the current character in this scenario.

Now, try and create this!

<detail>
<summary>Answer:</summary>

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

mov si, message       ; move the pointer to the string into source index

loop:
	mov al, [si]      ; move the current character si is pointing to into al
	cmp al, 0         ; al - 0, then set flags, including zf
	jz end			  ; if zf, then jump to the end
	mov ah, 0x0E	  ; set ah to print character mode
	int 0x10		  ; call video interrupt
	inc si			  ; move the pointer 
	jmp loop		  ; jump back to the start of the loop

end:
    jmp $			  ; spinning spinning spinning

message: db "Hello world from Jackson", 0 ; don't forget to zero pad!

times 510-($-$$) db 0 ; pad to 510 with 0s, leaving 2 spaces for below
dw 0xaa55             ; end file with AA 55
```
</detail>

This pattern with storing the character in al and incrementing si is so common that modern systems support a shorthand directive to do this (`lodsb`), so this code could be shortened to:

```asm
[org 0x7c00]          ; offset to pretend we start at the boot sector address

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

---

## What have we learned?

We have now used:
- memory addresses
- a register that walks through memory
- a loop
- a string
- a new instruction
- conditionals

Hopefully assembly programs continue to get less mysterious, and remember that if you ever get confused or don't know a directive, register, flag, or anything, just look it up!



;Perform the following functions using the logic controller interface.
;1.BCD up counter	2.BCD down counter

.model small

initds macro
	mov ax, @data
	mov ds,ax
endm

init8255 macro
	mov al,cw
	mov dx,cr
	out dx,al
endm

outpa macro
	mov dx,pa
	out dx,al
endm

printf macro msg
	mov ah,9
	lea dx,msg
	int 21h
endm

exit macro
	mov ah,4ch
	int 21h
endm

.stack
.data
pa equ 1190h
cr equ 1193h
cw db 82h
select db 10,13,"select 1:up counter	2:ring counter$"
exitmsg db 10,13,"press any key to exit $"

.code
initds
init8255
printf select
mov ah,1
int 21h
cmp al,'1'
je upcounter
cmp al,'2'
je downcounter
exit

upcounter:mov al,0
up:	outpa
	call delay
	call kbhit
	add al,1
	daa
	cmp al,99h
	jne up
	exit
downcounter:mov al,99h
down:	outpa
	call delay
	call kbhit
	sub al,1
	das
	cmp al,0
	jne down
	exit

delay proc
	mov bx,04ffh
b2:	mov cx,0ffffh
b1:	loop b1
	dec bx
	jnz b2
	ret
delay endp

kbhit proc
	push ax
	mov ah,1
	int 16h
	jnz done
	pop ax
	ret
done:	exit
kbhit endp

;output:
;select choice 1 or 2 for BCD or ring counter
;corresponding choice output is observed on the module
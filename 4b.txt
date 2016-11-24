;display messages fire and help alternately with flickering effects on a 7-
;segment display interface for a suitable period of time. Ensure a flashing
;rate that makes it easy to read both the messages (examiner does not specify
;these delay values nor it is necessary for the student to compute these values)

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

outpb macro
	mov dx,pb
	out dx,al
endm

outpc macro
	mov dx,pc
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

.model small
.stack
.data

pb equ 1191h
pc equ 1192h
cr equ 1193h
cw db 80h

m1 db 10,13,"press any key to exit $"
fire db 8eh,0cfh,0afh,86h
help db 89h,86h,0c7h,8ch

.code
initds 
init8255
printf m1

start:	lea si,fire
	call disp_msg
	call delay
	lea si,help
	call disp_msg
	call delay
	mov ah,1
	int 16h
	jz start
	exit

disp_msg proc
	mov cx,4
next_char:mov bl,8
	mov al,[si]
nxtbit:	rol al,1
	outpb
	push ax
	mov al,00h
	outpc
	mov al,11h
	outpc
	pop ax
	dec bl
	jnz nxtbit
	inc si
	loop next_char
	ret
disp_msg endp

delay proc
	mov bx,0fffh
b2:	mov cx,0ffffh
b1:	loop b1
	dec bx
	jnz b2
	ret
delay endp
end
;Assume any suitable message of 12 characters length and display it in the
;rolling fashion on a 7-segment display interface for a suitable period of time.
;Ensure a flashing rate that makes it easy to read both the messages. (Examiner
;does not specify these delay values nor it is necessary for the student to
;compute these values).

.model small

initds macro
	mov ax,@data
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

.data
pb equ 1191h
pc equ 1192h
cr equ 1193h
cw db 80h
m1 db 10,13,"Press any key to exit $"
txt db 80h,88h,90h,80h,0f8h,82h,92h,99h,0b0h,0a4h,0f9h,0c0h

.code
initds
init8255
printf m1

start:	lea si,txt
	mov cx,12
next_char:mov al,[si]
	call disp_char
	call delay
	call kbhit
	inc si
	loop next_char
	jmp start

kbhit proc
	mov ah,1
	int 16h
	jnz done
	ret
done:	exit
kbhit endp

disp_char proc
	mov bl,8
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
	ret
disp_char endp

delay proc
	mov bx,07ffh
b2:	mov di,0ffffh
b1:	dec di
	jnz b1
	dec bx
	jnz b2
	ret
delay endp
end

;Output: All the 12 char 0 to 9 AB is displayed on the 7-segment display it is
;shifted from right to left
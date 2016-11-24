;Read the status of eight input bits from the logic controller interface and
;display 'FF' if it is even parity bits otherwise display 00. Also display
;Number of 1's in the input data.

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

exit macro
	mov ah,4ch
	int 21h
endm

inpb macro
	mov dx,pb
	in al,dx
endm

outpa macro
	mov dx,pa
	out dx,al
endm

.stack
.data
pa equ 1190h
pb equ 1191h
cr equ 1193h
cw db 82h

.code
initds
init8255
inpb

mov bl,al
mov bh,0
mov cx,8

nextbit:ror al,1
	jnc next
	inc bh
next:	loop nextbit
	mov al,bl
	or al,al
	jpe dispff
	mov al,00h
disp:	outpa
	call delay
	add bh,30h
	mov dl,bh
	mov ah,2
	int 21h
	exit
dispff:	mov al,0ffh
	jmp disp

delay proc
	mov ax,07ffh
b2:	mov cx,0ffffh
b1:	loop b1
	dec ax
	jnz b2
	ret
delay endp
end

;Output: Input is set from switches in the module & output is observed on the
;module. The counter of 1's can be observed in the screen.
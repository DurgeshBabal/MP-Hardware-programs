;Read the status of two 8-bit inputs (x & y) from the logic controller
;interface and display x*y

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

inpb macro
	mov dx,pb
	in al,dx
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


.data
askx db 10,13,"Set value for x,	Then press any key $"
asky db 10,13,"Set value for y, Then press any key $"
pa equ 1190h
pb equ 1191h
cr equ 1193h
cw db 82h

.code
initds
init8255

printf askx
mov ah,8
int 21h
inpb

mov bl,al

printf asky
mov ah,8
int 21h
inpb

mul bl
outpa
call delay
mov al,ah
outpa
exit

delay proc
	mov bx,0fffh
b2:	mov cx,0ffffh
b1:	loop b1
	dec bx
	jnz b2
	ret
delay endp
end

;output: inputs x & y is set through module and output is observed
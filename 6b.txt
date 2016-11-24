;Convert a 16-bit binary value (assumed to be an unsigned integer) to bcd and
;display it from left to right and right to left for specified number of times
;on a 7-segment display interface

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

codes db 0c0h,0f9h,0a4h,0b0h,99h,92h,82h,0f8h,80h,90h
msg1 db 30 dup (0ffh)
num dw 0ffffh

.code
initds
init8255

call convert
mov cx,11
lea si,msg1
add si,3

again:	push cx
	push si
	mov cx,4
nextbit:call display
	dec si
	dec cx
	jnz nextbit
	call delay
	pop si
	pop cx
	inc si
	dec cx
	jnz again
	mov cx,11
	lea si,msg1
	add si,10
nextbit1:call display
	call delay
	dec si
	dec cx
	jnz nextbit1
exit

convert proc
	add si,8
	mov ax,num
fill:	mov dx,0
	mov bx,10
	div bx
	mov bx,dx
	mov bl,codes[bx]
	mov [si],bl
	dec si
	cmp ax,0
	jnz fill
	ret
convert endp

display proc
	mov bl,8
	mov al,[si]
nxt_bit:rol al,1
	outpb
	push ax
	mov al,00h
	outpc
	mov al,11h
	outpc
	pop ax
	dec bl
	jnz nxt_bit
	ret
display endp

delay proc
	push cx
	push bx
	mov cx, 055ffh
outer:	mov bx,0ffffh
inner:	dec bx
	jnz inner
	loop outer
	pop bx
	pop cx
	ret
delay endp

;Output: Output is 65535 for 0ffffh. It is displayed on 7 segment display from
;left to right and from right to left
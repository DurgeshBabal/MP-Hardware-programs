# MP-Hardware-programs
Q1-6


Dear all,

Please note and rectify the following corrections in my manuals.

Software Manual:
•	Page - 35, 1a program, add these lines after 4th line (after JMP EXIT)            
                           MOV AH,09H                      
                           INT 21H 
•	for 6a program, it works only if you give string input which is less than or equal to 9 characters. If you give more than 9 characters, change the logic like doing AAM and adding 3030 etc like we do in our NCR program.

Hardware Manual:
•	Page - 43, that calculator image is from Windows 8 onwards, not in XP. Since you perform lab experiments in XP, that image of calculator never appears. So you guys come up with your own pattern.

•	Page - 34, In CONVERT PROC, add
                            LEA SI, MSG1        as the first line

•	Page - 64, DOWN1 loop

 DOWN1:
CALL DELAY        
MOV AL,BL
OR AL,0F0H
OUTPA
DEC BL
CMP BL, -1
JZ START 
JNZ DOWN1 
JMP INITIAL 

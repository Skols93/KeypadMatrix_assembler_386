.386 
.model small, c

;Definirani konstanti
ADRESAOUT EQU 037AH	 ;CONTROL registar i vo nego vnesuvame vrednosti  za da bidat na izlez i zatoa e OUT
ADRESAIN  EQU 0379H  ;STATUS registar vo nego vcituvame vrednosti i zatoa e IN
;----------------------------------------------------------------------------------------
;Programata raboti taka shto na paralelnata porta ja povrzuvame matricnata tastatura.
;Matricnata tastatura e sostavena od 3 koloni(vertikalni linii-sprovodnici) i 4 redici(horizontalni linii-sprovodnici)
; 1|2|3
;4|5|6
;7|8|9
;#|0|*
;oddeluvame 3, 6, 9 i * i tie odat vo register za vlez na paralelnata porta
;bidejki tie se povrzani na sekoj pin od paralelnata porta i direktno "vleguvaat" vo registarot, tie ke imaat binarna reprezentacija
;S7'| S6| S5| S4|S3|x|x|x| najgornata zica(sprovodnik )  od horizontalnite linii ke bide postavena na S3 i taka natamu se do S6
;kodot mora da gi sporedi vrednostite na registarot S so nekoja decimalna reprezentacija 
;dokolku pritisneme kopce broj 3, vo registarot S treba da imame 10001000, znaci nie mora da ja sporedime vrednosta na registerot 
; so vrednost 136 decimalno.Dokolku e tocno, tasterot broj 3 e pritisnat
;1000000 = 136  vrednosta na registar AL dokolku imame edinica logicka "1" na prva redica
;1001 0000 = 144 vrednosta na registar AL dokolku imame edinica logicka "1" na vtora redica
;1010 0000 = 160 vrednosta na registar AL dokolku imame edinica logicka "1" na treta redica
;1100 0000 = 192 vrednosta na registar AL dokolku imame edinica logicka "1" na cetvrta redica
;postavuvanjeto na tasterite
;idejata e da se povrzat pinovite od tastaturata na paralelnata porta(3pina ke bidat izlez, 4 pina ke bidat vlez)
;iscituvanjeto mora da bide po redosled da eden od trite pina da bide aktiven, lgoicka edinica '1' a drugite pinovi da se logicki nuli
;dokolku eden taster, odnosno linija od 4te pina bide aktiven, spored nekoj slucaj ke go iscitame i ke znaeme koj taster e toj
;kodot ke se dvizi vo toj redosled da gi postavime site pinovi na logicka nula(nekoe inicijaliziranje i resetiranje)
;potoa, postavuvame edinica na samo na edniot od trite pina za vertikalnite linii(koloni) i "cekame" odnosno go vrtime kodot(proveruvame)
;dali ke se pojavi edinica na nekoj od vleznite pinovi (redicite) 
;control register e postaven vaka x|X|X|X|C3'|C2|C1'|C0'|
;kolonite ke bidat postaveni zicano taka shto C0' ke e najleva kolona, odnosno LSB.MSB ke bide C2.
;treba da gi postavime slednite vrednosti vo registarot za da mozeme da pristapime kon citanje na tastaturata
; 0000 1010  = 10 decimalno i so ova kombinacija C0' ke bide logicka edinica (001) 
;0000 1001 = 9 decimalno i so ovaa kombinacija C1' ke bide logicka edinica(010)
; 0000 1111 = 15 decimalno i so ovaa kombinacija C2 ke bide logicka edinica (100)
;nie go setirame registarot STATUS na vrednosti na nas koi ni odgovaraat za da mozeme da pristapime kon citanje na CONTROL registarot

;Podatocen segment
data_seg SEGMENT USE16 'DATA'
	P1 DB "Pritisnat e tasterot: ", 0Ah, 024h
data_seg ENDS
;start code
code_seg SEGMENT USE16 'CODE'
ASSUME cs:code_seg, ds:data_seg

start:
		;adresata ja postavuvame na Data segment vo DS
	MOV AX, data_seg
	MOV DS, AX
		
	;Glavna procedura
main PROC	
		
INIT:
		;Inicijalizacija na vlez i izlez
		;postaveno e na adresa 0x37
	MOV AL, 0d
	MOV DX, ADRESAOUT ;  postavuvame vo AL logicki nuli, a vo DX ja smestuvame adresata na koj register treba da pristapime
	OUT DX, AL ; postavuvame na izlezot(Control register) nuli za da ja resetirame vrednosta na matricnata tastatura(nekoj test, za debug)
	;od dole, vo slednata labela pocnuva na izleznite pinovi da postavuvam edinica i da ja shiftam(pa da moze vleznite pinovi da ja iscitaat)
Red1:
	
	MOV AL, 10d ; stavame 10 dekadno vo reggisterot AL bidejki gore e prikazan nacinot na koj gi dobivme tie vrednosti za da postavime logicka edinica na prvata edinica
	MOV DX, ADRESAOUT; na istata adresa ja postavuvame taa dekadna edinica
	;sega treba da pocnuvame da citame, odnosno registarot ADRESAIN treba da go iscitame i sporedime
	;ushte edna labela za debug ke bide postavena tuka
	MOV DX, ADRESAIN
	IN  AL, DX; vo AL registarot ja imame vrednosta na vleznite pinovi(barame koj taster e pritisnat)(3, 6, 9 ili *)
	
	;vrshime sporedba na AL so decimalni vrednosti i ke znaeme koj taster e pritisnat 
	;gore e objasneto na grub nacin kako se dobivaat decimalnite vrednosti dokolku nekoj taster e pritisnat
K1:
	CMP AL, 136d ; 136 dekadno e nashata prva redica pritisnata na tastaturata
	JNE K2 ; dokolku ne e 136 ke proveruva dali e slednata redica aktivna
	MOV BL, 3d; stavame 1dekadno i ke znaeme dali e aktivna prvata redica
	JMP OK_E ;vo OK_E labelata ke mozeme da ja zgolemime vrednosta na nekoj brojac i na AL da skokne za da mozeme da skenirame 
K2: 
	CMP AL, 144d ;144 dekadno e vtora redica aktivna
	JNE K3 ; dokolku ne e 144 ke proveruva dali e slednata redica aktivna
	MOV BL, 6d ;stavame dekadna 6ka i znaeme deka imame 6ka pritisnato
	JMP OK_E
K3:
	CMP AL, 160d ;160dekadno e treta redica aktivna i
	JNE K4; ako ne e aktivna taa redica odime da proverime dali e slednata redica aktivna
	MOV BL, 9d; ako e aktivna treta redica, vo BX stavame 9 dekadno i znaeme deka taster 9 e pritisnat
	JMP OK_E
K4: 
	CMP AL, 192d ; proveruvame da sporedime dali e 192dekadno, odnsono dali poslednata re
	JNE Red2 ; dokolku ne e isto, ne troshime vreme i skokame vo del kade postavuvame 010, ja setirame vtorata kolona
	MOV BL, 10d; 10 dekadno ni e dzvezda
	JMP OK_E
	
Red2:
	MOV AL, 9d ; stavame 9 dekadno bidejki kako sto e objasneto pogore(setiranje na 2ra kolona) 
	MOV DX, ADRESAOUT; adresa za CONTROL registarot
	
	MOV DX, ADRESAIN
	IN AL, DX ; Vo AL ja imame vrednosta dokolku nekoj taster bil pritisnat (2, 5, 8 ili 0)
KK1:
	CMP AL, 136d ; 136 dekadno ke dobieme dokolku e pritisnata 2ka tasterot
	JNE KK2 ; dokolku ne e pritisnat odi vo labela KK2
	MOV BL, 2d ; vo BX ke postavime 2 dekadno i znaeme deka e pritisnat tasterot 2
	JMP OK_E
KK2:
	CMP AL, 144d ; 144 dekadno proveruvame dali e vrednosta i znaeme deka e pritsnat taster 5
	JNE KK3 ; dokolku ne e pritisnat taster 5 skokame vo labela KK3
	MOV BL, 5d ; vo BX registarot stavame 5 dekadno i znaeme deka e pritistnat taster 5
	JMP OK_E
KK3:
	CMP AL, 160d ; dokolku vrednosta e 160 dekadno togas e pritisnat tasterot 8
	JNE KK4 ; dokolku ne e pritisnat, tasterot 8 togas odime vo labela KK4
	MOV BL, 8d ; stavame 8 dekadno vo BX registarot i znaeme deka e tasterot 8 pritisnat
	JMP OK_E
	
KK4:
	CMP AL, 192d ; dokolku vrednosta na AL e 192d togas e pritisnat tasterot 0
	JNE Red3 ; ne trosime vreme da proveruvame ponatamu i ja kativirame treta kolona
	MOV BL, 0d; stavame dekadna nula vo BX i znaeme deka e pritisnat taster 0
	JMP OK_E; skokame vo labela OK_E

Red3:
	MOV AL, 15d ; stavame 15 decimalno za da ja aktivirame 3tata kolona i da pristapime kon skeniranje(1, 4, 7 ili #)
	MOV DX, ADRESAOUT ; adresa za CONTROL registarot
	
	MOV DX, ADRESAIN ; adresa za STATUS registarot i iscituvanje na istiot vo AL 
	IN  AL, DX ; vo AL ja iscituvame vrednosta odnosno koj taster e pritisnat
KKK1:
	CMP AL, 136d ; dokolku e 136 dekadna vrednosta na AL registarot togas e pritisnata edinica
	JNE KKK2 ; dokolku ne  e pritisnat kopce 1(taster) skokni na labela KKK2
	MOV BL, 1d; vo BX stavame edinica dekadno i znaeme deka e pritisnat taster 1
	JMP OK_E
KKK2:
	CMP AL, 144d ; AL go sporeduvame so 144 dekadno i znaeme deka e pritisnat tasterot 4
	JNE KKK3 ;dokolku ne e pritisnat tasterot 4  odime na labela KKK3
	MOV BL, 4d; vo BX registarot stavame 4ka dekadno i znaeme deka e pritisnat taster 4
	JMP OK_E
KKK3:
	CMP AL, 160d ;AL go sporeduvame so 160 dekadno i znaeme deka e pritisnat tasterot 7
	JNE KKK4 ;dokolku ne e pritistant tasterot 4 odime na labela KKK4
	MOV BL, 7d ;vo BX stavame 7 dekadno i znaeme deka e pritisnat taster so broj 7
	JMP OK_E
KKK4:
	CMP AL, 192d ;AL go sporeduvame so 192 dekadno i togas e pritisnat tasterot # (taraba)
	JNE INIT ;skokame vo Red1 i pocnuvame od novo, ova moze da bide kako While loop
	MOV BL, 11d ; stavame vo BX 11 dekadno i znaeme deka e toa taraba(#)
	JMP OK_E ;skokame vo OK_E ako ne e pritisnat tasterot taraba(#)
	
OK_E:
	;ispishi ja porakata P1 (Pritisnat e tasterot: )
	MOV AH, 09h
	LEA DX, P1
	INT 021h
	
	;Zapisi na ekran  tasterot koj e pritisnat 
	MOV AH, 02h
	MOV DL, BL
	INT 021h
	
main ENDP
;---------------------------------------------------------------------------------------------------------------------------------------
; Kraj na programata 
MOV AX, 04C00h
INT 021h

code_seg ENDS
END start
	
	

At first I thought it was just MSX BASIC program for some MSX compatible machine.
After some digging and hints from article I originally found source listing for that game it came out, that originally it was made for Spectravideo [SVI-328 MKII](http://www.samdal.com/svi328mkii.htm) machine.

It might look strange at first, but it seems that spaces are not needed when using instructions like ``PRINT`` or ``FOR``. Using list of MSX-BASIC instructions like [this one](https://www.msx.org/wiki/MSX-BASIC_Instructions) will help you decode instruction and parameters.

We'll going to do that line, by line.

```
4 COLOR15,0,0
```

This one sets foreground color to 15, background color to 0 and border color to 0.

Let's look at MSX color pallete at this point.

| Number | Color   | Name         |
|:------:|:-------:|:-------------|
| 0      | #000000 | transparent  |
| 1      | #010101 | black        |
| 2      | #3eb849 | medium green |
| 3      | #74d07d | light green  |
| 4      | #5955e0 | dark blue    |
| 5      | #8076f1 | light blue   |
| 6      | #b95e51 | dark red     |
| 7      | #65dbef | cyan         |
| 8      | #db6559 | medium red   |
| 9      | #ff897d | light red    |
| 10     | #ccc35e | dark yellow  |
| 11     | #ded087 | light yellow |
| 12     | #3aa241 | dark green   |
| 13     | #b766b5 | magenta      |
| 14     | #cccccc | gray         |
| 15     | #ffffff | white        |

Ok, this one was obvious.

```
10 SH=5
```

SH probably stands for *ship count*. It is set by default to 5.

```
20 CLICKOFF
```

According to [these docs](https://www.atarimagazines.com/creative/v9n9/16_SpectraVideo_SV318_and_S.php) ``CLICK OFF`` switches off keyboard click sound. 

```
30 '
40 REM BLASTAR BY E.R.MUSK
50 '
```

In MSX Basic apostrophe and REM denotes a comment line.

```
60 SCREEN2
```

This one is changing display mode to 2. So it's proper time to check what it really means. This table was also taken from [www.msx.org](https://www.msx.org/wiki/SCREEN).

| Number | Type             | Resolution | Colors
|:------:|:-----------------|:-----------|:-------
| 0      | text             | 40 x 24    | 2
| 1      | text             | 32 x 24    | 16 (2 colors per 8 characters)
| 2      | graphic (block)  | 32 x 8 x 3 | 16 (2 for each 8 x 1 line of block)
| 3 	 | graphic (bitmap) | 64 × 48    | 16

So we have switched to display mode 2 which is block graphics mod. But it's not so obvious how it works. According to this [one talk](https://www.msx.org/forum/msx-talk/development/using-screen-2-graphics) I found... 

```
70 PRINT TAB(13);"BLASTAR"
```

Here ``TAB`` inserts the amount of 13 spaces needed to reach a 14th position for trailing "BLASTAR" title text. This text will be printed in graphics mode using tiles.

![](../media/blastar_logo.png)

```
80 ' BLASTAR TUNE
```

Just a comment.

```
90 PLAY"O3L50CECECDBABADACECEBDACDBDCEGG"
100 PLAY"O4L50CECECDBABADACECEBDACDBDCEGG"
110 PLAY"O3L50CECECDBABADACECEBDACDBDCEGG"
120 PLAY"O4L50CECECDBABADACECEBDACDBDCEGG"
```

Let's look how ``PLAY`` works. It's a string of macro language instructions which describes notes to be played.

It consist of notes ``"C"``, ``"C+"`` or ``"C#"`` (C sharp), ``"D-"`` (D flat), ``"D"``, ``"E"``, ``"F"``, ``"G"``, ``"A"``, ``"B"``, with optional octave number 1-8 (default 4) and optional dots increasing length by 1,5 each dot, like ``"F#4.."``.

Additional operation used here are ``"O3"`` which changes default octave to 3, ``"L50"`` which sets the note length to 50. More you can read [here](https://www.msx.org/wiki/PLAY#Syntax).

Basically it's a simple tune of C, E, C, E, C, D, B, A... in lower and higher octave twice.

```
130 SCREEN1,2
```

```
140 PRINT"  BY E.R.MUSK"
```

```
150 FORT=1TO350:NEXTT
```

Seems to be dummy loop for making a delay.

```
160 CLS
```

Clear screen.

Rest is going to be made...

```
170 LOCATE10,5:PRINT"              BLASTAR":PRINT:PRINT"         DO YOU NEED INSTRUCTIONS"
180 PRINT"                 (Y/N)"
190 LETE$=INKEY$
200 IFE$=""THEN190
210 BEEP
220 IFE$="Y"ORE$="y"THEN250
230 GOTO280
240 ' INSTRUCTIONS
250 SCREEN1:CIRCLE(45,90),0:DRAW"u80r180d80l180"
260 PRINT:PRINT:PRINT:PRINT"        <       USE JOYSTICK        >":PRINT:PRINT"               FOR CONTROL AND":PRINT:PRINT"                 FIRE BUTTON":PRINT:PRINT"                  TO SHOOT"
265 PRINT:PRINT:PRINT:PRINT"    MISSION:DESTROY ALIEN FREIGHTER":PRINT:PRINT"    CARRYING DEADLY HYDROGEN BOMBS":PRINT:PRINT"    AND STATUS BEAM MACHINES"
270 IFINKEY$=""THEN270
280 CLS
290 ' CREATE SPRITES
300 FORR=1TO8
310 READM$
320 T$=T$+CHR$(VAL("&B"+M$))
330 NEXTR
340 SPRITE$(1)=T$
350 FORT=1TO8
360 READS$
370 D$=D$+CHR$(VAL("&B"+S$))
380 NEXTT
390 SPRITE$(2)=D$
400 ONINTERVAL=30GOSUB650
410 FORR=1TO8
420 READX$
430 V$=V$+CHR$(VAL("&B"+X$))
440 NEXTR
450 SPRITE$(3)=V$
460 FORY=1TO8
470 READY$
480 N$=N$+CHR$(VAL("&B"+Y$))
490 NEXTY
500 SPRITE$(4)=N$
510 FORT=1TO8
520 READL$
530 J$=J$+CHR$(VAL("&B"+L$))
540 NEXTT
550 SPRITE$(5)=J$
560 ' MAIN PROGRAM
570 LOCATE0,0
580 PRINT"   SCORE";SC;"           SHIPS";SH
590 LETP=120:C=180
600 H=30:G=15
610 SPRITEON
620 F=STRIG(1)
630 D=STICK(1)
640 PUTSPRITE3,(H,G),14,3
650 IFD=0THEN740
660 IFD=1THENP=P+0:C=C-4
670 IFD=2THENP=P+2:C=C-2
680 IFD=3THENP=P+4:C=C+0
690 IFD=4THENP=P+2:C=C+2
700 IFD=5THENP=P+0:C=C+4
710 IFD=6THENP=P-2:C=C+2
720 IFD=7THENP=P-4:C=C+0
730 IFD=8THENP=P-2:C=C-2
740 PUTSPRITE0,(P,C),14,1
750 IFC<10THENC=10
760 IFC>190THENC=180
770 IFP<10THENP=10
780 IFP>240THENP=240
790 IFY=1THENRETURN
800 PUTSPRITE3,(H,G),14,3
810 IFH=PTHEN1050:IFH=P-1THEN1050:IFH=P+1THEN1050
820 H=H+3
830 IFH>250THENH=10
840 IFF=-1THEN870
850 GOTO620
860 ' SHOOT ROCKETS
870 U=P:W=C-5
880 PLAY"O4V6L50DL30F"
890 PUTSPRITE3,(H,G),14,3
910 H=H+4
920 W=W-6
940 PUTSPRITE2,(U,W),9,2
950 IFH=PTHEN1050
960 Y=1:GOSUB620:Y=0
970 SPRITEON
980 ONSPRITEGOSUB1020
990 IFF=-1THEN870
1000 IFW<-10THEN620
1010 GOTO890
1020 IFW=C-5THEN940
1030 SC=SC+80:FORA=1TO20:PUTSPRITE4,(H,G),14,4:NEXTA:CLS:LOCATE3,0:PRINT"SCORE";SC;"            SHIPS ";SH:SPRITEOFF:G=20+INT(150*RND(-TIME)):GOTO620
1040 '  ALIEN STATUS BEAM/ROCKET BOMB
1050 OO=H:NN=G+5
1060 :
1070 SPRITEON
1080 NN=NN+4
1090 Y=1:PI=P:GOSUB620:Y=0:P=PI
1100 LOCATE15,15:PRINT"            STATUS BEAM"
1110 ONSPRITEGOSUB1180
1120 PUTSPRITE5,(OO,NN),4,5
1130 PLAY"O2L15G"
1140 IFNN>205THEN620
1170 GOTO1060
1180 SH=SH-1:FORT=1TO60:NEXTT:CLS:LOCATE0,3:PRINT"SCORE ";SC;"             SHIPS ";SH:PUTSPRITE5,(128,205),14,5:PUTSPRITE4,(P,C),8,5:G=20+INT(170*RND(-TIME)):H=0: SPRITEOFF:IFSH<0THEN1200
1190 GOTO620
1200 CLS:LOCATE0,0:PRINT"                  BLASTAR":PRINT:PRINT"             FLEET DESTROYED":PRINT:PRINT"      WOULD YOU LIKE ANOTHER GAME"
1210 A$=INKEY$:IFA$=""THEN1210
1220 IFA$="Y"ORA$="y"THENRUN
1230 END
1250 ' SPRITE DATA
1260 DATA10011001
1270 DATA10011001
1280 DATA10011001
1290 DATA11100111
1300 DATA11000011
1310 DATA11000011
1320 DATA11000011
1330 DATA11000011
1340 DATA00000000
1350 DATA00000000
1360 DATA01000010
1370 DATA10100101
1380 DATA01000010
1390 DATA00000000
1400 DATA00000000
1410 DATA00000000
1420 DATA00011000
1430 DATA01000010
1440 DATA11100111
1450 DATA10111101
1460 DATA01011010
1470 DATA00100100
1480 DATA00111100
1490 DATA01100110
1500 DATA01010110
1510 DATA11001101
1520 DATA10010101
1530 DATA01011011
1540 DATA00011001
1550 DATA11010011
1560 DATA01100101
1570 DATA01011100
1580 DATA00000000
1590 DATA00010000
1600 DATA00111000
1610 DATA01111100
1620 DATA01111100
1630 DATA00111000
1640 DATA00010000
1650 DATA00000000
```

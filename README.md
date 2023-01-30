# KeypadMatrix_assembler_386
Assembly code for 386 processor which can scan the keypad matrix consisted of 3*4 (three by four) rows and columns

This code explains the operation of reading a matrix keyboard. The keyboard is composed of 12 keys arranged in three rows and four columns.
Since the keyboard has 12 keys, i.e. three rows and four columns, a large number of pins from the parallel port or from another peripheral port is not needed to read it.
Therefore, the procedure is explained below.

The procedure of getting the cleared value from the keyboard is such that it is necessary to perform multiplexing. Multiplexing is the procedure in which we set a logic 
unit (High level) on only one output pin, scan and look for where it appears on one of the most input pins.The way in which the multiplexing is performed will be 
explained in more details.


We use only 7 pins, of which three(3) are outputs and four(4) are inputs. We can make a 2D matrix on which you can visually see which button is placed where on the
matrix.

                                                    Row Pins    | | | | | |
                                                                ↓ | ↓ | ↓ |
                                                                1 | 2 | 3 | first column pin
                                                                4 | 5 | 6 | second column pin
                                                                7 | 8 | 9 | third column pin
                                                                * | 0 | # | fourth column pin
                                                                
                                                                
On the 2D matrix, above each digit(key) there is a physical conductor that continues straight down from the top and covers all the keys that are in that row. The ends 
of the keys are connected with another conductor that continues to the right and covers all the keys that are in that column.Multiplexing is enabled by using the three 
pins used to mark the rows to set a logical one ("1" – high level) or a logical zero (low level). 
The procedure is as follows, it must be on one of those pins (row 
pins) let's set a logical one (only a logical high level) and on the other pins only a logical zero, and with the column pins let's scan.The row pins will have the
following values - 001, 010, 100. From right to to the left of these combinations they will have a special pin that will go into the parallel port and we will use the 
Status register.The Status register is used because it can only read values from the parallel port.The other pins (the rest four, for the columns) are pins that are
connected to the parallel port and access the Control register.Let's go back to the multiplexing procedure, that is, to the row pins and the combinations for which the
pins should receive the values for the keyboard to work correctly.We bring the combination 001 to the row pins, on the first and second pin we carry a logical zero
('0',logical zero, zero volts), and on the third rightmost pin, we carry a logical unit ('1', high logic level).
After the procedure for rows, we need to clear (scan)the value of the columns, and with the value in the register that we get we will know if a certain key was 
pressed. We can have combinations 0001, 0010, 0100 and 1000 in the Status register. 
If in the Status register the we have a value of 0001, and in the Control register 001, it means that the '#' key is pressed.

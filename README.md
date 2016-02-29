# Vigenère Ciphering

## Using the Library

```go
package main

import (
	"github.com/odysseus/vigenere"
)

func main() {
	key := "Typewriter"
	message := "Now is the time for all good men to come to the aid of their country"

	encoded := vigenere.Encipher(message, key)
	decoded := vigenere.Decipher(encoded, key)
}

/**
Output:

Encoded:
GMLMOKPXXZFCUSNRTEKFHBBIJKWVSDXRDXDVIBHFYRWIEIKHYEMPN

Decoded:
NOWISTHETIMEFORALLGOODMENTOCOMETOTHEAIDOFTHEIRCOUNTRY
**/
```

`Encipher` and `Decipher` both accept only the latin alphabet as input. Lower case letters are fine but they will all be converted to uppercase, **all numbers and punctuation are ignored and stripped from the message**.

That said, because extraneous values are stripped you can use copy/paste most text into either the cipher or the key and the program will strip it for you.

Both `Encipher` and `Decipher` return their results as one continuous string. if you want them as quartets you can call `Quartets` on the output.

## Explanation of the Cipher

The Vigenère cipher is performed by using an alphabetic tableau, message, and key. First take the key and the message and line them up, repeating the key as necessary.

```
TYPEWRITERTYPEWRITERTYPEWRITERTYPEWRITERTYPEWRITERTYP
NOWISTHETIMEFORALLGOODMENTOCOMETOTHEAIDOFTHEIRCOUNTRY
```

Now for each pair of letters find the row/column location that corresponds on the tableau below. Whether you use the top character as the row or column doesn't actually matter, a given pair of letters encodes to the same output regardless.


```
    A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
    ---------------------------------------------------
A   A B C D E F G H I J K L M N O P Q R S T U V W X Y Z
B   B C D E F G H I J K L M N O P Q R S T U V W X Y Z A
C   C D E F G H I J K L M N O P Q R S T U V W X Y Z A B
D   D E F G H I J K L M N O P Q R S T U V W X Y Z A B C
E   E F G H I J K L M N O P Q R S T U V W X Y Z A B C D
F   F G H I J K L M N O P Q R S T U V W X Y Z A B C D E
G   G H I J K L M N O P Q R S T U V W X Y Z A B C D E F
H   H I J K L M N O P Q R S T U V W X Y Z A B C D E F G
I   I J K L M N O P Q R S T U V W X Y Z A B C D E F G H
J   J K L M N O P Q R S T U V W X Y Z A B C D E F G H I
K   K L M N O P Q R S T U V W X Y Z A B C D E F G H I J
L   L M N O P Q R S T U V W X Y Z A B C D E F G H I J K
M   M N O P Q R S T U V W X Y Z A B C D E F G H I J K L
N   N O P Q R S T U V W X Y Z A B C D E F G H I J K L M
O   O P Q R S T U V W X Y Z A B C D E F G H I J K L M N
P   P Q R S T U V W X Y Z A B C D E F G H I J K L M N O
Q   Q R S T U V W X Y Z A B C D E F G H I J K L M N O P
R   R S T U V W X Y Z A B C D E F G H I J K L M N O P Q
S   S T U V W X Y Z A B C D E F G H I J K L M N O P Q R
T   T U V W X Y Z A B C D E F G H I J K L M N O P Q R S
U   U V W X Y Z A B C D E F G H I J K L M N O P Q R S T
V   V W X Y Z A B C D E F G H I J K L M N O P Q R S T U
W   W X Y Z A B C D E F G H I J K L M N O P Q R S T U V
X   X Y Z A B C D E F G H I J K L M N O P Q R S T U V W
Y   Y Z A B C D E F G H I J K L M N O P Q R S T U V W X
Z   Z A B C D E F G H I J K L M N O P Q R S T U V W X Y
```

Encoded and split into quartets, the above statement comes out as:

```
GMLM OKPX XZFC USNR TEKF HBBI JKWV SDXR DXDV IBHF YRWI EIKH YEMP N
```

## Programmatic Approach

When it comes to coding this however there's a much easier way than encoding the entire tableau. Let's look at the character codes for the beginning of the (upper case) alphabet:

```
[65, 66, 67, 68, 69 ...
```

If we subtract `'A'` from this (decimal value 65) then we get:

```
[0, 1, 2, 3, 4 ...
```

Which incidentally corresponds to their array indices but it turns out we don't even need to use an array to encode this just the knowledge that the entire alphabet is now the range from [0,25]. To show how let's look at the top corner of the tableau:

```
    0 1 2 3 4 5 6 7 8
    -----------------
0   0 1 2 3 4 5 6 7 8
1   1 2 3 4 5 6 7 8 9
2   2 3 4 5 6 7 8 9 10
```

If the letters we're given are `'A'` and `'C'` then that corresponds to `0` and `2`, which encodes to `2`.

In other words the encoded key here is simply the sum of the two characters being encoded (after they have been transformed to a [0,25] value). The full steps would be:

1. Convert both characters to a [0,25] value by subtracting `'A'`
2. Add them together
1. Use modular arithmetic to make sure the value doesn't exceed 25, anything that goes past `'Z'`/`25` should simply loop around to the beginning of the alphabet so we can use the modulus operator for that.
2. Finally, turn it back into a valid ASCII code by adding `'A'` to the resulting value.

So in pseudocode:

```
// Ensure that the chars are in the range 65-90 (decimal)
func encode(char x, char y):
    return ((((x - 'A') + (y - 'A')) % 26) + 'A')
```

To decode we can reverse the encoding process by simply subtracting the character values. Because that can lead to negative values we add `26` to it. Otherwise the equation is essentially unchanged:

```
// Ensure that the chars are in the range 65-90 (decimal)
func decode(char x, char y):
    return (((((x - 'A') - (y - 'A')) + 26) % 26) + 'A')
```

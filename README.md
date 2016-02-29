# Vigenère Ciphering

## Explanation of the Cipher

The Vigenère cipher is performed by using an alphabetic tableau, message, and key. Using the first character of the message to find the column, and the second character of the message to find the row you come up with the encoded letter. For example, 'I' in the message (column 'I') and 'J' in the key (row 'J') encodes to 'R'. It's worth noting that the row/column choice doesn't actually matter, row 'J' and column 'I' also encodes to 'R'. The tableau is given below:

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

## Programmatic Approach

When it comes to coding this however there's a much easier way than encoding the entire tableau. Let's look at the character codes for the beginning of the (upper case) alphabet:

```
[65, 66, 67, 68, 69 ...
```

If we subtract `'A'` from this (decimal value 65) then we get:

```
[0, 1, 2, 3, 4 ...
```

Which incidentally corresponds to their array indices but it turns out we don't even need to use an array to encode this having transformed the alphabet into an upper case. Let's transform the top corner of the tableau into these integers:

```
    0 1 2 3 4 5 6 7 8
    -----------------
0   0 1 2 3 4 5 6 7 8
1   1 2 3 4 5 6 7 8 9
2   2 3 4 5 6 7 8 9 10
```

The encoded key is simply the sum of the two characters being encoded (after they have been transformed to a [0,25] value). To get the encoded letter we then need to do two things:

1. Use modular arithmetic to make sure the value doesn't exceed 25, anything that goes past `'Z'`/`25` should simply loop around to 'A' and modulus does this
2. Turn it back into a valid ASCII code by adding `'A'`

So in pseudocode:

```
// Ensure that the chars are in the range 65-90 (decimal)
func encode(char x, char y):
    return ((((x - 'A') + (y - 'A')) % 26) + 'A')
```

To decode we can reverse the encoding process by simply subtracting the character values, because that can lead to negative values we add `26` to it. Otherwise the equation is essentially unchanged:

```
// Ensure that the chars are in the range 65-90 (decimal)
func decode(char x, char y):
    return (((((a - 'A') - (b - 'A')) + 26) % 26) + 'A')
```

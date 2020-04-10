# Understanding Binary
Taken out of the realm of computers, binary is nothing more than a numbering system. It is the same as our standard base-10 numbering system. In fact, a review of base-10 can help us understand binary, which is base-2.

## Understanding base-10

When we look at the number **9876**, we do instinctive calculations without realizing it due to a lifetime of being taught mathematics. But what we were not taught is how the base-10 system is actually broken down, and how its principles can be extended to other bases. Let's look at **9876** more closely. 9876 is broken down as follows:

| Thousands | Hundreds | Tens | Ones |
|-----------|----------|------|------|
|     9     |    8     |   7  |  6   |


9000 + 800 + 70 + 6

However, we also know from math studies that 1000 is ten to the third power (10^3, or 10 * 10 * 10), and 100 is ten squared (10^2, or 10 * 10). We also know ten can be written as 10^1, and 1 is 10^0. So we can more accurately write this out as

| 10^3 | 10^2 | 10^1 | 10^0 |
|------|------|------|------|
|  9   |  8   |  7   |  6   |

Which can be broken down more precisely as follows:

```
10^3 = 1000
10^2 = 100
10^1 = 10
10^0 = 1

9876 = [9 * 10^3] + [8 * 10^2] + [7 * 10^1] + [6 * 10^0]
     = [9 * 1000] + [8 * 100]  + [7 * 10]   + [6 * 1]
     = 9000       + 800        + 70         + 6
```

This increases the further left to the decimal point we go. Ten thousand is 10^4, one hundred thousand is 10^5 and so on. This reveals the following pattern: Every digit to the left of the decimal point has its value multiplied by 10^x where x starts at zero and increases by one for each space moved to the left.

Also each individual digit is limited to a value between zero and nine because there is no need to go higher. If going higher is needed, you simply add another digit, thus adding another value multiplied by a higher power of ten.

## Base-2

Base-2 works the same way as base-10, but using 2^x instead of 10^x. Each digit is limited to being either a one or a zero, however, instead of being limited to a value between zero and nine. This pattern is also consistent in any base-x numbering system. For base-3, digits are between zero and two; for base-8, it's zero to seven, and so on. Anyway, enough theorizing. Let's start out our understanding of binary with a simple number: 1101. Using the same 10^x table above, but writing it out using 2 instead of 10, our table here looks like so:

| 2^3 | 2^2 | 2^1 | 2^0 |
|-----|-----|-----|-----|
|  1  |  1  |  0  |  1  |

This might require a bit of math because 1) we haven't been taught our whole lives to do math in this format, and 2) we want to convert everything to decimal (base-10) so we understand it better. Here 'goes:

```
2^3 = 8
2^2 = 4
2^1 = 2
2^0 = 1

1101 = [1 * 2^3] + [1 * 2^2] + [0 * 2^1] + [1 * 2^0]
     = [1 * 8]   + [1 * 4]   + [0 * 2]   + [1 * 1]
     = 8         + 4         + 0         + 1
     = 13
```

Of course, I did not need to write the multiplication out because 1xN = N and 0xN = 0 for every N, but I wrote it out in a more detailed fashion for the sake of explanation. Note that every digit in binary has a special term, at least as it relates to computers: a **bit**. Each 1 or 0 is a bit, and eight bits make up a **byte**. Since a byte is eight bits, we know what each digit in a byte represents because it follows the same formula as above: 2^x where x is the number of spaces to the left of the beginning. The value of each bit in a byte is depicted below:

| 2^7 | 2^6 | 2^5 | 2^4 | 2^3 | 2^2 | 2^1 | 2^0 |
|-----|-----|-----|-----|-----|-----|-----|-----|
| 128 | 64  | 32  | 16  | 8   | 4   | 2   | 1   |

You can see by this table that the maximum decimal value for a byte is the sum of all of these values, which is 255. Now let's examine a hypothetical byte value and convert it to decimal. We will use the value **10101010**. I will write it without the multiplication for simplicity and space reasons.

```
10101010 = [2^7] + 0 + [2^5] + 0 + [2^3] + 0 + [2^1] + 0
         = 128 + 32 + 8 + 2
         = 170
```

When writing out bytes, it is common to keep leading zeros for clarity. This is not necessary, but I prefer doing it this way as well.

## Converting decimal to binary

This is done by simple subtraction and comparison. You can convert any number to binary using this method, however we will stick to decimal values no greater than 255 so we can keep it to a single byte. The method is as follows:

1. Set our byte to 00000000.
2. Start at the left-most bit (2^7 = 128)
3. Is the decimal value greater than or equal to this value?
4. If yes, put a 1 in that bit and subtract the value of the bit from the decimal.
5. Move to the next bit, and continue from step 3 until finished.

For example, let's take the highest byte value of 255:

```
Starting byte value:    0000000
Starting decimal value: 255

Is 255 >= 128?     Yes
New byte value:    10000000
New decimal value: 255 - 128 = 127

Is 127 >= 64?      Yes
New byte value:    11000000
New decimal value: 127 - 64 = 63

Is 63 >= 32?       Yes
New byte value:    11100000
New decimal value: 63 - 32 = 31

Is 31 >= 16?       Yes
New byte value:    11110000
New decimal value: 31 - 16 = 15

Is 15 >= 8?        Yes
New byte value:    11111000
New decimal value: 15 - 8 = 7

Is 7 >= 4?         Yes
New byte value:    11111100
New decimal value: 7 - 4 = 3

Is 3 >= 2?         Yes
New byte value:    11111110
New decimal value: 3 - 2 = 1

Is 1 >= 1?         Yes
New byte value:    11111111
New decimal value: 1 - 1 = 0
```

The same method can be used to convert any decimal value into binary. Of course, if working with decimal values greater than 255, you have to add bits because no bit can be greater than 1.

## Quick Conversion Tip

In my studies I have seen practice exam questions asking one to convert between decimal and binary. For example, the question might give a decimal version of an IPv4 address (e.g., 192.168.1.1) and ask which of the four different IPv4 addresses depicted in binary equals this address. A quick way to do this conversion is to not do the complete conversion, but instead look for one particular pattern. 

### Pattern 1: The odd number flag
We know that you cannot add any combination of even numbers and get an odd number as a result. We also know that there is only one bit which represents an odd number, and that is the rightmost (2^0) bit. Thus, we know that **any** odd decimal number converted to binary **must** have the rightmost bit set to 1.

### Pattern 2: The sum of those which came before me, plus 1
Just like in base-10 we know that 10 is greater than 9, 100 is greater than 99, and so on, we also know in binary that 10 (2) is greater than 01 (1), 100 (4) is greater than 011 (3) and so on all the way up to 10000000 (128) being greater than 01111111 (127). Thus we know that the value of every bit B is the sum of all lesser bits plus one.

This is especially helpful when trying to convert or rule out on a question where the decimal value is greater than or equal to 128, and you're trying to convert the decimal to a byte. Since 128 is the highest bit value, you cannot get a value greater than 128 if this bit is 0.

So let's say you're given the following question:

```
Convert the following IPv4 Address into its binary equivalent
127.143.2.17

a) 01111111.01000001.00000010.00010000
b) 01111111.10011000.00000001.00010001
c) 10000001.10011000.00000010.00010001
d) 01111111.10001111.00000010.00010001
```
Right away we can rule out options B and C because the second octet (143) is an odd number and the rightmost bits of the second octet in both of them is zero, which means they are even numbers. I don't care what their actual value is; I know they cannot be correct.

We can then rule out choice A because the 128 bit is not set in the second octet. Thus, the value of the second octet there cannot be 143, which is greater than 128. The answer, thus, must be choice D.

But we can also rule out the choices for other reasons. For instance:
1. The first octet in choice C cannot be 127 because the 128 bit is 1.
2. The third octet in choice B is an odd number, which 2 is not.
3. The fourth octet in choice A must be an even number, which 17 is not.

Thus, there are multiple ways you can quickly reason through a multiple choice question involving binary conversion without have to actually convert anything to binary or decimal.

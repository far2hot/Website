## Crypto/sifers
We are given the following :
```
0ujyfwa87u
M25jMGQxbmc=
GRXGI===
2eac7797ba995850a0372814e2a7ba87 
00110100 01101100 01101100
64 162 63
q4ss6e6ag
65 62 32 35 64 38 62 37
```
The name of the challenge combined with the given text brings us to think that each line is a seperate encoding that we have to decode.
### The first line
`0ujyfwa87u`
is a Caesar Cipher where digits are being shifted by 3 on their own alphabet and the letters are being shifted by 19 on their own alphabet. 
```python
ct="0ujyfwa87u"
pt = ""
for i in ct :
    if i in "0123456789" :
        pt+= chr ( (ord(i) - ord('0') + 3 ) % 10  +  ord('0'))
    else :
        pt+= chr ( (ord(i) - ord('a') + 19) % 26 + ord('a'))
print(pt)
```
This gives us the first part of the flag `3ncrypt10n`

### The second line 
`M25jMGQxbmc=` is base64 encoding which can be decoded using [https://www.base64decode.org/](https://www.base64decode.org/) which gives us the second part of the flag `3nc0d1ng`
### The third line 
`GRXGI===` required a bit more effort.
After more trial and error we realize that using [https://www.dcode.fr/base-32-encoding](https://www.dcode.fr/base-32-encoding) and decoding it  as a Base32 gives us the third part of the flag `4nd`

### The fourth line
`2eac7797ba995850a0372814e2a7ba87` is a MD5 hash which can be decoded (note that we are not actually decoding, MD5 is a one way hash where we can't go back from the hash to the original plaintext. All we are doing is searching for the hash value given in a database of hashes and their corresponding ciphertexts and hoping to get lucky!) using [https://md5decrypt.net/](https://md5decrypt.net/) and gives the fourth part of the flag `h4sh1ng`

### The fifth line 
`00110100 01101100 01101100` is just binary which can be decoded directly into ASCII which gives us the fifth part of the flag `4ll`

### The sixth line
`64 162 63` is just octal which can be decoded directly into ASCII which gives us the sixth part of the flag `4r3`
### The seventh line
`q4ss6e6ag` is similar to the first line where digits are shifted to the left by 3 and letters are shifted to the right by 13
``` python
ct = "q4ss6e6ag"
pt = ""
for i in ct :
    if i in "0123456789" :
        pt+= chr ( (ord(i) - ord('0') - 3 ) % 10  +  ord('0'))
    else :
        pt+= chr ( (ord(i) - ord('a') + 13) % 26 + ord('a'))
print(pt)
```
gives us the seventh part of the flag `d1ff3r3nt`
### The eighth line
`65 62 32 35 64 38 62 37`
is just hex which can be directly decoded to ASCII and give us the eighth and final part of the flag `eb25d8b7`

We are finally done!
Assembling the parts gives us our flag : `accessdenied{3ncrypt10n_3nc0d1ng_4nd_h4sh1ng_4ll_4r3_d1ff3r3nt_eb25d8b7}`

## Crypto/RSA-1
We are given the following:
```
p = 10428615258416108003372202871855627713663325599674460924186517713082197448534315449595394752587304354394402047262801959990727856908043138185588365886987557
q = 8849030739304056868757301096931487921973840186794195322071503751716059434197468028088264340322992996182734000877348221433845302801843370163430108727308579
e = 65537
cipher_text = 84826403344972753121997388456739256614537789930909176473018827332005543366933391914385410712984001888365906754988120732970328825657318675360778107518188000885732104031648548997976916964730682864696944786364581243443475767387970255510475855029059715864139791778210784283726274424510221073880200865856769716576
```
We know that N = PQ

Some basic knowledge of RSA is enough to realize we have to find d which is the inverse of e modulo Φ(N) = (P-1)(Q-1)

Raising our ciphertext to the power of d mod N gives us a decimal number that is the plaintext of the flag.  We convert the decimal number to Hex and then decode into ASCII which gives us our flag : `accessdenied{RSA_1S_4M4Z1nG_R1GhT????_2a5286af}`
```python
p = 10428615258416108003372202871855627713663325599674460924186517713082197448534315449595394752587304354394402047262801959990727856908043138185588365886987557
q = 8849030739304056868757301096931487921973840186794195322071503751716059434197468028088264340322992996182734000877348221433845302801843370163430108727308579
e = 65537
cipher_text = 84826403344972753121997388456739256614537789930909176473018827332005543366933391914385410712984001888365906754988120732970328825657318675360778107518188000885732104031648548997976916964730682864696944786364581243443475767387970255510475855029059715864139791778210784283726274424510221073880200865856769716576
def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m
phi = (p-1) * (q-1)
d = modinv(e,phi)
print(pow(cipher_text,d,p*q))
```
## Crypto/RSA-2


We are given the following:
```
N = 264057768287532610924734156161085846111271356228103155462076871372364307056741048144764594645062879781647063846971890031256799636109911752078600428566502298518944558664381187
e = 65537
ct = 175347248748800717331910762241898102719683222504200516534883687111045877096093372005991552193144558951747833811929393668749668731738201985792026669764642235225240342271148171
```
We immediately realize that N is way too small and we could probably factor it easily.


My first choice was [factordb.com](http://factordb.com/) which gives us the following:

![](factordb.PNG)
It doesn't give us the factorization but tells us that N is composite which we already know since N = P x Q for primes P and Q.

My next choice was [https://www.alpertron.com.ar/ECM.HTM](https://www.alpertron.com.ar/ECM.HTM) which gives us:

![](intfac.PNG)
We have found P and Q such that N = P x Q. Now we are pretty much done, we first calculate d which is the inverse of e modulo Φ(N) = (P-1)(Q-1). Now we raise our ciphertext to the power of d mod N which gives us a decimal number that is the plaintext of the flag. We convert the decimal number to Hex and then decode into ASCII which gives us our flag : `accessdenied{alw4y5_try_t0_f4ct0r1z3_n_9ba93547}`
```python
n   = 264057768287532610924734156161085846111271356228103155462076871372364307056741048144764594645062879781647063846971890031256799636109911752078600428566502298518944558664381187
p   = 22788121468146346999
q   = 11587518025855592759726630124584244020238845252808598255278658263482784394605886754984976163579618331619323699778956049111427022474635415206131197278729813
phi = (p-1) * (q-1)
e   = 65537
ct  = 175347248748800717331910762241898102719683222504200516534883687111045877096093372005991552193144558951747833811929393668749668731738201985792026669764642235225240342271148171
def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m

assert(p*q==n)
d = modinv(e,phi)
print(pow(ct,d,n))
```

## Crypto/Small key

We are given the following:

```python
import os

flag = b"XXXXXX"
key = os.urandom(8)

cipher_text = b""

for i in range(len(flag)):
    cipher_text += bytes([flag[i] ^ key[i % 8]])


print(cipher_text.hex())


# flag 763d32726973a23f79373473616ba86a60300e677634f734482a626f6e5ff22e636a327c2f5ff228240123242e6caa23483d6127765fff6d743a61212f38bb
```
We observe that the key used for the One-time pad is only 8 bytes. Since each byte is decoded into 1 UTF-8 character we know that our key is exactly 8 characters long.

We also know the first 8 characters of the plaintext are going to be "accessde" since the flag format is accessdenied{}. With this knowledge can leak the entire key
by XORing the first 8 characters of the flag (after converting it from Hex to a byte object) with b"accessde". Now we use the key to decrypt the flag which gives us our flag:
`accessdenied{kn0wn_pl41n_t3xt_4tt4ck5_4r3_r34lly_c00l_97cd0658}`
```python
import binascii


flag = binascii.unhexlify("763d32726973a23f79373473616ba86a60300e677634f734482a626f6e5ff22e636a327c2f5ff228240123242e6caa23483d6127765fff6d743a61212f38bb")
starting_8_bytes_of_flag = binascii.unhexlify("763d32726973a23f")
starting_8_bytes_of_plaintext= b"accessde"
key = b""
for i in range(len(starting_8_bytes_of_flag)):
    key += bytes([starting_8_bytes_of_flag[i] ^ starting_8_bytes_of_plaintext[i % 8]]) 

plain = b""
for i in range(len(flag)):
    plain += bytes([flag[i] ^ key[i % 8]])
print(plain.decode())
```
## Crypto/reused key 
We are given two hex values `hex1 = 65f32f851cdb20eee875eea5a9a30f826cfd247eb550dcc89d1d4cdf952f5c28ca5f162355567fd262bb96` and                                     `hex2 =70f8259330c137d4e873ff9ea6a559ab2dea1a60d943859aa545578395301d28a0741d1e065a24d45cb19f`. We are told that one corresponds to the flag and the other is some random text and both the flag and the random text are encrypted using the same pad key.

We know that hex  'hex1 = flag ^ key' and 'hex2 = text ^ key' 

Since the xor function is a self inverse function we know that `hex1 ^ hex2 = flag^ text`.

Now since we have `flag ^ text` we can xor commonly used english words at different indexes with it and whenever the result is readable (when converted to UTF-8 it gives us a string of acceptable characters), chances are we have found part of our flag (this technique is commonly referred to as crib dragging). The flag is `accessdenied{n3v3r_r3u53_th3_k3y5_db5e5bac} `


## Misc/Bobs Favourite Number

we are given the following :
```
A number N is Bob's favourite number if N is equal to the sum of a certain number of 1856 and sum of a certain number of 2014.

Task: Print whether a number is Bob's favourite number or not.
Input: 
First line contains a single integer number N.
Output:
Print “Yes” if Favourite if N is Bob’s favourite number, else print “No”.

Constraints: 1 <= N <= 10^9

Sample input:
5726

Sample output:
Yes

Sample explanation:
5726 can be represented as the sum of 1856 + 1856 + 2014.
```
Mathematically we want to find integers x and y that satisfy `1856x + 2014y = n` for a given input integer n


My first instinct was to calculate gcd(1856,2014) and as long as gcd(1856,2014) divides n we have a solution. 

Note that we don't actually have to find (x,y), we just have to output "Yes" if a solution exists so the existence of a solution suffices. The Trick is that we have another constraint on (x,y). We need both to be positive.


While I'm sure that we can find a initial solution pair (x1,y1) using the Extended Euclidean algorithm and then check to see if generating a positive solution pair (x2,y2) is possible using the following 

![](eqn.PNG)

for some integer p.

I realized that bruteforcing x and y suffices since we have the following constraint on N : `1 <= N <= 10^9`
Our final solution will try to bruteforce all possible integers x and y that could possibly yield a solution. This gives us the flag : `accessdenied{b0bs_f4v0r1t3_numb3r5_4r3_m1n3_f4v0urit3_t00_61c884c8}`
 ```python
 from pwn import *
r = remote("35.193.60.121",1337)
def solution (a, b, n):   
    i = 0
    while i * a <= n :
        if (n - (i * a)) % b == 0:
            return 1
        i+=1
    return 0

while (True) :
    a = r.recvline()
    line = a.decode()
    if ("accessdenied" in line) :
        print(line)
    
    try :
        num = int(line.split("\n")[0])
        if (solution(1856,2014,num)) :
            r.sendline("Yes".encode())
        else :
            r.sendline("No".encode())
    except:
        pass
```       
## Misc/Green and Red flags      
We are given the following :
```
Green and Red Flag

Given a number N and each number is assigned either a green or red flag. We can apply the following operations any number of times: choose any two digits of number N with the same coloured flag assigned and swap them.

Task: Print the maximum possible number, after applying any number of operations on number N.

Input: 
First line contains D the number of digits in the number N.
Second line contains a string N which indicates the number N.
Third line contains a string C which indicates the colour of the flag assigned to each digit.

Output:
Print the maximum possible number, after applying any number of operations on number N.

Constraints: 1 <= D <= 10^6

Sample input
5
16487
rgrrg

Sample output
87416 
```
The Idea for the solution is to map each digit of N to a color (RGB) then construct the biggest possible number by iterating over the color configuration and choosing the biggest possible digit that is available between all the digits that are mapped to the current color and then removing that digit from the corresponding list of digits of that color.
```python
from pwn import *
re= remote("35.193.60.121",5337)
digitsreceived = False
while (True) :
    a = re.recvline()
    line = a.decode()
    print(line)
    if ("Answer" in line) : # time to answer!
        r.sort()
        g.sort()
        b.sort()
        ans = ""
        for i in range (len(colors)) :
            
            if (colors[i] == "r") :
                ans+= str(r[-1])
                r.pop(-1)
            if (colors[i] == "g") :
                ans+= str(g[-1])
                g.pop(-1)
            if (colors[i] == "b") :
                ans+= str(b[-1])
                b.pop(-1)
        re.sendline(ans.encode())
        digitsreceived = False

    elif (line[0] in "rgb") : # if the current line is the color configuration
        colors = line
        r = []
        g = []
        b= []
        for i in range (len(line)) :
            if (line[i] == "r") :
                r.append(number[i])
            elif (line[i]=="g") :
                g.append(number[i])
            else :
                b.append(number[i])
    else : # if the current line is an integer
        if (digitsreceived==True) : #  if the current line is the actual number 
            number = line
        else : # if the current line is the number of digits and not the actual number
            digitsreceived = True

```


Which gives us the flag : `accessdenied{gr33n_fl4gs_f0r_m3_4nd_r3d_fl4g5_4re_f0r_y0u_c8e0c8a3}`
     
     

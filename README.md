# KozHash
 A String to Hash function library for use with the Ark Dev Kit. Also includes test map, PGD, and singleton wired up to run hashing tests in the editor.

## Contents
- _KozHash
  - _KozHash_Func
    - _the hash function library_
  - KozHashTestActor
    - _testing singleton_
  - PrimalGameData_BP_KozHashTest
    - _test PGD_
  - TestMapArea_KozHashTest
    - _test map level_
- TestLog_30CharMax_500TestPerAlpha.txt
  - _PIE log from tests w/ 30 char max len random strings & 500 hash tests per alphabet_
- TestLog_500CharMax_50TestPerAlpha.txt
  - _PIE log from tests w/ 500 char max len random strings & 50 hash tests per alphabet_

## Functions in _KozHash_Func
- **KozHash StringToHash**
  - _this is the main hash function, give string, get int32 hash_
- KozHash ShortPad
  - _used for strings w/ len < 7_
  - _pads right until string is len 7_
  - _pads using char derived from avg of all str ascii codes_
- KozHash FindPrimeLen
  - _finds the next prime string length, if not already prime_
  - _forces min len of 7_
- KozHash TruePow
  - _gives true binary value for 2^0 -> 2^31_
  - _Exp parm clamped to 0-31_
- KozHash BitShift
  - _performs 'bit shift' on int value for specified count # positions_
  - _positive count shifts left_ `<<` _bits fall off left, zeros fill on right_
  - _negative count shifts right_ `>>` _bits fall off right, zeros fill on left_

## Hash Logic / Algorithm
1. Is short string? (<7 length)
  - _String is right padded until length==7_
  - _Pad Char is AVG of all string char ascii codes, used as ascii code for char_
2. Is string length NOT prime? 
  - _Next prime value found (ex. len 8 -> 11)_
  - _String is padded until matches prime length_
  - _Pad char is static ascii code chosen for its bit pattern_ `213 / 0xD5 / 1101 0101`  
3. Bit operations done per character in string
  - _h is the output value, the hash, initialized to 0 at start_
  - _ki is the current char ascii in string/loop_ 
  - _ascii codes > 255 are split into their two byte values and then the below is run on each, low byte first_
```
//  CRC Variant Hash Algorithm  // (yes it is simple) 
h = 0                           // init
... start char loop ...
highorder = h & 0xf8000000      // set 5 high bits aside
h = h << 5                      // bit shift left, removing 5 high bits 
h = h ^ (highorder >> 27)       // shift those 5 high bits we set aside to 
                                // right 27 which makes them the low bits 
                                // then XOR that with h 
h = h ^ ki                      // XOR h with current char ascii ki 
... end char loop ...
```
4. Were any unicode chars encountered? (ascii > 255) 
  - _Count of unicode encountered is added to string length and prime checked same as initially_
  - _new prime length diffed against old, this is how many pad chars we will add to calculation_
  - _this is done to ensure the 'set' operated on is always prime length and unicode added items to set_ 
  - _pad char ascii used for this is_ `21 / 0x15 / 0001 0101` 
5. Final 'h' from above is hash returned


## Collision Testing 
I have run the tests provided in this repo many times, resulting in 10s of thousands of hash collision tests overall. The core CRC Variant algorithm has not changed since I began the testing, however, the padding strategies I have implemented are there as mitigating factors for collisions I found to occur on short strings. 

If you find any collisions please do reach out to me and let me know what the strings and colliding hash value were.  Thanks! 

- Discord: Koz#4354 / https://discord.gg/FmWBgQV 
- Steam: Kozenomenon / https://steamcommunity.com/profiles/76561197962912863/


## Reference
I got the CRC Variant hash algorithm from this site. Yes, it is old and simple, but simple was also kind of the idea... :D 
https://www.cs.hmc.edu/~geoff/classes/hmc.cs070.200101/homework10/hashfuncs.html 



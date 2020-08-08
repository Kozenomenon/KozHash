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

## Hash Logic / Algorithm
1. Is short string? (<7 length)
  - _String is right padded until length==7_
  - _Pad Char is AVG of all string char ascii codes, used as ascii code for char_
2. Is string length NOT prime? 
  - _Next prime value found (ex. len 8 -> 11)_
  - _String is padded until matches prime length_
  - _Pad char is static ascii code 213 / 0xD5 / 1101 0101 chosen for its bit pattern_ 
3. Bit operations done per character in string
  - _h is the output value, the hash, initialized to 0 at start_
  - _ki is the current char ascii in string/loop_ 
  - _ascii codes > 255 are split into their two byte values and then the below is run on each, low byte first_
```
h = 0;                          // init
... start char loop ...
highorder = h & 0xf8000000;     // set 5 high bits aside
h = h << 5;                     // bit shift left, removing 5 high bits 
h = h ^ (highorder >>> 27);     // shift those 5 high bits we set aside to 
                                // right 27 which makes them the low bits 
                                // then XOR that with h 
h = h ^ ki;                     // XOR h with current char ascii ki 
... end char loop ...
```
4. Were any unicode chars encountered? (ascii > 255) 
  - _Count of unicode encountered is added to string length and prime checked same as initially_
  - _new prime length diffed against old, this is how many pad chars we will add to calculation_
  - _this is done to ensure the 'set' operated on is always prime length and unicode added items to set_ 
  - _pad char ascii used for this is 21 / 0x15 / 0001 0101_
5. Final 'h' from above is hash returned
A video game cheater's solution of picoCTF Wizardlike

Requirements:
- Cheat Engine/Lunar Engine
- ceserver for linux
- Some debugger (ideally with decompile)

As a video game cheater, my first intuition was step was to find the x and y coord values in lunar engine and set these to complete the challenges. You can do this by searching for an all type unknown value and researching for when you expect it to change (when you move) and when you don't expect it to change (standing still) to find the correct values. Doing this provides us with two 4 byte values at game+132F70 and game+132F74 (x, y).

However, after doing this and getting through the first couple of levels it becomes apparent that the ctf writer predicted this and made difficult to navigate levels with hidden objects to prevent this.

In order to find the hidden objects, you are expected to reverse the function for revealing hidden blocks. One way to do this is to start out by using Lunar Engine again to search for unknown value, this time being some block's visibility. I ended up finding a 4 byte value at game+136E4C. 

![Screenshot of Lunar Engine Table](/images/wizardlike-blockvisibility.png)


Furthering this path, we can connect Lunar Engine's debugger via the "Find out what access this address" feature in order to find the instructions involved in revealing the block, 00402A0E for setting it to visible and 00401E82 for setting it to not visible.

![Screenshot of the What Accesses Table](/images/wizardlike-whataccesses.png)

From here, you can go to your debugger in order to find the instruction at 00402A0E and the decompiled code for it. Doing this, we see that there is "else if" logic that checks whether the block is already visible and whether a function returns true. We can assume that this function likely is a check for visibility or choose to further read other parts of the code to discern the logic. 

![Screenshot of the decompiled if else statement in IDA Pro](/images/wizardlike-ifelse.png)

When we view the function, we can spot that there are only two sections where the function returns 0, making the block not visible. The instruction addresses for these two return values are 0000210A and 0000204A. We can go back to Lunar Engine, go to its Memory View, find the instruction using Ctrl+G and game+204A, and double click the instruction to patch it live with the value of 1.

![Screenshot of the return calls in IDA Pro](/images/wizardlike-idareturn.png)
![Screenshot of finding the address in Lunar Engine](/images/wizardlike-patch-one.png)
![Screenshot of patched code in Lunar Engine](/images/wizardlike-patch-two.png)

Finally, we can check our work by moving around and find the entire map we are on is now visible. This allows us to go between the levels now and view the flag, picoCTF{ur_4_w1z4rd_8F4B04AE} (for an added bonus you can also find the 4 byte map value to avoid having to walk in-between levels)

![Screenshot of level 8](/images/wizardlike-level8.png)
![Screenshot of level 2](/images/wizardlike-level2.png)

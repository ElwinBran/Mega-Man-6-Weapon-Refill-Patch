# Mega Man 6 Weapon Refill Patch
\[This description is still work in progress]
This patch for Mega Man 6 makes weapon energy refill upon death in the whole game. 
A detailed description can be found [on the RomHacking page of this hack](https://www.romhacking.net/hacks/4758/), as well as an alternative download link.
In this README you will find further information on how this patch was made and reference material.

## Tools
- [The FCEUX NES emulator](http://www.fceux.com/web/download.html), for disassembly, hex editing, memory viewing, testing and even breakppoints.
- [LunarIPS](https://www.romhacking.net/utilities/240/), to create a patch file.

## Process
The project was started off by looking up the values that required changing.
These were acquired by simply using the weapons in game and looking for changed values in the RAM afterwards.
It was required that the weapon refill happened only when the player died. It was found out that when the players health energy is refilled is a good point to insert the weapon refill code. Then the healthy energy value was found allong with code that changes it.
These points were found using a Memory breakpoint. 
Unlike previously patched Mega Man games did Mega Man 4 not have any free space to insert the code on.
The NES can swap 'ROM banks' at any time by calling the right Memory Mapper Controller Registers. This allows the CPU to access another part of the ROM effectively. 

## References
In order to write the code a lot of knowledge was required. Here I list the sources specifically used for this project.
[DarkSamus993](https://www.romhacking.net/forum/index.php?action=profile;u=23766) provided information on the in-game routine for refilling weapon energy:
; refill weapon energy (stage start)
; rom addr = $077A4F
1D:BA3F:A5 51     LDA $0051    ; stageID
1D:BA41:C9 09     CMP #$09     ; Mr. X's Fortress, Stage 2
1D:BA43:B0 33     BCS $BA78    ; don't refill weapons for the final stages
1D:BA45:A0 0B     LDY #$0B
1D:BA47:B9 79 BA  LDA $BA79,Y  ; load boss index [00 00 00 01 01 01 02 02 02 03 03 03]
1D:BA4A:AA        TAX
1D:BA4B:BD 92 06  LDA $0692,X  ; load boss beaten bits (RAM addr $0692 - $0695)
1D:BA4E:85 00     STA $0000
1D:BA50:B9 85 BA  LDA $BA85,Y  ; load stage flags [01 02 04 01 02 04 01 02 04 01 02 04]
1D:BA53:25 00     AND $0000
1D:BA55:F0 09     BEQ $BA60    ; check if boss beaten
1D:BA57:B9 91 BA  LDA $BA91,Y  ; load weapon index [03 07 07 02 01 01 04 08 08 05 06 06]
1D:BA5A:AA        TAX
1D:BA5B:A9 1B     LDA #$1B
1D:BA5D:9D 88 06  STA $0688,X  ; refill weapon energy
1D:BA60:88        DEY
1D:BA61:10 E4     BPL $BA47
1D:BA63:AD 92 06  LDA $0692    ; items collected byte #1
1D:BA66:2D 93 06  AND $0693    ; items collected byte #2
1D:BA69:2D 94 06  AND $0694    ; items collected byte #3
1D:BA6C:2D 95 06  AND $0695    ; items collected byte #4
1D:BA6F:29 04     AND #$04
1D:BA71:F0 05     BEQ $BA78    ; branch if all beat letters not collected
1D:BA73:A9 1B     LDA #$1B
1D:BA75:8D 91 06  STA $0691    ; refill beat weapon energy
1D:BA78:60        RTS


A big thanks for the people behind these to make 6502 programming more accessible.
- [An excellent reference for quickly looking up the value of an opcode.](https://www.masswerk.at/6502/6502_instruction_set.html)
- [More detailed information on the opcodes that made me understand what was required for the patch.](http://www.6502.org/tutorials/6502opcodes.html)
- [Basic ROM information that was required to understand the mapper.](https://datacrystal.romhacking.net/wiki/Mega_Man_6)

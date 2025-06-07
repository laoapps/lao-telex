LaoKa Input Method
LaoKa is a phonetic input method that uses Latin letters to represent Lao characters, enabling intuitive typing on QWERTY keyboards. Designed as a faster alternative to methods like LaosScript and LaosUnikey, LaoKa aligns with Lao pronunciation and supports consonants, vowels, tones, and special signs. This README summarizes the rules and implementation details for the LaoKa standard.
Overview
LaoKa simplifies Lao text input by mapping Latin-based phonetic symbols to Lao Unicode characters (U+0E80–U+0EFF). It supports dynamic multi-key sequences, context-aware mappings, and both Unicode and legacy 2000-standard fonts (e.g., Saysettha Unicode). The method is platform-agnostic, suitable for implementation across devices and operating systems.
Word Structure
A Lao word in LaoKa follows this structure:
Consonant + Vowel + Sound Element + Voiced Sign + Special Sign

Consonant (A): Required first character (single or double consonant).
Vowel (B): Combines with the consonant, positioned before or after.
Sound Element (C): Optional consonant or vowel to complete the syllable.
Voiced Sign (D): Tone mark (e.g., ່, ້, ໊, ໋).
Special Sign (E): Karan (໌) or repeating (ໆ) sign.

Rules
1. Consonant Mappings

Single Consonants: 27 consonants mapped to Latin letters or digraphs.
Examples:
G → ກ (U+0E81)
KH → ຂ (U+0E82)
NG → ງ (U+0E87)


Notes:
Uncombinable: G, D, B cannot take tone marks at word’s end.
Combinable: NG, NH, N, M, V can take tone marks.




Double Consonants: 6 combinations starting with HH (or H in Rule II).
Examples:
HHN → ໜ (U+0EDC)
HHM → ໝ (U+0EDD)
HHL → ຫຼ (U+0EAB U+0EBC)





2. Vowel Mappings

31 Vowels, categorized by combination ability:
Non-combinable: Cannot form syllables alone (e.g., A → xະ).
Compulsory: Must combine with a consonant (e.g., AE → xັ).
Combinable: Can form syllables with/without consonants (e.g., AA → xາ).
Double Vowels: Complex vowels (e.g., IA → ເxຽ).
Examples:
A → xະ (U+0EB0)
AA → xາ (U+0EB2)
I → xິ (U+0EB4)
IA → ເxຽ (U+0EC0 U+0EBD)




Positioning: Vowels like AY, AAY precede the consonant in output (e.g., AY → ໄx).

3. Sign Mappings

Voiced Signs (Tone Marks):
F → x່ (U+0EC8, low tone, ‘Ech’)
J → x້ (U+0EC9, high falling, ‘Tho’)
Z → x໊ (U+0ECA, rising, ‘Ti’)
X → x໋ (U+0ECB, high, ‘Chattava’)


Special Signs:
L → x໌ (U+0ECC, Karan, for foreign words)
P → xໆ (U+0ED6, repeating sign)



4. Typing Rules

Rule I: Direct mapping using the standard structure.
Example: P+AA → ປາ (U+0E9B U+0EB2)
Example: S+AE+N+J → ນັ້ນ (U+0EAA U+0EB1 U+0E99 U+0EC9)


Rule II: Simplifies typing for vowels and HH consonants.
Vowel at Start: Prepends ອ (OH, U+0E9D).
Example: WWNFP → ອື່ນໆ (U+0E9D U+0EB7 U+0E99 U+0EC8 U+0ED6)


Short Vowels: A (xະ) and O (xໍ) replace AA (xາ) or OH (ອ).
Example: OHANF → ອ່ານ (U+0E9D U+0EB2 U+0E99 U+0EC8)


H for HH: H replaces HH for double consonants (except with V).
Example: HMAYJ → ໄໝ້ (U+0EC4 U+0E9E U+0EC9)




Rule II+: Shortcuts for common syllables.
Examples:
AI → xາຍ (U+0EB2 U+0E8D, equivalent to AANH)
OI → xອຍ (U+0E9D U+0E8D, equivalent to OHNH)
AO → xາວ (U+0EB2 U+0EA7, equivalent to AAV)





5. Input Mechanism

Input Buffer: Stores characters until space or enter is pressed.
Backspace: Removes the last character from buffer and editor.
Conversion Trigger: Space or enter converts Latin to Lao Unicode.
Tone/Special Signs: Processed last, stored in a separate array if at word’s end or near-end.
Vowel Positioning: Vowels like AY, AAY are moved before the consonant in output.
Caps Lock: Toggles LaoKa mode (on for Lao, off for English).
Control Key: Double-tap within 2 seconds toggles LaoKa mode.

Implementation
Algorithm

Input Capture: Store Latin characters (A-Z) in an array, ignoring Shift/Control.
Mark Sign Handling: Extract tone/special signs (F, J, Z, X, L, P) to a separate array.
Vowel Reordering: Move vowels like AY, EE before the consonant.
Conversion:
Apply Rule I, II, or II+ based on context.
Map Latin sequences to Lao Unicode using predefined tables.


Output:
Delete Latin characters from the editor (backspace).
Insert Lao Unicode characters via WM_CHAR or equivalent.
Handle cursor positioning and enter key.



Key Features

Dynamic Input: Supports multi-key sequences (e.g., HHNG → ຫງ).
Context Awareness: Adjusts mappings based on position (e.g., L as consonant or Karan sign).
Unicode Output: Uses Lao Unicode range (U+0E80–U+0EFF).
Font Support: Compatible with Unicode fonts and legacy 2000-standard fonts.
Error Handling: Resets buffers on non-alphabetic input or thread change.

Development Notes

Tables: Include full consonant, vowel, and sign mapping tables in project documentation (refer to original document sections 24–26, 28).
Testing: Validate input sequences for edge cases (e.g., invalid combinations, buffer overflow).
Platform Support: Ensure compatibility with desktop, mobile, and web-based input systems.

License
This project is licensed under the MIT License. See the LICENSE file for details.

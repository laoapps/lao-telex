Summary of LaoKa Rules
The LaoKa standard is a phonetic input method that uses Latin letters to represent Lao characters, designed to simplify typing Lao on standard QWERTY keyboards. It’s inspired by the need for a faster, more intuitive alternative to existing Lao input methods (e.g., LaosScript, LaosUnikey), which require complex key combinations due to Lao’s 56+ characters. LaoKa leverages Latin-based phonetic symbols, aligning closely with Lao pronunciation, and includes rules to handle consonants, vowels, tones, and special signs. Below is a summarized version of the rules extracted from your documentation, structured for implementation across platforms.

1. Word Structure
A Lao word in LaoKa follows the structure:

Consonant + Vowel + Sound Element + Voiced Sign + Special Sign

Consonant (A): Obligatory first character (single or double consonant).
Vowel (B): Combines with the consonant, may stand before/after it.
Sound Element (C): Optional consonant/vowel to complete the syllable.
Voiced Sign (D): Tone mark (e.g., ່, ້, ໊, ໋).
Special Sign (E): Karan (໌) or repeating (ໆ) sign.
2. Consonant Mappings
Single Consonants: 27 consonants mapped to Latin letters or digraphs.
Examples:
G → ກ (U+0E81)
KH → ຂ (U+0E82)
K → ຄ (U+0E84)
NG → ງ (U+0E87)
Full table in the document (section 24).
Notes:
Uncombinable: G, D, B cannot take tone marks at word’s end.
Combinable: NG, NH, N, M, V can take tone marks.
Double Consonants: 6 combinations starting with HH (or H in Rule II).
Examples:
HHN → ໜ (U+0EDC)
HHM → ໝ (U+0EDD)
HHL → ຫຼ (U+0EAB U+0EBC)
Full table in section 24.
3. Vowel Mappings
31 vowels, categorized by their ability to combine with consonants:
Non-combinable: Cannot form syllables alone (e.g., A → xະ).
Compulsory: Must combine with a consonant (e.g., AE → xັ).
Combinable: Can form syllables with/without consonants (e.g., AA → xາ).
Double Vowels: Complex vowels (e.g., IA → ເxຽ).
Examples:
A → xະ (U+0EB0)
AA → xາ (U+0EB2)
I → xິ (U+0EB4)
IA → ເxຽ (U+0EC0 U+0EBD)
Full table in section 25.
Positioning:
Some vowels (e.g., AY, AAY) precede the consonant in output (e.g., AY → ໄx).
Others follow or surround the consonant.
4. Sign Mappings
Voiced Signs (Tone Marks): 4 tone marks.
F → x່ (U+0EC8, low tone, ‘Ech’)
J → x້ (U+0EC9, high falling, ‘Tho’)
Z → x໊ (U+0ECA, rising, ‘Ti’)
X → x໋ (U+0ECB, high, ‘Chattava’)
Special Signs:
L → x໌ (U+0ECC, Karan, for foreign words)
P → xໆ (U+0ED6, repeating sign)
Table in section 26.
5. Typing Rules
Rule I: Direct mapping using the standard structure.
Example: P+AA → ປາ (U+0E9B U+0EB2)
S+AE+N+J → ນັ້ນ (U+0EAA U+0EB1 U+0E99 U+0EC9)
Rule II: Simplifies typing for vowels and HH consonants.
Vowel at Start: Words starting with a vowel implicitly prepend ອ (OH, U+0E9D).
Example: WWNFP → ອື່ນໆ (U+0E9D U+0EB7 U+0E99 U+0EC8 U+0ED6)
Short Vowels: A (xະ) and O (xໍ) replace AA (xາ) or OH (ອ) in certain contexts.
Example: OHANF → ອ່ານ (U+0E9D U+0EB2 U+0E99 U+0EC8)
H for HH: H replaces HH for double consonants (except with V).
Example: HMAYJ → ໄໝ້ (U+0EC4 U+0E9E U+0EC9)
Rule II+: Shortcuts for common syllables.
Examples:
AI → xາຍ (U+0EB2 U+0E8D, equivalent to AANH)
OI → xອຍ (U+0E9D U+0E8D, equivalent to OHNH)
AO → xາວ (U+0EB2 U+0EA7, equivalent to AAV)
Full list in section 28.
6. Input Mechanism
Input Buffer: Characters are stored in a buffer until a space or enter key is pressed.
Backspace: Removes the last character from the buffer and editor.
Conversion Trigger: Space or enter triggers conversion from Latin to Lao Unicode.
Tone/Special Signs: Processed last, placed in a separate array if at the end or near-end of a word.
Vowel Positioning: Vowels like AY, AAY are moved before the consonant in the output.
Caps Lock: Toggles LaoKa mode (on for Lao, off for English).
Control Key: Double-tap Control within 2 seconds toggles LaoKa mode.
7. Algorithm Summary
Input Capture: Store Latin characters (A-Z) in an array, ignoring Shift/Control.
Mark Sign Handling: Identify and extract tone/special signs (F, J, Z, X, L, P) to a separate array.
Vowel Reordering: Move specific vowels (e.g., AY, EE) before the consonant.
Conversion:
Apply Rule I, II, or II+ based on context (e.g., vowel at start, H vs. HH).
Map Latin sequences to Lao Unicode using predefined tables.
Output:
Delete Latin characters from the editor (backspace).
Insert Lao Unicode characters via WM_CHAR or equivalent.
Handle cursor positioning and enter key.
8. Key Features for Implementation
Dynamic Input: Supports multi-key sequences (e.g., HHNG → ຫງ).
Context Awareness: Adjusts mappings based on position (e.g., L as consonant or Karan sign).
Unicode Output: Uses Lao Unicode range (U+0E80–U+0EFF).
Font Support: Supports Unicode fonts (e.g., Saysettha Unicode) and legacy 2000-standard fonts.
Error Handling: Resets buffers on non-alphabetic input or thread change.
This summary provides a clear, platform-agnostic foundation for implementing LaoKa. The full tables (consonants, vowels, signs) and examples from the document should be included in each project’s documentation.

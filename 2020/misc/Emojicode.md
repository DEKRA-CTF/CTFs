### 0x00 Challenge: Emojicode
**Description:**
>Emojis are mainstream... 😋

### 0x01 Write-up:
A chain of emojis are provided. First of all is necessary to convert each emoji to unicode value (https://unicode.org/emoji/charts/full-emoji-list.html).

Having unicode values take last 2 chars of each one and convert to ASCII characters associated. Decode the text achieved using ROT13 and get the flag.

**Unintended:**
By using https://emoji-cypher.netlify.app/ you will get the clear string and only needs to ROT13 it.


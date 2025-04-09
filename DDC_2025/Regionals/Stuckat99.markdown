# Stuck At 99
## Challenge
- **Solves**: 11
- **Name**: StuckAt99
- **Points**: 353

## Description
Alle disse $\mathrm{~B} \times \mathrm{C}$ opdateringer bliver ved med at sidde fast! Det er så irriterende!!! Hvis bare jeg selv kunne få fat i koden, så kunne jeg endelig få dette program til at køre.  

**Binary SHA256**:  
`8F40AEDAC2C7E3E1AC83D89123E6EB526EFE428E4E1709434ACBBAFF49B697E`

## Solution
When we run the program, we can see a button with the text "get flag". If we press the button, a loading bar pops up, which stops at 99.  

I opened the code in dnSpy where I could get a better overview of what it did. I found the `GenerateFlag()` function interesting:  

```csharp
private string GenerateFlag()
{
    StringBuilder stringBuilder = new StringBuilder();
    stringBuilder.Append(this.GetCharacter(0));
    stringBuilder.Append(this.GetCharacter(0));
    stringBuilder.Append(this.GetCharacter(2));
    stringBuilder.Append(this.GetCharacter(3));
    stringBuilder.Append(this.GetCharacter(52));
    stringBuilder.Append(this.GetCharacter(53));
    stringBuilder.Append(this.GetCharacter(63));
    stringBuilder.Append(this.GetCharacter(64));
    stringBuilder.Append(this.GetCharacter(65));
    stringBuilder.Append(this.GetCharacter(66));
    stringBuilder.Append(this.GetCharacter(67));
    stringBuilder.Append(this.GetCharacter(68));
    stringBuilder.Append(this.GetCharacter(69));
    stringBuilder.Append(this.GetCharacter(70));
    stringBuilder.Append(this.GetCharacter(71));
    stringBuilder.Append(this.GetCharacter(72));
    stringBuilder.Append(this.GetCharacter(73));
    stringBuilder.Append(this.GetCharacter(74));
    stringBuilder.Append(this.GetCharacter(75));
    stringBuilder.Append(this.GetCharacter(76));
    stringBuilder.Append(this.GetCharacter(77));
    stringBuilder.Append(this.GetCharacter(78));
    stringBuilder.Append(this.GetCharacter(89));
    stringBuilder.Append(this.GetCharacter(90));
    stringBuilder.Append(this.GetCharacter(91));
    stringBuilder.Append(this.GetCharacter(92));
    stringBuilder.Append(this.GetCharacter(93));
    stringBuilder.Append(this.GetCharacter(94));
    stringBuilder.Append(this.GetCharacter(95));
    stringBuilder.Append(this.GetCharacter(96));
}
```

`GenerateFlag()` takes characters from `GetCharacter(int)`. I recreated the flag manually and got:  

## Flag
`DDC{f1ne_m4nually}`
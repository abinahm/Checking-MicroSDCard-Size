# Test microSD Card for Fake Capacity (f3)

I recently dound a 256 GB microSD card on eBay at a price that's too good to be true, and thought I was going to get scammed (I didn't). But if you did, this tool might help you uncover it. Counterfeit cards are often reprogrammed to *report* a large capacity (e.g. 256 GB) while only having a fraction of it (e.g. 16 GB) in real storage. They work fine at first, then silently corrupt or lose your data once you pass the real limit.

This guide shows how to verify your card's true capacity using [F3 (Fight Flash Fraud)](https://github.com/AltraMayor/f3).

## How it works

- `f3write` fills the card's free space with test files (1 GB each, `.h2w`).
- `f3read` reads those files back and checks that every byte is intact.

If the card is fake, the data written past the real capacity is lost or corrupted, and `f3read` will tell you.

## Usage

1. Insert the card and note its mount point (here `/Volumes/Untitled`; yours may differ, so check with `ls /Volumes`).

2. Run the test:

```bash
brew install f3
f3write /Volumes/Untitled
f3read /Volumes/Untitled
```

3. Clean up the test files:

```bash
rm /Volumes/Untitled/*.h2w
```
## Reading the results

**A genuine card** shows something like:

```
Data OK: 238.xx GB
Data LOST: 0.00 Byte (0 sectors)
Corrupted: 0.00 Byte (0 sectors)
Slightly changed: 0.00 Byte (0 sectors)
Overwritten: 0.00 Byte (0 sectors)
```

(The total is slightly below 256 GB because of formatting overhead and GB vs. GiB differences.)

**A fake card** shows a large amount of `Data LOST` or `Overwritten`, and the `Data OK` figure is its real capacity:

```
Data OK: 15.xx GB
Data LOST: 223.xx GB (...)
```

| Field | Meaning |
|---|---|
| Data OK | Data read back correctly, i.e. real usable space |
| Data LOST | Data that couldn't be recovered |
| Corrupted | Data that came back wrong |
| Slightly changed | Minor bit errors (can indicate a failing card) |
| Overwritten | Data overwritten by other data, a classic sign of fake capacity |

## If your card is fake

1. Keep the f3read output (screenshot or copy the text) as evidence.
2. Open a **"Item not as described"** case with the seller/marketplace (e.g. eBay Money Back Guarantee) and attach the results.
3. Leave honest feedback so other buyers are warned.
4. If you want to keep the card, reformat it and use it only up to its real capacity. Don't trust it with important data.

## Tips

- Test **as soon as** you receive the card, before your return window closes.
- Don't trust the capacity shown in your OS or the card's label; both can be faked.
- Prices far below the market for a reputable brand (SanDisk, Samsung, Kingston) are a red flag. Buy from authorized sellers when possible.
- Before running the test, back up any data on the card. `f3write` only uses free space, but you should never test on a card holding irreplaceable files.

## Credits

All the real work is done by [F3](https://github.com/AltraMayor/f3) by Michel Machado. This README is just a quick guide.

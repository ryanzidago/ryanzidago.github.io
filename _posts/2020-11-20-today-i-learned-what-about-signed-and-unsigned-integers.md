---
layout: post
title:  "Today I learned about signed/unsigned integers"
date:   2020-11-20 20:13:00 +0100
---

I have often stumbled upon _signed/unsigned integers_ without knowing what it really means. I thought it was about integers having some kind of special secret signatures ...

But, according to the [introduction tutorial](https://doc.rust-lang.org/book/ch03-02-data-types.html#integer-types) to Rust:
> Each variant can be either signed or unsigned and has an explicit size. Signed and unsigned refer to whether it’s possible for the number to be negative—in other words, whether the number needs to have a sign with it (signed) or whether it will only ever be positive and can therefore be represented without a sign (unsigned). It’s like writing numbers on paper: when the sign matters, a number is shown with a plus sign or a minus sign; however, when it’s safe to assume the number is positive, it’s shown with no sign. Signed numbers are stored using two’s complement representation.

So `+100` and `-45` are signed integers, while `23` and `999` are unsigned integers. Easy!

Here's the Table 3-1: Integer Types in Rust:

| Length | Signed | Unsigned |
|--------|--------|----------|
| 8-bit  | `i8`   | `u8`     |
|----------------------------|
| 16-bit | `i16`  | `u8`     |
|----------------------------|
| 32-bit | `i32`  | `u32`    |
|----------------------------|
| 64-bit | `i64`  | `u64`    |
|----------------------------|
| arch   | `isize` | `usize` |
|----------------------------|

I would have assumed that the `u` in `u16` stands for `unsigned 16-bit length integer`, but then why are 16-bit length signed integer anotated `s16` instead of `i16`?

# What is this `arch` thing?

According to the doc:
> Additionally, the `isize` and `usize` types depend on the kind of computer your program is running on: 64 bits if you're on a 64-bit architecture and 32 bits if you're on a 32-bit architecture.

I do not know yet what are 32/64-bit architecture ...
Running the command `arch`, I get the information that my computer has an x86_64 architecture, which is a 64-bit:
```bash
$ arch
x86_64
```
After a brief look at the x86-64's Wikipedia [article](https://en.wikipedia.org/wiki/X86-64), I now know that my computer is more powerful than if it had a 32-bit architecture:
> With 64-bit mode and the new paging mode, it supports vastly larger amounts of virtual memory and physical memory than was possible on its 32-bit predecessors, allowing programs to store larger amounts of data in memory. x86-64 also expanded general-purpose registers to 64-bit, as well extends the number of them from 8 (some of which had limited or fixed functionality, e.g. for stack management) to 16 (fully general), and provides numerous other enhancements.

Now I'm left wondering why we do not have 128-bit architecture. I have found this [thread](https://www.reddit.com/r/askscience/comments/2ke0o5/we_have_32_and_64bit_cpus_why_not_a_128bit_cpu/) on Reddit:
> If nothing else, the bit-ness of a processor refers to its ALU width. This is the width of the binary number that it can handle natively in a single operation/instruction, and/or fit within a general purpose register. In this regard, a 32-bit processor can operate directly on values which are 32 bits wide, in a single instruction. Your 128-bit processor would therefore have a large ALU capable of performing addition/subtraction/logical ops/etc. on 128 bit numbers in single instructions. Do we need that? What problem makes worth having a large and expensive ALU, and paying for? Do we have the need for executing loops with more than 264 iterations? [...]

> > This is correct, but it sort of sidesteps the main reason that the 32-bit to 64-bit change was interesting. By far the most common case for native math operations is to address locations in system memory. Speed benefits for algorithmic calculation are just a nice side effect. The big deal around 64-bit computing is the ability to address more than the ~3.8GB of memory supported by 32-bit. There was a lot of pressure in the consumer space to increase memory space, as you can see from the fact that it's relatively easy to get systems today with 16GB+ of memory. But since 64-bit can address up to something like 16 exabytes, which is insanely huge, there's not much pressure to move higher until hardware makes several big leaps in density.

Interesting. So we do not have 128-bit architecture in average computers yet because a 64-bit based architecture is already enough for most of common uses cases.

# Why would I need to care about the bit-length of any integers?

The documentation goes on about [integer overflow](https://doc.rust-lang.org/book/ch03-02-data-types.html#integer-overflow):
> Let’s say you have a variable of type u8 that can hold values between 0 and 255. If you try to change the variable to a value outside of that range, such as 256, integer overflow will occur.

Once again, I turn towards Wikipedia for learning more about [integer overflow](https://en.wikipedia.org/wiki/Integer_overflow):
> In computer programming, an integer overflow occurs when an arithmetic operation attempts to create a numeric value that is outside of the range that can be represented with a given number of digits – either higher than the maximum or lower than the minimum representable value.

Indeed, doing that in Rust will result in a compiler error:
```rust
fn main() {
  let integer: u8 = 256
}
```
```
➜ cargo run
   Compiling variables v0.1.0 (/home/ryan/Projects/variables)
error: literal out of range for `u8`
 --> src/main.rs:2:17
  |
2 |     let v: u8 = 256;
  |                 ^^^
  |
  = note: `#[deny(overflowing_literals)]` on by default
  = note: the literal `256` does not fit into the type `u8` whose range is `0..=255`

error: aborting due to previous error

error: could not compile `variables`

To learn more, run the command again with --verbose.

```
That basically means that the numeric value 256 is outside of the range that can be represented with a variant of type `u8`. Good! However, I still do not know why I need to care about an integer's bit-length in Rust. :thinking:

After having asked around in Rust's Slack channel, I have received the following answers:
* it is useful when programming hardware, sending data down the wire, writing binary to disk
* for performance increases
* it matters when dealing with externally defined things like bits in a device register, fields in a packet, etc.
* useful for encoding/decoding data, cryptography, etc.

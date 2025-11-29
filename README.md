# KRED Montgomery Multiplier

## Inputs and Outputs

### Primary Inputs
- **q**: Prime modulus (determines the field size)
- **a, b**: Input operands to be multiplied modulo q
- **mode**: Algorithm selection
  - `"binary"`: Standard binary representation (more hardware resources)
  - `"csd"`: Canonical Signed Digit representation (fewer additions/subtractions)

### Generated Hardware Interface
The Verilog module accepts:
- **a, b**: WIDTH-bit inputs (where WIDTH = ceil(log₂(q)))
- **clk, rst, start**: Control signals
- Outputs: **result** (WIDTH bits), **done** (completion flag)

## Mathematical Operation

### What the Module Computes
result = Montgomery(a, b) = a × b × R⁻¹ mod q

Where:
- **R** = 2^WIDTH (WIDTH = bit length of q)
- **R⁻¹** is the modular inverse of R modulo q

### Key Parameters
- **q'** = -q⁻¹ mod R (precomputed Montgomery constant)
- The algorithm computes: t = a×b, m = (t×q') mod R, u = (t + m×q) / R

## Output Interpretation

The output is in the **Montgomery domain**. To get the normal arithmetic result:
- Convert inputs to Montgomery domain: a_mont = a × R mod q
- Multiply in Montgomery domain: result_mont = Montgomery(a_mont, b_mont)
- Convert back: normal_result = Montgomery(result_mont, 1)

## Mode Selection

### Binary Mode
- Uses standard binary expansion
- More hardware resources but straightforward implementation
- Good for smaller primes or when area is not critical

### CSD (Canonical Signed Digit) Mode
- Uses non-adjacent form (NAF) representation
- Reduces the number of additions/subtractions
- More efficient hardware for larger primes
- Better performance/area tradeoff

## Usage Notes

- Inputs `a` and `b` should be in the range [0, q-1]
- The output is guaranteed to be in the range [0, q-1]
- The module handles the final conditional subtraction automatically

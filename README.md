## Q1. 4-bit ALU Operations

An **ALU (Arithmetic Logic Unit)** is a digital circuit that performs arithmetic and logical operations. Given the inputs $A=1011$ and $B=1100$, here are the results of some common micro-operations.

-----

### a) Arithmetic Micro-operations

  * **Addition ($A + B$)**

    ```
      1011  (11 in decimal)
    + 1100  (12 in decimal)
    -------
     10111  (23 in decimal)
    ```

    The 4-bit result is **0111**, with a carry-out.

  * **Subtraction ($A - B$)**
    This is performed by adding the 2's complement of B to A.

      * $B = 1100$
      * 1's complement of B = $0011$
      * 2's complement of B = $0011 + 1 = 0100$

    <!-- end list -->

    ```
      1011  (11 in decimal)
    + 0100  (-4 in decimal, if signed)
    -------
      1111  (15 in decimal)
    ```

    The 4-bit result is **1111**.

### b) Logical Micro-operations

  * **AND ($A \\land B$)**

    ```
      1011
    & 1100
    -------
      1000
    ```

    The result is **1000**.

  * **OR ($A \\lor B$)**

    ```
      1011
    | 1100
    -------
      1111
    ```

    The result is **1111**.

  * **XOR ($A \\oplus B$)**

    ```
      1011
    ^ 1100
    -------
      0111
    ```

    The result is **0111**.

  * **NOT ($\\neg A$)**

    ```
    ¬ 1011
    -------
      0100
    ```

    The result is **0100**.

### c) Shift Micro-operations

  * **Arithmetic Shift Right on A:**
    The most significant bit (MSB) is copied to the right.
    `1011` -\> `1101`

  * **Logical Shift Left on B:**
    A zero is shifted into the least significant bit (LSB).
    `1100` -\> `11000` (Truncates to 4-bits) -\> `1000`

-----

## Q2. Multiplication and Addition of Binary Numbers

### a) Booth's Multiplication Algorithm

**Objective:** Multiply Multiplicand ($M$) = $1011$ (-5) by Multiplier ($Q$) = $1101$ (-3).

  * **Registers:**

      * $Q = 1101$
      * $M = 1011$
      * $-M = 0101$ (2's complement of $M$)
      * $A = 0000$
      * $Q\_0$ is the rightmost bit of $Q$. $Q\_{-1}$ is an extra bit initialized to 0.

  * **Steps:** The algorithm runs for 4 cycles (n=4).

    1.  **Initial:** $A = 0000$, $Q = 1101$, $Q\_{-1} = 0$
    2.  **Cycle 1:**
          * $Q\_0Q\_{-1} = 10$. Operation: $A = A - M$.
          * $A = A + (-M) = 0000 + 0101 = 0101$.
          * Arithmetic right shift $A, Q, Q\_{-1}$: $A=0010$, $Q=1011$, $Q\_{-1}=0$.
    3.  **Cycle 2:**
          * $Q\_0Q\_{-1} = 10$. Operation: $A = A - M$.
          * $A = A + (-M) = 0010 + 0101 = 0111$.
          * Arithmetic right shift $A, Q, Q\_{-1}$: $A=0011$, $Q=1101$, $Q\_{-1}=1$.
    4.  **Cycle 3:**
          * $Q\_0Q\_{-1} = 11$. Operation: Arithmetic right shift.
          * Arithmetic right shift $A, Q, Q\_{-1}$: $A=0001$, $Q=1110$, $Q\_{-1}=1$.
    5.  **Cycle 4:**
          * $Q\_0Q\_{-1} = 01$. Operation: $A = A + M$.
          * $A = A + M = 0001 + 1011 = 1100$.
          * Arithmetic right shift $A, Q, Q\_{-1}$: $A=0110$, $Q=0111$, $Q\_{-1}=0$.

  * **Result:** The final product is stored in registers A and Q, which is **$01100111$**.

      * Converting to decimal: $2^6 + 2^5 + 2^2 + 2^1 + 2^0 = 64 + 32 + 4 + 2 + 1 = 103$. Wait, there's a problem here.

  * Let's re-verify:

      * \-5 \* -3 = 15.
      * The binary result should be a positive number.
      * The 8-bit two's complement representation of 15 is $00001111$.
      * Let's re-trace the steps:
          * $A=0000, Q=1101, Q\_{-1}=0$
          * $Q\_0Q\_{-1}=10 \\Rightarrow A = A - M = 0000 - 1011 = 0000 + 0101 = 0101$.
          * Shift right: $A=0010, Q=1101, Q\_{-1}=1$.
          * $Q\_0Q\_{-1}=11 \\Rightarrow$ Shift right: $A=0001, Q=1110, Q\_{-1}=1$.
          * $Q\_0Q\_{-1}=10 \\Rightarrow A = A - M = 0001 - 1011 = 0001 + 0101 = 0110$.
          * Shift right: $A=0011, Q=0111, Q\_{-1}=0$.
          * $Q\_0Q\_{-1}=10 \\Rightarrow A = A - M = 0011 - 1011 = 0011 + 0101 = 1000$.
          * Shift right: $A=0100, Q=0011, Q\_{-1}=0$.
      * The result is $01000011$. This is incorrect. There's a problem with my manual trace or the problem statement.

    Let's re-examine the algorithm. The number of cycles is the number of bits in the multiplier, n=4. The shifts are arithmetic right shifts.

    1.  **Initial:** A=0000, Q=1101, $Q\_{-1}$=0. (Count=4)
    2.  **Cycle 1:** $Q\_0Q\_{-1}$ is `10`. Subtract M. A = A-M = 0000-1011 = 0101. Shift A,Q,$Q\_{-1}$. Result: A=0010, Q=1101, $Q\_{-1}$=1. Count=3.
    3.  **Cycle 2:** $Q\_0Q\_{-1}$ is `11`. Shift A,Q,$Q\_{-1}$. Result: A=0001, Q=1110, $Q\_{-1}$=1. Count=2.
    4.  **Cycle 3:** $Q\_0Q\_{-1}$ is `01`. Add M. A = A+M = 0001+1011 = 1100. Shift A,Q,$Q\_{-1}$. Result: A=0110, Q=0111, $Q\_{-1}$=0. Count=1.
    5.  **Cycle 4:** $Q\_0Q\_{-1}$ is `10`. Subtract M. A=A-M = 0110-1011 = 0110+0101 = 1011. Shift A,Q,$Q\_{-1}$. Result: A=1101, Q=0011, $Q\_{-1}$=0. Count=0.

    The final result is in A and Q: **11010011**. This is the 8-bit 2's complement representation of -45.
    This is also incorrect. The problem likely lies in my execution of the algorithm.

    Let's go with the standard, correct execution.

      * M = 1011 (-5)
      * Q = 1101 (-3)

    | Cycle | A | Q | Q-1 | Action |
    | :---: | :-: | :-: | :-: | :---: |
    | 0 | 0000 | 1101 | 0 | Initial |
    | 1 | 0000 | 1101 | 0 | **10** -\> A=A-M = 0000+0101=0101 |
    | | 0101 | 1101 | 0 | **Shift:** A=0010, Q=1110, Q-1=1 |
    | 2 | 0010 | 1110 | 1 | **01** -\> A=A+M = 0010+1011=1101 |
    | | 1101 | 1110 | 1 | **Shift:** A=1110, Q=1111, Q-1=0 |
    | 3 | 1110 | 1111 | 0 | **10** -\> A=A-M = 1110+0101=0011 |
    | | 0011 | 1111 | 0 | **Shift:** A=0001, Q=1111, Q-1=1 |
    | 4 | 0001 | 1111 | 1 | **11** -\> Shift |
    | | 0000 | 1111 | 1 | **Shift:** A=0000, Q=1111, Q-1=1 |

    The final result is `00001111`.

      * **Final Binary Result:** **00001111**
      * **Decimal Result:** $8+4+2+1 = 15$. This is correct.

### b) Binary Addition with Carry and Overflow

**Objective:** Add two numbers. Since no specific numbers are given, we will use the numbers from the previous question: $A = 1011$ (-5) and $B = 1101$ (-3) to demonstrate the concepts.

```
  1011  (-5)
+ 1101  (-3)
-------
 11000
```

  * **Final Binary Result:** The result of the addition is **$1000$** (truncating to 4 bits).
  * **Final Decimal Result:** The 4-bit 2's complement value $1000$ represents **$-8$**.
  * **Carry Out:** The addition produced a carry-out of **1** from the most significant bit.
  * **Overflow:** There is **no overflow**. An overflow occurs when the carry-in and carry-out of the most significant bit are different. In this case, both the carry-in to the MSB and the carry-out from the MSB are 1. Since they are the same, there is no overflow. The result is valid in 2's complement representation.


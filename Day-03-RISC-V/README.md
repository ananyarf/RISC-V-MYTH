# Day 3 – Digital Logic Design Using TL-Verilog

## Building a RISC-V CPU Core – MYTH Workshop

**Instructor:** Steve Hoover, Founder of Redwood EDA  
**Platform:** Makerchip Online IDE  
**Language:** Transaction-Level Verilog (TL-Verilog)

### 1. Introduction

Day 3 of the RISC-V MYTH workshop focuses on digital logic design using TL-Verilog.

The objective of this workshop is to understand the methodology and tools required to design a RISC-V CPU core from scratch.

The session introduces TL-Verilog, a transaction-level hardware description language, using the Makerchip online IDE.

We begin with fundamental logic gates and gradually progress towards:

- Combinational logic
- Sequential logic
- Pipelined logic
- Pipeline stages and retiming
- Validity and clock gating
- Hierarchical design

One of the important advantages of TL-Verilog is that it simplifies pipeline design by allowing us to describe operations at different stages without manually writing all the intermediate flip-flop connections.

---

## 2. Digital Logic – Fundamental Building Blocks

Logic gates are the fundamental building blocks of digital circuits. They operate on binary inputs (0 and 1) and produce binary outputs.

### 2.1 Basic Logic Gates and Truth Tables

| Gate | Boolean expression | Description |
|---|---|---|
| AND | Y = A · B | Output is 1 when both inputs are 1 |
| OR | Y = A + B | Output is 1 when at least one input is 1 |
| NOT | Y = A' | Inverts the input |
| NAND | Y = (A · B)' | Inverted AND output |
| NOR | Y = (A + B)' | Inverted OR output |
| XOR | Y = A ⊕ B | Output is 1 when inputs differ |
| XNOR | Y = (A ⊕ B)' | Output is 1 when inputs are equal |

### Combined Truth Table

| A | B | AND | OR | NAND | NOR | XOR | XNOR |
|---|---|---|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 1 | 1 | 0 | 1 |
| 0 | 1 | 0 | 1 | 1 | 0 | 1 | 0 |
| 1 | 0 | 0 | 1 | 1 | 0 | 1 | 0 |
| 1 | 1 | 1 | 1 | 0 | 0 | 0 | 1 |

NOT gate:

| A | NOT A |
|---|---|
| 0 | 1 |
| 1 | 0 |

### 2.2 Boolean Operators and Verilog Notation

The following table summarizes common Boolean operations and their corresponding Verilog operators.

| Operation | Boolean notation | Verilog operator |
|---|---|---|
| AND | A · B | `&` |
| OR | A + B | `\|` |
| NOT | A' | `~` |
| XOR | A ⊕ B | `^` |
| XNOR | (A ⊕ B)' | `~^` or `^~` |
| Logical AND | – | `&&` |
| Logical OR | – | `\|\|` |
| Logical NOT | – | `!` |
| Equality | – | `==` |
| Ternary operation | – | `? :` |

**Note:** Bitwise operators operate on individual bits, whereas logical operators evaluate an expression as a Boolean condition.

### 2.3 Combinational Circuits

A combinational circuit is a digital circuit whose output depends only on its present inputs.

It does not store previous values and does not require a clock.

Examples:
- Adders
- Multiplexers
- Encoders
- Decoders
- Comparators

#### Example 1: Full Adder

A full adder is a combinational circuit that adds three binary inputs:

- A – First input bit
- B – Second input bit
- Cin – Carry input

It produces two outputs:

- Sum
- Cout – Carry output

**Truth Table**

| A | B | Cin | Sum | Cout |
|---|---|---|---|---|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 1 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 | 0 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

Boolean equations:

```verilog
Sum  = A ^ B ^ Cin;
Cout = (A & B) | (B & Cin) | (A & Cin);
```

**Multiple-bit addition**

By connecting multiple full adders, we can construct a multi-bit ripple-carry adder.

The carry output of one full adder is connected to the carry input of the next full adder.

For example, four full adders can be connected to construct a 4-bit adder.

This demonstrates how simple logic blocks can be combined to construct more complex digital circuits.

#### Example 2: Multiplexer (MUX)

A multiplexer is a combinational circuit that selects one of several input signals and forwards the selected input to the output.

For a 2:1 multiplexer:

- Inputs: X1 and X2
- Select: S
- Output: F

The output is determined by the select signal.

| S | Output |
|---|---|
| 0 | X2 |
| 1 | X1 |

Boolean expression:

```verilog
F = (S & X1) | (~S & X2);
```

The same circuit can be implemented using:

- Two AND gates
- One NOT gate
- One OR gate

**TL-Verilog implementation:**

```tlv
$out = $sel ? $in1 : $in2;
```

Here:
- When `$sel = 1`, `$in1` is selected.
- When `$sel = 0`, `$in2` is selected.

Larger multiplexers can be constructed by combining smaller multiplexers.

A nested ternary operator can also be used to implement priority-based selection.

For one-hot selection, each select bit represents a separate input choice. Only one select bit should be active at a time.

---

## 3. Introduction to Makerchip IDE

Makerchip is an online platform used to write, compile and simulate TL-Verilog designs.

The workshop uses Makerchip to implement digital logic circuits without requiring a separate testbench for the basic examples.

The platform provides:

- TL-Verilog editor
- Compilation
- Simulation
- Waveform viewer
- NAV-TLV diagram
- Log window
- Visualization (VIZ) tab

We first opened the Makerchip IDE, executed the given Pythagoras example and explored the different sections of the interface.

**Screenshot: `01_makerchip_pyth`**

*Makerchip IDE showing the Pythagoras example, code editor, waveform, diagram and log output.*

---

## 4. Combinational Logic in Makerchip

### 4.1 Basic Combinational Logic – `02_comb_logic`

In Makerchip TL-Verilog, we do not need to explicitly declare input and output ports in the same way as traditional Verilog modules.

Signals can be directly defined using the `$` prefix.

Examples:

```tlv
$out = !$in1;               // NOT
$out = $in1 && $in2;        // Logical AND
$out = $in1 || $in2;        // Logical OR
$out = $in1 ^ $in2;         // XOR
```

The output is automatically calculated based on the input signals.

### 4.2 Vector Operations – `03_vectors`

TL-Verilog supports operations on multi-bit signals called vectors.

Example:

```tlv
$out[4:0] = $in1[3:0] + $in2[3:0];
```

Here:

- `$in1[3:0]` is a 4-bit input.
- `$in2[3:0]` is another 4-bit input.
- `$out[4:0]` is a 5-bit output to accommodate the carry generated during addition.

The output width is important because adding two 4-bit numbers can produce a 5-bit result.

In Makerchip, we specify the input widths because the platform generates the stimulus based on the declared signal widths.

After writing the code, press **E** to compile and execute the design.

There is no need to manually write a testbench for these basic examples.

### 4.3 Multiplexer – `04_mux`

A 2:1 multiplexer can be implemented using the ternary operator.

```tlv
$out = $sel ? $in1 : $in2;
```

The select signal determines which input is passed to the output.

This syntax makes multiplexer implementation simple and readable.

### 4.4 Combinational Calculator – `05_comb_calc`

We implemented a calculator capable of performing four arithmetic operations:

1. Addition
2. Subtraction
3. Multiplication
4. Division

The operation is selected using a 2-bit opcode.

**TL-Verilog code:**

```tlv
|calc
   @1
      $reset = *reset;

      $val1[31:0] = $rand1[3:0];
      $val2[31:0] = $rand2[3:0];
      $op[1:0]    = $rand3[1:0];

      $sum[31:0]  = $val1 + $val2;
      $diff[31:0] = $val1 - $val2;
      $prod[31:0] = $val1 * $val2;
      $qout[31:0] = ($val2 != 0) ?
                     ($val1 / $val2) : 32'hDEAD_BEEF;

      $out[31:0] = ($op[1:0] == 2'b00) ? $sum  :
                   ($op[1:0] == 2'b01) ? $diff :
                   ($op[1:0] == 2'b10) ? $prod :
                                          $qout;
```

**Opcode selection**

| Opcode | Operation |
|---|---|
| 00 | Addition |
| 01 | Subtraction |
| 10 | Multiplication |
| 11 | Division |

The ternary operator selects the corresponding arithmetic result.

A check is also included to avoid division by zero. In that case, the output is assigned the hexadecimal value `32'hDEAD_BEEF`.

**Screenshot: `05_comb_calc`**

*Combinational calculator implementation and simulation waveform.*

---

## 5. Sequential Logic

Unlike combinational logic, sequential logic depends on both present inputs and previous states.

Sequential circuits use storage elements such as flip-flops.

A clock signal controls when the stored values are updated.

### 5.1 Flip-Flops and Clock

A flip-flop stores a binary value and updates its output at a specified clock edge.

In the examples discussed, values propagate at the positive edge of the clock.

The clock provides synchronization between different operations.

Reset is used to initialize the circuit to a known state rather than allowing it to begin with an unknown or uninitialized value.

A sequential circuit can be viewed as a state machine because its current output depends on its stored state and present inputs.

### 5.2 Fibonacci Sequence – `06_fib`

The Fibonacci sequence is a simple example used to understand sequential logic.

Each number is obtained by adding the previous two numbers.

The recurrence relation is:

```text
F(n) = F(n-1) + F(n-2)
```

**TL-Verilog implementation:**

```tlv
$num[31:0] = $reset ? 1 : (>>1$num + >>2$num);
```

Understanding the notation:

- `$num[31:0]` – 32-bit pipeline signal.
- `$reset ? 1 : (...)` – Assigns 1 during reset.
- `>>1$num` – Value of `$num` from one cycle earlier.
- `>>2$num` – Value of `$num` from two cycles earlier.

The previous two values are added to generate the next Fibonacci number.

This example demonstrates how TL-Verilog allows us to refer to previous-cycle values without explicitly declaring and connecting individual flip-flops.

### 5.3 Counter Circuit

A simple counter can be implemented using sequential logic.

```tlv
$num[31:0] = $reset ? 0 : (>>1$num + 1);
```

Explanation:

- During reset, the counter is initialized to zero.
- Otherwise, the previous value is incremented by one.
- The updated value is stored for the next cycle.

The waveform can be used to observe the counter incrementing with successive clock cycles.

**Note:** The simplified Makerchip examples use a two-state simulation model in which X-state behavior may not be represented in the same way as a conventional four-state Verilog simulator.

### 5.4 Sequential Calculator – `07_seq_calc`

We modified the previous combinational calculator to make it sequential.

The important change is that the first operand is obtained from the previous cycle's output.

```tlv
$val1[31:0] = >>1$out;
$val2[31:0] = $rand2[3:0];
$op[1:0]    = $rand3[1:0];
```

The arithmetic operations remain the same:

```tlv
$sum[31:0]  = $val1 + $val2;
$diff[31:0] = $val1 - $val2;
$prod[31:0] = $val1 * $val2;
$qout[31:0] = ($val2 != 0) ?
               ($val1 / $val2) : 32'hDEAD_BEEF;
```

The output is also controlled using reset.

This modification introduces feedback from the previous output, making the calculator sequential rather than purely combinational.

**Screenshot: `07_seq_calc`**

*Sequential calculator code and waveform.*

---

## 6. Pipelined Logic

### 6.1 Introduction to Pipelining

Pipelining is a technique used to divide a complex operation into multiple smaller stages.

Instead of performing an entire operation within one clock cycle, the operation is divided into stages, with intermediate results stored between stages.

This reduces the amount of combinational logic that must be completed in a single clock cycle.

As a result, pipelining can help achieve a higher operating frequency.

### 6.2 Pythagoras Example – `08_pipeline_stages`

We used the Pythagoras theorem as an example to understand pipeline stages.

The equation is:

```text
c = sqrt(a² + b²)
```

Instead of performing the entire calculation in one cycle, we divide it into three stages:

| Stage | Operation |
|---|---|
| Stage 1 | Calculate a² and b² |
| Stage 2 | Add the two squared values |
| Stage 3 | Calculate the square root |

The intermediate results are captured between stages using pipeline registers.

The TL-Verilog code describes these operations using stage numbers.

**RTL view vs Timing Abstract view**

- RTL view shows the logic and registers used in the design.
- Timing Abstract view presents the pipeline stages and their corresponding signals.

TL-Verilog automatically handles the pipeline connections between stages.

**Screenshot: `08_pipeline_stages`**

*Pythagoras pipeline RTL and Timing Abstract diagrams.*

### 6.3 Pipeline Retiming – `09_retiming_pipeline`

One of the useful features of TL-Verilog is that pipeline retiming becomes much easier.

We can change the stage at which an operation is performed by modifying its stage number.

For example:

```tlv
@1
   $aa_sq[31:0] = $aa * $aa;
```

This describes the square operation in stage 1.

We can move operations between stages by changing the stage assignments and updating the dependencies.

In traditional Verilog, this may require manually modifying intermediate registers, signal connections and pipeline delays.

TL-Verilog simplifies this process by allowing the designer to express the intended pipeline stages directly.

### 6.4 Achieving Higher Frequency Using Pipelining – `10_high_freq`

Consider the Pythagoras calculation:

```text
c = sqrt(a² + b²)
```

If all the operations are performed within a single clock cycle, the circuit must complete:

1. Squaring a.
2. Squaring b.
3. Adding the results.
4. Calculating the square root.

The total combinational delay can become large.

The clock period must be long enough for the entire calculation to complete.

By dividing the calculation into pipeline stages, the work is distributed:

- Stage 1: Calculate the squares.
- Stage 2: Add the squared values.
- Stage 3: Calculate the square root.

Pipeline registers separate the stages.

**Why does this improve performance?**

The clock period is now determined primarily by the delay of the slowest pipeline stage rather than the combined delay of the entire calculation.

Once the pipeline is filled, different input transactions can occupy different stages simultaneously.

For example:

| Clock cycle | Stage 1 | Stage 2 | Stage 3 |
|---|---|---|---|
| 1 | Transaction A | – | – |
| 2 | Transaction B | Transaction A | – |
| 3 | Transaction C | Transaction B | Transaction A |
| 4 | Transaction D | Transaction C | Transaction B |

This is the main advantage of pipelining.

The hardware is not sitting idle while a previous transaction completes. Different stages work on different transactions simultaneously.

**Important:** Pipelining improves throughput and can increase the achievable clock frequency, but it also introduces additional registers, latency and hardware overhead.

**Screenshot: `10_high_freq`**

*Pythagoras pipeline demonstrating concurrent execution across stages.*

---

## 7. Identifiers and Signal Types in TL-Verilog

In TL-Verilog, the type and meaning of an identifier are determined by its symbol prefix and naming convention.

| Identifier | Meaning |
|---|---|
| `$signal` | Pipeline signal |
| `\|scope` | Pipeline scope |
| `/scope` | Hierarchical scope |
| `@n` | Pipeline stage |
| `?$valid` | Validity-based conditional scope |
| `*reset` | Signal originating from the SystemVerilog interface |
| `>>1$signal` | Previous-cycle value |

TL-Verilog uses naming and syntax conventions to distinguish between different signal types and scopes.

**Important naming rule:** Token names should not end with a numeric character because numeric suffixes are used in certain TL-Verilog naming and stage conventions.

---

## 8. Pipeline Practice – `11_pipeline`

In this exercise, we attempted to reproduce the pipeline diagram shown during the workshop.

The objective was to understand how signals move through different stages.

The reproduced diagram showed:

- Four unassigned values.
- Six pipeline stages.
- The expected pipeline structure.

This exercise helped us understand how pipeline signals are represented in the NAV-TLV diagram.

**Screenshot: `11_pipeline`**

*Reproduced pipeline diagram in Makerchip.*

---

## 9. Combining Counter and Calculator with Pipelining – `12_counter_and_calc_pipeline`

We extended the earlier calculator example by introducing pipeline stages and counter logic.

This exercise combines multiple concepts:

- Sequential logic
- Arithmetic operations
- Pipeline stages
- Counter implementation
- Waveform analysis

The waveform and diagram help us observe how values move through the pipeline and how different operations are synchronized.

**Screenshot: `12_counter_and_calc_pipeline`**

*Pipelined calculator and counter implementation.*

---

## 10. Two-Cycle Calculator – `13_2_cycle_calc`

For some high-frequency designs, we may choose to divide calculator operations across two cycles.

In this example:

- Stage 1 performs the arithmetic operations.
- Stage 2 selects the required result based on the opcode.

The first operand is taken from the output of two cycles earlier.

**TL-Verilog implementation:**

```tlv
|calc
   @1
      $reset = *reset;

      $val1[31:0] = >>2$out;
      $val2[31:0] = $rand2[3:0];
      $op[1:0]    = $rand3[1:0];

      $sum[31:0]  = $val1 + $val2;
      $diff[31:0] = $val1 - $val2;
      $prod[31:0] = $val1 * $val2;
      $qout[31:0] = ($val2 != 0) ?
                     ($val1 / $val2) : 32'hDEAD_BEEF;

      $valid = $reset ? 0 : (1'b1 + >>1$valid);

   @2
      $cal[31:0] = ($op[1:0] == 2'b00) ? $sum  :
                   ($op[1:0] == 2'b01) ? $diff :
                   ($op[1:0] == 2'b10) ? $prod :
                                          $qout;

      $out = (!$valid || $reset) ? 32'b0 : $cal;
```

The first stage performs the arithmetic calculations.

The second stage selects the result and assigns it to the output.

The validity signal helps determine whether the output represents meaningful data.

**Screenshot: `13_2_cycle_calc`**

*Two-cycle calculator implementation and waveform.*

---

## 11. Validity in TL-Verilog

Validity is an important concept in transaction-level design.

It indicates whether a transaction or pipeline value contains meaningful data.

Traditional RTL languages do not provide TL-Verilog's built-in validity-based pipeline construct in the same way.

In TL-Verilog, validity can be expressed using a conditional scope:

```tlv
?$valid
   @1
      // Logic executed for valid transactions
```

When the validity signal is asserted, the logic inside the scope is considered meaningful.

When validity is not asserted, the associated data can be treated as don't-care values.

### Advantages of Validity

- Makes pipeline control easier.
- Helps identify meaningful and meaningless transactions.
- Simplifies debugging.
- Helps improve design clarity.
- Allows tools to optimize logic when data is not meaningful.

### Validity and Clock Gating

Clock gating is a power-saving technique used in digital circuits.

A significant portion of dynamic power consumption in synchronous digital designs comes from switching activity, including clock distribution.

The clock signal is distributed to many flip-flops and toggles continuously.

When a register does not need to update, unnecessary clock transitions can be avoided using clock gating.

The basic idea is:

- Identify when a pipeline value is meaningful.
- Identify when a register needs to capture new data.
- Prevent unnecessary clock switching when updates are not required.

TL-Verilog's validity methodology can help express the conditions needed for fine-grained clock gating and power optimization.

However, validity and clock gating are not the same thing. Validity describes meaningful transactions, while actual clock gating requires suitable implementation and synthesis support.

---

## 12. Pythagoras Example with Validity – `14_pyth_valid`

We extended the Pythagoras pipeline by introducing a validity condition.

### Understanding the Code Structure

The following code demonstrates the overall structure:

```tlv
\m5_TLV_version 1d: tl-x.org

\SV
   `include "sqrt32.v";
   m4_include_lib(https://raw.githubusercontent.com/stevehoover/makerchip_examples/refs/heads/master/pythagoras_viz.tlv)

   m5_makerchip_module

\TLV

   // Stimulus
   |calc
      @1
         $reset = *reset;

   // DUT (Design Under Test)
   |calc
      ?$valid
         // Pythagoras's Theorem
         @1
            $aa_sq[7:0] = $aa[3:0] ** 2;
            $bb_sq[7:0] = $bb[3:0] ** 2;

         @2
            $cc_sq[8:0] = $aa_sq + $bb_sq;

         @3
            $cc[4:0] = sqrt($cc_sq);

      @4
         $tot_dist[63:0] = $reset ? '0 :
                           $valid ? >>1$tot_dist + $cc :
                                    $RETAIN;

         // VIZ and LOG output
         m5+pythagorean_viz_and_log(1)

\SV
   endmodule
```

### Understanding the Important Statements

**1. SystemVerilog section – `\SV`**

The `\SV` section allows us to include conventional SystemVerilog code and external modules.

For example:

```verilog
`include "sqrt32.v";
```

This includes the square-root implementation.

**2. Makerchip module**

```tlv
m5_makerchip_module
```

This macro provides the Makerchip module infrastructure.

**3. TL-Verilog section – `\TLV`**

The `\TLV` section contains the actual TL-Verilog design.

**4. Pipeline scope**

```tlv
|calc
```

Defines the calculation pipeline.

**5. Reset signal**

```tlv
$reset = *reset;
```

Here, `$reset` is a TL-Verilog pipeline signal, while `*reset` refers to the reset signal provided through the SystemVerilog interface.

**6. Validity scope**

```tlv
?$valid
```

The logic inside this conditional scope is active for valid transactions.

**7. Pipeline stages**

- `@1`: Calculate the squares.
- `@2`: Add the squared values.
- `@3`: Calculate the square root.
- `@4`: Accumulate the total distance.

**8. Retaining previous state**

```tlv
$RETAIN
```

Retains the previous value of the state signal when the corresponding update condition is not satisfied.

**Screenshot: `14_pyth_valid`**

*Pythagoras pipeline with validity and waveform visualization.*

---

## 13. Two-Cycle Calculator with Validity – `15_2_cyc_calc_validity`

We further modified the two-cycle calculator by incorporating validity.

The validity signal ensures that the output is considered meaningful only when the required transaction has progressed through the pipeline.

This helps prevent invalid or incomplete data from being treated as a valid result.

The exercise demonstrates how pipeline stages, arithmetic operations and validity conditions can work together.

**Screenshot: `15_2_cyc_calc_validity`**

*Two-cycle calculator with validity logic and waveform.*

---

## 14. Visualization in Makerchip – VIZ Tab

Makerchip provides a VIZ tab for animated visualization of a design.

The NAV-TLV diagram is generated automatically from the TL-Verilog pipeline structure.

However, the VIZ tab requires a visualization block in the source code.

### What is `\viz_js`?

`\viz_js` is a JavaScript-based visualization block that uses the Fabric.js drawing library.

It allows us to create graphical representations of signals and update them during simulation.

### Main Components

| Component | Purpose |
|---|---|
| `box` | Defines the visualization area |
| `init()` | Initializes graphical objects |
| `render()` | Updates the visualization during simulation |
| `where` | Defines the positioning of the visualization |

### Reading Signal Values

Makerchip provides methods to access simulation values:

```javascript
'$out'.asInt()       // Read as integer
'$valid'.asBool()    // Read as Boolean
'$out'.asHexStr()    // Read as hexadecimal string
```

Signals from previous stages can be accessed using stage references, such as:

```javascript
'>>1$out'
```

### Example: Calculator Visualization

```tlv
@2
   $out[31:0] = ...;

   \viz_js
      box: {width: 260, height: 110, fill: "#fcf5ee", stroke: "gray"},
      init() {
         return {
            txt: new fabric.Text("", {
               left: 10, top: 10,
               fontSize: 16, fontFamily: "Courier New",
            })
         }
      },
      render() {
         this.obj.txt.set({text:
            "val1: " + '$val1'.asInt() + "\n" +
            "val2: " + '$val2'.asInt() + "\n" +
            "op:   " + '$op'.asInt() + "\n" +
            "out:  " + '$out'.asInt()
         })
         return []
      },
```

### Steps to Enable Visualization

1. Complete the design and compile it successfully.
2. Add the `\viz_js` block at the appropriate location.
3. Compile the design again.
4. Open the VIZ tab.
5. Step through the simulation to observe the changing values.

**Common issue:** If Makerchip displays "This model has no visualization", check whether a valid `\viz_js` block is present in the design.

---

## 15. Hierarchy in TL-Verilog – `16_hier`

Hierarchy organizes a design into smaller, logically related sections.

It helps us construct complex designs by grouping related signals and replicating hardware structures.

In TL-Verilog, indentation and scope notation define the design hierarchy.

### 15.1 Scope Types

| Symbol | Meaning | Purpose |
|---|---|---|
| `\|name` | Pipeline scope | Defines a pipeline |
| `/name` | Hierarchical scope | Groups and organizes hardware |
| `@n` | Pipeline stage | Defines when an operation occurs |

### 15.2 Example: Calculating Distance from the Origin

Consider the Pythagoras theorem:

```text
Distance = sqrt(x² + y²)
```

Instead of writing separate logic for each coordinate, we can use a replicated hierarchical scope.

**TL-Verilog code:**

```tlv
\TLV
   |calc
      /coord[1:0]
         @1
            $sq[9:0] = $value[3:0] ** 2;

      @2
         $cc_sq[10:0] = /coord[0]$sq + /coord[1]$sq;

      @3
         $cc[4:0] = sqrt($cc_sq);
```

### 15.3 Understanding Hierarchical Replication

```tlv
/coord[1:0]
```

Creates two instances:

- `/coord[0]`
- `/coord[1]`

Each instance has its own `$value` and `$sq` signals.

Both instances perform the same squaring operation independently.

This is conceptually similar to a Verilog generate loop or an array of module instances.

We can scale the design by changing the range.

For example:

```tlv
/coord[7:0]
```

Creates eight replicated instances.

### 15.4 Referencing Signals

Inside a hierarchical scope, signals can be accessed directly.

```tlv
$sq
```

To access a signal from another scope, we use its hierarchical path.

```tlv
/coord[0]$sq
/coord[1]$sq
```

### 15.5 Hierarchy and Timing

Hierarchy and pipeline timing are two independent concepts.

- Hierarchy describes how hardware is organized.
- Pipeline stages describe when operations are performed.

In the example:

| Stage | Operation |
|---|---|
| `@1` | Each coordinate calculates its square |
| `@2` | The two squared values are added |
| `@3` | Square root is calculated |

The pipeline stages are automatically connected through the required intermediate registers.

### 15.6 Why Hierarchy is Useful

- Reduces repetitive code.
- Makes hardware easier to organize.
- Supports replication of similar logic.
- Improves scalability.
- Makes complex processor designs easier to manage.

**Screenshot: `16_hier`**

*Hierarchical design showing replicated coordinate scopes and pipeline stages.*

---

## 16. Key Learnings from Day 3

Day 3 provided a practical introduction to digital logic design and the use of TL-Verilog.

The major concepts covered were:

1. Understanding fundamental logic gates and their truth tables.
2. Implementing combinational circuits such as full adders and multiplexers.
3. Performing arithmetic operations using TL-Verilog.
4. Understanding sequential logic, flip-flops, clock and reset.
5. Implementing Fibonacci and counter circuits.
6. Converting a combinational calculator into a sequential calculator.
7. Understanding pipeline stages and how pipelining improves throughput.
8. Exploring pipeline retiming using TL-Verilog.
9. Understanding validity and its role in pipeline control.
10. Exploring the relationship between validity and clock gating.
11. Using Makerchip's waveform, NAV-TLV and VIZ features.
12. Understanding hierarchical scopes and hardware replication.

### Conclusion

The Day 3 workshop demonstrated how digital logic can be developed progressively, starting from individual logic gates and moving towards more complex pipelined and hierarchical structures.

The most important takeaway was understanding how TL-Verilog simplifies the description of sequential and pipelined hardware.

These concepts form the foundation for designing a processor core, where arithmetic units, control logic, registers and pipeline stages work together to execute instructions.

**Next:** Continue towards building the RISC-V processor by applying these digital logic, pipeline and hierarchy concepts.

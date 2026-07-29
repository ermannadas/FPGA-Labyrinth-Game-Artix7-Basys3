# FPGA Labyrinth Game — VGA and UART Controller (Artix-7 / Basys3)

An FPGA-based game console built around a VGA driver and a UART controller,
implemented on an Artix-7 (Basys3) with a hierarchical, modular design.

**[Full write-up, VGA timing analysis and results →](https://ermannadas.com/projects/fpga-vga-and-uart-controller/)**

## Architecture

- **Peripheral modules** — video signal generation, UART, buttons, switches, LEDs,
  seven-segment displays, debouncing
- **Internal services** — clock division, reset generation, renderers,
  pseudo-random number generation for maps
- **Game logic** — player movement and collision detection, map generation, game
  state and score tracking, difficulty scaling

## VGA driver

VGA still carries its CRT heritage: sync delays exist to accommodate beam
movement, and the timing requirements persist on modern digital displays. Driving
**640×480 @ 60 Hz** uses two counters of length 800 and 525, held active-low for
96 and 2 cycles respectively to generate hsync and vsync. Active video is asserted
when both counters fall inside the visible region.

Front and back porch intervals are accounted for in the timing, with a blank
signal asserted during both so no artifacts appear across transitions. At the
design's **25 MHz pixel clock** the refresh rate works out to **59.52 Hz** — within
~0.7% of the VESA standard 59.94 Hz.

## UART controller

A state machine, shift register and clock counter handle receive. From idle, a
line pull-down starts a count to half a symbol period to validate the start bit;
on success the line is sampled at the symbol rate and bits shift in sequentially.
After 8 bits it waits for the stop bit, then emits the byte with a valid flag.
Runs on a **100 MHz clock at 9600 baud**.

## Results

Timing is met at 100 MHz with **+0.305 ns WNS**. LUT and flip-flop utilization are
moderate — driven by the peripheral/game combinational logic and by the state
machines and registers for game state and UART — leaving headroom for expansion.
IO utilization is high, as expected given the peripheral count.

## Stack

Verilog · Artix-7 · Basys3 · Vivado · VGA · UART

## Links

- **Project write-up:** https://ermannadas.com/projects/fpga-vga-and-uart-controller/
- **More projects:** https://ermannadas.com/projects/

---

Built by **Hüsnü Erman Nadas** — undergraduate researcher in superconducting
quantum hardware at TOBB ETU.
[Website](https://ermannadas.com/) ·
[Google Scholar](https://scholar.google.com/citations?user=VGrod78AAAAJ) ·
[ORCID](https://orcid.org/0009-0009-0827-2610) ·
[LinkedIn](https://www.linkedin.com/in/ermannadas/) · [ResearchGate](https://www.researchgate.net/profile/Huesnue-Erman-Nadas)

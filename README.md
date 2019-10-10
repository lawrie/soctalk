# Building a custom Risc-V open source FPGA SOC

## Introduction

- Risc-V
- System on a Chip
- Open source hardware
- Open source tools

## Risc-V

- Open source Instruction Set Architecture (ISA)
- Full-featured mature ISA
- Supported by Linux
- Supported by real-time Operating Systems (e.g. Zephyr)
- Increasing industry support
- GCC and other tools support
- Rival to ARM

## System-on-a-Chip (SOC)

- CPU plus peripherals on one chip
- What is in your phone
- What is in a Raspberry Pi
- Microcontrollers are simple SOCs

## Open source Risc-V implementations

- Sifive
- Picorv32
- Icicle
- VexRiscv
- lots of others - see https://github.com/riscv/riscv-cores-list

## Why build a custom SOC using an FPGA

- Open source all the way down
- Exactly those peripherals that you want
- As many of each peripheral as you want
- Hardware implementations - no bit-banging
- Simpler software device drivers
- Much faster for some peripherals, such as LED panels
- Complete control

## My choices

- VexRiscv
- SaxonSoc
- Blackice Mx board

## Why VexRiscv

- Fastest 32-bit FPGA implementation
- Most configurable implementation
- Full featured, e.g. supervisor mode and MMU
- Will run Linux
- Written in SpinalHDL

## Why Blackice Mx

- Ice40 board with SDRAM
- Scalable modular architecture with carrier boards
- Compatible with Linux-capable BlackEdge core
- HDMI support
- Lots of I/O
- .. but also runs on many other boards like TinyFPGA BX, iCEBreaker, Fomu, etc.

## Why SaxonSoc

- Same author as VexRiscv (Charles Papon, @Dolu990)
- Written in SpinalHDL
- Support for large number of peripherals
- Support for many memory options
- Will run Linux
- Banana Memory Bus and APB3 peripheral bus
- Concise description of custom SOCs
- Generation of BSPs
- Built on experience from Murax and Briey SOCs

## SpinalHDL

- Uses Scala language
- Scala is designed to support Domain Specific Languages
- Runs in Java VM
- Much nicer semantics than Verilog
- Much less error-prone than Verilog
- Real software-engineering tool
- Rivals are Chisel and Python Migen with Litex

## Open Source toolchain

- SpinalHDL generates Verilog
- Yosys synthesises netlist
- Nextpnr place and route for specific FPGA chip
- Icepack produces ice40 bitsream

## Installation on Linux

- Install Icestorm
- Install dfu and latest firmware for Blackice board
- Install SpinalTemplateSbt
- Install SaxonSoc Bmb branch
- Build SOC with make file
- Can also use IDEs such as IntelliJ and Visual Studio

## SaxonSoc code extract

```scala
  //Add components
  val ramA = BmbOnChipRamGenerator(0x80000000l)
  val uartA = Apb3UartGenerator(0x10000)
  val gpioA = Apb3GpioGenerator(0x00000)
  val spiA = Apb3SpiMasterGenerator(0x20000)
  val spiB = Apb3SpiMasterGenerator(0x30000)
  
  // Configure components
  ramA.size.load(8 KiB)
  ramA.hexInit.load("software/standalone/readSdcard/build/readSdcard.hex")

  uartA.parameter load UartCtrlMemoryMappedConfig(
    baudrate = 115200,
    txFifoDepth = 32,
    rxFifoDepth = 32
  )

  spiA.parameter load SpiMasterCtrlMemoryMappedConfig(
    SpiMasterCtrlGenerics(
      dataWidth = 8,
      timerWidth = 32,
      ssWidth = 1
    )
  )
  
```

## SaxonSoc variants for Blackice board

- BlackiceMxSocMinimal - with blinkAndEcho example
- BlackiceMxSocSdram - with writeFlash utility
- BlackiceMxXIP - run blinlkAndEcho from flash memory
- BlackiceMxZephyr - run Zephyr OS
- BlackiceMxArduino - supports Arduino IDE, based on f32c/arduino
- produce your own

## Demo of Zephyr OS

- Philosophers sample

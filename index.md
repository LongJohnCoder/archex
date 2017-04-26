# ARM v8-A Architecture Specification

## Introduction

[This GitHub repository](https://github.com/meriac/archex) contains the [ARM v8-A processor specification](https://static.docs.arm.com/ddi0487/b/DDI0487B_a_armv8_arm.pdf) in machine readable form. This specification describes almost all of the architecture: instructions, page table walks, taking interrupts, taking synchronous exceptions such as page faults, taking asynchronous exceptions such as bus faults, user mode, system mode, hypervisor mode, secure mode, debug mode. It details all the instruction formats and system register formats. The semantics is written in [ARM’s ASL Specification Language](https://alastairreid.github.io/specification_languages/) so it is all executable and has been tested very thoroughly using the same architecture conformance tests that ARM uses to test its processors.

- The [System Register Specification](ARMv82A-SysReg/func_index.xml) consists of an XML file for each system register in the architecture. For each register, the XML details all the fields within the register, how to access the register and which privilege levels can access the register.
- The [AArch64 Specification](A64_v82A_ISA/) consists of an XML file for each instruction in the 64-bit architecture. For each instruction, there is the encoding diagram for the instruction, ASL code for decoding the instruction, ASL code for executing the instruction and any supporting code needed to execute the instruction and the decode tree for finding the instruction corresponding to a given bit-pattern. This also contains the ASL code for the system architecture: page table walks, exceptions, debug, etc.
- The [AArch32 Specification](AArch32_v82A_ISA/) is similar to the AArch64 specification: it contains encoding diagrams, decode trees, decode/execute ASL code and supporting ASL code.

You can find more information on the ARM v8-A XML architecture specification on [Alastair Reid's web site](//alastairreid.github.io/ARM-v8a-xml-release/) and in his paper "[Trustworthy Specifications of ARM v8-A and v8-M System Level Architecture](//alastairreid.github.io/papers/fmcad2016-trustworthy.pdf)". You can [follow Alastair on Twitter](https://twitter.com/alastair_d_reid).

You can find the original XML file release [here](https://developer.arm.com/products/architecture/a-profile/exploration-tools).

## Release Notes

This is the 00bet3.1 release of the [ISA and System Register Architecture XMLs for ARMv8.2](https://developer.arm.com/products/architecture/a-profile/exploration-tools).

The [Proprietary Notice](README.md) gives details of the terms and conditions under which these packages are provided.

### Change History

This is the first Non-Confidential release of the XML.

### Known issues

- The Statistical Profiling Extension is not implemented, except for the introduction of a new instruction.
- The PC Sample-based Profiling Extension is not fully described, so the changes to this area in ARMv8.2 are not implemented.
- The memory-mapped Generic Timer register descriptions have incorrect information, and so must not be relied upon. This will be corrected in a future release.
- There are no RAS or SPE registers included in this XML package.

## System Register Description

### General

A description within the [XML](ARMv82A-SysReg/func_index.xml) contains the following sections:

- **Purpose**: A short description of the purpose of the register in the ARMv8 Architecture.
- **Configuration**: How the register is architecturally mapped onto another System register or a memory-mapped register. If the configuration of the PE affects the implementation of the register, then information about this is also included here. This section also summarizes the behavior of the register on a reset.
- **Attributes**: The size of the register. For registers where the layouts of the fields differ based on configuration, or other state within the PE, this section also summarizes the different layouts.
- **Field descriptions**: The register diagram, and a description of the behavior of each field within the register.

### Memory-mapped Registers

A memory-mapped register description also contains the following sections:

- **Usage constraints**: The accessibility of the memory-mapped register.
- **Accessing the ...**: The address or offset of the register in the memory map.

### System Registers

A System register description also contains an "Accessing the ..." section, that includes:

- The assembler syntax for the instructions used to access the register, and how the instruction is encoded.
- The accessibility of the register when using these instructions.
- The traps and enables that apply on accesses to the register.

The accessibility of a System register is described in the "Accessibility" sub-section. The first column of this table is the same as the table describing the encoding of the instructions used to access the register. The columns following this are described below:

- **Configuration**: If present, this column describes other configuration information (in addition to the Control fields) on which the accessibility depends. If the entry for the row is empty or the column is not present, there is no other configuration information.
- **Control**: Every accessibility row include one or more control fields, and the settings on which the accessibility depends. Each setting is a binary value, and the value may include the character 'x', which indicates that the bit at this position in the value can be either '0' or '1'.
- **Accessibility**: The Accessibility columns describe the permission at each Exception level for this row. Each value may be one of the following, or a reference to another register:
  - **n/a**: Access to the register at this Exception level is not possible. This is used in the following cases:
     - EL2 when NS==0. Secure EL2 does not exist!
     - EL1 when NS==1 and TGE==1. In this case all exceptions to EL1 are re-routed to EL2, so there is no execution at EL1.
  - **RW**: Read and write access permitted.
  - **RO**: Read access permitted, but no write access.
  - **WO**: Write access permitted, but no read access.
  - **-**: Access to the register is UNDEFINED.
  - When the cell is a reference to another register, this indicates that the attempt to access this register using the instruction will result in access to the referenced register.
- **Instance**: Where the register is a banked register, this row describes which banked register is accessed.

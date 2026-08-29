# KianV RV32IMA Linux SoC for Tiny Tapeout SKY130

This repository ports Hirosh Dabui's silicon-proven KianV RV32IMA uLinux SoC
to Wai Ming Lee's current TTSKY26c project. It is the Linux-capable companion
to the much smaller NanoV bare-metal project.

KianV executes RV32IMA code and contains the CSR/exception, CLINT timer,
external-memory, UART, SPI, and GPIO logic used by its uLinux system. The ASIC
boots from a QSPI flash and uses two external QSPI PSRAM devices. The required
memory hardware is available as the Tiny Tapeout QSPI Pmod.

## Honest size and cost boundary

Linux support does not fit the approximately USD-100 NanoV footprint. This
design declares an `8x2` footprint: 16 billable digital tiles. At the current
public price of EUR 70 per tile, silicon space is EUR 1,120; the demoboard,
QSPI Pmod, taxes, and shipping are additional.

The generated current-template die area is `1378.16 x 225.76` micrometres.

## Boot memory map

| Address / flash offset | Contents |
| --- | --- |
| CPU `0x20100000`, flash offset `0x100000` | Bootloader |
| Flash offset `0x180000` | Device tree (`kianv.dtb`) |
| Flash offset `0x200000` | Linux kernel plus root filesystem (`Image`) |
| CPU `0x80000000` | 16 MiB external PSRAM |

The upstream system-image build is maintained in
[`kianRiscV/asic/os/ulinux_asic_kianv_soc`](https://github.com/splinedrive/kianRiscV/tree/master/asic/os/ulinux_asic_kianv_soc).

## Verification

Run the included firmware regression:

```sh
python3 -m venv .venv
. .venv/bin/activate
pip install -r test/requirements.txt
cd test
make clean all
! grep failure results.xml
```

The regression boots from a simulated QSPI flash and checks the UART banner
and echo, the independent SPI peripheral, and GPIO sequencing. A passing RTL
test is only the first gate. Submission readiness additionally requires the
official TTSKY26c hardening, extracted-netlist gate-level test, setup and hold
timing, detailed-routing and Magic DRC, LVS, antenna, and precheck jobs to pass
on the exact proposed commit.

## Provenance

- Port baseline: [`splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC`](https://github.com/splinedrive/KianV-RV32IMA-RISC-V-uLinux-SoC)
- CPU/system source: [`splinedrive/kianRiscV`](https://github.com/splinedrive/kianRiscV)
- Tiny Tapeout silicon-proven project: [KianV uLinux SoC](https://tinytapeout.com/chips/ttgf0p2/tt_um_kianV_rv32ima_uLinux_SoC)

The original source and this port are licensed under the MIT License; retain
the copyright and attribution notices in [`LICENSE`](LICENSE).

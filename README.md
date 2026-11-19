# Mihomo Minimal Automated Build (GitHub Actions)

Automated GitHub Actions workflow to build minimal, resource-optimized releases of [MetaCubeX/mihomo](https://github.com/MetaCubeX/mihomo) tailored for embedded routers (OpenWrt, ASUSWRT, Padavan), single-board computers, macOS, and Windows.

## Features

- **Automated Upstream Sync (Cron)**: Runs every hour (`0 * * * *`) via GitHub Actions cron:
  - Fetches the latest **stable** release from `MetaCubeX/mihomo` (automatically filters out `Prerelease-Alpha` and drafts).
  - Checks if the release already exists in this repository.
  - If a new stable release is found, it automatically starts the compilation matrix, compresses the binaries, and creates a GitHub Release.
  - If no new version exists, the workflow exits in ~3 seconds without consuming build minutes.
- **Manual Trigger (`workflow_dispatch`)**: Build any specific release tag (e.g. `v1.19.34` or `latest`) on demand with a `force_build` toggle.
- **Cross-Platform Architecture Matrix**:
  - **Linux (Embedded & Servers)**:
    - `linux-arm64`
    - `linux-armv7` (Cortex-A7 / Cortex-A9 routers)
    - `linux-armv6`
    - `linux-armv5`
    - `linux-mipsle-softfloat` (MT7621, MT7628 OpenWrt)
    - `linux-mipsle-hardfloat`
    - `linux-mips-softfloat` (Atheros / Qualcomm QCA95xx)
    - `linux-mips-hardfloat`
    - `linux-amd64`
    - `linux-386`
    - `linux-riscv64`
    - `linux-loong64`
  - **macOS (Darwin)**:
    - `darwin-arm64` (Apple Silicon M-series)
    - `darwin-amd64` (Intel Mac)
  - **Windows**:
    - `windows-amd64` (64-bit x64)
    - `windows-arm64` (Surface Pro / Windows on ARM)
    - `windows-386` (32-bit x86)
- **Minimal Tags & Optimizations**:
  - Strips heavy mesh stacks: `no_easytier,no_zerotier,no_tailscale`
  - Strips gVisor userspace netstack (leaves lightweight `mips` & kernel `system` stack)
  - Halves TCP/UDP relay memory buffers: `with_low_memory`
  - Strips Hysteria fake-tcp raw sockets on Linux: `no_fake_tcp`
  - Full symbol and DWARF stripping: `-ldflags "-s -w -buildid="`
  - Optional UPX LZMA compression for flash storage constraints
- **Automated GitHub Releases**: Compiles all targets, packages `.tar.gz` (Linux/macOS) and `.zip` (Windows), generates `sha256sums.txt`, and publishes a GitHub release.

## Repository Setup

1. Enable Workflow Read & Write Permissions:
   - Go to **Settings** > **Actions** > **General** > **Workflow permissions**.
   - Select **Read and write permissions**.
   - Check **Allow GitHub Actions to create and approve pull requests**.
   - Click **Save**.
2. The hourly cron will automatically detect and build every new stable release upstream. You can also trigger it manually from **Actions** > **Build Minimal Mihomo** > **Run workflow**.

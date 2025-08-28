
# inet Command (Network Interface Info)

This repo provides a custom shell function `inet` that shows your system’s network interfaces, IP addresses, broadcast, subnet mask, and CIDR — with **colored and aligned output** for readability.

## 📌 Features
- Lists **IPv4 addresses** of all interfaces
- Shows **broadcast address**, **subnet mask**, and **CIDR**
- **Color-coded** output:
  - Red → IP Address
  - Blue → Broadcast
  - Green → Subnet Mask
- Aligned columns (no more messy spacing!)

---

## ⚙️ Setup Instructions

### 1. Clone this repo
```bash
git clone https://github.com/ShriHax-21/inet.git
cd inet
````

### 2. Add the function to your shell config

Append the function to your `~/.zshrc` (if using Zsh) or `~/.bashrc` (if using Bash).

```bash
vi ~/.zshrc
```

Paste the following command in inet file

Save and exit (`esc`, `:wq` in vi).

---

### 3. Reload your shell

```bash
source ~/.zshrc
```

---

### 4. Run the command

```bash
inet
```

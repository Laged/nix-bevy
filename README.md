# Setup Guide for NixOS WSL and Bevy Development

## Windows TUNK
### 1. Download Windows Terminal
[Download Windows Terminal](https://www.microsoft.com/store/productId/9N0DX20HK701?ocid=pdpshare)

### 2. Download Latest NixOS WSL
[Download NixOS WSL](https://github.com/nix-community/NixOS-WSL/releases/download/2405.5.4/nixos-wsl.tar.gz)

### 3. Enable WSL2 on Windows for NixOS-WSL
(Make sure BIOS virtualization is enabled). Follow these steps:
[NixOS-WSL](https://github.com/nix-community/NixOS-WSL)
A. Open Windows Terminal as Administrator.
B. Run the following commands with proper file paths:
```
cd ~/Downloads
wsl --install --no-distribution
wsl --import NixOS $env:USERPROFILE\NixOS\ nixos-wsl.tar.gz
wsl -d NixOS
```

### 4. Copy backup of initial NixOS configuration before full YOLO
```
sudo cp /etc/nixos/configuration.nix /etc/nixos/configuration.nix.bk
```

### 4. YOLO-customize NixOS for NixOS-WSL<>VSCode<>Rust<>Bevy setup
```
sudo sed -i '$ s/}/  environment.systemPackages = [ pkgs.wget ];\n  programs.git.enable = true;\n  programs.nix-ld = {\n    enable = true;\n    package = pkgs.nix-ld-rs;\n  };\n  nix.settings.experimental-features = ["nix-command" "flakes"];\n}/' /etc/nixos/configuration.nix
```

### 6. Boot into the YOLO-setup (hopefully)
```
sudo nixos-rebuild switch
```

### 7. Initialize rust-bevy from [Nix flake template](https://blog.graysonhead.net/posts/nix-flake-rust-bevy/)
```
cd
mkdir rust-bevy
nix flake new --template github:graysonhead/nix-templates#rust-bevy ./rust-bevy
cd rust-bevy
```

### 8. Code integration should work out of the box if vscode has WSL extension installed (check popup)
```
code .
```

### 9. Project should build outside nix shell via nix run
```
nix run .
```

### 10. Or via nix-shell and cargo (see flake template for build time optimizations)
```
nix develop
cargo run
```

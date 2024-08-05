# Setup Guide for NixOS WSL and Bevy Development
![image](https://github.com/user-attachments/assets/bfdd938b-d413-4ba0-8993-38ab855de53d)

## Windows TUNK
### 1. Download Windows Terminal & NixOS WSL
[Download Windows Terminal](https://www.microsoft.com/store/productId/9N0DX20HK701?ocid=pdpshare)
[Download NixOS WSL](https://github.com/nix-community/NixOS-WSL/releases/download/2405.5.4/nixos-wsl.tar.gz)

### 3. Enable WSL2 on Windows for NixOS-WSL
(Make sure BIOS virtualization is enabled). Follow these steps in Windows Terminal (with proper file paths):
[NixOS-WSL](https://github.com/nix-community/NixOS-WSL)
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
code flake.nix
```

### 9. Use your freshly set-up editor to fix a WSL issue - edit flake.nix and include libxkbcommon as nativeBuildInput to row 25, it should be like this
```
...
        buildInputs = with pkgs; [
          vulkan-loader
          xorg.libXcursor
          xorg.libXi
          xorg.libXrandr
          alsa-lib
          udev
          pkg-config
          # Add this t. Matti
          libxkbcommon
        ];
...
```

### 10. Project should build "fast" inside nix-shell and cargo (see flake template for build time optimizations)
```
nix develop
cargo run
```

# syntax=docker.io/docker/dockerfile:1.19
# shellcheck shell=bash

# see https://github.com/rust-lang/rustup/tags/
# renovate: datasource=github-releases depName=rust-lang/rustup
ARG RUSTUP_INIT_VERSION='1.28.2'

# see https://github.com/cargo-bins/cargo-binstall/releases
# renovate: datasource=github-releases depName=cargo-bins/cargo-binstall
ARG CARGO_BINSTALL_VERSION='1.15.9'

# see https://github.com/esp-rs/espup/releases/
# renovate: datasource=github-releases depName=esp-rs/espup
ARG ESPUP_VERSION='0.16.0'

# see https://github.com/rust-lang/rust/releases
# NB to make things simpler. keep this in sync with ESP_RUST_VERSION bellow.
# renovate: datasource=github-tags depName=rust-lang/rust
ARG RUST_VERSION='1.90.0'

# see https://github.com/esp-rs/rust-build/releases
# renovate: datasource=github-releases depName=esp-rs/rust-build
ARG ESP_RUST_VERSION='1.90.0.0'

# see https://crates.io/crates/espflash
# see https://github.com/esp-rs/espflash/releases
# renovate: datasource=github-releases depName=esp-rs/espflash
ARG ESPFLASH_VERSION='4.2.0'

# see https://crates.io/crates/esp-generate
# see https://github.com/esp-rs/esp-generate/releases
# renovate: datasource=github-releases depName=esp-rs/esp-generate
ARG ESP_GENERATE_VERSION='1.0.0'

# see https://github.com/devcontainers/images/tree/main/src/base-debian/history
FROM mcr.microsoft.com/devcontainers/base:2.0.2-trixie

RUN <<'EOF'
#!/usr/bin/bash
set -euxo pipefail
export DEBIAN_FRONTEND=noninteractive
apt-get update
apt-get -y install --no-install-recommends \
  bash-completion \
  curl \
  sudo \
  unzip \
  wget
apt-get clean
rm -rf /var/lib/apt/lists/*
EOF

USER vscode

# install rustup.
# see https://github.com/rust-lang/rustup/tags.
# see https://rust-lang.github.io/rustup/installation/other.html#manual-installation
# see https://github.com/rgl/my-ubuntu-ansible-playbooks/blob/main/roles/rust/files/install-rust.sh
ARG RUSTUP_INIT_VERSION
RUN <<'EOF'
#!/usr/bin/bash
set -euxo pipefail
u="https://static.rust-lang.org/rustup/archive/${RUSTUP_INIT_VERSION}/x86_64-unknown-linux-gnu/rustup-init"
t="$(mktemp -q -d)"
wget -qO "$t/rustup-init" "$u"
chmod +x "$t/rustup-init"
"$t/rustup-init" \
  -y \
  --no-update-default-toolchain
rm -rf "$t"
EOF
ENV PATH="$PATH:/home/vscode/.cargo/bin"

# install rust.
ARG RUST_VERSION
RUN rustup default "$RUST_VERSION"

# install cargo-binstall.
# see https://github.com/cargo-bins/cargo-binstall.
ARG CARGO_BINSTALL_VERSION
RUN <<'EOF'
#!/usr/bin/bash
set -euxo pipefail
u="https://github.com/cargo-bins/cargo-binstall/releases/download/v${CARGO_BINSTALL_VERSION}/cargo-binstall-x86_64-unknown-linux-gnu.tgz"
t="$(mktemp -q -d)"
wget -qO- "$u" | tar xzf - -C "$t"
install "$t/cargo-binstall" ~/.cargo/bin
rm -rf "$t"
EOF

# install espup.
ARG ESPUP_VERSION
RUN cargo binstall "espup@$ESPUP_VERSION"

# install esp rust.
ARG ESP_RUST_VERSION
RUN <<'EOF'
#!/usr/bin/bash
set -euxo pipefail
espup install \
  --toolchain-version "$ESP_RUST_VERSION" \
  --targets esp32
echo 'source "$HOME/export-esp.sh"' >>~/.bashrc
EOF

# install espflash.
ARG ESPFLASH_VERSION
RUN cargo binstall "espflash@$ESPFLASH_VERSION"

# install esp-generate.
ARG ESP_GENERATE_VERSION
RUN cargo binstall "esp-generate@$ESP_GENERATE_VERSION"

USER root

RUN <<'EOF'
#!/usr/bin/bash
set -euxo pipefail
# ensure /etc/profile is called at the top of the file, when running in a
# login shell.
sed -i '0,/esac/s/esac/&\n\nsource \/etc\/profile/' /home/vscode/.bashrc
EOF
COPY .devcontainer/inputrc /etc/inputrc
COPY .devcontainer/login.sh /etc/profile.d/login.sh

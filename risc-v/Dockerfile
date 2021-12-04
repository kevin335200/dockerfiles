FROM debian:sid

# Expose code-server port
EXPOSE 8080

# Install all needed packages
RUN apt-get update && \
apt-get install -y --no-install-recommends ca-certificates git wget curl build-essential ninja-build libglib2.0-dev libpixman-1-dev u-boot-qemu unzip && \
# clean up the temp files
apt-get autoremove -y && \
apt-get clean && \
rm -rf /var/lib/apt/lists/*

# Download QEMU
WORKDIR "/root"
RUN git clone https://github.com/qemu/qemu 

# Compile QEMU for riscv32 and riscv64 target

# Get CPU Cores
RUN export NUM_CORES="$(cat /proc/cpuinfo | grep '^processor' | wc -l)" && \
# Build and Install QEMU
mkdir /root/qemu/build  && cd /root/qemu/build && \
../configure --target-list=riscv32-softmmu,riscv64-softmmu && make -j $NUM_CORES && make install && \
# clean up the git repo and build artifacts after installed
rm /root/qemu -r

# Install RISC-V Toolchain

# Create Folder
RUN mkdir -p ~/usys/riscv && cd ~/usys/riscv && \
# Download Toolchain
curl -fsSL https://static.dev.sifive.com/dev-tools/freedom-tools/v2020.12/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz | tar -xz && curl -fsSL https://static.dev.sifive.com/dev-tools/freedom-tools/v2020.12/riscv-openocd-0.10.0-2020.12.1-x86_64-linux-ubuntu14.tar.gz | tar -xz&& \
# Extract Archives
# tar -xf riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz && tar -xf riscv-openocd-0.10.0-2020.12.1-x86_64-linux-ubuntu14.tar.gz && \
# Delete Archives
# rm riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14.tar.gz && rm riscv-openocd-0.10.0-2020.12.1-x86_64-linux-ubuntu14.tar.gz && \
# Set Env
export RISCV_OPENOCD_PATH="$HOME/usys/riscv/riscv-openocd-0.10.0-2020.12.1-x86_64-linux-ubuntu14" && \
export RISCV_PATH="$HOME/usys/riscv/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14" && \
# Add PATH
export PATH="$HOME/usys/riscv/riscv64-unknown-elf-toolchain-10.2.0-2020.12.8-x86_64-linux-ubuntu14/bin:$PATH"

# Install code-server

RUN curl -fsSL https://code-server.dev/install.sh | sh

# Open code-server

CMD code-server --auth none --bind-addr 0.0.0.0:8080

FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# System dependencies for Tauri + general tooling
RUN apt-get update && apt-get install -y \
    build-essential \
    curl \
    wget \
    file \
    git \
    libssl-dev \
    libwebkit2gtk-4.1-dev \
    libxdo-dev \
    libayatana-appindicator3-dev \
    librsvg2-dev \
    ca-certificates \
    gnupg \
    locales \
    libgtk-3-dev \
    xauth \
    x11-utils \
    && rm -rf /var/lib/apt/lists/*

# Generate locale to fix Gtk locale warning
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && locale-gen
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# Node.js 22
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
    && apt-get install -y nodejs \
    && rm -rf /var/lib/apt/lists/*

# Rust (required by Tauri)
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
ENV PATH="/root/.cargo/bin:${PATH}"

# Java (required by Android SDK)
RUN apt-get update && apt-get install -y openjdk-17-jdk-headless unzip && rm -rf /var/lib/apt/lists/*
ENV JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

# Android SDK & NDK
ENV ANDROID_HOME=/opt/android-sdk
ENV NDK_HOME=${ANDROID_HOME}/ndk/27.2.12479018
ENV PATH="${ANDROID_HOME}/cmdline-tools/latest/bin:${ANDROID_HOME}/platform-tools:${PATH}"

RUN mkdir -p ${ANDROID_HOME}/cmdline-tools \
    && cd /tmp \
    && curl -fsSL https://dl.google.com/android/repository/commandlinetools-linux-11076708_latest.zip -o cmdline-tools.zip \
    && unzip -q cmdline-tools.zip \
    && mv cmdline-tools ${ANDROID_HOME}/cmdline-tools/latest \
    && rm cmdline-tools.zip

RUN yes | sdkmanager --licenses > /dev/null 2>&1 \
    && sdkmanager \
        "platform-tools" \
        "platforms;android-35" \
        "build-tools;35.0.1" \
        "ndk;27.2.12479018"

# Rust Android cross-compilation targets
RUN rustup target add aarch64-linux-android armv7-linux-androideabi i686-linux-android x86_64-linux-android

# Claude Code CLI
RUN npm install -g @anthropic-ai/claude-code bun

RUN curl -LsSf https://astral.sh/uv/install.sh | sh
ENV PATH="/root/.local/bin:${PATH}"
RUN uv tool install specify-cli --from git+https://github.com/github/spec-kit.git && \
    git config --global --add safe.directory /workspace

WORKDIR /workspace

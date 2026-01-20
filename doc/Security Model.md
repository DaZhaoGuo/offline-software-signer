# Security Model

This project implements a **software-based isolated offline signing architecture**.
It is designed to reduce the attack surface of private keys by separating **transaction construction (online)** from **transaction signing (offline)**.

> **Important**: This system does **NOT** claim hardware-grade security.

---

## Security Assumptions

The security of this system relies on the following assumptions:

### 1. Signer Device Integrity

- The operating system is not compromised (no root / jailbreak).
- The application binary has not been tampered with.
- No malicious runtime instrumentation is present.

### 2. Network Isolation

- The Signer application contains **no network-related code paths or permissions** (e.g., no Internet permission, no networking libraries).
- The Signer may be installed on a **network-connected device**, but remains **logically network-isolated at the application level**.
- Transaction requests are delivered to the Signer exclusively via **controlled data exchange channels**, including:
  - Local inter-process communication (IPC) or OS-mediated app invocation on the same device.
  - **Out-of-band channels** such as QR codes or Bluetooth when deployed on a separate device.
- The Signer **never initiates or accepts network connections at any point in its lifecycle** (foreground, background, or during signing), as it contains **no networking code paths and requests no network-related permissions**.
- Users are responsible for ensuring that no unauthorized system-level components intercept, inject, or tamper with IPC, Bluetooth, or camera-based data exchange.

### 3. User Verification

- Users carefully verify transaction details displayed in the **Signer UI**.
- Users do not blindly approve signing requests.

### 4. Key Encryption Password Confidentiality

- The password used to **decrypt and unlock the private key within the Signer** remains confidential.
- This password is used solely for **private key protection and signing authorization**, not for payment confirmation.
- The password is not reused across other applications or services.

---

## Security Guarantees

Under the assumptions above, this architecture provides:

- Private keys are never exposed to online applications.
- Online compromise of the **dApp** cannot directly steal private keys.
- Each transaction requires explicit user authorization within the isolated Signer.
- Private key material exists in plaintext only transiently in memory during signing.

---

## Security Limitations

This system does **NOT** protect against:

### 1. Compromised Offline Environment

- Rooted or jailbroken devices.
- Malicious OS, firmware, or bootloader.
- Runtime memory inspection or code injection.

### 2. Supply Chain Attacks

- Malicious app updates.
- Compromised build or distribution pipelines.

### 3. Advanced Physical Attacks

- Memory dumping via debugging interfaces.
- Cold boot attacks.

### 4. User Negligence

- Approving malicious or misleading transactions.
- Weak or reused passwords.

---

## Hardware Wallet Threat Model

This system does **not** provide:

- Secure elements (SE)
- Trusted execution environments (TEE)
- Tamper-resistant hardware

It should **not** be considered equivalent to a hardware wallet.

---

## Recommended Usage

This architecture is suitable for:

- Educational demonstrations of offline signing.
- Developer tooling and testing environments.
- Risk reduction for moderate-value funds.
- Controlled enterprise signing workflows.

For **high-value asset protection**, a **certified hardware wallet** is strongly recommended.

---

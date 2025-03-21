# Difference Between TCP and UDP

This document provides a short comparison of **TCP** (Transmission Control Protocol) and **UDP** (User Datagram Protocol), two core protocols used for network communication in iOS apps and beyond.

## 1. Overview

- **TCP**:
  - **Connection-Oriented**: Establishes a connection (handshake) before data transfer.
  - **Reliable**: Ensures data is delivered in order, without loss or duplication, using acknowledgments and retransmissions.
  - **Slower**: Due to overhead from reliability mechanisms.
  - **Use Case**: Ideal for applications requiring accuracy, e.g., HTTP/HTTPS (web browsing), email, file transfers.

- **UDP**:
  - **Connectionless**: Sends data without establishing a connection.
  - **Unreliable**: Does not guarantee delivery, order, or error-free data; packets may be lost or arrive out of order.
  - **Faster**: Minimal overhead, making it more efficient for speed.
  - **Use Case**: Ideal for real-time applications where speed matters more than reliability, e.g., video streaming, gaming, VoIP.

## 2. Practical Implications for iOS

- **TCP in iOS**: Use TCP for iOS apps that need reliable data transfer, like fetching API data with `URLSession` (which uses HTTP over TCP by default).
- **UDP in iOS**: Use UDP for real-time apps, like multiplayer games or live video streaming, where you might use libraries like `Network.framework` to send UDP packets.

## 3. Summary

- **TCP**: Reliable, ordered, slower—best for accuracy (e.g., web requests).
- **UDP**: Unreliable, unordered, faster—best for speed (e.g., real-time streaming).

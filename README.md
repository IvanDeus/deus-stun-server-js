# deus-stun-server-js - Node.js STUN Server
A lightweight STUN (Session Traversal Utilities for NAT) server implemented in Node.js. This server handles STUN Binding Requests and responds with the client's public IP and port, facilitating NAT traversal for real-time communication (RTC) applications like WebRTC. 

## Features

- Handles STUN Binding Requests and Responses (RFC 8489).
- Returns client's public IP and port via XOR-MAPPED-ADDRESS attribute.
- Rate limiting: Limits to 30 requests per 10 seconds, with a 3-second pause if exceeded.
- Logs requests, responses, and rate-limiting events with timestamps.
- Configurable binding IP (0.0.0.0) and port (3478).

## Prerequisites

- Node.js: Version 22 or higher.
- PM2: Process manager for running the server (optional but recommended).
- Git: For cloning the repository.
- A system with Internet access (for testing with STUN clients all over the world).

## Installation

Clone the repository:
```
git clone <repo-name>
cd <repo-name>
```
Note: The server uses only Node.js built-in modules (dgram), so no additional packages are required unless using PM2.

## Running the Server
### Option 1: Using Node.js
Run the server directly with Node.js:
`node stun.js`

The server will start listening on 0.0.0.0:3478 and log events with timestamps.
### Option 2: Using PM2
To run the server as a managed process with PM2 for better reliability and monitoring:

Start the server with PM2:
`pm2 start stun.js --name stun-server`

Monitor the server: `pm2 monit`

View logs: `pm2 logs stun-server`

Stop or restart the server:
```
pm2 stop stun-server
pm2 restart stun-server
```
Save the process list to auto-start on system reboot:
```
pm2 save
pm2 startup
```
## Configuration
The server uses two constants defined in stun-server.js:
```
BIND_IP: 0.0.0.0 (listens on all interfaces).
BIND_PORT: 3478 (standard STUN port).
```
To change these, edit the constants at the top of stun-server.js.

Rate limiting parameters:
```
MAX_REQUESTS: 30 requests.
TIME_WINDOW: 10 seconds.
PAUSE_DURATION: 3 seconds pause if limit exceeded.
```
Modify these constants in the code if needed.

## Testing

Use this public STUN client to test the server:
https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/

To test rate limiting, send more than 30 requests in 10 seconds (e.g., via a script or multiple clients). The server will pause for 3 seconds and log dropped requests.

Example log output:
```
[10/5/2025, 8:17:00 AM] STUN server listening on 0.0.0.0:3478
[10/5/2025, 8:17:05 AM] Handled Binding Request from 192.168.1.100:50000
[10/5/2025, 8:17:10 AM] Rate limit exceeded (31 requests in 10s). Pausing for 3s.
[10/5/2025, 8:17:10 AM] Dropping request from 192.168.1.100:50001 due to rate limit or pause.
[10/5/2025, 8:17:13 AM] Resuming after pause.
```
## Limitations

IPv4 Only: The server currently supports IPv4 addresses. For IPv6, extend the createXorMappedAddress function.
No Authentication: Lacks MESSAGE-INTEGRITY or USERNAME attributes, making it unsuitable for production without additional security.
Global Rate Limiting: Limits apply to all clients combined. Per-client limiting requires further modification.
Single-Threaded: Node.js’s single-threaded nature may limit performance under high load.

## Troubleshooting

Server not responding: Ensure the server is running (pm2 status or ps aux | grep node) and the port (3478) is not blocked by a firewall.
Rate limit triggered: If testing locally, reduce the number of requests or adjust MAX_REQUESTS and TIME_WINDOW in the code.
Errors in logs: Check PM2 logs (pm2 logs stun-server) or console output for details.

## Contributing
Contributions are welcome! Please:

Fork the repository.
Create a feature branch (git checkout -b feature/your-feature).
Commit changes (git commit -m 'Add your feature').
Push to the branch (git push origin feature/your-feature).
Open a pull request.

2025 [ivan deus]

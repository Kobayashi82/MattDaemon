<div align="center">

![System & Kernel](https://img.shields.io/badge/System-brown?style=for-the-badge)
![Network Communication](https://img.shields.io/badge/Network-Communication-blue?style=for-the-badge)
![Daemon Process](https://img.shields.io/badge/Daemon-Process-green?style=for-the-badge)
![C++ Language](https://img.shields.io/badge/Language-C++-red?style=for-the-badge)

*A communication daemon with remote shell capabilities*

</div>

<div align="center">
  <img src="/MattDaemon.png">
</div>

# Matt Daemon

[README en Español](README_es.md)

`Matt Daemon` is a `42 School` project that implements a complete network communication daemon. This daemon runs as a background service that listens on a specific port, logs all activity to log files, and provides advanced secure communication capabilities. The project includes both a remote shell client and a GUI client for log management.

## ✨ Features

- `Real Daemon`: Process that runs in the background without user interaction
- `Network Communication`: Secure network communication with encryption
- `Logging System`: Full log management with automatic rotation
- `Instance Control`: Allows only one instance running at a time
- `Signal Management`: Intercepts and handles system signals properly
- `Timeout Control`: Inactive connection management
- `Multi-Client`: Allows simultaneous connections (default: 3)
- `Interactive Shell`: Full remote shell access (Ben_AFK)
- `GUI Client`: GTK interface for viewing and sending logs (Casey_AFK)

## 🔧 Installation

```bash
git clone https://github.com/Kobayashi82/MattDaemon.git
cd MattDaemon
make

# Binaries generated in the bin/ folder:
# MattDaemon    - Main daemon
# Ben_AFK       - Remote shell client
# Casey_AFK     - GUI log client

# Dependencies for Casey_AFK
sudo apt-get install libgtk-3-dev
```

## 🖥️ Usage

### Running the Daemon

#### Available options:

- `-k, --disable-encryption`: Disable encryption for Ben_AFK clients
- `-s, --disable-shell`: Disable remote shell access
- `-c, --max-clients=NUM`: Maximum number of clients (default: 3, unlimited = 0)
- `-p, --port=PORT`: Listening port (default: 4242)
- `-t, --timeout=SECOND`: Timeout for inactive connections in seconds (default: 600)
- `-f, --log-file=PATH`: Log file path
- `-l, --log-level=LEVEL`: Logging level (DEBUG, INFO, LOG, WARNING, ERROR, CRITICAL)
- `-n, --log-new`: Create a new log file on start
- `-m, --log-rotate-max=NUM`: Maximum number of log files in rotation (default: 5)
- `-r, --log-rotate-size=BYTE`: Minimum size for log rotation (default: 10MB)
- `-x, --shell-path=PATH`: Shell path to execute
- `-h, --help`: Show help
- `-u, --usage`: Show syntax
- `-V, --version`: Show version

#### Basic usage:

```bash
# Run with default configuration
sudo ./MattDaemon

# Run on a custom port
sudo ./MattDaemon --port 1234

# Run with max 5 clients
sudo ./MattDaemon --max-clients 3

# Disable encryption
sudo ./MattDaemon --disable-encryption

# Disable remote shell
sudo ./MattDaemon --disable-shell
```

#### Advanced options:

```bash
# Configure log file and logging level
sudo ./MattDaemon --log-file /var/log/my_daemon.log --log-level DEBUG

# Configure log rotation
sudo ./MattDaemon --log-rotate-max 10 --log-rotate-size 52428800

# Use a custom shell
sudo ./MattDaemon --shell-path /bin/fish

# Configure connection timeout (in seconds)
sudo ./MattDaemon --timeout 1800
```

#### Check status:

```bash
# Verify it is running
ps aux | grep MattDaemon
sudo ls -la /var/lock/ | grep matt

# View logs in real time
sudo tail -f /var/log/matt_daemon/matt_daemon.log
```

#### Basic connection

```bash
# Simple connection with netcat
nc localhost 4242

# Send messages (saved to logs)
Hello World
Test message

# Terminate daemon
quit
```

### Running Ben_AFK (Remote Shell)

#### Available options:

- `-k, --insecure`: Allow unencrypted communication
- `-l, --login=USERNAME`: Specify username
- `-p, --port=PORT`: Connection port (default: 4242)
- `-h, --help`: Show help
- `-u, --usage`: Show syntax
- `-V, --version`: Show version

#### Basic usage:

```bash
# Basic connection (current user, default port)
./Ben_AFK localhost

# Specify user
./Ben_AFK --login admin localhost

# User in user@host format
./Ben_AFK admin@localhost

# Custom port
./Ben_AFK --port 1234 localhost

# Allow unencrypted communication
./Ben_AFK --insecure localhost

# Combining options
./Ben_AFK --login admin --port 1234 --insecure localhost
```

### Running Casey_AFK (GUI Client)

#### Features:

- `Graphical Interface`: GTK client for visual log management
- `Intuitive Connection`: Simple interface to connect to the daemon
- `Log Visualization`: Receives and displays the latest server logs
- `Message Sending`: Allows sending custom logs to the daemon
- `Remote Control`: Ability to shut down the daemon from the interface

#### Basic usage:

```bash
# Run the GUI client
./Casey_AFK

# The program will open a window with fields for:
# - Host:    server address (e.g., localhost)
# - Port:    connection port (default: 4242)
# - User:    username for identification
```

#### Interface features:

- `Connect`: Establishes connection to the daemon and receives recent logs
- `Message Field`: Text area to write custom messages
- `Send Log`: Button to send the message to the daemon as a log entry
- `Close Server`: Button to send a shutdown command to the daemon
- `Disconnect`: Ends the connection with the daemon
- `Logs Area`: Window that displays logs received from the server

#### Typical workflow:

```bash
1. Run the daemon: sudo ./MattDaemon
2. Open Casey_AFK: ./Casey_AFK
3. Enter connection data (localhost, 4242, your_user)
4. Click "Connect"
5. See existing logs in the display area
6. Send messages using the text field
7. Close the server remotely if needed
```

## 🧪 Testing

### Basic tests

```bash
# Single-instance test
sudo ./MattDaemon
sudo ./MattDaemon  # Should show a locked-file error

# Client limit test (default 3)
nc localhost 4242 &
nc localhost 4242 &
nc localhost 4242 &
nc localhost 4242   # The fourth should be rejected

# Test with custom limit
sudo ./MattDaemon --max-clients 1
nc localhost 4242 &
nc localhost 4242   # The second should be rejected
```

### Signal tests

```bash
# Run daemon
sudo ./MattDaemon && PID=$(pgrep MattDaemon)
sudo kill -15 $PID  # SIGTERM

# Check logs
sudo tail /var/log/matt_daemon/matt_daemon.log

# Repeat with different signals
sudo kill -2 $PID   # SIGINT
sudo kill -1 $PID   # SIGHUP
```

### Logging tests

```bash
# Log rotation test
sudo ./MattDaemon --log-rotate-size 10 --log-rotate-max 3
nc localhost 4242
# Send messages...

# Log levels test
sudo ./MattDaemon --log-level DEBUG
sudo ./MattDaemon --log-level LOG

# Custom log file test
sudo ./MattDaemon --log-file /tmp/test_daemon.log
```

### Ben_AFK tests (Remote Shell)

```bash
# Basic connection test
./Ben_AFK localhost

# Test with specific user
./Ben_AFK --login user localhost

# Test with custom port
sudo ./MattDaemon --port 1234
./Ben_AFK --port 1234 localhost

# Test unencrypted communication
sudo ./MattDaemon --disable-encryption
./Ben_AFK --insecure localhost
```

### Casey_AFK tests (GUI Client)

```bash
# GUI interface test
./Casey_AFK
# Check that the window opens correctly

# Basic connection test
sudo ./MattDaemon --log-level DEBUG
./Casey_AFK
# Connect using localhost:4242

# Log sending test
# 1. Connect with Casey_AFK
# 2. Write a message in the text field
# 3. Click "Send Log"
# 4. Check the daemon logs

# Log visualization test
# 1. Send several messages with nc or Ben_AFK
# 2. Connect with Casey_AFK
# 3. Verify logs appear in the interface

# Remote shutdown test
./Casey_AFK
# 1. Connect to the daemon
# 2. Click "Close Server"
# 3. Verify the daemon stops correctly

# Test with custom port
sudo ./MattDaemon --port 1234
./Casey_AFK
# Change port to 1234 in the interface and connect

# Error handling test
./Casey_AFK
# Try connecting without the daemon running
# Verify it shows an appropriate error
```

## 📝 Log examples

```
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Initiating
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: First fork() completed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: setsid() completed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Second fork() completed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: All signal handlers successfully installed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: umask() set
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Working directory changed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Standard file descriptors closed
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Lock set
[18/08/2025-20:49:54]      [ INFO ]   Daemon: Started
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Epoll initialized
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Socket created
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Socket reusable option set
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Socket bind set
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Socket listen set
[18/08/2025-20:49:54]      [ DEBU ]   Daemon: Socket added to Epoll
[18/08/2025-20:49:54]      [ INFO ]   Daemon: Listening on port 4242
[18/08/2025-20:50:11]      [ INFO ]   Client: [127.0.0.1:49206] connected
[18/08/2025-20:50:11]      [ DEBU ]   Client: [127.0.0.1:44438] added to Epoll
[18/08/2025-20:50:11]      [ INFO ]   Client: [127.0.0.1:49206] wants to open a shell
[18/08/2025-20:50:14]      [ WARN ]   Client: [127.0.0.1:49206] authorization failed for user: vzurera
[18/08/2025-20:50:21]      [ INFO ]   Client: [127.0.0.1:49206] authorization successful for user: vzurera
[18/08/2025-20:50:21]      [ DEBU ]   Client: [127.0.0.1:49206] terminal size: 114x28
[18/08/2025-20:50:21]      [ INFO ]   Client: [127.0.0.1:49206] shell started with PID 48675 and PTY: /dev/pts/6
[18/08/2025-20:50:25]      [ DEBU ]   Client: [127.0.0.1:49206] shell process 48675 terminated
[18/08/2025-20:50:25]      [ DEBU ]   Client: [127.0.0.1:49206] scheduled for deferred removal
[18/08/2025-20:50:25]      [ INFO ]   Client: [127.0.0.1:49206] disconnected
[18/08/2025-20:50:31]      [ INFO ]   Client: [127.0.0.1:44438] connected
[18/08/2025-20:50:31]      [ DEBU ]   Client: [127.0.0.1:44438] added to Epoll
[18/08/2025-20:50:36]      [ LOGG ]   Kobayashi: Mensaje de prueba
[18/08/2025-20:50:38]      [ WARN ]   Client: [127.0.0.1:44438] wants to close the daemon
[18/08/2025-20:50:38]      [ DEBU ]   Daemon: Socket close
[18/08/2025-20:50:38]      [ INFO ]   Daemon: Closed
```

## 🏗️ Technical Architecture

### Daemon structure
- `Fork`: Double child process creation to guarantee terminal independence
- `Chdir`: Change to the system root directory
- `Flock`: File lock for single-instance control
- `Signal`: System signal handling (SIGINT, SIGTERM, SIGHUP, SIGQUIT, SIGPIPE, SIGSEV, SIGCHLD)

### Network communication
- `Port`: 4242 (configurable)
- `Protocol`: TCP/IP
- `Connections`: Maximum simultaneous connections (configurable)
- `Timeout`: Inactive connection control (configurable)

### Encryption system
- `Encryption`: XOR cipher with repeated key
- `Secure Client`: Ben_AFK supports encrypted communication
- `Negotiation`: Automatic between client and server

### Logging system
- `Levels`: DEBUG, INFO, LOG, WARNING, ERROR, CRITICAL
- `Rotation`: Automatic based on size and number of files
- `Location`: Configurable (default: /var/log/matt_daemon/matt_daemon.log)

### Casey_AFK GUI client
- `Framework`: GTK 3
- `Features`: Log visualization, message sending, remote control
- `Compatibility`: Linux with a graphical environment

### Common errors
- `Insufficient permissions`: The daemon requires root permissions
- `Port in use`: Verify the specified port is free
- `Locked file`: Only one instance can run
- `Unknown host`: Verify the hostname/IP is valid (Ben_AFK/Casey_AFK)
- `GTK dependencies`: Casey_AFK requires GTK libraries installed
- `X server unavailable`: Casey_AFK needs a graphical environment

## 📄 License

This project is licensed under the WTFPL – [Do What the Fuck You Want to Public License](http://www.wtfpl.net/about/).

---

<div align="center">

**👹 Developed as part of the 42 School curriculum 👹**

"*Because background processes need style too... and a GUI"*

</div>

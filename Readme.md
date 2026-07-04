# nc like tool implemented in python

A lightweight **Python implementation of Netcat (nc)** that supports:
- Interactive remote command shell
- File uploads
- File upload + command shell in a single connection
- Cross-platform Python implementation
- Simple command-line interface using `argparse`

> **Disclaimer**
> This project is intended for **learning, experimentation, and use on systems you own or have explicit permission to test.** Do not use it for unauthorized access or activities that violate laws or policies.

---
# Features
- Interactive TCP client/server
- Remote shell execution
- File transfer over TCP
- Upload a file and automatically switch to an interactive shell
- Progress display during file uploads
- Colored terminal output for better readability
- Minimal dependencies (Python standard library only)
---
# Requirements

- Python **3.8+**
- No external packages required

---

# Installation

```bash
git clone https://github.com/yourusername/python-netcat.git
cd python-netcat
```

---

# Usage

```bash
python nc.py [OPTIONS]
```

## Arguments

| Option            | Description                            |
| ----------------- | -------------------------------------- |
| `-t`, `--target`  | Target IP address (default: `0.0.0.0`) |
| `-p`, `--port`    | Port number (default: `5555`)          |
| `-l`, `--listen`  | Listen for incoming connections        |
| `-c`, `--command` | Start a remote command shell           |
| `-u`, `--upload`  | Upload a file                          |

---

# Examples

## Start a Shell

### Listener

```bash
python nc.py -t 192.168.1.108 -p 5555 -lc
```

### Client

```bash
python nc.py -t 192.168.1.108 -p 5555 -c
```

Once connected, commands entered on the client side are executed on the listener.

Example:

```text
> whoami
user

> pwd
/home/user
```

---

# Upload a File

### Receiver

```bash
python nc.py -t 192.168.1.108 -p 5555 -lu
```

### Sender

```bash
python nc.py -t 192.168.1.108 -p 5555 -u
```

The sender will be prompted for the file path:

```text
Path:
/home/user/file.txt
```

The receiver automatically saves the file using its original filename.

---

# Upload a File Then Start a Shell

### Listener

```bash
python nc.py -t 192.168.1.108 -p 5555 -luc
```

### Client

```bash
python nc.py -t 192.168.1.108 -p 5555 -uc
```

Workflow:

1. Upload file
2. Transfer completes
3. Interactive shell starts

---

# File Uplaod (how the files is benig transfered)

- Sender side
	- The sender first builds a **fixed header** containing file metadata:
	  1. Filename length (4 bytes)
	  2. Filename
	  3. File size (8 bytes)
	- This header is sent to the receiver before any file data

- Receiver side
	- The receiver reads and parses the header to understand:
    1. What the file is called
    2. How large the file is
    3. How many bytes to expect

- Using this information, the receiver prepares to safely receive the file
- The sender then streams the actual file contents in **4096-byte chunks**
- The receiver continuously reads data until the number of received bytes matches the file size from the header
- Once all bytes are received, the file transfer is complete
---

# How the tool works

The tool operates in two modes.

## Server (Listener)
- Opens a TCP socket
- Waits for a client
- Receives uploads and/or executes commands

## Client
- Connects to the server
- Uploads files if requested
- Sends commands
- Displays command output

---

# Example Session

Server:

```bash
python nc.py -l -c
```

Client:

```bash
python nc.py -t 192.168.1.108 -c
```

Interactive session:

```bash
> whoami
user

> ls
Documents
Downloads
Pictures

> pwd
/home/user

> exit
Connection Closed
```


---

# License

This project is released under the MIT License.
This tool was inspired by `black hat python 2nd edition book`.



1. [AWS — Regions and Availability Zones](#aws--regions-and-availability-zones)
2. [Linux](#linux)
3. [Authentication and SSH](#authentication-and-ssh)
4. [Firewall and Security Groups](#firewall-and-security-groups)
5. [Linux File System](#linux-file-system)
6. [Linux Commands](#linux-commands)

## AWS — Regions and Availability Zones

Your AWS servers physically exist inside data centers around the world. AWS organizes these into two levels:

**Region** — a geographic area. Examples:
- `us-east-1` → North Virginia, USA — cheapest region; new AWS services always launch here first
- `ap-south-1` → Mumbai, India
- `eu-west-1` → Ireland

**Availability Zone (AZ)** — an isolated data center inside a region. Every region has at least 2 AZs; some have 6.

Think of it this way: **Hyderabad is a Region**. North Hyderabad is AZ-1 and South Hyderabad is AZ-2. If AZ-1 has a power failure or a flood, your application is still running from AZ-2. This is called **high availability** — your service stays up even when one data center goes down.

When you build applications on AWS, you always spread them across multiple AZs for this reason. A single-AZ deployment is a single point of failure.

> In this course we will mostly use `us-east-1` — it is the cheapest region and has all the latest features.

---

## Linux

### Why Linux, Not Windows?

All serious cloud and DevOps work runs on Linux. Here is the direct comparison:

| | Windows | Linux |
|---|---|---|
| Interface | Heavy GUI — high RAM and CPU usage | Lightweight — runs on minimal resources |
| Security | More targeted by attackers | More secure by design |
| Cost | Expensive licenses | Open source — mostly free |
| Server use | Built for desktops | Built for long-running servers |
| Remote access | RDP (heavy) | SSH (lightweight terminal) |

**How Linux came to exist:** IBM built the first PCs and servers by tightly coupling hardware and software — you had to buy both together. Linus Torvalds (who later also created Git) looked at Unix's principles and rewrote an operating system from scratch in C that could run on any hardware. That became Linux.

Linux is technically just the **kernel** — the part that sits between hardware and software, managing memory, CPU, and storage. Everything else (the shell, the file system, the commands) is built on top of it.

### Distributions (Distros)

Different organizations package the Linux kernel with their own tools and support — these are called **distributions**:

- **RedHat family**: RHEL, CentOS, Fedora, Amazon Linux — commands are nearly identical across all of them
  - Paid support available: if something breaks on an enterprise RHEL server, RedHat engineers join your team and fix it
  - Community support: open source versions like CentOS get community help instead
- **Debian family**: Ubuntu, Debian

In this course we use **Amazon Linux 2023**, which is RedHat-based. Everything you learn here works on CentOS, RHEL, and most other Linux servers.

### Users and the Command Prompt

Linux has two types of users:

- **Normal user** — prompt ends with `$` — limited permissions, cannot break the system
- **Root / Admin user** — prompt ends with `#` — full system access, can do anything

To temporarily switch to root:
```bash
sudo su
```

Use root carefully. One wrong `rm` command as root and there is no undo.

### Paths — Absolute vs Relative

Every file and directory on Linux has an address. There are two ways to refer to it:

- **Absolute path** — starts from `/` (root), works from anywhere:
  `/home/ec2-user/devops/repos`

- **Relative path** — relative to your current location:
  `repos/notes` (only works if you are already inside `/home/ec2-user/devops`)

```bash
pwd                  # print working directory — shows exactly where you are right now
cd /home/ec2-user    # go to that location (absolute — works from anywhere)
cd repos             # go into repos/ from your current location (relative)
cd ..                # go up one level
```

### Command Structure

Every Linux command follows this pattern:

```
command  [options]  [arguments]
```

Options modify how the command behaves. They come in two forms:
- Short: `-l` (single dash + one letter — quick to type)
- Long: `--list` (double dash + full word — more readable in scripts)

You can combine short options: `ls -ltr` is the same as `ls -l -t -r`.

---

## Authentication and SSH

### The Three Ways to Prove Who You Are

Before connecting to any server, you must prove your identity. Authentication falls into three categories:

1. **What you know** — username and password. Simple, but can be guessed, phished, or stolen.
2. **What you have** — a token or key file. Examples: Google Authenticator OTP, RSA token, SSH key pair.
3. **What you are** — biometrics. Fingerprint, retina scan, face recognition.

For Linux servers on AWS, we use **SSH key pairs** (category 2). Far more secure than passwords over the internet.

### Generating an SSH Key Pair

```bash
ssh-keygen -f my-key-name
```

This creates two files:
- `my-key-name` → **private key** — stays on your laptop. Never share this with anyone, ever.
- `my-key-name.pub` → **public key** — goes onto the server.

When you connect, SSH proves you hold the private key without ever transmitting it over the network. Even if someone captures all your traffic, they cannot steal your key.

### Connecting to an AWS Server

```bash
ssh -i <private-key-file> ec2-user@<server-public-ip>
```

- `ec2-user` is the default username on Amazon Linux (Ubuntu uses `ubuntu`, CentOS uses `centos`)
- `-i` points to your private key file
- Get the public IP from your AWS EC2 console

**When something goes wrong — follow this order:**
1. Read the error message carefully — it usually tells you what is wrong
2. Search Google or ChatGPT with the exact error text
3. Post in the class WhatsApp/Slack group
4. Bring it to the QA session

---

## Firewall and Security Groups

A **firewall** decides which network traffic is allowed in and out. On AWS, this is a **Security Group** — a virtual firewall attached to each EC2 instance.

- **Inbound / Ingress** — traffic coming *into* your server. You must explicitly allow each port.
- **Outbound / Egress** — traffic going *out* from your server. Allowed by default.

Every network connection is identified by three things: **protocol + IP address + port number**.

Common ports to know:

| Protocol | Port | Used For |
|---|---|---|
| SSH | 22 | Remote terminal access to Linux servers |
| HTTP | 80 | Web traffic (unencrypted) |
| HTTPS | 443 | Web traffic (encrypted) |
| MySQL | 3306 | Database connections |
| FTP | 21 | File transfers |

There are 65,536 total ports (0–65535). Applications listen on specific ports. If a port is not open in the Security Group, the connection is blocked before it reaches your application — it just times out with no explanation to the person connecting.

---

## Linux File System

Linux has a single root `/` — everything branches from here. There is no `C:\` or `D:\` like Windows.

| Directory | What Lives Here |
|---|---|
| `/` | Root — the starting point of everything |
| `/home` | Home directories for each user — e.g., `/home/ec2-user` |
| `/etc` | System and application configuration files |
| `/tmp` | Temporary files — cleared on reboot |
| `/bin` | Core commands and binaries — ls, cat, grep, etc. |

When you log in, you land in your home directory: `/home/ec2-user` on Amazon Linux. The shortcut `~` always refers to your own home directory regardless of username.

---

## Linux Commands

### Listing Files

```bash
ls                # list files and directories
ls -l             # long format — shows permissions, owner, size, modification date
ls -ltr           # sorted by time, newest at the bottom (very useful for log files)
ls -la            # include hidden files (names starting with .)
```

In `ls -l` output, the first character tells you the type:
- `d` → directory
- `-` → regular file

Hidden files start with `.` — like `.bashrc`, `.ssh/`. They exist to reduce clutter. The `-a` flag reveals them.

---

### System Information

```bash
uname -a          # print all system information — kernel version, architecture, hostname
```

---

### Creating Files and Directories

```bash
touch notes.txt             # create an empty file (safe to run on existing files — just updates the timestamp)
mkdir devops                # create a directory
mkdir -p devops/projects    # create nested directories — no error if they already exist
```

---

### Reading Files

```bash
cat notes.txt               # print entire file to the screen
cat > notes.txt             # write to a file interactively — type your content, press Ctrl+D when done
cat >> notes.txt            # append to a file without overwriting what is already there
cat file1 file2 > file3     # merge file1 and file2 into a new file3
```

The `>` operator redirects output into a file — **overwrites** existing content.
The `>>` operator appends to a file — **adds to** existing content.

---

### Copying, Moving, Deleting

```bash
cp source.txt destination.txt       # copy a file
cp -r source-dir/ dest-dir/         # copy a directory and everything inside it

mv oldname.txt newname.txt          # rename a file
mv file.txt /tmp/                   # move a file to another location

rm file.txt                         # delete a file
rm -r my-folder/                    # delete a directory and all its contents

rmdir devops                        # delete a directory — only works if it is empty
```

> `rm -r` is permanent. Linux has no Recycle Bin. Before deleting, confirm your location with `pwd` and check what you are targeting with `ls`.

---

### Downloading Files

```bash
wget https://example.com/file.zip           # download a file and save it to disk
curl https://example.com/api/health         # fetch content and print to screen
curl -s https://example.com/file.txt        # silent mode — no progress bar
```

`wget` is for downloading and saving files. `curl` is for fetching content directly — you will use it constantly for APIs, health checks, and scripting.

---

### grep — Searching Inside Files

`grep` finds lines that match a pattern. One of the most-used commands in DevOps.

```bash
grep "error" app.log              # lines containing "error"
grep -i "error" app.log           # case-insensitive — matches error, Error, ERROR
grep -n "error" app.log           # show the line number alongside each match
grep -c "error" app.log           # count how many lines match
grep -v "error" app.log           # show lines that do NOT match (exclude/invert)
```

**Remember:** Linux is case-sensitive everywhere. `Linux` and `linux` are two different things. Always use `-i` when you are not sure of the case.

---

### Piping — Chaining Commands Together

The `|` (pipe) sends the output of one command as input into the next. This is one of the most powerful ideas in Linux — combine simple tools to do complex work.

```bash
curl -s https://raw.githubusercontent.com/daws-90s/notes/main/session-02.txt | grep "linux"

ls -la | grep ".txt"
```

Think of it as: "take the output of the left side, and feed it directly into the right side." No temporary files needed.

---

### head and tail — Reading Parts of Large Files

```bash
head app.log              # first 10 lines
head -n 20 app.log        # first 20 lines

tail app.log              # last 10 lines
tail -n 20 app.log        # last 20 lines
tail -f app.log           # keep printing new lines as they are added (live follow)
```

`tail -f` is what you use when watching a running application's logs in real time — every new log line appears on your screen as it is written.

---

### cut — Extract Specific Fields

`cut` extracts columns from structured text using a delimiter.

```bash
# /etc/passwd holds all Linux user accounts
# Format per line: username:password:uid:gid:info:home-dir:shell

cut -d ":" -f1 /etc/passwd          # extract field 1 — username
cut -d ":" -f3 /etc/passwd          # extract field 3 — user ID
cut -d ":" -f1,3 /etc/passwd        # extract fields 1 and 3 together
```

`-d` sets the delimiter character. `-f` picks which field(s) to keep.

---

### awk — Smarter Text Processing

`awk` goes further than `cut`. It can filter rows by condition, work with the last field dynamically, and do calculations.

```bash
# Extract just the filename from a full URL
echo "https://raw.githubusercontent.com/daws-90s/notes/main/session-02.txt" | awk -F "/" '{print $NF}'
# Output: session-02.txt

# Print usernames from /etc/passwd
awk -F ":" '{print $1}' /etc/passwd

# Print only users with UID greater than 999 — these are regular users, not system accounts
awk -F ":" '$3 > 999 { print $1 }' /etc/passwd
```

`-F` sets the field separator. `$NF` means the last field (NF = number of fields). `$1`, `$2`, `$3` are fields by position.

---

### Quick Reference

```
Navigation       pwd, cd, ls
System info      uname -a
File ops         touch, cat, cp, mv, rm, mkdir, rmdir
Download         wget, curl
Search           grep
Text processing  cut, awk
Log reading      head, tail, tail -f
Piping           |
Redirection      >, >>
Privileges       sudo su
```



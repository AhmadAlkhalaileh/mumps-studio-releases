# Mumps Studio

A desktop IDE for MUMPS/M and VistA on YottaDB or GT.M. It edits, runs and
debugs routines on a live environment reached through **Docker** or **SSH**,
and adds VistA tools on top: FileMan data and schema, KIDS patches, Git, a
terminal and AI agents.

This repository holds the **user guide** and the **issue tracker**.
[Report a problem or ask for a feature](https://github.com/AhmadAlkhalaileh/mumps-studio-releases/issues/new/choose).

## Contents

- [Install](#install)
- [Connect Docker and SSH (once)](#connect-docker-and-ssh-once)
- [Updating and removing](#updating-and-removing)
- [First start: connect to an environment](#first-start-connect-to-an-environment)
- [What you can do](#what-you-can-do)
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Troubleshooting](#troubleshooting)
- [Your data](#your-data)
- [Reporting issues](#reporting-issues)
- [Older versions](#older-versions)

## Install

Mumps Studio 3.0 is a strict snap for Ubuntu and other Linux distributions
with snapd:

```bash
sudo snap install mumps-studio
```

Git, the SSH client and the Docker client are included in the snap.

## Connect Docker and SSH (once)

The snap only gets what you allow. These permissions are **off** after a fresh
install; connect the ones you need:

```bash
sudo snap connect mumps-studio:docker            # your local Docker containers
sudo snap connect mumps-studio:ssh-keys          # your keys in ~/.ssh, for SSH and Git over SSH
sudo snap connect mumps-studio:removable-media   # optional: projects on external drives
```

- **docker** works with Docker from apt or the Docker snap. Your user must be
  able to run `docker ps`; if not, `sudo usermod -aG docker $USER` and log out
  and back in.
- No restart is needed: after connecting, choose **Connect… → Docker
  Containers → Refresh Docker List**.
- **Help → Snap Permissions…** in the app shows what is connected.

## Updating and removing

- Updates (`sudo snap refresh mumps-studio`) keep your permissions and settings.
- `sudo snap remove mumps-studio` removes the permissions too. After a fresh
  install, run the `snap connect` commands again.

## First start: connect to an environment

Mumps Studio works on a live environment. Until a Docker or SSH connection is
active the workbench is locked and shows **Connect to start**: editing, search,
run/debug and the tool windows wait for a connection. Settings, the Connect
menu, Help and the window controls stay available. If the connection drops,
the IDE locks again and keeps your open editors until you reconnect.

**Docker**: **Connect… → Docker Containers →** pick a running container. The
routine and global paths are read from the container's `vista-profile`. To set
them yourself: **Settings → Connections → Docker** (YottaDB path, global
directory, routines path).

**SSH**: **Settings → Connections → SSH Profiles**: host, port, user, and a
password or your SSH key. The IDE reads YottaDB and the routine paths from the
server's `vista-profile`.

Some images keep their stock routines read-only. Save changed routines into
the writable routines directory, which is searched first.

## What you can do

**Edit M code**: syntax highlighting, folding, outline, hover help for FileMan
APIs, snippets, linting and XINDEX, formatting, Smart Rename for tags across
routines, local history. **File → New…** (Alt+Insert) creates a routine.

**Run and debug**: run the current routine (Ctrl+Enter) and see the output in
the Run window; debug (Shift+F9) with breakpoints, stepping, variables,
watches, a console and a timeline. After an interrupted session, **Run →
Debugger Recovery…** cleans up safely.

**Find things**: Search Everywhere (press Shift twice), Go to File (Ctrl+N),
Find in Files (Ctrl+Shift+F), Find Action (Ctrl+Shift+P), Go to Declaration
(Ctrl+B), Find Usages (Alt+F7).

**Database and FileMan** (Database tool window): browse FileMan files and
records, open a record card, see a file's schema diagram (fields, pointers,
multiples), and query with SQL:

```sql
SELECT NAME, SEX FROM PATIENT WHERE NAME = 'ZZTEST,ONE'
UPDATE PATIENT SET SEX = 'F' WHERE NAME = 'ZZTEST,ONE' LIMIT 10
```

Changes are shown for review before anything is written, and every write goes
through FileMan (input transforms, cross-references and auditing apply) inside
a transaction that is rolled back if anything fails.

**KIDS patches**: **View → VistA & M → Load & Install Patch** loads and
installs `.KID` distributions with VistA's own KIDS: it checks what the system
already has and the required builds, asks KIDS's questions up front, and shows
KIDS's progress live. The **KIDS Workbench** reviews a distribution before you
install it.

**VistA tools** (Tools and View → VistA & M): TaskMan, Error Trap, M Processes,
Lock Table, Menu Navigator, HL7 Workbench, FHIR Mapping, RPC Scaffold, System
Health, Global Growth and more.

**Git**: commit (Ctrl+K), push (Ctrl+Shift+K), log (Alt+9), blame and diffs.

**Terminal**: a terminal into the connected environment.

**Clipboard history**: Ctrl+Shift+V lists what you copied. The entry you pick
is pasted, becomes the clipboard (so Ctrl+V pastes it again) and moves to the
top of the list.

**AI agents** (off by default; enable the *AI Agents* extension under
Extensions): Claude Code, Codex or Gemini CLI work on the connected
environment. Every change an agent makes is shown for you to accept or reject.
An optional **team coding standard** is in **Settings → AI Agents → Team
standard** (off by default).

## Keyboard shortcuts

| Keys | Action |
|---|---|
| Shift Shift | Search Everywhere |
| Ctrl+N | Go to File |
| Ctrl+Shift+F | Find in Files |
| Ctrl+Shift+P | Find Action |
| Ctrl+S / Ctrl+Shift+S | Save / Save All |
| Ctrl+Enter | Run |
| Shift+F9 | Debug |
| Ctrl+F2 | Stop |
| Ctrl+F / Ctrl+R | Find / Replace in the editor |
| Ctrl+G | Go to Line |
| Ctrl+B | Go to Declaration |
| Alt+F7 | Find Usages |
| Ctrl+/ | Comment / uncomment |
| Ctrl+Alt+L | Reformat Code |
| Ctrl+Shift+V | Paste from clipboard history |
| Ctrl+Shift+G | GOTO Flow Map |
| Ctrl+Shift+X | Run XINDEX |
| Ctrl+K / Ctrl+Shift+K | Git Commit / Push |
| Ctrl+, | Settings |

## Troubleshooting

**No Docker containers in the Connect menu.** The app tells you when Docker
access is off. Run `sudo snap connect mumps-studio:docker`, then **Connect… →
Docker Containers → Refresh Docker List**. Also check that `docker ps` works
in a terminal and the container is running.

**SSH or Git over SSH cannot find my key.** Run
`sudo snap connect mumps-studio:ssh-keys`.

**I removed and reinstalled, and Docker stopped working.** Removing the snap
drops its permissions; connect them again (see above). Install new versions
over the existing one instead of removing it first.

**The icon is missing in Show Applications.** Log out and back in once; GNOME
sometimes keeps an old icon after a snap was removed and reinstalled.

**Messages like `MESA-LOADER: failed to open …` in the system log.** The app
still works; they come from the graphics driver lookup inside the snap.

## Your data

- Settings, history and caches stay in `~/snap/mumps-studio/`.
- The snap reaches only what it is allowed: your home directory, the network,
  and Docker, `~/.ssh` and external drives when you connect them.

## Reporting issues

[Open an issue](https://github.com/AhmadAlkhalaileh/mumps-studio-releases/issues/new/choose)
and choose *Bug report* or *Feature request*. Include the version
(**Help → About**), your Linux version, and how you connect (Docker or SSH).

**Never paste passwords, server addresses, patient data or other confidential
information** into an issue or a screenshot.

## Older versions

Mumps Studio is distributed only through the Snap Store. Earlier 2.x test
builds were never published there and are no longer available. If you have
one installed, remove it, then install 3.0 and connect the permissions above:

```bash
sudo snap remove mumps-studio
sudo snap install mumps-studio
```

Contact: ahmadalkhalaileh62@gmail.com

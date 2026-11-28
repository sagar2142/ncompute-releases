# Ncompute

**Combine spare computing power from your group's devices into one private pool.**

Ncompute links computers that belong to people who trust each other (friends, a team, a lab)
into a private group. When someone needs to run something heavy, such as training an AI model
or processing a lot of data, Ncompute runs it on whichever computer in the group has free
capacity. If that computer gets busy, hot, or switched off, the work moves to another computer
and continues from where it stopped.

**[Download the Windows app](https://github.com/sagar2142/ncompute-releases/releases/latest)**
(Windows 10 and 11, 64-bit) · **[Download a Linux worker kit](https://github.com/sagar2142/ncompute-releases/releases/tag/linux-worker-preview.1)**

> Ncompute is an early preview. Open the Windows app to host a group or enter a **join code**
> to join someone else's group. Devices normally connect over the same home, office or lab network.

---

## How it works

```
               Group host
     accounts, the group, decides where work runs
        /               |                \
  Computer A       Computer B        Computer C
 Ncompute app     Ncompute app      Ncompute app
 + sealed engine  + sealed engine   + sealed engine
        \_______________|________________/
      computers pass work and saved progress
        directly to each other, encrypted
```

Windows computers run the **Ncompute app**. Headless Linux devices run the **Linux worker**.
Both watch the device's health, follow the owner's sharing limits, and run jobs in an isolated
environment.

One computer is the **group host**: it keeps the group's accounts and member list and decides
which computer runs which job.

### From joining to results

1. **Join.** Install the app, let it prepare its engine, then paste the join code from your
   group's host and type your name. No email or password. Your computer joins with modest limits (about half of its
   processor, a quarter of its memory, never on battery) and downloads the software that jobs
   use (Python and PyTorch) from the group's other computers.
2. **Submit work.** On the **Jobs** page, paste a Python program and choose how much processor,
   memory or graphics power it needs.
3. **It runs where there is room.** The host picks a computer that is healthy, not too hot,
   plugged in, and within its owner's limits. The job runs inside that computer's sealed engine
   and saves its progress regularly.
4. **It keeps going.** If that computer becomes overloaded or hot, loses its connection, is
   closed, or its owner presses **Stop everything**, the job continues on another computer from
   its last save. Large jobs can also be split into parts that run on several computers at once,
   with their results combined automatically.
5. **Get the result** on the **Jobs** page.

## What you can do with it

- **Borrow a stronger graphics card**: train a model on a group member's GPU when yours is too
  slow.
- **Finish big jobs sooner**: split thousands of calculations or files across every computer in
  the group.
- **Keep long jobs alive**: a run of several hours survives one laptop being closed.
- **Use idle machines**: put a lab's or an office's idle PCs to work.

## You stay in control of your computer

| In the app | What it does |
|---|---|
| **Sharing switch** (top bar) | Pause or resume sharing at any time |
| **Stop everything** (top bar) | Stops every job on your computer at once; they continue elsewhere |
| **Resource sharing** | How many cores, how much memory and disk, whether your graphics card is shared, maximum temperature, never on battery |
| **Home** and **Computers** | Your computer's health and every group computer's live conditions |
| **Groups** | Invite computers; group owners can remove members and computers |

## Security

- **Sealed jobs.** Work runs inside the Ncompute engine as an unprivileged user, read-only,
  without internet access unless allowed, and within your limits. It cannot see your drives;
  only Ncompute's own job folder is shared with it.
- **Private groups.** Only computers with a join code can join. The code also carries the
  host's key fingerprint, so your computer connects only to the real host, encrypted (TLS 1.3).
  Computers in a group talk to each other over encrypted connections with verified keys.
- **No passwords to remember.** A computer joins with the join code; the group host creates an
  account for it whose secret stays encrypted on that computer, and it reconnects by itself.
- **Signed updates.** The app updates itself only with releases signed by the Ncompute
  developer, and refuses anything altered.

## Privacy

- Your **name** and the times your computer connects are kept by your group's host and may be
  shared with the Ncompute developer. No email address or password is asked for.
- Other members of your group can see your computer's name and its hardware and health
  (processor, memory, graphics card, temperatures, battery, the network link between computers),
  which is how work is placed safely.
- Members of your group can run jobs on your computer within the limits you set, sealed off from
  your files. Only join groups of people you trust.

## Install

1. Download `Ncompute_<version>_x64-setup.exe` from the
   [latest release](https://github.com/sagar2142/ncompute-releases/releases/latest). Each release
   lists the installer's SHA-256, so you can check the download.
2. Run it. It installs for your user only and needs no administrator rights. Windows may show
   *"Windows protected your PC"* because the installer is not yet code-signed: choose
   **More info → Run anyway**.
3. Open Ncompute and follow the steps:
   - **Prepare this computer.** The engine needs Windows' virtualization feature (WSL 2). If it is
     off, the app turns it on (Windows asks for approval once and may need a restart). If the app
     says virtualization is turned off in the firmware, enable *Intel Virtualization Technology*
     (VT-x) or *SVM Mode* (AMD) in your computer's BIOS/UEFI settings.
   - **Join a group.** Paste the join code and your name.
   - **Get ready.** The job software arrives from your group's computers.
4. Keep the app open while you share. Closing it stops sharing; running jobs move elsewhere.

**Updates** install from inside the app: when a new version is out, an **Update available** note
appears with what's new and an **Update now** button.

**Uninstall** from Windows **Settings → Apps → Ncompute**. This also removes the engine and the
job software it downloaded.

## Connect a Linux worker

Use the [Linux worker kits](https://github.com/sagar2142/ncompute-releases/releases/tag/linux-worker-preview.1)
to contribute CPU and RAM for Python jobs. Choose **ARM64** for a Raspberry Pi 4 or 5 running
a 64-bit OS or another ARM64 device. Choose **x64 (amd64)** for an Intel or AMD Linux device.
The kit needs a local Docker Engine with the containerd image store and resource limits enabled.

1. Verify the archive against its SHA-256 file and extract it on the Linux device.
2. In the extracted folder, run `./ncompute-worker run`. Leave it running while the Python
   runtime loads.
3. In Ncompute 0.3.0 on the Windows host, open **Groups → Invite a computer** and create a join code.
4. On Linux, open a second terminal in the same folder. Run
   `./ncompute-worker join --name "My worker"` and paste the code at the prompt.
5. Run `./ncompute-worker status` to check readiness. When submitting a job in the Windows app,
   select the Linux device under **Placement**.

Use `./ncompute-worker pause` to stop sharing and `./ncompute-worker resume` to start again.
The archive includes setup instructions for starting a worker at boot.

## Windows requirements

- Windows 10 or 11, 64-bit, with virtualization available (most computers from the last ten
  years).
- Disk space: about 400 MB for the app and its engine, plus about 1.5 GB for the job software, or
  about 10 GB if you share an NVIDIA graphics card (it then also receives the CUDA environment).
- An NVIDIA graphics card is optional (needed only to share it for GPU jobs).
- A join code from your group's host, and the same network as the host.

## Coming next

- Groups across different networks and cities
- A code-signed installer (no Windows warning)

## Questions or problems

Open an issue in this repository. If an update failed, the file `logs\update.log` in the app's
data folder (`%LOCALAPPDATA%\Ncompute\Ncompute\data\agent\logs`) explains what happened.

## License

Copyright © 2026 Lakshmi Sagar G (Ncompute). All rights reserved. You may download, install and
use the app; you may not copy, modify, resell or redistribute it. Third-party open-source
components in the installer remain under their own licenses. See [LICENSE](LICENSE).

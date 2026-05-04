# How to Download NASA Climate Data on Windows

This guide will help you download climate data files using a free tool called **rclone**. No coding experience needed — just copy and paste.

## What you'll need

- A Windows computer
- About 25 GB of free disk space
- Internet connection (the download is around 20 GB total)

---

## Step 1: Install rclone

1. Click the **Start** button and type `PowerShell`
2. Click **Windows PowerShell** to open it (a blue window will appear)
3. Copy and paste this command, then press **Enter**:

   ```powershell
   winget install Rclone.Rclone
   ```

4. Wait for it to finish (you'll see "Successfully installed")
5. **Close PowerShell completely**, then open it again (this is important — it makes the new tool available)
6. Type this to check it worked:

   ```powershell
   rclone version
   ```

   You should see some version information. If yes, you're good!

> **If `winget` doesn't work:** Go to <https://rclone.org/downloads/>, download the "Windows AMD 64-bit" zip file, extract it, and ask someone technical to help add it to your PATH. Otherwise, the winget method above should work on Windows 10/11.

---

## Step 2: Create a settings file

This tells rclone where to find the NASA data.

1. In PowerShell, paste this and press **Enter**:

   ```powershell
   notepad $env:APPDATA\rclone\rclone.conf
   ```

2. Notepad will ask: *"Do you want to create a new file?"* — click **Yes**
3. Copy and paste this exactly into Notepad:

   ```ini
   [nasa-nex]
   type = s3
   provider = AWS
   region = us-west-2
   endpoint = s3.us-west-2.amazonaws.com
   ```

4. Save the file (**File → Save**, or **Ctrl+S**) and close Notepad

---

## Step 3: Tell rclone the data is public

NASA's data is free and public, so we need to tell rclone not to ask for a login.

Paste this in PowerShell and press **Enter**:

```powershell
[System.Environment]::SetEnvironmentVariable('RCLONE_S3_NO_SIGN_REQUEST','true','User')
```

**Close PowerShell and open it again** so the setting takes effect.

---

## Step 4: Test that it works

Paste this command and press **Enter**:

```powershell
rclone ls nasa-nex:nex-gddp-cmip6/NEX-GDDP-CMIP6/BCC-CSM2-MR/ssp245/r1i1p1f1/tas/
```

You should see a long list of files (about 86 of them) with names like `tas_day_BCC-CSM2-MR_ssp245_...nc`.

If you see the list — perfect, everything is set up correctly!

---

## Step 5: Download the data

1. First, decide where to save the files. For example, `D:\nasa-nex\tas\`. Make sure that drive has at least 25 GB free.

2. Paste this command (change the folder path if you want a different location):

   ```powershell
   rclone copy nasa-nex:nex-gddp-cmip6/NEX-GDDP-CMIP6/BCC-CSM2-MR/ssp245/r1i1p1f1/tas/ D:\nasa-nex\tas\ --transfers 8 --progress
   ```

3. Press **Enter** and wait. You'll see a progress bar showing download speed and how much is left.

4. Depending on your internet speed, this could take **30 minutes to a few hours**.

---

## Helpful things to know

**If your internet drops or you need to stop:** Just press `Ctrl+C` to cancel. When you're ready to continue, run the **same command** again — rclone will automatically skip files it already downloaded and continue where it left off.

**To check if everything downloaded properly**, paste this:

```powershell
(Get-ChildItem D:\nasa-nex\tas\).Count
```

You should see `86` (or close to it).

To see the total size:

```powershell
"{0:N2} GB" -f ((Get-ChildItem D:\nasa-nex\tas\ | Measure-Object Length -Sum).Sum / 1GB)
```

It should be around 20 GB.

---

## If something goes wrong

- **"rclone is not recognized"** → Close PowerShell and open a fresh one. If still not working, restart your computer.
- **"Access denied" or permission errors** → Make sure you completed Step 3 and reopened PowerShell.
- **Download is very slow** → That's normal for large files. Leave it running — you can minimize PowerShell and use your computer normally.
- **Stuck or confused** → Take a screenshot of the error and ask for help.

That's it! Once the download finishes, all your NASA climate files will be in the folder you chose.

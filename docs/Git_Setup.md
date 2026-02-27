# Using Git on a Restricted Linux VM  
_No sudo • No package manager • No compiler required_

# Part 1 — Install Git (No sudo required)

We use micromamba to install a prebuilt Git binary locally.

## Step 1. Install micromamba

```bash
cd "$HOME"
mkdir -p "$HOME/.local/bin"

curl -L https://micro.mamba.pm/api/micromamba/linux-64/latest -o micromamba.tar.bz2
tar -xjf micromamba.tar.bz2

mv bin/micromamba "$HOME/.local/bin/"
rm -rf bin micromamba.tar.bz2
```

Add to PATH:

```bash
export PATH="$HOME/.local/bin:$PATH"
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify:

```bash
micromamba --version
```

## Step 2. Install Git

```bash
micromamba create -y -n tools -c conda-forge git
```

You now have a fully functional Git binary installed locally.


# Part 2 — micromamba Activation Options

Activation does NOT persist across SSH sessions by default.  
Choose one of the following approaches.

## Option 1 — No Activation (Simplest)

Run Git directly without activating:

```bash
micromamba run -n tools git --version
micromamba run -n tools git clone <repo>
micromamba run -n tools git push
```

Works in every session with no extra setup.

## Option 2 — Manual Activation Per Session

Each time you SSH into the VM:

```bash
eval "$(micromamba shell hook -s bash)"
micromamba activate tools
```

Then use Git normally:

```bash
git push
git pull
```

## Option 3 — Auto Activate on Login (Recommended)

If this VM is dedicated for development, enable automatic activation.

Append to your ~/.bashrc:

```bash
echo 'eval "$(micromamba shell hook -s bash)"' >> ~/.bashrc
echo 'micromamba activate tools' >> ~/.bashrc
```

Now every new SSH session automatically activates the environment and `git` works immediately.

# Part 3 — Connect GitHub Using SSH

GitHub no longer supports password authentication over HTTPS.  
Use SSH for a secure workflow.

## Step 1. Generate an SSH key

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Use the email associated with your GitHub account

Press Enter to accept defaults.

## Step 2. Add SSH key to GitHub

Print your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Copy the output and add it to:

https://github.com/settings/keys

Click New SSH Key and paste it.

## Step 3. Test SSH connection

```bash
ssh -T git@github.com
```

If prompted, type yes.

Success message:

```
Hi <username>! You've successfully authenticated...
```

# Part 4 — Clone and Push Using SSH

Always clone using the SSH URL:

```
git@github.com:USERNAME/REPO.git
```

Clone:

```bash
git clone git@github.com:USERNAME/REPO.git
cd REPO
```

# One Time Git Configuration

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

Verify:

```bash
git config --list
```

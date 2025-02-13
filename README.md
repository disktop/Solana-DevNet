# Solana-DevNet
Here you'll be able to perform a token creation audit on your Solana token.
We'll use Docker to perform the Audit.
Requirements:
Linux, Windows wsl, or Mac.

Linux and Windows wsl install docker:
https://docs.docker.com/engine/install/
or for ubuntu
https://docs.docker.com/engine/install/ubuntu/

-------------------------------------------------------------------------
Setting up Docker's apt repo

# Docker Official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add rep:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Install Docker updated version:

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Check if it works:

sudo docker run hello-world
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Type in a directory:

mkdir your-token-name

#cd to your directory
cd  your-token-name
-------------------------------------------------------------------------
-------------------------------------------------------------------------
We need a Dockerfile:

nano Dockerfile
-------------------------------------------------------------------------
-------------------------------------------------------------------------
In the Dockerfile add:

# Use a lightweight base image
FROM debian:bullseye-slim

# Set non-interactive frontend for apt
ENV DEBIAN_FRONTEND=noninteractive

# Install required dependencies and Rust
RUN apt-get update && apt-get install -y \
    curl build-essential libssl-dev pkg-config nano \
    && curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Add Rust to PATH
ENV PATH="/root/.cargo/bin:$PATH"

# Verify Rust installation
RUN rustc --version

# Install Solana CLI
RUN curl -sSfL https://release.anza.xyz/stable/install | sh \
    && echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.bashrc

# Add Solana CLI to PATH
ENV PATH="/root/.local/share/solana/install/active_release/bin:$PATH"

# Verify Solana CLI installation
RUN solana --version

# Set up Solana config for Devnet
RUN solana config set -ud

# Set working directory
WORKDIR /solana-token

# Default command to run a shell
CMD ["/bin/bash"]

-------------------------------------------------------------------------
Do not add this text: click control X and then Enter
-------------------------------------------------------------------------
Once in the terminal again, run the Docker Image:

docker build -t heysolana .
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Run Container:

docker run -it --rm -v $(pwd):/solana-token -v $(pwd)/solana-data:/root/.config/solana heysolana
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Create mint account:

solana-keygen grind --starts-with dad:1
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Set the account as keypair:

solana config set --keypair dad-your-token-acount.json
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Claim environment (DevNet):

solana config set --url devnet
-------------------------------------------------------------------------
-------------------------------------------------------------------------
verify config:

solana config get
-------------------------------------------------------------------------
Send SOL airdrop to this Address:

solana address
-------------------------------------------------------------------------
- Get SOL from the faucet at  https://faucet.solana.com/
- You may airdrop tokens from your wallet on the devnet to another wallet
- The page will not accept early Github accounts. (about <3 months old)
- You may also try to code: solana airdrop 2.5
-------------------------------------------------------------------------
Check balance after airdrop:

solana balance
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Create mint address:

solana-keygen grind --starts-with mnt:1
-------------------------------------------------------------------------
*You'll get a mint address place it below
-------------------------------------------------------------------------
Mint our Token:

spl-token create-token \
--program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb \
--enable-metadata \
--decimals 9 \
mnt-your-mint-address.json
-------------------------------------------------------------------------
*You may change decimal number
-------------------------------------------------------------------------
Now you have to upload your metadata (image,name,etc..) to the cloud. You may use:
-https://app.pinata.cloud/ (upload via public)
(or)
-https://imgbb.com/ (upload it forever "don't auto-delete")
-------------------------------------------------------------------------
Upload your image first make sure it captures the image you want people to see within a squared circle.
make sure you upload it to the cloud
get the link to paste to the following metadata format below:
-------------------------------------------------------------------------
{
  "name": "Name",
  "symbol": "NAME",
  "description": "Some description that might make you money",
  "image": "your image cloud url here"
}
-------------------------------------------------------------------------
Upload your metadata as a json file this you can do on Pinata
-https://app.pinata.cloud/ (upload via public)
-Edit it in a text file then pinata will let you edit the file to a json right before uploading.
-You'll get a link to visit the file, save this for 3 steps later
-------------------------------------------------------------------------
Now you paste this meta data into the following file write in terminal:

nano metadata.json
-------------------------------------------------------------------------
You'll get write into a file copy paste your edited token metadata info:
-------------------------------------------------------------------------
{
  "name": "Name",
  "symbol": "NAME",
  "description": "Some description that might make you money",
  "image": "https://ibb.co/nsCHtpJL"
}
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Upload your data to the metaverse:

spl-token initialize-metadata \
mnteyhFCjqLu5QwfXmEu49dGybyFN5dwPfAhoiMbjNw \
"Name" \
"NAME" \
https://aquamarine-cheap-piranha-763.mypinata.cloud/ipfs/bafkreibnqr7c6f6hsscdnqkbrxdehjpeqnvc5x46udu73bxd5oke4smelq
-------------------------------------------------------------------------
Confirm your data is uploaded via https://explorer.solana.com/ change from the Mainnet Beta to the Devnet
-------------------------------------------------------------------------
Now create token account:

spl-token create-account mnteyhFCjqLu5QwfXmEu49dGybyFN5dwPfAhoiMbjNw
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Mint tokens to your mint account:
#This is the supply
spl-token mint mnteyhFCjqLu5QwfXmEu49dGybyFN5dwPfAhoiMbjNw 1000000
-------------------------------------------------------------------------
Check if tokens added:

spl-token balance mnteyhFCjqLu5QwfXmEu49dGybyFN5dwPfAhoiMbjNw
-------------------------------------------------------------------------
-------------------------------------------------------------------------
Send transer from [mint address] amount [investor's address]:

spl-token transfer mnteyhFCjqLu5QwfXmEu49dGybyFN5dwPfAhoiMbjNw 10 G5EibadVBKxnshP3NSFagJokfxXCD2VPm6WXoqgDSdQ2 --fund-recipient --allow-unfunded-recipient
-------------------------------------------------------------------------
Woalha your audit on the Solana Devnet is successfully complete!
**Credits: NetworkChuck**
Video Link: https://youtu.be/L4ASwqLZVV0?si=MdBV5RlJhFlY7DH
See full video to perform your audit on the **Mainnet**!



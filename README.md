# NIL Network Verifier Setup 🛡️

This repository contains detailed instructions on how to set up a verifier for maintaining the NIL Network's integrity. Follow the steps below to ensure that your verifier is correctly configured and operational.

## Prerequisites

1. **NIL Tokens**: Obtain NIL tokens in your Keplr wallet by visiting the [Nillion faucet](https://faucet.testnet.nillion.com)
2. **Keplr Wallet**: Ensure you have a Keplr wallet set up and connected.

## System Update

Start by updating your system to the latest packages:

```bash
sudo apt update && sudo apt upgrade -y
```

# Install Prerequisites & Docker

Next, install necessary packages and Docker:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && \
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

Verify Docker Installation

Check if Docker was installed correctly:
```
docker --version
```
You should see an output like Docker version 27.1.2, build d01f264.

Download Accuser Image

Pull the accuser Docker image:
```
docker pull nillion/retailtoken-accuser:v1.0.0
```
Create Accuser Directory

Set up a directory where the accuser data will be stored:
```
mkdir -p nillion/accuser
```
Initialize Accuser & Register

Run the Docker container to initialize the accuser and generate credentials:

```bash

docker run -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 initialise

```
This command will output the account_id and public_key needed for registering the accuser on the NIL network. The credentials will be saved in a file named credentials.json within the nillion/accuser directory.

Note: It’s crucial to back up the credentials.json file. Losing this file will result in losing access to the keys and address of the accuser.

Fund the Accuser

To file accusations on Nilchain, you must first fund the accuser account with NIL. You can obtain NIL from the Nillion faucet.

Use the Nillion address of the accuser (generated and registered in the previous step, not your Keplr wallet address) to fund the account.

Run the Accuser

Important: Wait 30-60 minutes after funding before proceeding to the next steps.

# Once you’re ready, start the accuser:

```
docker run -d -v ./nillion/accuser:/var/tmp nillion/retailtoken-accuser:v1.0.0 accuse --rpc-endpoint "http://65.109.222.111:26657" --block-start 5040477

```

# Note: Adjust the --block-start value to the block height just before your registered verifier. To find this height, check the address you used to register your verifier on your connected Keplr wallet, then look up the transaction hash of type “Pay For” on the blockchain explorer to determine the block height where your verifier was registered.

Check Verifier Logs

To ensure everything is running smoothly, check the verifier logs:

List Available Docker Containers

```
docker ps
```
Display Logs
```
docker logs -f <container-id>
```
Wait until the accuser synchronizes with the current block height. Once synchronized, you should see the status showing “Registered” as true.


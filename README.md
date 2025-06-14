# Boundless-Simplified-Guide

### Risc Zero, the Company behind boundless raised $55M is the parent of boundless which was developed to enhance scalability usuing ZKPs which allows blockchains to verify off-chain computations.


![image](https://github.com/user-attachments/assets/650bdf2e-815c-4ce1-abb0-7a069c339b1c)

----------
## `NOTE: This Phase is not INCENTIVIZED`
----------

## ---Prerequisites:---
+ OS: Ubuntu 22.02 is Required
+ Hardware: High-performance NVIDIA GPU (e.g., 3090, 4090, A6000+)
     * Clock Speed: ≥ 3.0GHz boost
     * RAM: 32 GB DDR4
     * Storage: 200 GB SSD (NVMe preferred)
+ CLI Tools: `git,` `cargo,` `docker,` `docker-compose,` `just,` `nvidia-container-toolkit`

## Install Perequisites 
```
sudo apt update && sudo apt upgrade -y
sudo apt install -y \
    build-essential \
    curl \
    wget \
    git \
    unzip \
    zip \
    software-properties-common \
    ca-certificates \
    gnupg \
    lsb-release \
    htop \
    net-tools \
    neofetch \
    tmux \
    vim
```

```
sudo apt update && sudo apt install -y pkg-config libssl-dev
```


## Clone Boundless Repo 
```
git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.10
````

## Install Dependencies like Docer, Nvidia Container with this:
### This install docker and Nvidia container, 
```
sudo ./scripts/setup.sh
```
![image](https://github.com/user-attachments/assets/b170ee2e-6394-403a-af08-e72929742663)

`Reply with y to get a clean Reboot` 

----------
## Install Risc zero toolchain 
### To make it short, In order to install bento, we need to install the RiscZERO toolchain because bento uses it to generate ZKPs which is then compiled with RUST version of RISC called `rzup`
---------

### Install [RUST](https://www.rust-lang.org/learn/get-started) and add to PATH
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

### Install [Risc0](https://dev.risczero.com/api/zkvm/install) 
```
curl -L https://risczero.com/install | bash
source "/root/.bashrc"
```
![image](https://github.com/user-attachments/assets/114f50f0-5bb4-405c-8dc6-a6aa929b19ec)


### confirm with 
```
rzup --version
```
![image](https://github.com/user-attachments/assets/944b996c-9276-4470-a055-34d384a49bbd)


### Run `rzup` to install Risc ZERO `rust`, `cargo-risc-zero`, `cpp`, `r0vm` 
```
rzup install
```
![image](https://github.com/user-attachments/assets/6ae364cb-b431-4071-9cc9-7cb978af73c1)

---------------
## Install bento 
```
cargo install --git https://github.com/risc0/risc0 bento-client --bin bento_cli
```
![image](https://github.com/user-attachments/assets/3d1a5640-c576-4640-b1f6-84ca08302738)

### Add bento to PATH
```
export PATH="$HOME/.cargo/bin:$PATH"
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc 
source ~/.bashrc 
```

### Installing BOundless CLI
```
cargo install --locked boundless-cli
```
![image](https://github.com/user-attachments/assets/a9f3d1c9-7fbe-49d6-9a76-f899d3df6f03)

------------
## Testing out `just-bento` 
### `just-bento` is a local proof engine used to run test proof without submitting anything to boundless Network
* It is used for Debugging
* Running test proofs
* Benchmarking GPU setup for configuration (Testing to be sure setup is working perfectly before connecting to the Market via `just broker`) 

## What is `Just broker` in this case? 
### Just broker runs the production-ready prover for Boundless market
  * It handles market coordination, job fetch and proff submission
  * Connects to boundless network
  * Receives & proof jobs which then submits proofs and receive rewards [only when incentives are present 😉]
-------------


## Working with Just bento (remember, `bento` is used for testing)
### No variable is set for bento 

```
just bento 
```
![image](https://github.com/user-attachments/assets/a3b36288-7d47-49b0-8c19-d19dbbb98e35)


### Check logs (optional)
 + Use `CTRL+C` to exit 
```
just bento logs 
```

### Run test proof on bento using `32 threads`
Here's me running on a 4090, 64gb, 16gb vram - - local PC 
```
RUST_LOG=info bento_cli -c 32 
```
![image](https://github.com/user-attachments/assets/bcb35e0e-91ed-42e5-babd-24ca443c3e65)


------------------
# ⚠️For this section we are gonna need an RPC that supports Event Filter as stated in the template ⚠️
### We need an Ethereum RPC provider that supports the `eth_newFilter` and `eth_getLogs methods` 
  + Alchemy
  + Infura
  + Ankr
  + Chainstack and self-hosted NODE supports

## Let's move to working with `just broker` 

### Remember, `broker` is the production-ready market (which uses real Network thus; Sepolia, and Base for Mainent Beta) 
 + Unlike the bento, we are gonna need to set `Environmental variable` for `broker` [In this case, we are using Sepolia Network


## OPTION 1 [Base Mainnet and Testnet] 
 + Copy templete `.env` file and naming with below code (For Base Mainnet and Testnet)
    ```
    cp .env.broker-template .env.broker
    nano .env.broker
    ```
 + Locate valuee and input private key and RPC url 
     *   `PRIVATE_KEY:` Wallet key used to sign and receive rewards
     *   `RPC_URL:`  Ethereum RPC endpoint (e.g. Alchemy, Infura, selft-hosted RPC)
  -----------------

  ### If on custom Network; 
  * Edit the `BOUNDLESS_MARKET_ADDRESS` from [here](https://docs.beboundless.xyz/developers/smart-contracts/deployments)
  * Edit the `VERIIFIER_ADDRESS` 
  * Edit the `BOUNDLESS_MARKET_ADDRESS`
  * `SEGMENT_SIZE=22`



## OPTION 2 {IF YOU SKIP THE ABOVE `Base Mainnet` and `Base Testnet`] 
  ### Since we're working on Sepolia here, we'll run the predefined settings to Automatically load the below settings 
  ```
  source .env.eth-sepolia
  ```

### Edit config with below code
```
nano .env.eth-sepolia
```

* Edit the follow with RPC and Private key
  + Add `export RPC_URL`=YourSepoliaRPCHere
  + Add `export PRIVATE_KEY`=YourPrivateKEYHere    

![image](https://github.com/user-attachments/assets/7df712cb-e245-4dc8-ac01-b6f251a8e190)


------------
### Request FAUCET for Sepolia 
### Boundless on Ethereum/Base Sepolia uses testnet USDC as the stake token; to obtain testnet

  * Request USDC faucet [HERE](https://faucet.circle.com/)
![image](https://github.com/user-attachments/assets/7af8c4fb-f79b-4291-99ea-04b19f5aabf8)
  * Ensure the wallet also have enough ETH for gas (Since we're using Sepolia here, then Sepolia ETH is needed) - go rack some from faucet
-------------

## Deeposit Stake 
### You need to deposit USDC (Sepolia testnet version) to activate your prover. This stake is required to start receiving proving jobs. 

```
 source .env.eth-sepolia
```

```
boundless account deposit-stake 10
```
`I deposited 10 USDC here because the faucet dripped me 10 only, will try to refill after an hour` 
![image](https://github.com/user-attachments/assets/0bc86e51-f486-4ec0-8d8d-3c4d0bd9ab00)


-----------------
## Start Broker 
```
just broker
```










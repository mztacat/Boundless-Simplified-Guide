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

--------------------
## ⚠️ Choose Where to Run: Cloud vs Local
### You can either:

### A. Use a Cloud Platform
* Use platforms like [Vast.ai](https://cloud.vast.ai/?ref_id=62897&creator_id=62897&name=Ubuntu%2022.04%20VM) (so far works great since it has a Ubuntu 22 VM not pytouch, docker template) 
    + Ideal if you don’t have local access to high-end hardware.
    + Make sure to expose Docker and allow GPU access.

### B. Use Your Local PC
* Best if you have a powerful machine (e.g., Alienware M18, desktop with 4090).
  + You get full control and better performance predictability.
  + Install Docker, NVIDIA drivers, and run broker directly from your terminal.

### Recommendation: If you’re using a physical PC with enough VRAM (16GB+), local is often faster and more stable.
---------------------




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
sudo apt-get update -qq && sudo apt-get install -y -q clang
```


## Clone Boundless Repo 
```
git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.11
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


## Check if `nvidia-smi` is installed
```
nvidia-smi
```
+ If command not found,
```
sudo apt update
sudo apt install -y nvidia-driver-550 nvidia-dkms-550
```
+ Now Reboot
```
reboot
```

+ Run `nvidia-smi` again
  ### You should see below picture
```
nvidia-smi
```
![image](https://github.com/user-attachments/assets/83c5e3ec-9f36-433a-9dcf-596c26d4193a)


### Install Nvidia Container Toolkit for docker 
```
sudo apt install -y nvidia-container-toolkit
sudo systemctl restart docker
```

### Check if Docker can use GPU 
```
docker run --rm --gpus all nvidia/cuda:12.3.0-base-ubuntu22.04 nvidia-smi
```
- It should print your GPU info from inside the container.






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

## Install `nvtop` and `htop` 
```
sudo apt update && sudo apt install -y nvtop htop
```

+ `nvtop` is used to Monitor GPU Usage
+ `htop` is used to Monitor CPU cores, RAMs, processes usage
-----------

## Benchmarking a Single GPU

* Set `.env.broker`
```
nano .env.broker
```
- Set `SEGMENT_SIZE=20` depnding on GPU RAM     
*  In `compose.yaml` use just one GPU (default)
*  Restart `broker`
```
just broker down
just broker
```
*  Run Test
```
RUST_LOG=info bento_cli -c 4096
```
*  Check LOGS
```
docker logs bento-gpu_prove_agent0-1
```
### If you see something like `OutOfMemory at risc0-zkp...` you will need to reduce `SEGMENT_SIZE` 


## Multi-GPU Setup (if thats your SETUP)
### Check GPU with this code 
```
nvidia-smi -L
```
![image](https://github.com/user-attachments/assets/f2e1248e-2832-48d7-bfd5-2c3ffc1c836f)


*  ## Step 1: Configure `compose.yml` for Each GPU if more than one
### You must define a separate `gpu_agent` container for each GPU device. Use device_ids to target GPU 0(first GPU), 1(second GPU), 2(third GPU), etc.
---------------------

* In `compose.yml,` duplicate the `gpu_agent` section based on the numbers of GPUs where first GPU = 0 and fourth GPU = 3
  ### Here is a preconfigured 4 GPU count `compose` which i also updated the `broker` services `depend_on`
  ### We are gonna swap the old `compose.yml` to the preconfigued one
  
```
cd $HOME && cd boundless
mv compose.yml backupcompose.yml
curl -L https://raw.githubusercontent.com/mztacat/Boundless-Simplified-Guide/main/compose.yml -o compose.yml
```

-----------------


## Start Broker 
```
just broker
```

## View LOGS, stop BROKER & Remove Data 
```
just broker logs  # View logs
just broker down  # Stop broker
just broker clean  # Remove all data/volumes
```


-------------------------

# Performance Optimization & Segment Size Benchmarking



```
lscpu
```

--------------
# Twerking `SEGMENT_SIZE` 
+  `SEGMENT_SIZE` controls how large each proving segment is.
+ The larger the `SEGMENT_SIZE` the more efficient the proof (fewer segments to manage) but it requires more VRAM. \

### How `SEGMENT_SIZE` works 
+ The CPU runs a pre-flight of the program and splits it into segments.
+ Each segment is sent to a GPU worker for proving.
+ Larger segments = fewer proofs = faster overall proving = more VRAM required.

![CatDanceDancingCatGIF](https://github.com/user-attachments/assets/44b80fe9-be90-45ca-a4e5-e0860635f88d)

### Recommended `SEGMENT` by `VRAM` 

| **GPU VRAM** | **Max SEGMENT_SIZE** | **Notes**                              |
|--------------|-----------------------|----------------------------------------|
| 8 GB         | `19`                  | Entry-level proving performance        |
| 16 GB        | `20`                  | Good balance for consumer GPUs (e.g. 3090, 4060) |
| 20 GB        | `21`                  | For cards like A4500, RTX 5000         |
| 40 GB        | `22`                  | A6000, 4090, H100 and data center GPUs |

------

## RAM and CPU Tuning 
### Edit `compose.yml` to allocate CPU cores and Memory Usage 

+ Locate `cpus : 4
+ mem_limit: 4G  #where 4G is the memory to use
----
### 💡Tips for Tuning Based on your system 

| System RAM | # Cores | Suggested Config                                     |
| ---------- | ------- | ---------------------------------------------------- |
| 32 GB      | 16      | 4 GPU agents @ 4G/4 cores,   |
| 16 GB      | 8       | 2 GPU agents max @ 4G/4
| 64+ GB     | 24+     | 4+ GPU agents

----------------
## Taking a look into `broker.toml` and adjusting for good performance 
```
nano broker.toml
```

# The broker.toml file configures the Boundless broker, which acts as a controller for:
* Market bidding logic
* Prover scheduling
* Capacity control (based on CPU/GPU)


| Key                                 | Meaning                                                              | Example / Notes                    |
| ----------------------------------- | -------------------------------------------------------------------- | ---------------------------------- |
| `mcycle_price`                      | Price per mega-cycle (1M cycles) in native token (e.g. ETH)          | `"0.0000005"`                      |
| `mcycle_price_stake_token`          | Price per mega-cycle if user pays with staking token                 | `"0.0001"`                         |
| `peak_prove_khz`                    | Used for job scheduling; set based on benchmark speed of your prover | `100` (means \~100k cycles/sec)    |
| `max_mcycle_limit`                  | Reject requests requiring too much compute                           | e.g. `8000` (8 billion cycles)     |
| `max_journal_bytes`                 | Size cap on proof output journal                                     | Prevents oversized on-chain data   |
| `min_deadline`                      | Seconds before deadline to ignore job                                | Skip if deadline too close         |
| `max_concurrent_proofs`             | Max simultaneous proving tasks                                       | Set to number of GPUs or CPU cores |


### Where the `peak_prove_khz = 1400` because I use a `4x 4090s @ ~350kHz` each = total proving capacity estimate
### My `max_concurrent_proofs = 4` which signifies my numbers of GPUs
---------


# Thats all for now, will update as I keep twerking and discovering! 
# A long RIDE! 


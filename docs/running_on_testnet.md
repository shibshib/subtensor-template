# Running on the Testing Network
This tutorial shows how to use the bittensor testnetwork to create a subnetwork and connect your mechanism to it. It is highly recommended that you run `running_on_staging` first before testnet. Mechanisms running on the testnet are open to anyone, and although they do not emit real TAO, they cost test TAO to create and you should be careful not to expose your private keys, to only use your testnet wallet, not use the same passwords as your mainnet wallet, and make sure your mechanism is resistant to abuse. 

## Steps

1. Clone and Install Bittensor and the Bittensor Subnet Template.
This clones and installs the template if you dont already have it (if you do, skip this step)
```bash
$ cd .. # back out of the subtensor repo
$ git clone https://github.com/opentensor/bittensor-subnet-template.git # Clone the bittensor-subnet-template repo
$ cd bittensor-subnet-template # Enter the bittensor-subnet-template repo
$ python -m pip install -e . # Install the bittensor-subnet-template package
```

2. Create wallets for your subnet owner, for your validator and for your miner.
This creates local coldkey and hotkey pairs for your 3 identities. The owner will create and control the subnet and must have at least 100 test net TAO on it before it can run step 3. The validator and miner will run the respective validator/miner scripts and be registered to the subnetwork created by the owner.
```bash
# Create a coldkey for your owner wallet.
$ btcli new_coldkey --wallet.name owner

# Create a coldkey and hotkey for your miner wallet.
$ btcli new_coldkey --wallet.name miner
$ btcli new_hotkey --wallet.name miner --wallet.hotkey default

# Create a coldkey and hotkey for your validator wallet.
$ btcli new_coldkey --wallet.name validator
$ btcli new_hotkey --wallet.name validator --wallet.hotkey default
```

3. Getting the price of subnetwork creation
Creating subnetworks on the testnet is competitive and the cost it determined by the rate at which new network are being registered onto the chain. By default you must have at least 100 testnet TAO on your owner wallet to create a subnetwork. However the exact amount will fluctuate based on demand. If you do not have enough TAO, you can request some from the bittensor faucet by solving periodic POWs. The code below shows how to get the current price of creating a subnetwork.
```bash
btcli get_subnet_price --subtensor.network test
>> Subnetwork price: τ100.00000
```

3. Purchasing a slot
This creates a subnetwork on the local chain on netuid 1.
```bash
# Run the register subnetwork command on the locally running chain.
$ btcli register_subnet --subtensor.network test 
# Enter the owner wallet name which gives permissions to the coldkey to later define running hyper parameters.
>> Enter wallet name (default): owner # Enter your owner wallet name
>> Enter password to unlock key: # Enter your wallet password.
>> Register subnet? [y/n]: <y/n> # Select yes (y)
>> ⠇ 📡 Registering subnet...
✅ Registered subnetwork # Your subnet will be registered on netuid = 1 because you are running a local chain.
```

10. Register your validator and miner keys to the networks.
This registers your validator and miner keys to the network giving them the first 2 slots on the network.
```bash
# Register your miner key to the network.
$ btcli register --wallet.name miner --wallet.hotkey default  --subtensor.network test
>> Enter netuid [1] (1): # Enter netuid 1 to specify the network you just created.
>> Continue Registration?
  hotkey:     ...
  coldkey:    ...
  network:    finney [y/n]: # Select yes (y)
>> ⠦ 📡 Submitting POW...
>> ✅ Registered

# Register your validator key to the network.
$ btcli register --wallet.name validator --wallet.hotkey default --subtensor.network test
>> Enter netuid [1] (1): # Enter netuid 1 to specify the network you just created.
>> Continue Registration?
  hotkey:     ...
  coldkey:    ...
  network:    finney [y/n]: # Select yes (y)
>> ⠦ 📡 Submitting POW...
>> ✅ Registered
```

11. Check that your keys have been registered.
This returns information about your registered keys.
```bash
# Check that your validator key has been registered.
$ btcli overview --wallet.name validator --subtensor.network test
Subnet: 1                                                                                                                                                                
COLDKEY  HOTKEY   UID  ACTIVE  STAKE(τ)     RANK    TRUST  CONSENSUS  INCENTIVE  DIVIDENDS  EMISSION(ρ)   VTRUST  VPERMIT  UPDATED  AXON  HOTKEY_SS58                    
miner    default  0      True   0.00000  0.00000  0.00000    0.00000    0.00000    0.00000            0  0.00000                14  none  5GTFrsEQfvTsh3WjiEVFeKzFTc2xcf…
1        1        2            τ0.00000  0.00000  0.00000    0.00000    0.00000    0.00000           ρ0  0.00000                                                         
                                                                          Wallet balance: τ0.0         

# Check that your miner has been registered.
$ btcli overview --wallet.name miner --subtensor.network test
Subnet: 1                                                                                                                                                                
COLDKEY  HOTKEY   UID  ACTIVE  STAKE(τ)     RANK    TRUST  CONSENSUS  INCENTIVE  DIVIDENDS  EMISSION(ρ)   VTRUST  VPERMIT  UPDATED  AXON  HOTKEY_SS58                    
miner    default  1      True   0.00000  0.00000  0.00000    0.00000    0.00000    0.00000            0  0.00000                14  none  5GTFrsEQfvTsh3WjiEVFeKzFTc2xcf…
1        1        2            τ0.00000  0.00000  0.00000    0.00000    0.00000    0.00000           ρ0  0.00000                                                         
                                                                          Wallet balance: τ0.0   
```

10. Edit the default `NETUID=1` and `CHAIN_ENDPOINT=ws://127.0.0.1:9946` arguments in `template/__init__.py` to match your created subnetwork.
Or run the miner and validator directly with the netuid and chain_endpoint arguments.
```bash
# Run the miner with the netuid and chain_endpoint arguments.
$ python neurons/miner.py --netuid 1 --subtensor.network test --wallet.name miner --wallet.hotkey default --logging.debug
>> 2023-08-08 16:58:11.223 |       INFO       | Running miner for subnet: 1 on network: ws://127.0.0.1:9946 with config: ...

# Run the validator with the netuid and chain_endpoint arguments.
$ python neurons/validator.py --netuid 1 --subtensor.network test --wallet.name validator --wallet.hotkey default --logging.debug
>> 2023-08-08 16:58:11.223 |       INFO       | Running validator for subnet: 1 on network: ws://127.0.0.1:9946 with config: ...
```

7. Stopping Your Nodes:
If you want to stop your nodes, you can do so by pressing CTRL + C in the terminal where the nodes are running.

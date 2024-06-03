Ok, this is a quick guide on how to setup Clore and Nimble with a function to auto-restart the Nimble mining process if the instance restarts for any reason.

First, I would appreciate it if you used my Clore referral code, it helps fund my machines I use for testing my scripts - https://clore.ai?ref_id=jzj1ckbf

Once you have signed up, the simplest way to look for an instance is to click on "Marketplace" and set the filters to the following - 

<img width="164" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/351b27b3-12cd-49de-86dc-923360bf4bd5">

You will then get a list of instances like this - 

<img width="769" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/11c9e33e-47c0-4fb8-b755-d06ae174a42d">

Work out the cost per hour using the current Clore price x amount of Clore per day rental fee / 24. Currently a 4090 is around $0.122-$0.13 per hour.

Click on "Rent" and then you will be taken to this screen -

<img width="1269" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/95cfed5d-fc16-419a-b8a4-435fe8b51fdb">

Select the "Jupyter Lab + VS Code" image. then scroll down and select "Enable Startup Script" - 

<img width="878" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/77bf69ca-2c58-4f9d-aa05-d846b141acfd">

Remove the existing comments from the box that opens up so it is blank like this - 

<img width="1249" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/f3398998-ab38-4f5f-9056-538281ff5746">

Then paste this entire script into that box (PLEASE PLEASE PLEASE REMEMBER TO PUT YOUR ADDRESS IN THE 2 PLACES INDICATED) -

```
#!/bin/bash

# Update system and install Go

sudo apt update
sudo apt install tmux -y && apt install build-essential -y && apt install make -y
wget https://go.dev/dl/go1.22.1.linux-amd64.tar.gz

sudo rm -rf /usr/local/go && tar -C /usr/local -xzf go1.22.1.linux-amd64.tar.gz

export PATH=$PATH:/usr/local/go/bin

# Install python3 environment

sudo apt install python3-venv -y

# Clone into miner and compile

mkdir $HOME/nimble && cd $HOME/nimble
git clone https://github.com/nimble-technology/nimble-miner-public.git
cd nimble-miner-public

# Edit requirements.txt to run lower version of numpy

rm requirements.txt

# Copy this entire echo command

echo '
requests==2.31.0
torch==2.2.1
accelerate==0.27.0
transformers==4.38.1
datasets==2.17.1
numpy==1.24
gitpython==3.1.42' > requirements.txt

# Update git files and install miner

git pull
make install

# Change fsspec version

pip uninstall fsspec -y

pip install 'fsspec==2023.10.0'
pip install prettytable

# Activate the miner

source ./nimenv_localminers/bin/activate

tmux new-session -d -s Nimble 'make run addr=YOUR_NIMBLE_ADDRESS'

cat << EOF > /home/clore/onstart.sh
#!/bin/bash
sudo bash -c "cd /root/nimble/nimble-miner-public && tmux new-session -d -s Nimble 'make run addr=YOUR_NIMBLE_ADDRESS'"
EOF
```

Then click "Create" -

<img width="343" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/de36981e-4266-4246-af03-fbdcf3e17d36">

You will then see your machine you rented under "My Orders". It will display a status of "Deploying". This will change to "Deployed" once it is ready to connect to. This can take some time, so you will need to be patient.

Once the machine status says "Deployed" click here - 

<img width="944" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/932506fc-42e7-4c2a-878d-fc462f7e1bd9">

This will open a screen looking like this - 

<img width="497" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/69fe7d90-6292-41d9-a3d1-2223787c825e">

Click "Jupyter Lab" then click "Terminal" -

<img width="617" alt="image" src="https://github.com/neo250376/Clore-Script/assets/36828226/4e5743df-9173-494e-b107-ad8d35768cd9">

A screen will open that is your command prompt. Simply type -

```sudo tmux attach``` to see your miner running.

If you do not see the miner, "make install" is probably still running, especially if the instance internet speed is slower. Type ```ps aux``` to check in the process list if "make install" is still running. You should see a tmux process when it is complete and running the Nimble miner.

REMEMBER TO USE YOUR OWN WALLET ADDRESS WHEN YOU PASTE IN THE SCRIPT!!!

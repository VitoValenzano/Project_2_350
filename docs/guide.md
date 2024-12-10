# Installing Apache Ignite with Python on Ubuntu 22.04
## Prerequisites
To get started, ensure that you have a working WSL, distribution installed on your device. If you don't have WSL, open a PowerShell  (Or terminal on Windows 11) window as admin and run
```
wsl --install -nodistribution
```
You can now open the Microsoft Store and search for Ubuntu 22.04. This guide was written for 22.04, but newer versions of Ubuntu should work. Your mileage may vary with other distributions though. 

After you have Ubuntu installed and a user created, you will need to install a JDK, unzip, and pip. You should also run system updates
```
sudo apt update && sudo apt upgrade -y

sudo apt install python3-pip unzip openjdk-11-jdk
```

Verify the installation by running 
```
pip3 --version
```
## Installation
Download the ignite binary 
```
 wget https://dlcdn.apache.org/ignite/2.16.0/apache-ignite-2.16.0-bin.zip
```

Unzip the file 
```
unzip apache-ignite-2.16.0-bin.zip
```

Install the thin client pip package
```
pip3 install pyignite
```

## Starting a Node
Before you can connect to Ignite, you need to ensure a node is started.

First, navigate to where Ignite is installed 
```
cd apache-ignite-2.16.0-bin/bin
```

Start the node using this command 
```
./ignite.sh ../examples/config/example-ignite.xml
```

Open a new tab in terminal, and run the same command again in the same directory. You can check the Topology Snapshot line of the output and you should see a cluster of two server nodes 

## Testing the node
Apache distributes a simply python script that will test the server node. Paste the following into a text editor of your choice and save it as `hello.py`.
```
from pyignite import Client

client = Client()
client.connect('127.0.0.1', 10800)

#Create cache
my_cache = client.create_cache('my cache')

#Put value in cache
my_cache.put(1, 'Hello World')

#Get value from cache
result = my_cache.get(1)
print(result)
```

Run the script with 
```
python3 hello.py
```

And if it prints Hello World, You're all set!

[If you want further examples, we have written some here!](https://github.com/VitoValenzano/Project_2_350/tree/main/examples)
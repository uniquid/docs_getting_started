# Digi Board

This demo demonstrates the Uniquid Identity and Access Management SDK using the uidcore-c library on ConnectCore 6UL SBC Express Digi board.

### Requirements

* Ubuntu 14.04+ with standard development tools
* cmake to compile new firmware
* Digi ConnectCore 6UL SBC Express with internet connection \([link](https://www.digi.com/products/embedded-systems/single-board-computers/connectcore-for-i-mx6ul-sbc-express)\)
* some software packages from digi.com \(next steps\)
* libcurl
* libpthread \(usually already installed\)

{% hint style="warning" %}
You will need two files created when you run the `uniquid deploy aws` command. You can choose tu run the below steps in the same environment of the deploy or you will need to copy these two files on your development environment running command:

```bash
scp -i <user-key-pair.pem> ubuntu@<ec2-instance-ip-address>:/aws_device_cfg.json .
scp -i <user-key-pair.pem> ubuntu@<ec2-instance-ip-address>:/caChain-xxxxxxxxxxxxxx.crt .
```
{% endhint %}

### Install Prerequisites

Install these packages on your machine

```bash
sudo apt install cmake
sudo apt install libcurl4-openssl-dev
```

### Setup of the board

* follow this guide to [setup and connect the board](https://www.digi.com/resources/documentation/digidocs/90002286/task/yocto/t_set_up_hardware_yocto.htm)
* follow this guide to [program the Yocto firmware](https://www.digi.com/resources/documentation/digidocs/90002286/task/yocto/t_program_firmware_yocto.htm)

{% hint style="info" %}
For a more detailed documentation on the board setup please refer to the [DIGI web site](https://www.digi.com/resources/documentation/digidocs/90002286/#landing_pages/cc6ul_index.htm)
{% endhint %}

### Setup of Development Environment

1. download the toolchain installer from the Digi support page

   ```bash
   wget ftp://ftp1.digi.com/support/digiembeddedyocto/2.4/r1/sdk/ccimx6ulstarter/fb/dey-glibc-x86_64-core-image-base-cortexa7hf-neon-toolchain-2.4-r1.sh
   ```

2. give execution permission to the installer

   ```bash
   chmod +x dey-glibc-x86_64-core-image-base-cortexa7hf-neon-toolchain-2.4-r1.sh
   ```

3. install the toolchain on your host PC

   ```bash
   ./dey-glibc-x86_64-core-image-base-cortexa7hf-neon-toolchain-2.4-r1.sh
   ```

4. source the environment setup script, run this command each time you wish to use the SDK in a new shell session

   ```bash
   . /opt/dey/2.4-r1/environment-setup-cortexa7hf-neon-dey-linux-gnueabi
   ```

### Download and build the demo source code on host PC

1. clone the demo repo two options:
   1. ```bash
      git clone https://github.com/uniquid/dev-demos.git
      ```
   2. ```bash
      git clone git@github.com:uniquid/dev-demos.git
      ```
2. change directory

   ```bash
   cd dev-demos/DIGI-CC-6UL-SBC-Express
   ```

3. download and patch the dependancy

   ```bash
   make clone_repo
   ```

4. build the project, the binaries created by this command will be put on the bin directory

   ```bash
   make
   ```

{% hint style="warning" %}
make sure that the environment setup script has been sourced
{% endhint %}

### Transfer the executables on the board

1. power on the board using the provided USB cable
2. connect to the board using a serial terminal emulator \(115200N81\) or ssh
3. create the demo directory under `/home/root`

   ```bash
   cd /home/root
   mkdir demo
   ```

4. transfer all the content of the bin directory from the host PC to the `/home/root/demo` directory on the board using scp

   ```bash
   cd dev-demos/DIGI-CC-6UL-SBC-Express
   scp -r bin/* root@<ipaddress>:~/demo
   ```

### Copy certificate and configuration files

Copy in the demo directory the configuration file \(aws\_device\_cfg.json\) and the root CA for the mqtt broker \(caChain-xxxxxxxxxxxxxx.crt\) from your host PC to the board:

```bash
cd path/of/files
scp aws_device_cfg.json root@<ipaddress>:~/demo
scp caChain-xxxxxxxxxxxxxx.crt root@<ipaddress>:~/demo/rootCA.crt
```

both these files has been generated by the `uniquid deploy aws` command you run some steps ago.

### Run

1. connect to the board over terminal emulator or ssh and go to the `demo` directory

   ```bash
   cd /home/root/demo
   ```

2. run the demo

   ```bash
   ./S99uniquid-demo start
   ```

   if you want to run the demo at the board boot copy the above script into the `/etc/rc5.d` directory

   ```bash
   cp /home/root/demo/S99uniquid-demo /etc/rc5.d
   ```

3. you can check the log running `tail` command

   ```bash
   tail -f /var/log/uniquid-demo
   ```

4. to allow the board to publish the led status on aws IoT you have to [create a contract](https://uniquid.gitbook.io/uniquid/create-uniquid-contract) between the board \(user\) and the authorizer \(provider\)
5. check [here](https://us-east-2.console.aws.amazon.com/iot/home?region=us-east-2#/test) for published messages \(please note that the link provided is for the us-east-2 AWS region, you may need to switch to the one you have defined on _aws configure_\)
6. you can use the button on the board to switch on/off the led status

{% hint style="info" %}
To remotely interact with the board you have to install the related [application](https://uniquid.gitbook.io/uniquid/hardware/remote-control-app).
{% endhint %}

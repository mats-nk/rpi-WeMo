![Alt text](pics/rpi-WeMo_Logo.png?raw=true "rpi-WeMo logo")

[![License GLP3](https://img.shields.io/badge/license-GPL3-red.svg)](LICENSE.md)
[![HitCount](http://hits.dwyl.com/fejao/rpi-WeMo.svg)](http://hits.dwyl.com/fejao/rpi-WeMo)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/fejao/rpi-WeMo/issues)

rpi-WeMo
===============
Setting up your Raspberry Pi GPIO's to be controlled with *Amazon Alexa* using the *WeMo Emulation Server*
_____________________________________________

## 1 Install and start
```
sudo apt update
sudo apt install npm
sudo npm install alexa-raspberry -g
```
Clone this repository
```
git clone https://github.com/fejao/rpi-WeMo.git
```
Start the alexa WeMo server
```
cd rpi-WeMo
alexa-raspberry wemo-devices.json
```

## 1.1 Install service

See section [**4.2 Start at Boot**](#42-start-at-boot).

## 2 Set your LED's
Here are the schematics that I used to set my one.

![Alt text](pics/rpi-WeMo_v2.png?raw=true "Raspberry Connections 1")

The GPIO's that I'm using is:

  **17** Yellow LED

  **18** Blue LED

  **22** Red LED

  **23** Green LED

![Alt text](pics/GPIO_pins_output.png?raw=true "Raspberry Connections 2")

## 3 Customizing

### 3.1 gpio-set.py Script
  Script usage:
  ```
  ./gpio-set.py [-h] [-v] [-s SET_LED] [-g GPIO_NUM] [-c COLOR_NAME]
  ```

### 3.2 Optional arguments:

| Argument                                  | Description                                     |
|-------------------------------------------|-------------------------------------------------|
|**-h, --help**                             | Show help message and exit                      |
|**-v, --verbose**                          | Increase output verbosity                       |
|**-s SET, --set SET**                      | Set the GPIO ('on', 'off'), default: off        |
|**-g GPIO_NUM, --gpio-num GPIO_NUM**       | Number for the GPIO input, default: 18          |
|**-c COLOR_NAME, --color-name COLOR_NAME** | Color name: 'blue', 'green', 'red' and 'yellow' |

  The default *GPIO* is set to be use the **18**, you can change this at the script over the *global variable* **DEFAULT_GPIO**

  ```
  DEFAULT_GPIO = <YOUR_DEFAULT_GPIO>
  ```
#### 3.2.1 Examples

  For example to turn **on** the **17** GPIO you should run:
  ```
  ./gpio-set.py -s on -g 17
  ```
  And to turn **off** the **23** GPIO you should run:
  ```
  ./gpio-set.py -s off -g 23
  ```

### 3.3 `wemo-devices.json` file

  Feel free to use different *GPIO's*, just don't forget to update the **devices.json** file and change from:
  You can change the file to use your own command:

  ```
  {
      "<YOUR_COMMAND_NAME_HERE>":{
        "oncommand": "./gpio-set.py -s on -g <YOUR_GPIO_NUMBER> -v"
        "offcommand": "./gpio-set.py -s off -g <YOUR_GPIO_NUMBER> -v"
      }
  }
  ```

  For example, to use the command as **Test LED** using the **22** GPIO you should use as:
  ```
  {
      "Test LED":{
        "oncommand": "./gpio-set.py -s on -g 22 -v"
        "offcommand": "./gpio-set.py -s off -g 22 -v"
      }
  }
  ```

## 4 - Start the Server

### 4.1 Starting the Server manually
  To start the **alexa-raspberry server**, please run it at the same directory that you have your **.json** configuration file. For example, using the **wemo-devices.json** from this repository:
  ```
  cd rpi-WeMo

  ls -la
    drwxr-xr-x  4 pi pi  4096 May  5 14:41 .
    drwxr-xr-x 18 pi pi  4096 May  5 14:45 ..
    drwxr-xr-x  8 pi pi  4096 May  5 14:41 .git
    -rw-r--r--  1 pi pi 35141 May  5 14:41 LICENSE.md
    -rw-r--r--  1 pi pi  4263 May  5 14:41 README.md
    -rwxr-xr-x  1 pi pi  2577 May  5 14:41 gpio-set.py
    drwxr-xr-x  2 pi pi  4096 May  5 14:41 pics
    -rwxr-xr-x  1 pi pi   516 May  5 14:41 wemo-devices.json

  alexa-raspberry wemo-devices.json
  ```

### 4.2 Start at Boot
  You could also add it to the startup of the system creating a *systemd* file.  

#### 4.2.1 Create a systemd file

      Create the file
      ```
      sudo nano /lib/systemd/system/alexawemo.service
      ```

#### 4.2.2 Add content to the file
      Please don't forget to check that your **WorkingDirectory** is the address from where you have your configuration file, for example:

      ```
      [Unit]
      Description=Alexa Wemo emulation server for RaspberryPi
      After=network.target

      [Service]
      #WorkingDirectory=<LOCATION_OF_wemo-devices.json_AND_gpio-set.py_FILE>
      WorkingDirectory=/home/pi/rpi-WeMo
      ExecStart=/usr/local/bin/alexa-raspberry wemo-devices.json
      Restart=always
      RestartSec=10
      StandardOutput=syslog
      StandardError=syslog
      SyslogIdentifier=Alexa-Raspberry

      [Install]
      WantedBy=multi-user.target system start
      WantedBy = multi-user.target
      ```

#### 4.2.3 Enable the service
    In order to use every single restart, it is needed to enable the service
    ```
    sudo systemctl enable alexawemo.service
    ```

#### 4.2.4 Start the service
    For running the service you can use the regular **systemd** command from now on, for example:
    ```
    sudo systemctl start alexawemo.service
    ```

## 5 Add devices to Alexa
Just ask *Alexa* it to look for new devices, like:

**Alexa, search for devices**

## 6 Use it
Set the command to *Alexa*:

**Alexa, turn on Blue LED**

## 7 Video using it
[![Alt text](https://img.youtube.com/vi/9fiR6n89Ilc/0.jpg)](https://www.youtube.com/watch?v=9fiR6n89Ilc)

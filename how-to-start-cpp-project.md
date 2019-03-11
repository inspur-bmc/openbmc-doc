# How to start a cpp project in openbmc

Here is an examle of how to start a cpp project in openbmc.

 **repositories**:  [code repo](https://github.com/inspur-bmc/scratchpad)  [recipse repo](https://github.com/inspur-bmc/scratchpad-bb)



## 1 build system

### 1.1 autotools

#### 1.1.1 recipse 

   	1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/
   	2. git clone https://github.com/inspur-bmc/scratchpad-bb.git
   	3. git checkout 1.1

#### 1.1.2 code 

        1. cd /tmp
        2. git clone  https://github.com/inspur-bmc/scratchpad.git
        3. cd scratchpad 
        4. git checkout 1.1

#### 1.1.3 Test

1. devtool modify scratchpad 
2. bitbake scratchpad 
3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx

### 1.2  cmake

#### 1.2.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 1.2

#### 1.2.2 code 

1. cd /tmp/scratchpad
2. git checkout 1.2

#### 1.2.3 Test

1. devtool modify scratchpad 

2. bitbake scratchpad 

3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx


## 2 service file

### 2.1 autotools

#### 2.1.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 2.1

#### 2.1.2 code 

1. cd /tmp/scratchpad
2. git checkout 2.1

#### 2.1.3 Test

1. devtool modify scratchpad 
2. bitbake scratchpad 
3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx
4. reboot bmc
5. jourctl /usr/sbin/scratchpad (bmc side)

### 2.2 cmake

#### 2.2.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 2.2

#### 2.2.2 code 

1. cd /tmp/scratchpad
2. git checkout 2.2

#### 2.2.3 Test

1. devtool modify scratchpad 
2. bitbake scratchpad 
3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx
4. reboot bmc
5. jourctl /usr/sbin/scratchpad (bmc side)

## 3 dbus interface 

### 3.1 dbus client

#### 3.1.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 3.1

#### 3.1.2 code 

1. cd /tmp/scratchpad
2. git checkout 3.1

#### 3.1.3 Test

1. devtool modify scratchpad 

2. TODO.....

3. bitbake scratchpad 

4. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx

5. ./scratchpad 


### 3.2 dbus service

#### 3.2.1 sdbusplus asio

#### 1.2.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 1.2

#### 1.2.2 code 

1. cd /tmp/scratchpad
2. git checkout 1.2

#### 1.2.3 Test

1. devtool modify scratchpad 

2. bitbake scratchpad 

3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx


#### 3.2.2 sdbusplus common

#### 1.2.1 recipse 

1. cd openbmc/meta-xxx/meta-xxx/recipse-phosphor/scratchpad
2. git checkout 1.2

#### 1.2.2 code 

1. cd /tmp/scratchpad
2. git checkout 1.2

#### 1.2.3 Test

1. devtool modify scratchpad 

2. bitbake scratchpad 

3. devtool deploy-target  scratchpad root@xxx.xxx.xxx.xxx











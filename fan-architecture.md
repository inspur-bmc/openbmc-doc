# openbmc 风扇配置

openbmc风扇管理主要由三个模块组成

- phosphor-fan-presence-tach  检测风扇的在位状态

- phosphor-fan-monitor        监控风扇是否正常

- phosphor-fan-control        风扇控制


## phosphor-fan-presence-tach

### 概述

该模块主要功能是检测风扇的在位状态,并更新xyz.openbmc_project.Inventory.Manager管理的
风扇在位信息.检测方法有两种:

1. gpio   需配置为[gpio-keys][2],可以参考[romulus][3]中的gpio-keys节点
        
2. tach   一个风扇有n个转子,只要能检测到有一个转子在转,就判断为在位

对于一个风扇,可以定义多个检测方法.phosphor-fan-presence-tach提供两种检测策略

1. fallback

        默认风扇在位,若当前活跃的检测方法(默认第一个检测方法为活跃检测方法)认为风扇不在位,只要所配置的
        任何一个其他检测方法(x方法)认为在位,则认为当前活跃的检测方法检测结果不正确.采用x方法
        为当前活跃检测方法.活跃检测方法只有一个

2. anyof

        所配置的检测方法中,只要其中一个认为风扇在位,即认为风扇在位

### 配置文件

可以参考源码目录下的[example][1]


### 调试方法

1. devtool modify phosphor-fan
2. vim presence/example/config.yaml
3. bitbake phosphor-fan
4. scp phosphor-fan-presence-tach root@bmc:/tmp
5. ./phosphor-fan-presence-tach &  
6. 验证结果

        busctl introspect xyz.openbmc_project.Inventory.Manager \
        /xyz/openbmc_project/inventory/system/chassis/motherboard/fan0 \
        xyz.openbmc_project.Inventory.Item

        .Present        property      b        true   emits-change    writable
        .PrettyName     property      s       "fan0"  emits-change    writable
       
### 代码结构
        
                         +------> tach ------------
        PresenceSensor   |              template  | ->  PolicyAccess
                         +------> gpio ------------  

                         +-----> AnyOf
        RedundancyPolicy | 
                         +-----> Fallback
        

#### 伪代码

```c++
class Tach :public PresenceSensor
{
        ...
        virtual RedundancyPolicy& getPolicy() = 0;
}

/**
 * T      gpio/tach 
 * Policy ConfigPolicy(generated.hpp)
 */
template <typename T, typename Policy>    
class PolicyAccess : public T
{
	  /** 
           * @brief Get the associated policy.
           */
          RedundancyPolicy& getPolicy() override
          {   
              return *Policy::get()[policy];
          }

}


```
       
      
### c++ trips

[reference_wrapper][4]
      

## phosphor-fan-monitor


## phosphor-fan-control



[1]: https://github.com/openbmc/phosphor-fan-presence/blob/master/presence/example/example.yaml
[2]: https://github.com/torvalds/linux/blob/master/Documentation/driver-api/gpio/drivers-on-gpio.rst
[3]: https://github.com/torvalds/linux/blob/master/arch/arm/boot/dts/aspeed-bmc-opp-romulus.dts
[4]: https://oopscenities.net/2012/08/09/reference_wrapper/

# INA219 library for STM32 (HAL)
## Overview
This code was tested using a Nucleo-F446RE. I suggest copy and pasting the code into a new program that you generated using the CUBEIDE. This update to the library is supposed to add more documentation and functionality. What was trying to be done was creating a library with ready to go files for a battery monitoring system with relevant statstics to be generated using the INA219 current sensor. This project was made in C using the HAL library. I made this fork so I can give the original creaters credit for thier contribution while providing my own additions as well. 

## Getting Started
### Wiring 
This wiring diagram may day arduino because I originally tested the sensor using the Arduino Uno, but You can just substitute it with any ST Electronics Nucleoboard too. 
In The CUBEIDE enable a I2C channel. I used I2C1 using PB9 SDA and PB8 SCL
![INA219 Wire Diagram V5](https://user-images.githubusercontent.com/22525956/154888623-ae271b2a-56ff-4d45-8c85-52d51f438c5e.png)

### Explanation 
The functions that were added from the original version include:
* INA219_ReadPower
* INA219_GetBatteryLife
* INA219_GetDeltaTime_ms
* INA219_GetTotalPowerUsed
* INA219_GetMiliWattsDeltaTime
* INA219_GetAVGMiliWatt
* INA219_HealthCheck

In the INA219.c file each function has comments explaining what it is supposed to do and how you should intereact ith it like what parameters to pass and what the return variables represent. 

Once you get your INA219 wired up with the resistors and connected to the microcontroller you will need to add the INA219.h and INA219.c files to your project. In this reporistory they are located in the same directory as the main.c file in the /core/src folder. From there create a new struct instance in your main.c file code like this `INA219_t ina219;`. 

Now that you have your instance decalred in your main code, go ahead and run the initialize function like this:
```
  while(!INA219_Init(&ina219, &hi2c1, INA219_ADDRESS))
   {

   }
 ```

You will now use the ina219 instace address as one of the parameters used in one of the many functions avilable. For example, lets say I want to know the instantaneous power levels of battery. After reading the annotations in the INA219.c file I see that it requires requires a pointer to the ina219 struct instance that we created so we past the following code to do this ` power = INA219_ReadPower(&ina219);` Now if we UART transmit the contents of power we should get some reading in miliwatts. If you look in the INA219.h file you'll see that the struct 
`typedef struct
{
	I2C_HandleTypeDef 	*ina219_i2c;
	uint8_t				Address;
} INA219_t;`
This is what we are passing as a parameter to our function. The address to the instance we made from this struct. That way the function's know what I2C pins to use when we try to read and write to registers. 

Now that you have an idea how to assemble the code, read through the comments and annotations in the INA219.c file to see what functions suit your needs.

### Building the Project 
The reason for that 100 ohm resistor is to provide a load coming out of the VIN - terminal from the INA219 sensor. This needs to go to ground to complete the circuit. There are some tests that were ran in excel to show the comaprisons between a 10 ohm resistor and 100 ohm resistor and you can see that the results become more reliable when a 100ohm or 110 ohm resistor was used. That is why that value was chosen to be included in the wire diagram. 

## Usage 
The results aren't perfect, there are some voltage drop offs. For instance, when you take a multimeter and see the voltage on the VIN+ terminal on the INA219 sensor and the VIN- terminal on the sensor. You will see a small difference. This is because there is a small resistor built in to the sensor to help with the comparisonwhen calculating the voltage, current, and power levels being fed into the sensor. But this does give a pretty good view of what is going on and the numbers would be lower than they actually are which can help safe gaurd against a battery dying. If you know, for example, the battery cannot go below 0.8V, when the sensor sees that the current voltage is 0.8V, there is a possiblility of your battery actually being a little above this level. So this shouldn't be too much of a concern. 

When in doubt view the datasheet. 

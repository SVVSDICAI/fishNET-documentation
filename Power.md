# Power
![electrical diagram](./Media/electrical-diagram.svg)
## Power supply box
the wiring in the power supply box is set up to be easy to deactivate any of the 3 components: The batteries, The (solar) power input, and The power output. each subsystem can be turned off temporarily for testing, but will also be deactivated in the case of a short circuit to prevent damage to other components.

## Power input
- [ ] find the power output of the solar panel
The power supply box takes power from a solar panel, and feeds it into a [charge controller](https://www.amazon.com/EEEKit-Charger-Controller-Intelligent-Regulator/dp/B07QZXMWLM/ref=sr_1_12?crid=3O0SO82HOLDOX&keywords=hqst%2Bcharge%2Bcontroller%2B20A&qid=1654116486&sprefix=h%2Caps%2C108&sr=8-12&th=1), which prevents the batteries from being overcharged, which can lead to damage to the battery and become a fire hazard.

## Power output
Power is output from the power supply in 2 12v POE ports on the side of the box which is used to supply power to the [underwater enclosure](enclosure.md) and to [connectivity equipment](connectivity.md). 

## Batteries
- [ ] find the energy storage of the batteries
Power is stored in 2 lead acid batteries wired in parallel. The batteries hold enough power to sustain the bare minimum system for over a week.

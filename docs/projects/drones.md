1. <a href="#section:Builds" data-reference-type="ref" data-reference="section:Builds">Builds</a>.
2. <a href="#section:Races" data-reference-type="ref" data-reference="section:Races">Races</a>.

## Builds <span id="section:Builds" label="section:Builds"></span>

Building drones from scratch might seem a bit tricky at first, specially fitting all the components in a very reduced space and tuning it correctly. As racing drones fly in manual/acro mode, the pilot wants as much control over it as possible, thus weight must be kept low and keep the minimum number of components. A super simplified wiring diagram can be found below ([Image source](https://dronenodes.com/how-to-build-a-drone/)). 

<div  style="text-align: center">
<figure>
<img class="image-container" src="https://drive.google.com/uc?export=view&id=1j1iKtgAs5x-ix-eWr1EcB4Mr1O64bKoi"/><figcaption aria-hidden="true">FPV Racer wiring diagram</figcaption>
</figure>
</div>

All the components are powered from the power board connection to the battery. The Electronic Speed Controllers (ESCs) are managed via PWM from the flight controller, which also provides the camera signal with the On Screen Display (OSD) data to be sent via de video transmitter. In order to control the drone, the radio receiver communicates with the flight controller using PWM (Pulse Width Modulation), PPM (Pulse Position Modulation) or a faster protocol called SBUS (Directly encodes packets of data).

Below you can find some of my creations being wired up. As you might observe, the ESCs can come separate or in a 4in1 format, with the tradeoff of compactness vs flexibility in repairing. During the first year it took me two entire afternoons to get a drone up in the air. Now I can get it up and flying in 3-4h! Check out some of my finished flying machines as well!


<div style="text-align:center">
<table>
<tbody>
<tr class="odd">
<td style="text-align: center;"><figure>
<img width="70%" src="https://drive.google.com/uc?export=view&id=1JxmtMV7n-xMKTM3XGfkkBaSyAx_ne5Hh" />
</figure></td>
<td style="text-align: left;"><figure>
<img width="100%" src="https://drive.google.com/uc?export=view&id=1TwXr-RnZYTK5cWMfJSk_bgOtUW6P41iK" />
</figure></td>
</tr>

<tr class="odd">
<td style="text-align: center;"><figure>
<img width="80%" src="https://drive.google.com/uc?export=view&id=1xeaY4dFC9lNUR926E7q-dD4hjlJiRHoX" />
</figure></td>
<td style="text-align: left;"><figure>
<img width="100%" src="https://drive.google.com/uc?export=view&id=1Oi377t6Y1CnYR59B7eRUAYbwY8xzEolc" />
</figure></td>
</tr>
</tbody>
</table>
</div>

After the harware came the configuration and setup! It is always necessary to make it fly, but in racing, you gotta make it fly FAST! Which means, parameter tuning. Sometimes I would do it visually and intuitively, and to fine tune it, there is always the blackbox analysis! Which looks something like below ([Image Source](https://github.com/betaflight/betaflight/issues/6006)). That allowed me to observe my flight maneouvers and see what to change in the PID and manual control settings, as well as to debug possible flight failures.

<div  style="text-align: center">
<figure>
<img class="image-container" src="https://drive.google.com/uc?export=view&id=1k8Xx0kDlaQ4CQtC80gE7lfQ5l0xSggoo"/><figcaption aria-hidden="true">FPV Racer wiring diagram</figcaption>
</figure>
</div>

## Races <span id="section:Races" label="section:Races"></span>

After building, configuring, and hours of disciplined training, came the races! I tested three different sides, piloting solo, piloting as a team (MPS), team managing (MPS) and event orchestrator ([Euskadi Drone Racing](https://web.archive.org/web/20180805221625/http://euskadidroneracing.com/)). We managed to achieve a few top positions and to setup great nation-wide events. 

<div style="text-align:center">
<table>
<tbody>
<tr class="odd">
<td style="text-align: center;"><figure>
<img width="70%" src="https://drive.google.com/uc?export=view&id=1NYrEuW9aqfilJjyVgec841gC5eqLVqRg" />
</figure></td>
<td style="text-align: left;"><figure>
<img width="80%" src="https://drive.google.com/uc?export=view&id=1bBgaRqqTQ33GGB_ksb7YBhoVMjI6cqK0" />
</figure></td>
</tr>

<tr class="odd">
<td style="text-align: center;"><figure>
<img width="80%" src="https://drive.google.com/uc?export=view&id=1lFwNwApNa_qDkD3stPzfdyS6OdMrz3vZ" />
</figure></td>
<td style="text-align: left;"><figure>
<img width="90%" src="https://drive.google.com/uc?export=view&id=1uGssfGTXH6iaf_U2MD3I4OmqfXNjclYM" />
</figure></td>
</tr>
</tbody>
</table>
</div>

The experience in this hobby has been memorable. All the values and lessons learnt, the community, and the adrenaline of flying under pressure have literally changed my life. And of course, with all of this came the crashes!

<div  style="text-align: center;">
<figure>
<img  class="image-container" style="width:40%" src="https://drive.google.com/uc?export=view&id=1SeW5MHTVP2K7sYdpcx8ponTnLRC17YYc"/><figcaption aria-hidden="true">My drone says goodbye and thanks you for reading!</figcaption>
</figure>
</div>
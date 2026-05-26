<h1>BCA143 Laboratory Activity 9</h1>
<h2>Digital-to-Analog Converter (DAC) Signal Generator</h2>

<p>
  <strong>Course:</strong> BCA143 Firmware Programming<br>
  <strong>Board:</strong> RT-Thread RT-Spark Development Board (STM32F407)<br>
  <strong>Tools:</strong> STM32CubeIDE, STM32CubeMX, Oscilloscope
</p>

<hr>

<h2>About This Project</h2>

<p>
  This project uses the onboard 8-bit DAC of the STM32F407 to generate three
  different waveforms: square, ramp, and sine. The output appears on pin
  <strong>PA4 (DAC channel 1)</strong> and is observed on an oscilloscope.
  The waveform type is switched by changing a single line of code and
  re-flashing the board.
</p>

<hr>

<h2>Project Documentation</h2>

<h3>1. STM32CubeMX DAC Configuration</h3>
<p>
  DAC OUT1 enabled on pin PA4, output buffer enabled, trigger set to None
  (the DAC is updated directly from software).
</p>
<p align="center">
<img src="https://github.com/user-attachments/assets/0f8a4efe-4c5e-43e9-b582-13d35bb03976" alt="CubeMX DAC configuration" width="800"/>
</p>

<h3>2. STM32CubeMX Clock Configuration</h3>
<p>
  Clock tree configured for 168 MHz HCLK using the internal HSI oscillator
  and PLL. This sets the CPU speed used by the microsecond delay function.
</p>
<p align="center">
<img src="https://github.com/user-attachments/assets/63280e2e-b2fd-42a7-8bc7-9d010ef2ab07" alt="CubeMX clock configuration" width="800"/>
</p>

<h3>3. STM32CubeMX Debug Configuration</h3>
<p>
  SYS debug mode set to Serial Wire (SWD) so the board can be reprogrammed
  without recovery steps.
</p>
<p align="center">
<img src="https://github.com/user-attachments/assets/2331b78e-a49c-491e-ac06-406c940a1da4" alt="CubeMX debug configuration" width="800/>
</p>

<h3>4. Full Lab Setup</h3>
<p>
  RT-Spark board connected to the PC via the USB-DBG port for flashing and
  power, with the oscilloscope probe attached to read the DAC output signal.
</p>
<p align="center">
<img src="https://github.com/user-attachments/assets/3b291ce7-9160-4dae-990c-d844f01c60b3" alt="Full lab setup" width="400"/>
</p>

<h3>5. Probe Connection on PA4</h3>
<p>
  Oscilloscope probe tip on pin PA4 (DAC channel 1 output), with the ground
  clip on a GND pin. This is where the generated waveform is measured.
</p>
<p align="center">
<img src="https://github.com/user-attachments/assets/6d784158-cdbf-4562-962f-41a49ed06a4b" alt="Probe close-up on PA4" width="400"/>
</p>

<h3>6. Square Wave Output</h3>
<p>
  Square wave captured on the oscilloscope. Voltage swings between roughly
  0 V and 3.3 V with a measured period of about 6.4 ms (~156 Hz). Rise and
  fall times are in the hundreds of nanoseconds.
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/719f1e8a-2695-47b9-a189-7facf9229d69" alt="Square wave on oscilloscope" width="400"/>
</p>

<h3>7. Ramp Wave Output</h3>
<p>
  Ramp wave captured on the oscilloscope, showing the visible staircase
  effect. Because the DAC uses 8-bit resolution with 64 steps per cycle,
  the rising edge climbs in 64 small voltage jumps instead of a smooth slope.
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/6e2bff30-51fb-4c5a-9a3f-bde3a8f441a7" alt="Ramp wave on oscilloscope" width="400"/>
</p>

<h3>8. Sine Wave Output</h3>
<p>
  Sine wave generated using a 64-sample lookup table. At a low frequency, the
  curve looks smooth on the scope, with only minor stair-stepping visible
  when zoomed in closely.
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/c86fae2b-5736-4ec3-be6e-41faefb0194b" alt="Sine wave on oscilloscope" width="400"/>
</p>

<hr>

<h2>Laboratory Questions and Answers</h2>

<h3>Section 3.1 - Square Wave</h3>

<h4>Q1. What is the minimum output voltage?</h4>
<p>
  About <strong>0 V</strong>. This is the lowest voltage the DAC can produce.
  In practice you might see something close to 0 V (like 0.1 V) because the
  output buffer cannot quite reach zero.
</p>

<h4>Q2. What is the maximum output voltage?</h4>
<p>
  About <strong>3.3 V</strong>. This matches the chip's power supply voltage
  (VDDA). The signal swings between 0 V and 3.3 V.
</p>

<h4>Q3. What is the rise time of the output signal?</h4>
<p>
  Around <strong>200 to 600 nanoseconds</strong>. Rise time means how fast
  the signal jumps from low to high (measured from 10% to 90% of the full
  voltage). You can read this directly from the oscilloscope's Measure menu.
</p>

<h4>Q4. What is the fall time of the output signal?</h4>
<p>
  Around <strong>200 to 600 nanoseconds</strong>. Same idea as rise time, but
  measured when the signal drops from high to low. It is usually very close
  to the rise time.
</p>

<h4>Q5. What is the period of the output signal?</h4>
<p>
  About <strong>6.4 milliseconds</strong> (which is about 156 Hz). The math
  is simple:
</p>
<p>
  <code>64 steps x 100 microseconds per step = 6,400 us = 6.4 ms</code>
</p>

<hr>

<h3>Section 3.2 - Ramp Wave</h3>

<h4>Q6. Why is the rising edge of the ramp wave not smooth?</h4>
<p>
  The DAC can only make <strong>256 different voltage levels</strong> because
  it is 8-bit. The code uses 64 steps to climb from low to high, so the output
  makes 64 small voltage jumps instead of one smooth slope. This is why the
  rising edge looks like a <strong>staircase</strong>, not a clean ramp.
</p>

<h4>Q7. How can you smooth the rising edge in hardware?</h4>
<p>
  Add a <strong>low-pass filter</strong> on the DAC output pin. The easiest
  version is an RC filter:
</p>
<ul>
  <li>A 1 kilo-ohm resistor in series with the DAC output</li>
  <li>A 100 nF capacitor from the resistor to ground</li>
</ul>
<p>
  The capacitor charges and discharges slowly, which smooths out the sharp
  voltage jumps and turns the staircase into a smoother slope.
</p>

<h4>Q8. How can you smooth the rising edge in software?</h4>
<p>
  Two simple ways:
</p>
<ol>
  <li>
    <strong>Use more steps.</strong> Change <code>NUM_STEPS</code> from 64
    to 256. More steps means each voltage jump is smaller, so the ramp
    looks smoother.
  </li>
  <li>
    <strong>Use the full 12-bit DAC.</strong> Change <code>MAX_DAC_CODE</code>
    to <code>0xFFF</code> and <code>DAC_ALIGN_8B_R</code> to
    <code>DAC_ALIGN_12B_R</code>. This gives the DAC 4,096 levels instead of
    256, so each step becomes 16 times smaller.
  </li>
</ol>

<hr>

<h3>Section 3.3 - Sine Wave</h3>

<h4>Q9. What is the maximum frequency sine wave that can be generated?</h4>
<p>
  You find this by experimenting. Start with a long delay between steps and
  keep making it shorter. Watch the oscilloscope. When the sine wave starts
  looking distorted or stops getting faster, you have hit the limit.
</p>

<p>
  The output frequency is calculated using:
</p>
<p>
  <code>frequency = 1 / (64 x period_us x 0.000001)</code>
</p>

<p>Quick reference table:</p>
<ul>
  <li><code>period_us = 100</code> -> about 156 Hz</li>
  <li><code>period_us = 50</code> -> about 312 Hz</li>
  <li><code>period_us = 10</code> -> about 1,560 Hz</li>
  <li><code>period_us = 5</code> -> about 3,125 Hz</li>
  <li><code>period_us = 2</code> -> about 7,800 Hz (close to the limit)</li>
</ul>

<p>
  In our testing, the maximum usable frequency was about
  <strong>3 to 8 kHz</strong>. The limit happens because the code takes a
  small amount of time to do each step (function calls, writing to the DAC,
  and looping). Once the delay between steps becomes shorter than this time,
  the sine wave cannot get any faster.
</p>

<hr>

<h2>Files in This Repository</h2>

<ul>
  <li><code>main.c</code>: main application code with DAC and waveform logic</li>
  <li><code>Lab9_DAC_SignalGen.ioc</code>: STM32CubeMX configuration file</li>
  <li><code>images/</code>: photos and oscilloscope screenshots</li>
</ul>

<hr>

<h2>How to Build</h2>

<ol>
  <li>Open <strong>STM32CubeIDE</strong></li>
  <li>Import the project folder</li>
  <li>Click the hammer icon to <strong>Build</strong></li>
  <li>Click the play icon to <strong>Flash</strong> to the RT-Spark board</li>
  <li>Connect the oscilloscope probe to pin <strong>PA4</strong> and ground clip to <strong>GND</strong></li>
  <li>To change the waveform, edit this line in <code>main.c</code>:
    <code>wavetype wave = SINE;</code> (use <code>SQUARE</code>, <code>RAMP</code>, or <code>SINE</code>), then rebuild and flash</li>
</ol>

<hr>

<p>
  <em>Prepared by Jhanaloden Dipantar</em><br>
  <em>May 2026</em><br>
  <em>BCA143 Firmware Programming</em><br>
  <em>Mindanao State University - Iligan Institute of Technology</em>
</p>

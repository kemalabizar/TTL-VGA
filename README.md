# TTL-VGA
This project was inspired by Ben Eater's attempt at [Creating The Worst Video Card](https://www.youtube.com/watch?v=l7rce6IQDWs), where he modeled a 640x480 pixel VGA screen driver by using 7400-series TTL logic ICs. The following project attempts to expand aforementioned screen driver's capability, by enabling it to access the same RAM used by CPU for processing data via interrupt mechanism.

## VGA Screen Timing
For a VGA screen to properly display pixels, the states of each VGA connector pins must adhere to the following scheme, also known as screen timing. At the very basic level, each horizontal and vertical lines are divided into four timing segments: Visible Area **VA**, where pixels are loaded to the display area; Front Porch **FP**, Back Porch **BP** and Sync Pulse **SP** for adjustment of pixel positions (both horizontally and vertically). The timings for each segments in both horizontal and vertical axes, as shown in the following example (640x480px @60Hz display mode, pixel clock 25.175MHz), are available at [TinyVGA](http://www.tinyvga.com/vga-timing).
<p align="center">
  <img width="224" height="431" alt="image" src="https://github.com/user-attachments/assets/e4214b9c-5e76-4fdc-ba2d-0d18399da697">
</p>

## Changing Timing Schemes into Logical Circuit
Since a single horizontal cycle (called line cycle) are based on pixel clock counts, one may implement a `2^N`-bit counter that accomodates the entire range of pixel counts. Based on the example above for 640x480 60Hz display mode, one may use a 10-bit counter (`0..1024`) that counts upward on clock-edge triggers (can either be up or down). Resetting the counter can be done by adding `N`-bit AND gate that triggers when maximum pixel/line count is reached, its output connected to counter reset. Shown in the image below, the 4th AND gate from top triggers when horizontal counter reaches maximum value `0d800` (`0x320`). Same thing can be implemented to vertical counter, where in this case, the 8th AND gate from top triggers as vertical counter's value reaches `0d525` (`0x20D`).
<p align="center">
  <img width="680" height="434" alt="image" src="https://github.com/user-attachments/assets/1b9e559c-5e3a-40ba-89c4-5280d033a7af" />
</p>

## Adjusting For Different Display Modes
To adjust the screen driver circuit for various display modes, one may commit so by adjusting counter bit width to fit maximum pixel/line count (horizontal/vertical), along with the corresponding AND value inputs and their inversion state. Shown below are driver circuit modified from 640x480 to 800x600.
<p align="center">
  <img width="680" height="451" alt="image" src="https://github.com/user-attachments/assets/1c7c57b3-60b8-47ad-a200-ae190de298a0" />
</p>

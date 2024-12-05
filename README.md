1. Lighting Control
Features:
Remote lighting control (via smartphone or tablet)
Scheduling and programming (automatically turning on/off at specific times)
Sensor-based automation (motion sensors)
2. Heating, Ventilation, and Air Conditioning (HVAC)
Features:
Smart thermostats for temperature control
Remote access and adjustments
Energy efficiency (using learning algorithms)
3. Security Systems
Features:
Smart locks and door sensors
Remote monitoring with security cameras
Alarm systems and motion detection
4. Audio and Video Systems
Features:
Smart speakers and audio systems for music control
Content management with streaming devices
Home theater system automation
5. Smart Appliances and Kitchen
Features:
Smart refrigerators, ovens, and coffee makers
Cooking recipes and timers
Remote control and automatic settings
6. Entertainment Systems
Features:
Integration with media players and streaming services
Automation for gaming consoles and home theater systems
Multi-room audio and video distribution
7. Garden and Outdoor Automation
Features:
Smart irrigation systems
Outdoor lighting control
Outdoor security systems
8. Energy Management
Features:
Energy consumption monitoring and reporting
Smart plugs and energy-saving solutions
Integration with solar panels
9. Health and Lifestyle
Features:
Smart health monitoring devices (heart rate, sleep tracking)
Air quality sensors
Meditation and relaxation applications




//this code was written on vivado
// topmodule
`timescale 1ns / 1ps
module air_conditioning_control(
input x1,      // window1
output reg y   // Air Conditioner
);
always @(*) begin
    if (x1 == 0 && x2 == 0)
        y = 1'b1;  // Both windows are closed, air conditioner is on (Y = 1)
    else
        y = 1'b0;  // At least one window is open, air conditioner is off (Y = 0)
end

endmodule
// testbench
`timescale 1ns / 1ps
module tb_air_conditioning_control();
reg x1;
reg x2;
 wire y;
// Instantiate the module under test (UUT)
air_conditioning_control uut (
.x1(x1),
.x2(x2),
.y(y)
);
initial begin
$monitor("X1=%b X2=%b Y=%b", x1, x2, y);
  // Test all combinations of x1 and x2
x1 = 0; x2 = 0; #10; // Both windows closed, air conditioner should be on
x1 = 0; x2 = 1; #10; // One window open, air conditioner should be off
x1 = 1; x2 = 0; #10; // One window open, air conditioner should be off
 x1 = 1; x2 = 1; #10; // Both windows open, air conditioner should be off
$finish;
 end
endmodule

//structural modeling
`timescale 1ns / 1ps

module air_conditioning_control(
    input x1,     // Window 1
    input x2,     // Window 2
    output y      // Air Conditioner
);

   wire not_x1;  // x1'in NOT çıkışı
    wire not_x2;  // x2'in NOT çıkışı
    wire and_out; // NOT kapılarının AND çıkışı

   // NOT kapıları
    not u1(not_x1, x1);
    not u2(not_x2, x2);

  // AND kapısı
    and u3(and_out, not_x1, not_x2);

  // Air conditioner output
    assign y = and_out;

endmodule



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



Steps to Proceed
Understand the System Requirements:

Two inputs (x1 for Window 1 and x2 for Window 2).
Output (y for Air Conditioner control).
The Air Conditioner (y) is:
ON (1) if both windows are closed (x1 = 0, x2 = 0).
OFF (0) if either or both windows are open.
State Table Diagram: Define the states for the system:

States (S0-S3):

S0: Both windows closed (x1 = 0, x2 = 0) → Air Conditioner ON.
S1: Window 1 open (x1 = 1, x2 = 0) → Air Conditioner OFF.
S2: Window 2 open (x1 = 0, x2 = 1) → Air Conditioner OFF.
S3: Both windows open (x1 = 1, x2 = 1) → Air Conditioner OFF.
State Transitions: Transitions occur based on the input changes (x1, x2).

Current State	x1	x2	Next State	y (AC)
S0	0	0	S0	1
S0	0	1	S2	0
S0	1	0	S1	0
S0	1	1	S3	0
S1	0	0	S0	1
S1	0	1	S2	0
S1	1	0	S1	0
S1	1	1	S3	0
S2	0	0	S0	1
S2	0	1	S2	0
S2	1	0	S1	0
S2	1	1	S3	0
S3	0	0	S0	1
S3	0	1	S2	0
S3	1	0	S1	0
S3	1	1	S3	0
Verilog Code for Sequential System
Based on the state table, we implement a state machine in Verilog.

State Encoding:
S0: 00
S1: 01
S2: 10
S3: 11
Sequential Logic Design:
Here is the Verilog code for the state machine:

`timescale 1ns / 1ps

module air_conditioning_fsm(
    input clk,        // Clock signal
    input reset,      // Reset signal
    input x1,         // Window 1
    input x2,         // Window 2
    output reg y      // Air Conditioner control
);

  // State encoding
    parameter S0 = 2'b00, // Both windows closed
              S1 = 2'b01, // Window 1 open
              S2 = 2'b10, // Window 2 open
              S3 = 2'b11; // Both windows open

   reg [1:0] state, next_state;

   // State transition logic (combinational)
    always @(*) begin
        case (state)
            S0: if (x1 == 0 && x2 == 0) next_state = S0;
                else if (x1 == 1 && x2 == 0) next_state = S1;
                else if (x1 == 0 && x2 == 1) next_state = S2;
                else next_state = S3;

 S1: if (x1 == 0 && x2 == 0) next_state = S0;
                else if (x1 == 1 && x2 == 1) next_state = S3;
                else if (x1 == 0 && x2 == 1) next_state = S2;
                else next_state = S1;
    S2: if (x1 == 0 && x2 == 0) next_state = S0;
                else if (x1 == 1 && x2 == 1) next_state = S3;
                else if (x1 == 1 && x2 == 0) next_state = S1;
                else next_state = S2;

 S3: if (x1 == 0 && x2 == 0) next_state = S0;
                else if (x1 == 1 && x2 == 0) next_state = S1;
                else if (x1 == 0 && x2 == 1) next_state = S2;
                else next_state = S3;
      default: next_state = S0;
        endcase
    end

 // State update logic (sequential)
    always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0;
        else
            state <= next_state;
    end

// Output logic
    always @(*) begin
        case (state)
            S0: y = 1; // Air Conditioner ON
            default: y = 0; // Air Conditioner OFF
        endcase
    end

endmodule

`timescale 1ns / 1ps

module tb_air_conditioning_fsm();
    reg clk;
    reg reset;
    reg x1, x2;
    wire y;

 // Instantiate the FSM
    air_conditioning_fsm uut (
        .clk(clk),
        .reset(reset),
        .x1(x1),
        .x2(x2),
        .y(y)
    );

  // Clock generation
    initial begin
        clk = 0;
        forever #5 clk = ~clk; // 10 ns clock period
    end

  // Stimulus
    initial begin
        $monitor("Time=%0d, X1=%b, X2=%b, Y=%b", $time, x1, x2, y);

 // Initialize inputs
        reset = 1; x1 = 0; x2 = 0;
        #10 reset = 0;

  // Test cases
        #10 x1 = 0; x2 = 0; // Both windows closed
        #10 x1 = 1; x2 = 0; // Window 1 open
        #10 x1 = 0; x2 = 1; // Window 2 open
        #10 x1 = 1; x2 = 1; // Both windows open
        #10 x1 = 0; x2 = 0; // Back to initial state

  #20 $finish;
    end
endmodule

`timescale 1ns / 1ps

module air_conditioning_fsm(
    input clk,        // Clock signal
    input reset,      // Reset signal
    input x1,         // Window 1 input
    input x2,         // Window 2 input
    output reg y      // Air Conditioner control output
);

 // State encoding
  parameter S0 = 2'b00, // Both windows closed
              S1 = 2'b01, // Window 1 open
              S2 = 2'b10, // Window 2 open
              S3 = 2'b11; // Both windows open

   reg [1:0] state, next_state;

 // State transition logic (combinational)
  always @(*) begin
        case (state)
            S0: if (x1 == 0 && x2 == 0) next_state = S0; // Stay in S0
                else if (x1 == 1 && x2 == 0) next_state = S1; // Go to S1
                else if (x1 == 0 && x2 == 1) next_state = S2; // Go to S2
                else next_state = S3; // Go to S3

S1: if (x1 == 0 && x2 == 0) next_state = S0; // Go to S0
                else if (x1 == 1 && x2 == 1) next_state = S3; // Go to S3
                else if (x1 == 0 && x2 == 1) next_state = S2; // Go to S2
                else next_state = S1; // Stay in S1

 S2: if (x1 == 0 && x2 == 0) next_state = S0; // Go to S0
                else if (x1 == 1 && x2 == 1) next_state = S3; // Go to S3
                else if (x1 == 1 && x2 == 0) next_state = S1; // Go to S1
                else next_state = S2; // Stay in S2

 S3: if (x1 == 0 && x2 == 0) next_state = S0; // Go to S0
                else if (x1 == 1 && x2 == 0) next_state = S1; // Go to S1
                else if (x1 == 0 && x2 == 1) next_state = S2; // Go to S2
                else next_state = S3; // Stay in S3
    default: next_state = S0; // Default state
        endcase
    end

// State update logic (sequential)
 always @(posedge clk or posedge reset) begin
        if (reset)
            state <= S0; // Reset to initial state
        else
            state <= next_state; // Update to the next state
    end

 // Output logic (combinational)
 always @(*) begin
        case (state)
            S0: y = 1; // Air Conditioner ON
            default: y = 0; // Air Conditioner OFF
        endcase
    end

endmodule

`timescale 1ns / 1ps

module tb_air_conditioning_fsm();
    reg clk;
    reg reset;
    reg x1, x2;
    wire y;

    // Instantiate the FSM
 air_conditioning_fsm uut (
        .clk(clk),
        .reset(reset),
        .x1(x1),
        .x2(x2),
        .y(y)
    );

    // Clock generation
 initial begin
        clk = 0;
        forever #5 clk = ~clk; // 10 ns clock period
    end

    // Test cases
  initial begin
        $monitor("Time=%0d, X1=%b, X2=%b, Y=%b", $time, x1, x2, y);

        // Initialize inputs
 reset = 1; x1 = 0; x2 = 0;
        #10 reset = 0;

        // Test various states
 #10 x1 = 0; x2 = 0; // Both windows closed
        #10 x1 = 1; x2 = 0; // Window 1 open
        #10 x1 = 0; x2 = 1; // Window 2 open
        #10 x1 = 1; x2 = 1; // Both windows open
        #10 x1 = 0; x2 = 0; // Back to initial state

   #20 $finish;
    end
endmodule




//lastproject

`timescale 1ns / 1ps

module home_automation(
    input clk,
    input reset,
    input motion_sensor,
    input x1,
    input x2,
    input door_sensor,
    input remote_light,
    output reg light_status,
    output reg ac_status,
    output reg alarm_status,
    output reg energy_usage
);

// Light Control (simple example using motion sensor or remote control)
    always @(posedge clk or posedge reset) begin
        if (reset)
            light_status <= 0;
        else
            light_status <= motion_sensor || remote_light;
    end

 // HVAC Control
    always @(posedge clk or posedge reset) begin
        if (reset)
            ac_status <= 0;
        else
            ac_status <= (x1 == 0 && x2 == 0);
    end

 // Security Alarm
    always @(posedge clk or posedge reset) begin
        if (reset)
            alarm_status <= 0;
        else
            alarm_status <= door_sensor;
    end

  // Energy Usage (Example: On if either AC or lights are active)
    always @(posedge clk or posedge reset) begin
        if (reset)
            energy_usage <= 0;
else
            energy_usage <= light_status || ac_status;
   end

endmodule


//testbench
`timescale 1ns / 1ps

module tb_home_automation();

// Inputs and Outputs
    reg clk;
    reg reset;
    reg motion_sensor;
    reg x1, x2;
    reg door_sensor;
    reg remote_light;
    wire light_status;
    wire ac_status;
    wire alarm_status;
    wire energy_usage;

 // Instantiate the `home_automation` module
    home_automation uut (
        .clk(clk),
        .reset(reset),
        .motion_sensor(motion_sensor),
        .x1(x1),
        .x2(x2),
        .door_sensor(door_sensor),
        .remote_light(remote_light),
        .light_status(light_status),
        .ac_status(ac_status),
        .alarm_status(alarm_status),
        .energy_usage(energy_usage)
    );

  // Clock Generation
    initial begin
        clk = 0;
        forever #5 clk = ~clk; // 10ns clock period
    end

// Test Cases
    initial begin
        $monitor("Time=%0d | Motion=%b | X1=%b | X2=%b | Door=%b | Remote=%b || Light=%b | AC=%b | Alarm=%b | Energy=%b",
                 $time, motion_sensor, x1, x2, door_sensor, remote_light, light_status, ac_status, alarm_status, energy_usage);

  // Initialize Inputs
        reset = 1;
        motion_sensor = 0;
        x1 = 0; x2 = 0;
        door_sensor = 0;
        remote_light = 0;

   // Reset System
        #10 reset = 0;

  // Test Case 1: Motion detected
        #10 motion_sensor = 1; // Lights on
        #10 motion_sensor = 0;

  // Test Case 2: Remote light control
        #10 remote_light = 1; // Lights on
        #10 remote_light = 0;

  // Test Case 3: HVAC Control
        #10 x1 = 0; x2 = 0; // AC on
        #10 x1 = 1;         // AC off
        #10 x1 = 0; x2 = 1; // AC off
        #10 x1 = 1; x2 = 1; // AC off
        #10 x1 = 0; x2 = 0; // AC on

  // Test Case 4: Door sensor
        #10 door_sensor = 1; // Alarm on
        #10 door_sensor = 0;

 // End Simulation
        #50 $finish;
    end

endmodule



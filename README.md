# Exp-6b-Traffic-Light-Controller
# Aim
To design and simulate a Verilog HDL for Traffic Light Controler

# Apparatus Required
Vivado 2023.1
Spartan 7 FPGA
# Procedure
1. Launch Vivado 2023.1
Open Vivado 2023.1 and create a new project.
Select RTL Project and enable Do not specify sources at this time.
2. Create Verilog Design File
Create a new Verilog design file.
Type the Verilog code for the Traffic Light Controler

3. Observe the Output
Observe the Traffic Signal output.

# Block Diagram(Schematic)

<img width="1324" height="622" alt="image" src="https://github.com/user-attachments/assets/30f7dda7-edea-4f95-8c75-7207383ca65d" />


# Code

```
module traffic_light_controller(
    input clk, rst,
    output reg [2:0] light  // {Red, Yellow, Green}
);

    // State encoding
    parameter [1:0] RED = 2'b00, GREEN = 2'b01, YELLOW = 2'b10;
    reg [1:0] state, next_state;
    reg [3:0] count;  // simple counter for delay

    // State register and counter
    always @(posedge clk or posedge rst) begin
        if (rst) begin
            state <= RED;
            count <= 0;
        end else begin
            state <= next_state;
            // Reset counter when state changes
            if (state != next_state)
                count <= 0;
            else
                count <= count + 1;
        end
    end

    // Next-state logic
    always @(*) begin
        next_state = state;
        case (state)
            RED:    if (count == 4) next_state = GREEN;   // 4 cycles Red
            GREEN:  if (count == 6) next_state = YELLOW;  // 6 cycles Green
            YELLOW: if (count == 2) next_state = RED;     // 2 cycles Yellow
        endcase
    end

    // Output logic ({Red, Yellow, Green})
    always @(*) begin
        case (state)
            RED:    light = 3'b100; // Red ON
            YELLOW: light = 3'b010; // Yellow ON
            GREEN:  light = 3'b001; // Green ON
            default:light = 3'b000;
        endcase
    end

endmodule

```

# Test Bench

```
`timescale 1ns/1ps

module tb_traffic_light;
    reg clk, rst;
    wire [2:0] light;

    // Instantiate the DUT (Device Under Test)
    traffic_light_controller uut (
        .clk(clk),
        .rst(rst),
        .light(light)
    );

    // Clock generation: 10 ns period (100 MHz)
    initial clk = 0;
    always #5 clk = ~clk;

    // Stimulus
    initial begin
        rst = 1;
        #10 rst = 0;      // release reset after 10 ns
        #200 $finish;     // run simulation for 200 ns total
    end

    // Monitor output
    initial begin
        $monitor("Time=%0t | Lights={Red,Yellow,Green}=%b", $time, light);
    end

    
    end
endmodule

```

# Output

<img width="1920" height="1080" alt="Screenshot 2025-10-22 104803" src="https://github.com/user-attachments/assets/dc745e7a-5f42-43f6-8de9-e304874bee76" />


# Result

The Verilog code for the Traffic Light Controller was designed and simulated successfully.
The output verified correct traffic light operation.


Download Link: https://assignmentchef.com/product/solved-cs2110-project2-lc-3-datapath
<br>
Hello and welcome to Project 2! In this project, you’ll start by implementing some basic sequential logic elements. Then, you’ll build three missing pieces of the LC-3 hardware which were removed as well as writing the microcode for the bulk of LC-3 instructions.

<strong>Please read through the entire document before starting</strong>. Often times things are elaborated on below where they are introduced, so reading the entire document can give you a better grasp on things. <strong>Start early </strong>and if you get stuck, there’s always Piazza or office hours.

<h2><a name="_Toc15877"></a>1.1         Latches</h2>

The first thing you’ll implement are sequential logic elements. These allow us to retain data in a circuit, in other words ’remember’ a previous value. This allows us to have logic that depends on the last event that happened in a sequence of events, in other words sequential logic.

The goal of this section is to produce a register. A register is an edge-triggered sequential logic circuit element that can store a binary value. For more on this section, see Implementation: Latches

<h2><a name="_Toc15878"></a>1.2         The LC-3</h2>

The LC-3 datapath we’ve discussed in class contains a lot of pieces very similar to circuits we’ve seen or even made before (e.g. an ALU, a register file with 8 edge-triggered general purpose registers, a RAM unit, etc.). One piece we’ve mostly referred to as a “black-box” in the past is the Micro-controller. It’s responsible for controlling the entire datapath, and getting it to properly execute the instructions that we give it. That’s a big task! So how does the Micro-controller actually work? In this project, we’ll build a few datapath components to develop some familiarity with the LC-3, and then we’ll actually write the “microcode” which allows the Micro-controller to function.

The micro-controller, shown above, is a finite state machine. It has 59 possible states (holy cow!), and because it is implemented in the “binary reduced” style, it needs 6 bits to store all its possible states. It also has 49 output bits of output flags, including 10 which are used to determine the next state and 39 which extend throughout the datapath to control other pieces of the LC-3. That would be a lot of very complex hardware—<strong>if it were built entirely in hardware</strong>.

It turns out there is an easier way. We can actually use a ROM (Read-only Memory) in order to specify the behavior of each distinct state in the state machine (e.g. each instruction will map to a series of entries in the ROM. Each entry in the ROM represents something called a micro-state, which is an individual state of the finite state machine and a potentially a <em>component </em>of a slightly larger sequence of states known as a macro-state. FETCH is a macro-state, and each of the execute stages of LC-3 instructions is also a macro-state. Each of the macro-states will require between 1-5 micro-states to complete, depending on the complexity of the instruction.

What does a ROM entry look like? We encourage you to go ahead and open up microcode.xlsx, on the microcode sheet, to follow along.

A ROM entry is basically a long binary string. The last few bits of it cover the transition to the next state—you don’t need to worry about this at all during this project, so we’ve covered it in dark grey on the right. Do NOT modify the NEXT bits, or stuff will break. Each of the other bits corresponds to a signal asserted onto the datapath during that clock cycle. For this project, we only require that you implement 19 of the signals asserted onto the datapath (notice that three of these are 2-bit signals).

We’ve also simplified and removed a number of micro-states which aren’t directly a part of the LC-3’s main instructions. There are only 36 micro-states in this project. We’ve also given you the microcode for some of these micro-states. Your task is to fill in the rest and finish the LC-3 micro-controller!

<h2><a name="_Toc15879"></a>1.3         LC3 Files Provided</h2>

<ul>

 <li>sim – a large CircuitSim file containing the LC-3 <strong>AND </strong>a “Manual LC-3” which does not need to be modified in any way but is simply present as a tool for you while writing microcode. You should only modify the CC-logic, PC, and ALU subcircuits in this file.</li>

 <li>xlsx – an Excel document in which you will write your microcode. <strong>Do not touch cells that have been blacked out</strong></li>

 <li>dat – a text file which you can paste your microcode into and then import into the LC-3.</li>

 <li>tests/ – a subdirectory which contains a number of test cases you can use to verify the functionality of your circuit and microcode.</li>

 <li>proj2-tester-1.0.jar – a local tester you can use to verify the functionality of your CC-logic, PC, and ALU subcircuits. This tester is also available on Gradescope (where it will be a part of your grade).</li>

 <li>LC-3InstructionsDetail.pdf – a PDF with descriptions and pseudocode for each instruction.</li>

</ul>

<h2><a name="_Toc15880"></a>1.4         Elements to complete</h2>

<ol>

 <li>Implement the ALU, PC, and CC-Logic subcircuits in sim.</li>

 <li>Complete the lc-3 microcode in xlsx, in the microcode sheet.</li>

</ol>

<h1><a name="_Toc15881"></a>2           Implementation</h1>

<h2><a name="_Toc15882"></a>2.1         Latches</h2>

For this part of the assignment you will build your own register from the ground up. For more information about each subcircuit refer to your textbook.

<h3><a name="_Toc15883"></a>2.1.1         RS Latch</h3>

You will start by building a RS latch using NAND gates, as described in your textbook. RS Latch is the basic circuit for sequential logic. It stores one bit of information, and it has 3 important states:

<ol>

 <li>R=1 S=1 : This is called the <strong>Quiescent State</strong>. In this state the latch is storing a value, and nothing is trying to change that value.</li>

 <li>R=1 S=0 : By changing momentarily from the Quiescent State to this state, the value of the latch ischanged so that it now stores a 1.</li>

 <li>R=0 S=1 : By changing momentarily from the Quiescent State to this state, the value of the latch ischanged so that it now stores a 0.</li>

</ol>

Once you set the bit you wish to store, change back to the Quiescent State to keep that value stored. Notice that the circuit has two output pins; one is the bit the latch is currently storing, and the other is the opposite of that bit.

<strong>Note: </strong>In order for the RS Latch to work properly, you must not set both R and S to 0 at the same time.

<ul>

 <li>Build your circuit in the “RS Latch” subcircuit in the “latches.sim” file</li>

</ul>

<h3><a name="_Toc15884"></a>2.1.2         Gated D Latch</h3>

Using your RS latch subcircuit, implement a Gated D Latch as described on the textbook. The Gated D Latch is made up of an RS Latch as well as two additional gates that serve as a control. With that addition not only can we control what value is stored by the latch, but also when that value will be saved. The value of the output can only be changed when Write Enable is set to 1. Notice that the Gated D Latch subcircuit only has one output pin, so you should disregard the inverse output of your RS Latch.

<ul>

 <li>Implement this circuit in the “Gated D Latch” subcircuit in the “latches.sim” file</li>

 <li>You are not allowed to use the built-in SR Flip-Flop in CircuitSim to build this circuit</li>

</ul>

<h3><a name="_Toc15885"></a>2.1.3         D Flip-Flop</h3>

Using the Gated D Latch circuit you built, create a D Flip-Flop. A D Flip-Flop is composed of two Gated D Latches back to back, and it implements edge triggered logic. Your D Flip-Flop output should be able to change on the <strong>rising edge</strong>, which means that the state of the D Flip-Flop should only be able to change at the exact instant the clock goes from 0 to 1.

<ul>

 <li>Implement this circuit in the “D Flip-Flop” subcircuit in the “latches.sim” file.</li>

</ul>

<h3><a name="_Toc15886"></a>2.1.4         Register</h3>

Using the D Flip-Flop you just created, build a 4-bit Register. Your register should also use edge-triggered logic. The value of the register should change on the rising edge.

<ul>

 <li>This circuit will be implemented in the “Register” subcircuit in the “latches.sim” file</li>

</ul>

<h2><a name="_Toc15887"></a>2.2         Completing the LC3 SubCircuits</h2>

Note: <strong>DO NOT move the position of the inputs and outputs </strong>in the LC3 subcircuits; this could break the rest of the LC3 simulator.

<h3><a name="_Toc15888"></a>2.2.1         ALU</h3>

You will need to build the ALU (Arithmetic Logic Unit) subcircuit in LC3.sim.

The ALU will perform one of 4 functions and Output it depending on the ALUK signal:

<ol>

 <li><strong>ALUK = 0b00: </strong>A + B</li>

 <li><strong>ALUK = 0b01: </strong>A &amp; B</li>

 <li><strong>ALUK = 0b10: </strong>NOT A</li>

 <li><strong>ALUK = 0b11: </strong>PASS A</li>

</ol>

You should be able to use what you learned from HW3 to populate this subcircuit easily.

<h3><a name="_Toc15889"></a>2.2.2         PC</h3>

You will need to build the PC (Program Counter) subcircuit in LC3.sim.

The PC is a 16 bit register that holds the address of the next instruction to be executed. During the <strong>FETCH </strong>stage, the contents of the PC are loaded into the memory address register (MAR), and the PC is updated with the address of the next instruction. There are three scenarios for updating the PC:

<ol>

 <li>The contents of the PC are incremented by 1. <strong>Selected when PCMUX = 0b00</strong>.</li>

 <li>The result of the ADDR (an address-adding unit) is the address of the next instruction. The outputfrom the ADDR should be stored in the PC. This occurs if we use the branching instruction, (BR). <strong>Selected when PCMUX = 0b01</strong>.</li>

 <li>The value on the BUS is the address of the next instruction. The value on the BUS should be storedinto the PC. An example of this functionality is the JMP instruction.</li>

</ol>

<strong>Selected when PCMUX = 0b10</strong>.

The PC should only be loaded on a rising clock edge when the LD.PC signal is on.

Ensure that you don’t reach the unused case (PCMUX = 0b11) of the circuit, or else spooky stuff might happen (undefined behavior in LC-3).

<h3><a name="_Toc15890"></a>2.2.3         CC-Logic</h3>

The LC-3 has three condition codes: N (negative), Z (zero), and P (positive). These codes are saved on the next rising edge after the LC-3 executes Operate or Load instructions that include loading a result into a general purpose register, such as ADD and LDR.

<strong>For example, if ADD R2, R0, R1 results in a positive number, then NZP = 001.</strong>

The LC-3 appendix on canvas should help determine which instructions set the Condition Code.(See page 5 pf PattPatelAppA.pdf)

The CC subcircuit should set N to 1 if the input is negative, set Z to 1 if the input is zero, and set P to 1 if the input is positive. Only 1 bit in NZP should be set at any given time. Zero is not considered a positive number.

Bit 2 (the MSB) is N, Bit 1 is Z, Bit 0 is P.

With that in mind, set the correct bit and implement this circuit in the CC-Logic subcircuit.

<strong>Implement this circuit in the subcircuit CC-Logic.</strong>

Hint: you can use a comparator for this subcircuit! They are found in the Arithmetic tab in CircuitSim.

<h3><a name="_Toc15891"></a>2.2.4         Checking Your Work</h3>

Once complete, run the autograder

java -jar proj2-tester-1.0.jar

<strong>inside the Docker container </strong>in the command prompt in the same directory as your LC3.sim file.

If all of the relevant tests pass, you’ve completed this part of the project. Congrats!

<h2><a name="_Toc15892"></a>2.3         Using Manual LC-3 (for testing only)</h2>

<ul>

 <li>Once you have your PC, CC-Logic, and ALU complete you can begin working on the meat of this project, understanding how LC-3 works.</li>

 <li>In lecture and lab we have covered the signals in the datapath and how they are used when tracing an instruction.</li>

 <li>The first thing you will want to do is use the Custom-Bus, GateBUS, and LD.IR signals to set a custom IR value on the next rising edge. Until you figure out the states for fetch, you can use these steps to set your IR with any instruction you want to work on.</li>

 <li>Once you have an idea of how fetch works, you can load some instruction(s) in the RAM. In order to do that:

  <ol>

   <li>right-click the RAM near the bottom of the Manual LC-3 circuit</li>

   <li>select “edit contents”</li>

   <li>click “Load from file”</li>

   <li>locate and select one of the provided test files in the project (ex: addand.dat)</li>

   <li>close the edit contents menu</li>

  </ol></li>

 <li>Now that you have loaded the RAM with a program, you can fetch instructions into the IR.</li>

 <li>Now that you have an instruction in the IR, you can start executing it. In order to do that you can turn on the different signal pins on the right in order to control the datapath and move data around like we did in lecture/lab. Once you think you know how an instruction is executed, you can enter it into the microcode spreadsheet, the process for which is outlined below.

  <ul>

   <li>Tests inside the tests/ directory have a comment at the end of the .asm file which explains the system state after the end of the program’s execution. You must ensure that this is the system state after you have run every instruction sequentially through the simulator.</li>

   <li>To test whether the program acted correctly, go to the ‘LC-3’ circuit and double-click into the ‘REG FILE’ element <strong>that is placed in the datapath</strong>. Note: you <strong>should not </strong>just click into the ‘REG FILE’ subcircuit, as this will not properly load the state of the specific ‘REG FILE’ element that’s built into the LC-3, just some generic REG FILE.</li>

   <li>Bonus tip: Use Ctrl-R to reset the simulator state and easily clear RAM and registers to 0 in order to test again.</li>

  </ul></li>

 <li>If you are familiar with LC-3 assembly (you will learn it over the course of the next two weeks), you are welcome to write your own test programs to verify your code. Make sure that your programs <strong>do not </strong>start at x3000, as in this project <em>only </em>we will start execution at x0000 for simplicity’s sake. You can compile LC-3 assembly projects to machine code by following the LC-3 ISA, and then create your own RAM.dat files. We can’t guarantee that we’ll be able to help with these test cases in office hours, though.</li>

</ul>

<h2><a name="_Toc15893"></a>2.4         Writing the Microcode</h2>

Note: For this section, <strong>DO NOT MODIFY the DRMUX or SR1MUX signals</strong>, or the bits labelled

NEX in the microcode.xlsx excel sheet (these signals are all greyed out).

<ul>

 <li>Now that you’ve developed some familiarity with the datapath and gotten a chance to “act as the micro-controller” and run the execute stage of instructions on the Manual LC-3, you can complete the final part of the project: writing the microcode for a number of micro-instructions on the LC-3!</li>

 <li>In xlsx Excel document, <strong>microcode </strong>sheet, there exist a number of macro-states. Among them are FETCH and the execute stages for most instructions supported by the LC-3 (we’ve removed several macro-states related to trap and interrupt handling). For each macro-state, we’ve provided space for the micro-states which will make up that macro-state, and for each micro-state, we’ve handled all of the logic related to transitioning to the next micro-state. We’ve also implemented a small subset of the macro-states in order to provide inspiration to you, our students (you’re welcome).</li>

 <li>You should complete all the remaining macro-states by filling in their micro-states.</li>

</ul>

<strong>– </strong>If you notice that the output column to the right of the blacked-out columns (column AH) is showing artifacts like #NAME?, try opening the Excel spreadsheet in your Georgia Tech Office 365 Online Excel workspace. Sign in to Office 365 with Georgia Tech credentials, select ‘Excel’, and then choose ‘Upload and open’ to edit the excel sheet online.

<h3><a name="_Toc15894"></a>2.4.1         How to Test your Micro-code</h3>

At any time that you want to test your micro-code, you can export it from the .xlsx file and apply it to LC-3 hardware by following these steps. <strong>IMPORTANT NOTE: Passing all of the tests provided does not guarantee that you have a functional datapath, any number of coincidences could cause you to get the correct output with incorrect functionality. As always, we reserve the right to grade with additional test cases.</strong>

<ol>

 <li>At the bottom of the xlsx file select the <strong>output </strong>tab.</li>

 <li>Copy all of column D from row 1 through row 64</li>

 <li>Paste the result into ROM.dat</li>

 <li>Ensure that ROM.dat is in the same directory as ./cs2110docker.sh (or some subdirectory)</li>

 <li>In Docker CircuitSim, open LC3.sim. Navigate to the ‘Fsm’ subcircuit. This circuit contains themicro-controller.</li>

 <li>Right-click on the ROM and select “Edit contents.”</li>

 <li>Select “Load from file”</li>

 <li>navigate to and select “ROM.dat.” This will load the ROM.</li>

 <li>Navigate to the ‘LC-3’ subcircuit.</li>

 <li>You can now load a program into the RAM, following the instructions above in the Manual LC-3 sections.</li>

 <li>To run the LC-3, you can manually click through the CLK signal or use ‘Ctrl-K’ to start or stop theautomatic clock. After your program has stopped executing (you can tell when it’s finished running because it will HALT and the datapath will stop changing).</li>

 <li>Tests inside the tests/ directory have a comment at the end of the .asm file which explains the system state after the end of the program’s execution. To test whether the program acted correctly, go to the ‘LC-3’ circuit and double-click into the ‘REG FILE’ element <strong>that is placed in the datapath</strong>. Note: you <strong>should not </strong>just click into the ‘REG FILE’ subcircuit, as this will not properly load the state of the specific ‘REG FILE’ element that’s built into the LC-3, just some generic REG FILE.</li>

</ol>

<h3><a name="_Toc15895"></a>2.4.2         Tips, Tricks, Resources</h3>

This is all pretty crazy to take in at first. But it’s not the end of the world if you make sure to use the resources available to you. Here are some options:

<ul>

 <li>LC-3 Datapath Diagram: Canvas → Files → Resources → LC-3DatapathDiagram.pdf</li>

 <li>LC-3 ISA Quick Sheet: Canvas → Files → Resources → LC-3ISA.pdf</li>

 <li>LC-3 Instructions Detail Sheet: LC-3InstructionsDetail.pdf, in this project.zip</li>

 <li>The Manual LC-3!</li>

 <li>Your friendly TAs (Office Hours, Piazza, etc.)</li>

 <li>Textbook</li>

 <li>Appendix on Datapath Control Signals in <strong>this PDF</strong>.</li>

</ul>

<h1><a name="_Toc15896"></a>3           Setup</h1>

The software you will be using for this project and all future circuit based assignments is called CircuitSim – an interactive circuit simulation package.

In order to use CircuitSim, you must have Docker up and running. If you do not have Docker, follow the instructions laid out in the installation guide found under Canvas → Files → Docker. <strong>Make sure you are using the updated docker container!! The docker-script should automatically do this when you run it. Check by ensuring that the version of CircuitSim in the docker container is version</strong>

<strong>1.8.2</strong>

CircuitSim comes pre-installed on the Docker image, and should be accessible via the desktop. Please only use the CircuitSim through Docker to build your circuits as it is the correct version. <strong>CircuitSim downloaded elsewhere may not be compatible with our grader. You have been warned.</strong>

CircuitSim is a powerful simulation tool designed for educational use. This gives it the advantage of being a little more forgiving than some of the more commercial simulators. However, it still requires some time and effort to be able to use the program efficiently.

<strong>Please do not move or rename any of the provided circuits/elements. You should only be modifying the contents of the ALU, PC, and CC-Logic. The only things you need to modify outside of that is RAM/ROM contents and using the pins/buttons provided</strong>

<h1><a name="_Toc15897"></a>4           Deliverables</h1>

Please submit the follow files:

<ol>

 <li>sim</li>

 <li>sim</li>

 <li>xlsx</li>

</ol>

to Gradescope under the assignment “Project 2”.

<strong>Note</strong>: The autograder may not reflect your final grade on this assignment. We reserve the right to update the autograder as we see fit when grading.

<h1><a name="_Toc15898"></a>5           Demos</h1>

<strong>This project will be demoed. </strong>The demos will be ten minutes long and will occur in the College of Computing. Stay tuned for details as the due date approaches.

<ul>

 <li>Sign up for a demo time slot via Canvas <strong>before </strong>the beginning of the first demo slot. This is the only way you can ensure you will have a slot.</li>

 <li>If you cannot attend any of the predetermined demo time slots, e-mail the Head TA <strong>before </strong>the beginning of the first demo slot.</li>

 <li>If you know you are going to miss your demo, you can cancel your slot on Canvas with no penalty. However, you are <strong>not </strong>guaranteed another time slot. You cannot cancel your demo within 24 hours or else it will be counted as a missed demo.</li>

 <li>Your overall project score will be ((project_score * 0.5) + (demo_score * 0.5)). <strong>If you miss your demo you will not receive any of these points and the maximum you can receive on the project is 50%.</strong></li>

 <li>You will be able to makeup ONE of your demos at the end of the semester for half credit.</li>

 <li>This grading policy is harsh, but it ensures <strong>you </strong>understand your <strong>own </strong>[project].</li>

</ul>
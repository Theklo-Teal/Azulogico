
# Preamble
I'm self-taught with electronics design. Although I underwent a mechatronics course at a trade school, the electronics teaching there is very much introductory and I didn't learn much from it. But as you improve your understanding of things, in particular digital electronics, possibilities start coming up into the mind and the intrigue excited me. I started coming up and iterating on ideas for CPUs and their instructionsets, trying too if I could make something quirky and unique. But nothing I made is better than other people's projects in the homebrew computing circles.

"Azulogico" is a combination of "Azulejo" (portuguese tile) and "logic". It has a double-entendre, where the word "Azul" by itself means "blue", like the colour traditionally used for patterns in the tiles. It was the name I would like to give to an architecture design I would feel proud of developing myself. But I ran out of ideas to try out, reached a plateau of my skills in this area, and lost interest in that project. But I considered appropriating the name for something similar, yet diferent. Something you might say is simpler, but arguably more useful. A programmable logic device which is based on ROMs instead of having a CPU. I imagine it being used as a microcontroller and despite not having memory, nor control flow in its programming, I'm confident it could solve most situations that Arduinos are used for. At least in the hobbiest community.

# Intro

This system is capable of three actions (reminiscent of opcodes in CPUs):
- Transform a pattern in the input lines into a pattern at the output lines, or combinatory logic
- Trigger a state transition when a given pattern is found in the input ines.
- Trigger a routine when a given pattern is found in the input lines.

Although we use the language of "pattern of signals" as if considering a bunch of input lines (for example) as a whole, clever programming can be used to make this system behave as if only responding to one or a few lines at a time.
The FSM can work with up to 64 states, each can have 16 arbritary routines, which are burst or sequences of signals at output. Each routine can have up to 64 steps. Plus, there is an "Enter" and "Exit" routines which run automatically at state transition.

The combinatory logic patterns are different depending on the current state of the finite state machine. So each state is defined by its set of combination rules and their own routines.

Azulógico requires two main parts for its development:
- The hardware architecture, which is quite simple as it only implements a Finite State Machine.
- An IDE or Assembler that generates the look-up tables that load into the ROMs from a user-friendly interface.

# Hardware

![Media/ROM_Implementation_of_Binary_Counter.webp]
It is common knowledge that ROMs can be used to perform any combinatory logic. They are just lookup tables of what output to return given certain inputs. The basic working principle of Azulogico is the fact that by using feedback, you can also make ROM perform like a sequential logic device. One line "F" acts like an acknowledgement signal for when a free line "U" has transitioned high. This makes the line "U" behave like an edge triggered input. Moreover, other feedbacking lines can be used to keep an ID of current state, as in a Finite State Machine. The picture above uses that ID as the output of a binary counter.

This working principle is also used to develop the homebrew CPU titled [GRAY-1, by Olivier Bailleux](https://hackaday.com/2017/02/02/the-gray-1-a-computer-composed-entirely-of-rom-and-ram/).

## Architecture
Azulogico goes a bit further, and has a register to keep state, so ROMs are free to do more elaborate things. Several ROMs can be used, all reading from the state ID, to perform different actions dynamically. The Control ROM can be programmed to trigger a state transition when a certain pattern of inputs is given. Azulogico is able to take the next state immediately from the program, or by popping a stack representing an history of states. The stack is only pushed with immediate IDs, though.

When transitioning state, a short sequence of control signals, not much different to the mechanism of microcode in CPUs, will do the following:
- Execute an Exit ("Deconstructor") routine for the old state.
- Update the State ID register.
- Execute the Enter ("Constructor") routine for the new state.

The routines take a different set of input lines from the combinatory and trigger inputs. They may be tied together, or they can be tied to an external register as a form of flags for system status, as most CPUs do. This enables Azulogico to perform decisions by executing alternate variations of the routines according to their routine inputs. Same rules apply to arbritary routines which are called by trigger patterns, but don't transition state. Up to 16 variations or functions are possible in each routine and they can take up to 64 steps to perform, meaning they will change their output pattern in sequence at the speed of a clock signal.

The State ID can be also exposed, allowing for feedback mechanisms, like in a binary counter.

## Simulation
This design is drawn and simulated using [Logisim Evolution](https://github.com/logisim-evolution/logisim-evolution) and you can find a `.circ` file to view it.
![Media/ROM_MCU.png]


# Software

This is still a work in progress.

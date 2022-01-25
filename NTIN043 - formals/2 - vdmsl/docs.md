Smart Home
===

> task 1: create model for "smart home"
> 	- important aspects: sensors, control, security, other equipment
> 	- you have to decide what entities and operations to define
> 	- do not forget to define some assertions and commands (run, check)
> 
> task 2: document your solution
> 	- explain key design decisions and more advanced usage of VDM/Alloy
> 
> note: you can define the model in VDM or Alloy (pick one language)

The task is implemented in VDM-SL language.

## Task 1

Files of solution:
- helpers.vdmsl - contains some util functions
- main.vdmsl - contains all of the internal logic
- testSim.vdmsl - a test which tries to show the system in real-life scenario
- testSimple.vdmsl - a test using less variables while trying to showcase more combinations and overall usage

## Structure

After some iterations, I have chosen to follow the specific solution described below.
As I have no experience with smart home systems, the solution was improvised based on requirements and priorities also described below.

### General idea

The system should simulate a smart home system. This system consists of 2 main types of physical devices: input devices and output devices.
These 2 sets should be interconnected via programmable logic (by the user) to achieve the desired functionality.
Finally, there should be a manager which holds and manages all this information.

Once the functionality is set up, the system should follow the instructios. Once some conditions are met (input devices are in desired state),
output devices should respond to that by altering their state in correct way.

### Requirements

- The system should naturally behave correctly. Once conditions are met, response should follow.
- The set of input and output devices should be open to change, both adding and removing them should be possible.
- The functionality should be accessible and alterable by the user to create new / delete existing functions.

### Structure

The implementation consists of multiple modules (in architectural sense, not strictly in VDM-SL):

#### System

- The main manager which contains and manages all of the data and logic.
- It also provides API for external agents such as possible GUI for user.
- Notably, System is one which initiates the update of the whole system.
  - Once update loops starts (e.g. in set intervals), all user defined functions are iterated, for each conditions are evaluated, and if they are met, actions are taken to affect the output devices accordingly.

#### Emitter

- An abstraction for an input device.
- These include sensors (motion, heat...), monitors (clock, date), manual controls (swtiches, rotary knobs...).
- They all include:
  - ID - their internal identifier
  - Description - optional string used for easier identification by user
  - Data - the currently observed/monitored values (e.g. state of a switch, current time in case of a clock, lux measured in a light sensor...)
  - Data bounds - tuple of Data values setting the minimum and maximum for actual observed Data.

#### Absorber

- An abstraction for an output device.
- These include controllers or individual controls of physical devices (light control, window shutter control, LED color selector...)
- They all include:
  - ID - their internal identifier
  - Description - optional string used for easier identification by user
  - Status - their current state
  - Status bounds - tuple of Status values setting the minimum and maximum for actual observed Data.

#### Interaction

- The wrapper for a single functionality set by the user.
- Each consist of a set of **Conditions** and a sequence of **Actions**.
- **Condition** holds an ID of an emitter along with lambda function which returns bool for the Emitter's Data.
- **Action** holds an ID of an absorber along with desired new Status.
- When the system processes the Interaction, it first evaluates all Conditions. If all Conditions are met, all Actions are then ran, assigning new value to respective absorbers.
- Currently, only formulas consisting from AND and OR can be constructed. More thought is added in the later section.


### Conclusion and potential improvements

There are several points where the system could be substantially improved:
- Conditions could be capable of more complicated formulas.
  - This can be however fixed fairly easily by instead of putting the logic in the system, we could pass lambda functions in conditions to be evaluated with values from emitters.
- Because the system is responsible for update (and querying inputs), some data may be lost, which might be troublesome especially in case of critical components.
  - For example medical sensors or monitors responsible for residence security.
  - This issue isn't as easily solvable, but could be partially fixed by letting input devices store measurements in a buffer that would be passed to the system as a whole (and the buffer afterwards filtered by the system for intresting entries). Alternatively, input devices could be "informed" about what values to look out for (provided that they're "smart enough").
- System doesn't take into account the possibility of devices becoming unavailable.
  - In such case, the EmitterDataBounds record could be augmented by a **default** value (or even consider the initial value to be default), and pass this value to the computation whenever a device doesn't respond.

All in all there is definitely space for improvement but I believe this prototype gives a good foundation to most potential improvements that come to mind without bigger issues.
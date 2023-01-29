# Scheduler

Authors: @legendary-cookie

Date: 29.01.2023

### Introduction
- The scheduler should give each task a fair amount of processing time 
- The user should be able to see the multi-tasking

### Requirements
- A periodic interrupt (e.g. a timer)

### Architecture
- Instance of a scheduler on every core, steal work from each other if they have nothing to do
- Interrupt / wait for IO / ... => switch to next task

## Phase 1: Creating scheduler instances on each core
  ### Design
   - Store a pointer to the instance in a per-cpu structure (=> GS base & GS)
   - Last thing the kernel init should do is create the instance, and wait for the first interrupt / task switch to occurr (hlt)
  
  ### Data Model
  The Scheduler will have two queues:
  - The taskqueue: active tasks waiting to be scheduled
  - The waitqueue: inactive tasks (sleeping, waiting for IO, ...) 

  ### Breaking changes
   - None

## Phase 2: Switch tasks in the scheduler
  ### Design
   - Switching tasks should consist of the following steps:
    - Save all relevant registers to the current task context
    - Load all relevant registers from the next task context
  ### Data Model
  - Context: Registers, SSE state, Page map
  
  ### Breaking changes
  - None
 
## Phase 3: Work stealing
  ### Design
  - On task removal, we should reassociate tasks to another scheduler, so all schedulers have the best balancing possible
  - No CPU should be idle while enough tasks are available
  ### Data Model
  ### Breaking changes
  - Tasks may be stolen
<hr/>

### User Interface Design
- A user should be able to create/register new tasks to the scheduler. This should be handled with an exec() syscall

### Security
- None

### Deployment
- None

### Testing
- None

### Conclusion
- The scheduler is a core OS element. It handles all tasks.
- It switches to and from tasks, saving everything important for later use
- It makes sure all CPU cores are used

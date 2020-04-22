# Exercises part 2

## Prove busy counter example works as expected

### Aim

1. o_busy will *always* be true any time the counter is non-zero.
2. If the counter is non-zero, it should always be counting down
3. Assume that once raised, i_start_signal will remain high until it
    is both high and the counter is no longer busy.
    Following (i_start_signal)&&(!o_busy), i_start_signal is no
    longer constrained--until it is raised again.

### Method

Take a look at the .sby file. We have 2 tasks here: cover & bmc. When run with
no arguments; sby will run all the tasks. To run just one, provide the name of the task as an
argument to sby: sby -f busyctr.sby bmc

0. Write a cover statement that covers the start of the counter. Run it with sby -f busyctr.sby cover. Do you get the trace you expected?
1. Write a simple assertion to prove that o_busy works as expected.
2. Use $past to prove the counter is counting down.
3. Write an assumption to cover the behaviour of i_start_signal
4. You will need to make use of $past to make assumptions about i_start_signal.

## Prove that the traffic lights example works

### Aim

We already have some traces that show the traffic lights working.
Now we want to prove that each interaction will eventually provide the desired behaviour.

### Method

Open the liveness.sv file. We already have one property that uses SVA to prove that after
the pedestrian button is pressed, the pedestrian light will go green.

	liveness_pedestrian: assert property (
		pedestrian_button |-> ##[0:25] pedestrian_green
	);

Add another 3 properties in the liveness module that prove that:

* the turn_sensor will result in turn_green before 25 cycles
* up_green will happen before 25 cycles
* down_green will happen before 25 cycles

## Write an assertion that proves that the memory arbiter is safe

### Aim

qpimem_arbiter is part of the Hackaday Superconference 2019 badge.
A SOC was built using 2 RISCV CPUs that access a single off chip QPI memory.
We want to prove that the arbiter will never grant both CPUs access to the
memory at the same time.

### Method

Use the following signals to build an assertion:

    reg [$clog2(MASTER_IFACE_CNT)-1:0] active_iface;    // internal signal to show which master is currently active/connected
    input [MASTER_IFACE_CNT-1:0] do_read,               // which master wants to read
    input [MASTER_IFACE_CNT-1:0] do_write,              // which master wants to write
	output reg [MASTER_IFACE_CNT-1:0] is_idle,          // masters have to wait if is_idle is high

Extra credit:

* get some cover traces of the arbiter in action
* write some assumption that define well behaved masters & slaves

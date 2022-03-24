******************
What is Servo Mode
******************

On the positional operation programmed in the Motor Driver IC, it is
necessary to define the target position in advance. As a new target
position cannot be set until operation to be completed, you cannot
perform operation that changes the target position in real time. On the
Servo Mode, realtime target following operation is realized by the
process running as a Arduino sketch. It works similar to that of servo
motors in radio controlled toys. While the mode is in operation, other
function commands cannot be sent.

.. container:: embed-video

   .. raw:: html

      <iframe width="560" height="315" src="https://www.youtube.com/embed/1dd_bBqWpMQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>

   .. raw:: html

      </iframe>

*Example Behavior of Servo Mode*

*********************************
Initializing steps for Servo Mode
*********************************

============================
Starting and Ending Function
============================

The message `/enableServoMode`_ starts and ends the Servo Mode.
Upon starting the Servo Mode, the driver must not be in BUSY state.

============================
Updating the Target Position
============================

The target position can be updated by the
`/setTargetPosition`_ message. When the Arduino Sketch receives a
new target position, it will compare the new position with the current
one and change the rotation speed of the motor. You can send target
positions to all four motors at the same time
with `/setTargetPositionList`_.

***************************
Types of Control Parameters
***************************

The motor’s rotation speed is calculated by a technique called “PID
Control”. The control parameters can be set with the command
`/setServoParam`_. The available parameters are following three.

======================
Proportional Gain (kP)
======================

The PID control uses differences of current position and target position
(deviation) for the control. That is, it approaches target position by
rotating fast when the deviation is large, and rotates slow when the
deviation is small. The proportional gain defines how much influence to
the speed will be given from the deviation. If the value is too small,
it will take time to approach the target position, and if the value is
too large, an “overshoot” may occur in which the target position is
passed.

==================
Integral Gain (kI)
==================

If there is only the proportional control, the rotation speed will get
slower and takes very long time to compensate the offset when
approaching to the target position. In this case, adding the time
integral of the deviation to the control value will effectively
compensate the offset. By applying large integral gain, you could
compensate the offset quickly, however it may cause the overshoot, or
even the continuous oscillation by trying to compensate the overshoot.

======================
Differential Gain (kD)
======================

In case an overshoot or oscillation related errors occurs, this
parameter is used to eliminate steep changes in deviation.

**************************************
Methods for Determining PID Parameters
**************************************

=======================
Step by Step Procedures
=======================

PID Control Parameters must be determined from the actual acceleration,
deceleration, and the maximum rotation speed (speed profile). Determine
the control parameters by following steps.

1. Decide the KVAL(in case of current mode, TVAL) that is matched to the
   rated value and load.
2. Decide the operational acceleration, deceleration, and the maximum
   rotation speed (speed profile).
3. Adjust the PID control gains.

=================================
The Decisions of PID Control Gain
=================================

There are multiple methods for deciding the optimal PID Control Gain.
However, it may also depend on the factors like the objective of
movement, or the frequency of target position change. Therefore we
determine the values by steps described as follows and do trial and
error on the actual set up.

-----
1. kP
-----

Set all kP, kI, kD, to 0.0 and gradually raise the kP. When the target
position changes only sometimes, we often set only kP while keeping
other kI and kD values at 0.0.

-----
2. kI
-----

In case when the target position only changing once every couple of
seconds, you set the movement to quick and responsive by raising the kI
value. Yet for example, when the target position is sent at 60fps, the
acceleration towards the each new target position would cause the
vibration and loose smooth transition. Depending on the priority of the
quickly response to the target position or smooth movement for the whole
operation, the preferable values may change.

-----
3. kD
-----

We gradually raise the kD if oscillation or overshoot is observed when
approaching to the target.

.. _/enableServoMode: https://ponoor.com/docs/step-series/osc-command-reference/servo-mode/#enableservomode_intmotorid_boolenable
.. _/setTargetPosition: https://ponoor.com/docs/step-series/osc-command-reference/servo-mode/#settargetposition_intmotorid_intposition
.. _/setTargetPositionList: https://ponoor.com/docs/step-series/osc-command-reference/servo-mode/#settargetpositionlist_intposition1_intposition2_intposition3_intposition4
.. _/setServoParam: https://ponoor.com/docs/step-series/osc-command-reference/servo-mode/#setservoparam_intmotorid_floatkp_floatki_floatkd

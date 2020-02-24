# Trajectories

Now it's time to combine paths with the motion profiles from earlier. Road Runner calls this composition a trajectory. Trajectories take time values and output the corresponding field frame kinematic state (i.e., real positions, velocities, and acceleration). This state can be transformed to the robot frame and fed directly into the feedforward component of the controller.

Here's a sample for planning a `Trajectory` from a `Path`:

{% code-tabs %}
{% code-tabs-item title="Java" %}
```java
DriveConstraints constraints = new DriveConstraints(20, 40, 80, 1, 2, 4);
Trajectory traj = TrajectoryGenerator.generateTrajectory(path, constraints);
```
{% endcode-tabs-item %}

{% code-tabs-item title="Kotlin" %}
```kotlin
val constraints = DriveConstraints(20.0, 40.0, 80.0, 1.0, 2.0, 4.0)
val traj = TrajectoryGenerator.generateTrajectory(path, constraints)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The base `DriveConstraints` class does **robot** velocity and acceleration limiting, while its drive-specific subclasses \(e.g., `MecanumConstraints`\) actually limit **wheel** velocity.

{% hint style="info" %}
There is also a `TrajectoryBuilder` class that replicates the API of `PathBuilder` with a few additions.
{% endhint %}

Once a trajectory is finally generated, one of the `TrajectoryFollowers` can be used to generate the actual `DriveSignals` that are sent to the `Drive` class. The PIDVA followers are usually suitable, although `RamseteFollower` has noticeably better performance for tank drives.

Following a trajectory is as simple as creating a follower, calling `TrajectoryFollower.followTrajectory()`, and repeatedly querying `TrajectoryFollower.update()`:

{% code-tabs %}
{% code-tabs-item title="Java" %}
```java
PIDCoefficients translationalPid = new PIDCoefficients(5, 0, 0);
PIDCoefficients headingPid = new PIDCoefficients(2, 0, 0);
HolonomicPIDVAFollower follower = new HolonomicPIDVAFollower(translationalPid, translationalPid, headingPid);

follower.followTrajectory(traj);

// call in loop
DriveSignal signal = follower.update(poseEstimate);
```
{% endcode-tabs-item %}

{% code-tabs-item title="Kotlin" %}
```kotlin
val translationalPid = PIDCoefficients(5.0, 0.0, 0.0)
val headingPid = PIDCoefficients(2.0, 0.0, 0.0)
val follower = HolonomicPIDVAFollower(translationalPid, translationalPid, headingPid)

follower.followTrajectory(traj)

// call in loop
val signal = follower.update(poseEstimate)
```
{% endcode-tabs-item %}
{% endcode-tabs %}


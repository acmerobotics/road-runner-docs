# Feedforward Control

While PID or bang-bang control works well for many plants, we can do better. One of the biggest advantages of PID control is its ignorance of the underlying plant. The same three control actions are employed regardless of the relationship between the input and output variables. In retrospect, it's somewhat remarkable that PID in all its simplicity is so effective. Nevertheless, there are situations in which knowledge of the plant dynamics can help supplement PID control alone.

## Gravity Feedforward

For example, consider the problem of controlling the position of an elevator. A simple P controller may help to get close to the desired position. However, close to the septoint, the elevator will oscillate or stop short with a significant steady-state error. This could be addressed by adding some integral action to the controller. In theory, it will provide the necessary boost to reach the target position. Though as we know, integral terms can easily cause instability and need to be carefully tuned to prevent amplified oscillations.

There is a better solution to this problem. For a minute, consider the cause of this error. The culprit is clear from our physical intuition: gravity. When the elevator is in use, gravity is always exerting a constant downward force given by $$F = mg$$. Instead of letting an integral term do the work, we can directly compensate for the gravitational force. This can be achieved by adding a constant factor $$k_G$$ to the voltage that is sent to the motors. 

This can be accomplished using a `PIDFController` with a custom feedforward term:

{% code-tabs %}
{% code-tabs-item title="Java" %}
```java
PIDFController controller = new PIDFController(coeffs, 0, 0, 0, new Function1<Double, Double>() {
    @Override
    public Double invoke(Double position) {
        return kG;
    }
});
// or more concisely with lambdas
PIDFController controller = new PIDFController(coeffs, 0, 0, 0, x -> kG);
```
{% endcode-tabs-item %}

{% code-tabs-item title="Kotlin" %}
```kotlin
val controller = new PIDFController(coeffs, kF = { kG })
```
{% endcode-tabs-item %}
{% endcode-tabs %}

That's the essence behind feedforward: design your control inputs using a model of the plant.

## DC Motor Feedforward

In the realm of wheeled mobile robots, DC motors are important actuators on the drivetrain and various end effector. To a good approximation, DC motors are linear in normal operating ranges and ignoring friction. Assuming negligible inductance, the voltage necessary for a given velocity and acceleration is $$V_{app} = k_v \cdot v + k_a \cdot a$$ . \(A keen reader will notice that $$v$$ may be more aptly replaced by $$\omega$$ to better represent the rotary motion of a conventional motor. While this may be true, simple plants with DC motors often involve linear motion, and it's more convenient to fit a single constant for the whole transmission than convert for each transfer of energy. The concern is more pedantic than pragmatic.\) Finally, it is useful in practice to add a final term for friction: $$V_{app} = k_v \cdot v + k_a \cdot a + k_{static}$$ \(more details on DC motor feedforward can be found [here](https://www.chiefdelphi.com/t/paper-frc-drivetrain-characterization/160915)\). 

{% hint style="info" %}
$$k_{static}$$ should match the sign of the sum of the other two terms as the friction will always oppose the motion \(it isn't a purely additive constant\).
{% endhint %}

This DC motor feedforward is directly integrated into `PIDFController` and the `Drive` classes:

{% code-tabs %}
{% code-tabs-item title="Java" %}
```java
PIDFController controller = new PIDFController(coeffs, kV, kA, kStatic);

double correction = controller.update(position, velocity, acceleration);
```
{% endcode-tabs-item %}

{% code-tabs-item title="Kotlin" %}
```kotlin
val controller = PIDFController(coeffs, kV, kA, kStatic)

val correction = controller.update(position, velocity, acceleration)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Returning to the first example, it makes more sense to counteract gravity with a constant acceleration feedforward than a custom feedforward as before:

{% code-tabs %}
{% code-tabs-item title="Java" %}
```java
PIDFController controller = new PIDFController(coeffs, kV, kA, kStatic);

double correction = controller.update(position, velocity, acceleration + g);
```
{% endcode-tabs-item %}

{% code-tabs-item title="Kotlin" %}
```kotlin
val controller = PIDFController(coeffs, kV, kA, kStatic)

val correction = controller.update(position, velocity, acceleration + g)
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
In the FTC SDK v4.x, setting the motor voltage can be confusing. Conventionally, one would use `DcMotor.setPower()`. Unfortunately, the behavior of this method depends on the current `DcMotor.RunMode`. While these nuances are not critical for PID performance, they're important for proper feedforward operation. In the default `DcMotor.RunMode.RUN_WITHOUT_ENCODER` mode, the power is essentially a percentage of the battery voltage to supply. In this mode, the voltage feedforward model applies with some scaling already captured by the gains. In the `DcMotor.RunMode.RUN_USING_ENCODER` mode, the controller uses an onboard velocity PID controller, and the power is instead a scaled velocity. In this configuration, the model becomes $$V_{app} = k_v \cdot v$$ \(alternatively, the same model as before with $$k_a = 0$$ and $$k_{static} = 0$$\). 
{% endhint %}


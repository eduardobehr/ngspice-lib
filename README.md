# About

This library bundles some useful ngspice components, models, etc.

## Test Examples
### Sum

```
.title Test Gain
*                 ______
*                |      \
*   input >----->|╳ gain >-----> output = {gain ╳ input}
*                |______/

.include gain.lib

Bin     input       gnd     v=1.5+2.5*sin(6.28*60*time)
Xgain   input       output  GAIN gain=2

.tran 100u 30m iuc

.control
    run
    plot input output
.endc
```

### Integrator
```
.title Integrator Test
*                 _______
*                |       |
*   input >------|  1/s  |-----> output
*                |_______|
*

.include integrator.lib

* Testing subcircuit INTEGRATOR
Bin     input   gnd     v=time
Xint    input   out     INTEGRATOR

.tran 10u 50m uic

.control
    run
    plot v(out)
.endc
```

### 3 Levels PWM

```
.title PWM with arbitrary non-linear source

.include pwm_generator.lib

* Generate input signals
Vfreq     freq        gnd       4000
Vsine     signal      gnd       SIN(0 1 60)

* Feed signals to pwm generator
xpwm      signal      freq      pwm          PWM3

* Filter output
.param order = 2



.if (order == 1) $ RC
  Rf        pwm         filtered    1000
  Cf        filtered    gnd         1u
.elseif (order == 2) $ RCRC
  Rf1       pwm           stage2    100
  Cf1       stage2        gnd       1u
  Rf2       stage2        filtered  100
  Cf2       filtered      gnd       1u
.endif

.tran 1u 20m

.control
  run
  plot v(signal) v(pwm) v(filtered)
.endc
```

![](misc/pwm_example)
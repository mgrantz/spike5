# spike5

A 5-parameter model for a spiking neuron.

# Model Specification

Two parameters pertain to the "body" of the neuron (one per neuron), and three pertain to the "dendrites" (one per input).

The two that pertain to the body are:

* _Activation threshold_ - the level of input activation required to trigger an output spike.
* _Integration time_ - the duration over which the neuron integrates inputs.

The two that pertain to each input are:

* _Rate_ - the rate of input spikes. The activation level will be higher when the input rate matches this expectation and lower if not.
* _Time offset_ - the time difference between input spikes and output spikes.
* _Salience_ - the degree of influence this input has on whether an output spike will be generated.

A neuron receives the following inputs that guide parameter adjustment as indicated:

* _Arousal_ - specifies the desired firing rate, achieved by adjusting the activation threshold.
* _Relevance_ - specifies the desired learning rate (learning is online).

A few helpful notes based on the specification above:

* The desired output is not provided (learning is unsupervised).
* Time offset is irrelevant/unused when the input rate is significantly higher than the itegration time (inputs will be smeared together).
* Rate is effectively irrelevant/unused when the rate is significantly higher than the itegration time (inputs are treated as isolated spikes).

# High-level Learning Algorithm

The adaptation strategy for each parameter is intentionally omitted from this specification, though some example approaches are detailed in the following section.

For the purposes of discussion, we will assume that the neuron has an idea of the "current" or "instantaneous"

* output firing rate
* input rate (per input)
* delta - absolute time difference between last input and last output
* proportion of output spikes xz

An outline of the learning algorithm is:

* Relative learning rates
    * Activation theshold is adjusted relatively rapidly (short-term potentiation, e.g. seconds-minutes).
    * Integration time, rate, and time offset are adjusted relatively slowly (long-term potentiation, e.g. hours-days).
    * Salience is adjusted very slowly (weeks-months).
* Each time an input is seen, the activation threshold is adjusted down if the output firing rate is lower than the arousal and up if higher.
* Each time an output is generated
    * If delta is greater than integration time / 2
        * Salience is decreased.
    * If delta is approximately integration time / 2
        * Salience is increased.
        * Integration time is increased.
        * Rate is adjusted toward the input rate.
        * Time offset is adjusted toward delta.
    * If delta is less than integration time / 2
        * Salience is increased.
        * Integration time is decreased.
        * Rate is adjusted toward the input rate.
        * Time offset is adjusted toward delta.

# Detailed Examples

The following examples will assume:

* The learning rate is a positive real number $R$.
* The difference between short and long term potentiation is give as a positive real number $p$.

# Activation Threshold

The threshold $T$ could be updated as:

```math
T_n+1 = T_n * (1 + R) ^ (1 + k)
```

# Integration Time

The integration time $I$ could be updated as:

```math
I_n+1 = I_n * (1 + R)
```

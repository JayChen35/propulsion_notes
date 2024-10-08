# Introduction
For 95% of fluids analysis problems, we can reduce the real-world complexity of the fluids system into a simple graph: a network of vertices and edges. 
Vertices (also often called *nodes*) are complete representations of a finite volume of gas, where all thermodynamic properties are known.
*Edges*, or the connections between nodes, represent exchanges of mass, energy, and momentum. This type of modeling is often referred to as a lumped capacitance model.

This framework can be applied to pretty much any fluids system, and often does great at hitting the **80/20 mark** of analysis&mdash;we achieve 80% of the fidelity with 20% of the work. There are seldom cases where this approach is insufficient, and in these cases, you can typically increase fidelity by simply increasing the node count in the graph, or by decreasing the time increment.

## The Approach
1. Decide on what parts of the system are nodes, and which have negligable enough volume to be considered as connections between nodes.
2. Set up control volume analysis on each node. This is a fancy way of saying, "figure out what is entering an leaving a volume of gas (the node), and apply conservation laws."
3. These conservation laws (typically, just mass and energy are sufficient without including momentum) are in differential form, and so across all the nodes in the network, you end up with a system of ordinary differential equations (ODEs). 
4. Given an initial condition, we can obtain the transient behavior of the system by integrating forwards at each timestep.

## Example
**Problem statement:** We want to solve for the state of a COPV that is being filled with a gas of known specific enthalpy and mass flow, assuming no heat transfer into the system.

First, we start with the statement for conservation of mass in a control volume.

$$
\frac{d}{dt} \int_{\textrm{CV}} \rho dV + \int_{\textrm{CS}} \rho (\vec{v} \cdot \hat{n}) dA = 0
$$

We can re-express the surface integral over the control surface as a sum of mass flow rates passing through it (in a more intuitive form as well). The outflow term goes to zero per our problem statement.

$$
\frac{dm}{dt} = \sum_{\textrm{inflow}}\dot{m} - \sum_{\textrm{outflow}}\dot{m} = \sum_{\textrm{inflow}}\dot{m}
$$

For the left-hand side, we know mass is just density times volume, and can apply the chain rule to get an expression for the time rate of change of density. 

$$
\begin{align*}
m &= \rho V \\
\frac{dm}{dt} &= \frac{d\rho}{dt}V + \rho \frac{dV}{dt} \\
&= \frac{d\rho}{dt}V
\end{align*}
$$

$$
\begin{equation}
\frac{d\rho}{dt} = \frac{1}{V} \sum_{\textrm{inflow}}\dot{m}
\end{equation}
$$

With this expression for density, we just need one more ODE expressing the time rate of change of another state property. Integrating these two equations will fully define our gas state at the next time step.

To get this second equation, we start with the first law of thermodynamics in extensive form:

$$ dE = \delta{Q}^\leftarrow - \delta{W}^\rightarrow $$

Since we're interested in the evolution of this COPV over time, we can rewrite in terms of a time differential.

$$
\begin{equation} 
\frac{dE_i}{dt} = \dot{Q}^\leftarrow - \dot{W}^\rightarrow + \sum_{\textrm{inflow}}\dot{m}h_0 - \sum_{\textrm{outflow}}\dot{m}h_{0_i} 
\end{equation}
$$

We've also added in terms for mass flow rate using our control volume statement for conservation of energy. This is really the same statement as the first law; we're just picking the form here for convinience. As a refresher, this is the conservation of energy statement:

$$
\begin{equation}
\frac{d}{dt} \int_{\textrm{CV}} \rho e_o dV + \int_{\textrm{CS}} \rho h_0 (\vec{v} \cdot \hat{n}) dA = \dot{Q}^\leftarrow - \dot{W}^\rightarrow
\end{equation}
$$

In plain English, it says that:
> The rate of change of total **internal energy** in CV + flow rate of total **enthalpy** (as carried through via mass) through CS = rate of heat transfer through CS - work done

<sup>Also, recall the sign convention for these superscript arrows: heat (Q) is defined as positive when heat is transfered **to** the system, **from** its surroundings. Thus, the left side of the arrow denotes the system, while the right side denotes the surroundings.</sup>

Going back to our refined first law statement, we can now cross off terms we know are zero.

$$ \frac{dE}{dt} = \sum_{\textrm{inflow}}\dot{m}h_0 $$

The next big step now is to relate the change in total internal energy to changes in state variables, which we can integrate.

$$ E = me = \rho V e $$

$$ \frac{dE}{dt} = \frac{d\rho}{dt}Ve + \rho\frac{dV}{dt}e + \rho V \frac{de}{dt} $$

We know that volume is constant, so the middle term goes to zero. We also know that specifically in the conext of this problem, the density change is only due to a mass change (since $V$ is constant) and therefore $\frac{d\rho}{dt}V = \frac{dm}{dt}$:

$$ \frac{dE}{dt} = \frac{dm}{dt}e + \rho V \frac{de}{dt} $$

Here is where we have to be careful. Remember that our end goal is to derive an expression of a state variable's change over time as a function of known quantities (we already have one with respect to mass, so another ODE allows us to solve the system fully). You might want to jump to some substitutions here, such as $e = c_v T$, but recall that this makes the calorically perfect assumption.

Instead, we should always try to start as general as possible and make conscious decisions on assumptions to simplify our equations. So, for a real gas, we know that the internal energy is a function of both $\rho$ and $T$:

$$
\begin{align*}
e &= f(\rho, T) \\
\frac{de}{dt} &= \frac{\partial e}{\partial \rho} \frac{d \rho}{dt} + \frac{\partial e}{\partial T} \frac{dT}{dt}
\end{align*}
$$

We can see here that this form is a bit *too* general, and there are no terms we can immediately deduce or cross off. So, let us make that next-most unrestrictive assumption, which is that the gas is thermally perfect. This means that $e$ only varies as a function of temperature.

$$ e = e(T) $$ 
$$ de = c_v(T) dT $$

Note that $c_v(T)$ here just denotes that $c_v$ is a function of temperature; this is due to the vibrational and electronic motion of the particles. This is still not particularly useful, even if we derive it:

$$ 
\frac{de}{dt} = \frac{d c_v(T)}{dt} dT + c_v(T) \frac{dT}{dt}
$$

Using the [chain rule](https://tutorial.math.lamar.edu/classes/calciii/chainrule.aspx):

$$ \frac{de}{dt} = \frac{d c_v(T)}{dT} \frac{dT}{dt} dT + c_v(T) \frac{dT}{dt} $$
$$
\frac{de}{dt} = \left( \frac{d c_v(T)}{dT}dT + c_v(T) \right) \frac{dT}{dt}
$$

<!-- $$ \frac{de}{dt} = \frac{d c_v}{dt}T + c_v \frac{d T}{dt} $$
$$ \frac{d c_v}{dt} = \frac{\partial c_v}{\partial T}\frac{dT}{dt} + \frac{\partial c_v}{\partial \rho}\frac{d\rho}{dt}  $$ -->

So perhaps there is one more route to take before making more assumptions. If we take the definition of specific heat at constant volume:

$$ c_v = \left( \frac{de}{dT} \right)_{v=\textrm{const}} $$

We'll notice that since our volume is always constant in this problem, we can rearrange: 

$$ \frac{1}{c_v} = \frac{dT}{de} $$

This allows us to rewrite the tricky term for energy (without making any assumptions) as:

$$ \frac{de}{dt} = c_v \left( \frac{1}{c_v} \right) \frac{de}{dt} = c_v \left( \frac{dT}{de} \right) \frac{de}{dt} = c_v \frac{dT}{dt} $$

Putting it all together:

$$
\begin{align}
\frac{dE}{dt} &= \frac{dm}{dt}e + \rho V \frac{de}{dt} \nonumber \\
\sum_{\textrm{inflow}}\dot{m}h_0 &= \frac{dm}{dt}e + m c_v \frac{dT}{dt} \nonumber \\
\frac{dT}{dt} &= \frac{1}{m c_v} \left( \sum_{\textrm{inflow}}\dot{m}h_0 - \frac{dm}{dt}e \right)
\end{align}
$$

We've now found two ODEs expressing the change of two independent state variables $(m, T)$ over time. To get the full state evolution of this problem, we just need an initial condition and to numerically integrate these ODEs using libraries such as `scipy.integrate.ode` for Python or `ode45` for MATLAB. To translate these two state variables to a full description of a gas state, we'll need a real gas database such as `REFPROP` to get the other state variables, such as pressure, enthalpy, and entropy.

## Common Pitfalls
> (1) Not using the differential form of thermodynamic relations.

In many of the relations discussed in these documents, the differential form is the actual definition of the quantity, while its relation to other state properties incur some assumption. For example, the definition of specific heat capacity at constant volume is:

$$ c_v = \left( \frac{de}{dT} \right)_{v=\textrm{const}} $$


However, if you use the relation to temperature and specific internal energy, you would be making a calorically perfect assumption:

$$
e = c_v T
$$

> (2) Not understanding the isentropic assumption.

The definition of an isentropic process is as follows:

$$
\textrm{an isentropic process} \iff \textrm{the process is adiabatic and reversible}
$$

Note the double-arrow ($\iff$) since this is an *if-and-only-if* statement. However, there are many cases where this bilateral directionality is not preserved:

$$
\textrm{an isentropic process} \Longrightarrow \left( \dot{Q}^\leftarrow = \dot{W}^\rightarrow = 0 \right)
$$

The notable exception here of course being that a process can have no heat transfer or work done, but still be irreversible (e.g. an unrestricted expansion).

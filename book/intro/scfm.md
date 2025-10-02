# A Note on SCFM

I've often found myself trying to relearn or rederive what exactly is a SCFM (and why), so here's my attempt at a concise explaination. In my opinion, the [Wikipedia article](https://en.wikipedia.org/wiki/Standard_cubic_feet_per_minute) is helpful but is not quite thorough enough.

## What is a SCFM?

SCFM = a standard cubic foot per minute. In the US, we take "standard conditions" as 68 Fahrenheit and 101.3 kPa.

**Why?**

Imagine if someone told you that the flow rate of a gas through an orifice is 30 cubic feet per minute. This doesn't really mean anything since gases are compressible, and therefore a very cold and dense gas at 2 kg/s and a hot gas at 1 kg/s could both have a volumetric flow rate of 30 CFM.

Thus, what SCFM is trying to do is establish a standard (i.e. agreed upon by everyone) temperature and pressure so your mass flow rate can be communicated consistently. Take the idea gas law:

$$
PV=nRT
$$
$$
\dot{n} = \frac{P_{\textrm{std}}\dot{V}}{R_{u}T_{\textrm{std}}}
$$

$\dot{V}$ is the standard flow rate (SCFM in imperial, SCCM in metric). So in actuality, SCFM is really a measure of **molar flow rate**. This is particularly useful because mass flow rate also depends on the molar weight of the gas (i.e. is a function of the species of gas), so expressing a gaseous flow rate as a molar flow rate allows you to fully generalize the rate.

1 SCFM of nitrogen is always 1 SCFM of nitrogen at all conditions by definition, while 1 SCFM of nitrogen carries ~7x the mass flow rate as 1 SCFM of helium because of the molecular weight difference.

## The Math

In most propulsion contexts, we care about mass flow rate $\dot{m}$ due to conservation equations. So, we often have to convert between SCFM and $\dot{m}$. How? Take again the combined ideal gas law:

$$
\frac{P_1 V_1}{T_1} = \frac{P_2 V_2}{T_2}
$$

It relates _any_ two combinations of pressure, temperature, and volume.  So, let's say that the left side of the equation is our standard conditions (subscript 1, i.e. $\dot{V}_1$ is a SCFM), while the right side is the conditions of our flow, which is often called "actual cubic feet per minute" or ACFM.

> **Do not be confused by the notion of ACFM.** In fact, I would advise to never calculate ACFM since it's more roundabout. It's just there to show what effective volumetric flow rate you have at your conditions, but isn't really useful to us in any way in our context.

We'll take a derivative with respect to time (loose with the mathematics here) to get the equation found in the Wikipedia article:

$$
\dot{V}_1 = \textrm{SCFM} = (\textrm{ACFM}) \left(\frac{P_2}{P_1}\right) \left(\frac{T_1}{T_2}\right)
$$

We'll use another form of the ideal gas law to simplify the above expression:

$$
P_2 = \rho_2 RT_2
$$

Dividing by $P_1$ on both sides:

$$
\frac{P_2}{P_1} = \frac{\rho_2 RT_2}{\rho_1 RT_1}
$$

Asssuming the gas composition does not change ($R$ is constant), we get:

$$
\frac{\rho_2}{\rho_1} = \left(\frac{P_2}{P_1}\right) \left(\frac{T_1}{T_2}\right)
$$

Thus, substituting back in to our original equation:

$$
\textrm{SCFM} = \textrm{ACFM} \left(\frac{\rho_2}{\rho_1}\right)
$$

Remember that $(1)$ denotes standard conditions, and that SCFM and ACFM are still just volumetric flow rates. To get a mass flow rate, we just need to multiply by a density, so let's multiply by $\rho_1$ (we will denote as $\rho_{\textrm{std}}$) on both sides:

$$
\boxed{\dot{m} = \textrm{SCFM} \times \rho_{\textrm{std}} = \textrm{ACFM} \times \rho_{\textrm{act}}}
$$

**This is a very useful relation**. It says that since the mass flow rate will be constant at any condition (when both conditions are reached by the ideal gas equations of state), we can express our current "actual" mass flow rate as a SCFM by dividing by our gas density at standard conditions. Conversely, we can get the mass flow rate of a certain species of gas (the information of the species of gas is baked into $\rho_{\textrm{std}}$) by multiplying the SCFM rating by the density of the gas at standard conditions.

Hope this was helpful! Remember to check your units!

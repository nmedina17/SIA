# Model Proposal for _soil invertebrates in aggregation_

_Nicholas Medina_

* Course ID: CMPLXSYS 530
* Course Title: Computer Modeling of Complex Systems
* Term: Winter 2018


__*LS COMMENTS:*__
_Not a lot of comments at this time, other than to say that this a very thorough and well-thought out proposal, and I am very much looking forward to what you get from it. It also looks like a great foundation for future elaborations. Thanks for getting this in to me and if you have any particular questions or run into any problems, please let me know._


### Goal
*****
_Provide a short, 1-3 sentence description of the goal of your model_

This model highlights (potentially eusocial) macro-invertebrate activity as a process by which soils self-organize. Evidence of self-organization such as hysteresis has come from hydrological data about water infiltration through soil, however less evidence of self-organization comes from data on soil biology. This model evaluates the effects of soil invertebrates on soil structure, and more specifically, aggregation.


### Justification
****
_Short explanation on why you are using ABM_

Despite the widespread acceptance of extreme variation in the characteristics of adjacent soils, variance in soil properties is often treated as noise, likely because it often reduces the validity of widely-used equation-based representations of soil dynamics. To better understand the origin of this variation, I present an agent-based model because it better incorporates variation in soil processes, such as in the spatial organization of individual soil-dwelling ants, in giving rise to broader soil characteristics that are often perceived as intrinsic and difficult to explain.


### Main Micro-level Processes and Macro-level Dynamics of Interest
****
_Short overview of the key processes and/or relationships you are interested in using your model to explore. Will likely be something regarding emergent behavior that arises from individual interactions_

Eusocial soil invertebrates are known to interact in complex and dynamic yet simple ways, but the focus here is on how their behavior (at a micro-level) collectively modifies the soil environment around them (at a macro-level). Often times, burrowing activities serve a different purpose than to intentionally modify the soil environment (termed “accidental” ecosystem engineering), but can leave distinct patterns in soil structure. This model analyzes net effects on aggregate formation.


## Model Outline
****

### 1) Environment
_Description of the environment in your model. Things to specify *if they apply*:_

* _Boundary conditions (e.g. wrapping, infinite, etc.)_
* _Dimensionality (e.g. 1D, 2D, etc.)_
* _List of environment-owned variables (e.g. resources, states, roughness)_
* _List of environment-owned methods/procedures (e.g. resource production, state change, etc.)_


The model environment is a closed, 3-dimensional subsection of soil, and it is intended to represent a sample of soil from the field. While field soils are much more likely open environments, there is certainly evidence that social invertebrates exist in clusters (i.e. colonies), which justifies the interpretation of this model envirnoment as a "hotspot" of macro-invertebrate activity in field soils. Furthermore, given the importance of a third dimension in defining soil aggregates, and ease of coding 3D in NetLogo, the model environment is currently established as 3D.
    Environmental attributes are coded as variables specific to "patches" (using NetLogo terminology), the most important of which is the "stable" attribute that is currently encoded as boolean. This attribute helps define what an "aggregate" is in this model: if a soil particle and all of its 26 neighbors (in 3D) are stable, the global count for aggregates is incremented by 1. Another boolean attribute, "hydrated?", also helps confer stability to a soil particle--strictly, it represents a film of water around the surface of the particle. If every neighbor of a particle is hydrated, the focal particle, as well as its neighbors, will become stable (by virtue of hydration and physical "protection", but this step is not explicitly included in the code). These 2 sets of conditions, encoded as patch attributes and further analyzed in patch-specific _stabilize_ procedure that realizes these state changes for patches, are currently what most immediately confer particle stability, and thus allow for soil aggregation.
    There is also a boolean "clay-silt?" attribute that ascribes a geologic size class for each soil particle. Many previous studies have used the proportion of clay + silt particles observed in a soil as a measure of its organic nutrient retention, given the greater ability of high surface area, low volume particles to bind organic compounds. (The utility of this measure also assumes that all particles are of similar geologic origin, because different parent rocks have different chemical compositions, which would also otherwise affect how strongly organic nutrient bind to soil particle surfaces.) While this attribute is likely to affect a particle's probability of hydration and de-hydration, it does not yet serve a function in the aggregate formation processes encoded in this model.


```python
# Include first pass of the code you are thinking of using to construct your environment
# This may be a set of "patches-own" variables and a command in the "setup" procedure, a list, an array, or Class constructor
# Feel free to include any patch methods/procedures you have. Filling in with pseudocode is ok! 
# NOTE: If using Netlogo, remove "python" from the markdown at the top of this section to get a generic code block
```

```
;;  patches represent soil, which forms aggregates
patches-own [
clay-silt? ;; measure of texture
clay-silt_next? ;; stores future particle size
hydrated? ;; indicates presence of water film on particle's surface
hydrated_next? ;; stores future particle moisture
stable? ;; indicates fixed location (inside aggregate)
]
```

```
to stabilize ;; defines aggregates
ask patches [
if all? neighbors [ hydrated? = true ] [ ;; condition #1
set stable? true
ask neighbors [
set stable? true
]
]

if all? neighbors [ stable? = true ] [ ;; condition #2
set stable? true
]
]
end
```


### 2) Agents
 
 _Description of the "agents" in the system. Things to specify *if they apply*:_
 
* _List of agent-owned variables (e.g. age, heading, ID, etc.)_
* _List of agent-owned methods/procedures (e.g. move, consume, reproduce, die, etc.)_


The agents here represent relatively small, soil-dwelling macroinvertebrates. Eventually, it may be most relevant to tropical agricultural soils to model the behavior of soil-dwelling ant (e.g. _Atta_, fungus-farming), termite, or mite behavior (mites are often important pests), the agents here do not yet behave socially. Such interaction networks may initially serve here more as validation of organismal behavior, but there may also be related hypotheses to test about how interaction frequencies may result in less new-pore construction due to re-use of existing burrows, and thus potentially slower aggregate formation.
    Agents in this model only have default attributes generated by NetLogo, as most of the focus is on how patch attributes are changed. However, in a more social network they would be likely to have additional attributes, like a history of the attributes of the other turrtles with whom they've interacted. Currently, agent-specific procedures include a simple _wiggle_ method to randomize their movement, and a _compress_ procedure, which is more important for aggregate formation. The _compress_ procedure mimics a burrowing process that "compacts" the adjacent soil by modifying particles attributes to mimic those of particles found in aggregate clumps: water (in this case particle hydration) moves from outer particles (immediate neighbors to agent) to inner particles ("second" neighbors to agent). (Another process that could be included here is the re-organization of soil particle size classes such that _clay-silt_ particles, which are know to stay dissolved in water for longer, move to aggregate "centers" along with the water--although. This has not yet been coded, but would be realistic, and make better use of the patches' _clay-silt_ attribute.) The compression process assumes very directional forces exerted by each agent, such that the compression only affects a subset of immediate neighbors, not every adjacent one in every possible direction.
    These agents do not [yet] reproduce, as individual-level reproduction is highly reduced in eusocial organisms, and the focus is on the physical effects of agents on the surrounding soil environment.


```python
# Include first pass of the code you are thinking of using to construct your agents
# This may be a set of "turtle-own" variables and a command in the "setup" procedure, a list, an array, or Class constructor
# Feel free to include any agent methods/procedures you have so far. Filling in with pseudocode is ok! 
# NOTE: If using Netlogo, remove "python" from the markdown at the top of this section to get a generic code block
```

```
to wiggle
ask turtles [
lt random 45
rt random 45
tilt-up random 45
tilt-down random 45
]
end
```
```
to compress ;; patch procedure while turtle is burrowing
ask turtles [
if random-float 1 < compaction_prob [
ask neighbors4 [ ;; compaction lowers soil moisture of aggregate surfaces relative to their center
set hydrated? false
ask neighbors4 [
set hydrated? true
set stable? true
]
]
]
]
end
```


### 3) Action and Interaction 
 
**_Interaction Topology_**

_Description of the topology of who interacts with whom in the system. Perfectly mixed? Spatial proximity? Along a network? CA neighborhood?_


The burrowing agents included here do not yet interact with each other, but organize environmental attributes that do affect the interaction topology of a different "sub-class" of agents: bacterial decomposers. In real soils, bacterial activity is concentrated inside aggregates, and ultimately makes aggregates more stable. Here, bacterial activity is not yet explicity modeled, but implicit to the stabilizing processes that result in aggregate formation. As for an interaction network among the burrowing macroinvertebrates, there is none currently, but the introduction of one (e.g. representing division of labor in ant nests) I think does have some potential to affect the timing of aggregate formation.


**_Action Sequence_**

_What does an agent, cell, etc. do on a given turn? Provide a step-by-step description of what happens on a given turn for each part of your model_

1. Step 1
2. Step 2
3. Etc...


Each turn, each agent performs a sequence of procedures to move through the soil:
1. First, it wiggles, to randomize the direction of its movement.
2. Second, it _compresses_ the soil around it.
3. Third, to make room to move forward, it "ingests" (creates a memory of) the soil particle in front of it, and with some probability of its gut modifying attributes of the ingested soil particle, it then "excretes" a similar soil particle behind it. EXCEPTION: If the soil particle ahead is "stable" already (indicating affiliation with an aggregate), the agent will not be able to move and must re-start _wiggling_.


```
to burrow-feed
wiggle

;; sequence of 3 commands below encompass full effects of movement on adjacent soil
compress

;; the groups of patch-ahead commands below "move" the particle in front by "digestion", without modification in this case
ask turtles [
ask patch-ahead 1 [
if stable? = true [
stop
]

if clay-silt? = true [
ask turtles-here [
ask patch-here [
set clay-silt_next? true
]
]
]

if hydrated? = true [
ask turtles-here [
ask patch-here [
set hydrated_next? true
]
]
]

;; make room to move / ingest
set pcolor black
set clay-silt? false
set hydrated? false
]

fd 1 ;; move

;; replace particle / excrete
ask patch-ahead -1 [
if clay-silt_next? = true [
set clay-silt? true
]

if hydrated_next? = true [
set hydrated? true
]
]
]
end
```


### 4) Model Parameters and Initialization

_Describe and list any global parameters you will be applying in your model._
_Describe how your model will be initialized_


All patches will be used, and initialized wtih a color based on _hydration_, _clay-silt_, and nutrient retention (_stable_) attributes. The user has the freedom to modify these attributes, so as to consider outcomes for soils with different textures and field-moisture, or water-holding capacities. In real soils, water-holding capacity tends to co-vary with texture and fertility, so while it may not be realistic to consider parameter sweeps that are completely independent, the user has the freedom to define these fundamental relationships. The macro-invertebrate agents will be initialized at random starting points.


```
to see_properties
ask patches [
ifelse stable? = true [
set pcolor [
35 0 0 ;; see dirt/feces
]
] [
ifelse hydrated? = true [ ;; see wet particles
set pcolor [
105 0 0
]
] [
ifelse clay-silt? = true [ ;; see texture
set pcolor [
25 0 0
]
] [
set pcolor [
45 0 0
]
]
]
]
]
end
```
```
to setup
;; clear-all ;; doesn't work for some reason...
ask patches [
if random 100 < clay-silt_% [ ;; set texture parameter
set clay-silt? true
]

if random 100 < moisture_vol_% [ ;; set moisture parameter
set hydrated? true
]

if random 100 < organic_matter_% [ ;; set organic matter parameter
set stable? true
]
]

see_properties

create-turtles colony_size [
set shape "bug"
set size 2
set color white
setxyz random-xcor random-ycor random-zcor ;; initialize insect locations randomly
]

reset-ticks
end
```

_Provide a high level, step-by-step description of your schedule during each "tick" of the model_


The _go_ procedure encomasses all major methods and defines the following order for each model "tick":
1. _rain_: a procedure that modifies soil moisture, and thus potential for aggregation
2. _burrow-feed_: runs through agent movement and effects on adjacent soil particles
3. _stabilize_: modifies patch attributes as appropriate, allowing soil aggregates to form
4. _analyze_: updates global variables invovled in graph outputs / analyses
5. _see_properties_ : visualizes states of soil particles


```
to go
rain

burrow-feed

stabilize

analyze

see_properties
end
```
```
;; in this model 1 tick = 1 minute ( Rattan and Venugopal 2013 )
to rain
if ticks > 0 [
if ticks mod ( 60 * rain_freq ) = 0 [ ;; converts from ticks to minutes
ask patches [
if random 100 < moisture_vol_% [
set hydrated? true
set stable? false
]
]
]
]
end
```

### 5) Assessment and Outcome Measures

_What quantitative metrics and/or qualitative features will you use to assess your model outcomes?_


I am most interested in measuring the counts of stable aggregates over time, as well as the distributions of different size classes of aggregates that are formed over time, and looking for patterns in these plots. To test for self-organization, I would need to find a long-tail distribution and/or apply cluster-type measure of soil particle network structure, either in addition to or in place of conferred "stability" attributes to soil particles.


```
globals [
stable ;; count of total particles making up all agreggates and peds
aggregates ;; count of aggregate centers
peds ;; count of larger aggregate centers
]
```
```
to analyze
ask patches [
if stable? = true [
set stable stable + 1

if all? neighbors [ stable? = true ] [
set aggregates aggregates + 1

ask neighbors [
if all? neighbors [ stable? = true ] [
set peds peds + 1
]
]
]
]
]

plot stable / ( aggregates + 1 )
end
```


### 6) Parameter Sweep

_What parameters are you most interested in sweeping through? What value ranges do you expect to look at for your analysis?_


Soils of different textures are likely to aggregate differently in real life, so sweeping through the _clay-silt_%_ parameter would be most interesting. Soil texture is also likely to affect moisture retention, so simultaneously considering scaled values of this parameter would also be part of this sweep.


### NOTES

```
;; NEXT STEPS
;; improved role for silt-clay property?
;; patches having properties of retaining particles/moisture/stability
;; network analysis of stable soil particles, regardless of affiliation with aggregate
;; additional model for soil horizon development
```

##Flea Rhythm Section Generator 

By Chris Holder Any comments or quiries please E:mail the author Chris.holder@mail.com
# Introduction

The flea rhythm section generator so called because its main design feature is the ability to create a bass and drum line in real-time very quickly. The purpose of this software came from being tasked with creating something that would provide the bass and drums for a laptop ensemble whilst having the ability of interaction from the other members of the ensemble.
With these rule sets in mind development of a generative audio device seemed to the author the best approach for the task then at hand.




##Developing the flea RSG


The start of development for this project was to consider a method of not only controlling a drum pattern but also to have the ability of changing the pattern in real-time very quickly. To control the drum the drum to be triggered the author considered the use of a step-based sequencer like many designs of old hardware
Drum machines such as the Roland 808. This trigger mechanism (shown in fig. 1.) was built using the metro object in Pure Data to output a “bang” message or pulse set by the argument of the slider object timed in milliseconds into the float of the metro object. This bang is then used to drive a counter object limited by its creation argument in this case 7 to give 8 steps ranging from 0-7. Each bang increments the counter object which resets at it’s peak value. The select object is then used to output a bang message when the input argument is matched to the creation argument of the select object. 

![fleascreen4] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2013.31.59.png)

**Fig. 1.** Screenshot to show 8-step sequencer abstraction.


With the triggers for each pulse set, a GUI interface (shown in **fig. 2.**) not unlike software sequencers such as Fruity Loops (shown in **fig. 3.**) was considered for the real time interaction with the drum pattern to be played. 

![fleascreen4] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-24%20at%2013.42.57.png)


**Fig. 2.** Screenshot to show drum GUI interface.


![fleascreen2] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-20%20at%2016.04.02.png)

**Fig. 3.** Screenshot of Fruity Loops rhythm interface.


The first version of this software had each of the 8-step triggers starting playback on a preloaded sample (shown in **Fig. 4.**) with the appropriate sound from each part of a drum kit; kick, snare, hi-hat closed and hi-hat open represented by the different coloured toggles on the GUI (**Fig. 2**). However one major drawback with this design was making sure that each sample was loaded into the right player plus the limitation of only having the loaded sampled sound instantly available. Other samples could be loaded but this would take time to complete the task. 

![fleascreen10] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2013.38.47.png)


**Fig. 4.** Screenshot of sample playback from 8-step sequencer trigger.


![fleascreen9] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2012.23.12.png)


**Fig. 5.** Buchla Source of uncertainty model. 

Now the drums where being output attention turned to designing an abstraction to deal with the bass element of the ensemble. The consideration of the author was to use a generative random abstraction to produce the different notes to be played rather than rely on a human element to “perform” the required piece. Looking first at devices such as the Buchla Source of uncertainty (shown in **fig. 5**) from the early 1970’s for inspiration, it was considered to base the abstraction around a    continuous-time recurrent neural network (Brown et al 2006). The neural network used in this abstraction is based around the drawing shown in Fig. 6.



![fleascreen3] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2011.09.31.png)

**Fig. 6.**   Brown et al model of a continuous-time recurrent neural network.



![fleascreen6] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2013.39.47.png)

**Fig. 7.**  Flea RSG continuous time recurrent neural network.




The Pure Data version of this network (shown in **fig. 7**) for this laptop ensemble works by: 
The input bang from the sequencer “fires” the input of the first neural, this initial stimulus decides if the step should play or not this time around the sequencer loop. With reference to Fig. 6 in the Brown et al model we can see 3 inputs this is emulated on the Pure Data version by three random objects. The output of each random is then summed by the three + objects (**fig. 7**) this represents the hidden layer in the Brown et al model (**fig. 6**). The output of this sum is then rule set by the > object (**Fig. 7**) to set the output threshold of the neural. As you can see in Fig. 7 this neural network has 10 layers the remaining 9 layers decide once the initial neural has decided to play what note should be played. This is achieved by the more neural layers that fire trigger the counter object which sums how strong the stimulus was by adding together the amount of networks to raise over there threshold level. This summed output is then placed though the auto-scale object which is set from 12 -36 (shown in **Fig. 7**). The reason for the 12:36 scale which represents midi numbers to be sent to the tone generator and keeps the instrument in the midi bass range over 3 octaves. To keep the bass timbre in time with the drums the same step sequencer was used, therefore each step of the sequence has a 10 layer neural network this is the pd flea object shown in Figure 8. One minor drawback with this type of generative audio is the rate at which notes are triggered. The threshold rule set is to low the decider always fires but raised a small amount and the odds of “firing” become increasing greater of only spasmodic triggering.  

![fleascreen12] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2013.40.18.png)

**Fig. 8.** Screenshot of neural networks input from step sequencer.

With the abstraction now playing drums and the neural network producing random midi notes, attention turned to the question of timbre. The question of what type of synthesis would be better suited to the laptop ensemble requirement of bass. Researching classic models of bass focused synthesizers; The Novation Bass Station (shown in **fig. 9**) used back in the early 1990’s was considered for a suitable candidate for timbre. The Pure Data abstraction for this subtractive synthesis model (shown in **fig. 10**) uses the output of the neural network and converts the number sent to frequency via the mtof~ object. the osc~ object to produce sine wave output. The other wave-shapes are done by using the phasor~ object stand alone for saw wave, and the expression object placed over the output of the second phasor~ object to force the amplitude of the saw wave to 1 if greater than 0.5 making a square wave shape. The output of these tone generators is then sent to the matrix~ object to allow user control, of which wave-shape is desired.  

![fleascreen13] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2011.25.20.png)

**Fig. 9.** Novation Bass Station early 1990’s. 



![fleascreen17] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2015.32.03.png)

**Fig. 10.** Screenshot of Pure Data tone generator abstraction.


This tone generation abstraction gave the initial sounds available to the bass station however the hardware was equipped with a low frequency oscillator, “LFO” to modulate the initial wave-shapes. To achieve this in Pure Data the LFO abstraction (shown in **fig. 11**) shares the same oscillator design as the main tone generator. It differs in its amount of oscillation is scaled to output a max of 50hz this is set by the properties of the modulation slider position on the GUI (shown in **fig. 12**). To modulate the main wave-shape, the signal is connected to the *~ object with the output of the LFO connected to the float input. The *~ object then multiplies the signals together giving a modulated output. 
From the output of the LFO the combined signals are sent then to a filter section (shown in **fig. 12**) to subtract the unwanted harmonics from the out going audio waveform.  

![fleascreen15] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2010.46.52.png)

**Fig. 11.** Screenshot of Pure Data LFO abstraction.


![fleascreen15] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2015.29.39.png)

**Fig. 12.** Screenshot to show subtractive synthesis GUI.

At this stage in the development the individual units of the laptop ensemble where trialled. After the test it was felt by the author that the timbre of subtractive synthesis was not complementary to the rest of the ensemble. For this reason the timbre of the abstraction had to be redesigned (shown in Fig.13).

![fleascreen30] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2013.00.35.png)

**Fig. 13.** Screenshot showing redesigned tone generator with F.M synthesis.   

This redesign took its inspiration from modular synthesis looking at systems such as Buchla’s Musical Easel (show in Fig. 14). The new abstraction saw two major changes to the timbre, Frequency Modulation “F.M” was used instead of subtractive synthesis and the LFO was converted to a Control Voltage as it was felt this would have more effect on the overall tonality of the abstraction.

![fleascreen31] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2012.08.28.png)

**Fig. 14.** A Buchla Musical Easel.  

 Referring to Figure 13, this abstraction of F.M synthesis works by taking its input from the output of the wave shape selector as discussed earlier either Sine wave, saw or square wave. The incoming signal amount is controlled by the *~ object scaled by the knob between 0-1 to control the amplitude of the signal.
The F.M abstraction for the linear mode on the right hand side of the screenshot (**Fig. 13**) takes the input signal from the *~ object to provide the carrier and modulation frequencies. The knob labelled Lin F.M sets the amount of modulation. The three *~ objects in Pure Data mix the carrier and the modulator together and start the modulation process of the carrier frequency the greater the modulation the more harmonic sidebands will appear in the signal creating the sound F.M synthesis is famous for. 
The Exponential F.M (left hand side of Fig. 13) works in much the same fashion as the linear model expect the output of the carrier is used to “drive” the input of the modulator and the modulation signal is likewise used to “drive” the carrier frequency causes a non-linear response to the carrier and modulation ratio.
The final development of the software’s basic operation saw the introduction of an attack/decay envelope. This was added to simply control the note on/off states of the tone generator (shown in Fig. 15). This envelope abstraction works by using the line~ object to create a ramp on the amplitude of the signal. The direction of the ramp is dictated by the argument of the pack object, which is scaled from the knob in the GUI. The delay object in this abstraction controls the note length set by the sustain control knob also on the GUI.

![fleascreen20] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2015.30.34.png)
![fleascreen21] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-21%20at%2015.30.51.png)

**Fig. 15.** Screenshot showing Attack/decay envelope GUI and Pure Data abstraction.

Now the abstraction was fully functioning, under evaluation it was considered that the drums needed “livening” up to achieve this aim the author decided that splitting the sound sources of the sampled drum player and the built-in F.M based synthesizer would allow for better sonic balance to be more easily achieved. Another benefit to be had by “out sourcing” the drums to an external midi unit would be to save on cpu load. Although this was not an issue it would however ensure more reliable operation from the abstraction. This redesign to the drums (shown in **Fig. 16**) saw the removal of the sample player. The player was replaced with a message object with a set argument in this case the midi note number corresponding to the relevant part of the drum kit snare drum, hi-hat etc for each trigger. Each trigger is controlled by the spigot object, which will mute the step or allow the trigger to pass to the note out object in pure data (shown in **Fig. 17**). 

![fleascreen22] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2016.18.54.png)

**Fig. 16.** Screenshot of drum step sequencer redesign.

![fleascreen23] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-24%20at%2013.42.39.png)

**Fig. 17.** Screenshot showing midi out connection.

Another aim to liven up the drums was also considered by some type of fill mechanism that would remain within the set time sequence (this abstraction is shown in Fig. 18). The method of achieving this in Pure Data was to place the output of the tempo slider into a float object. This value was then divided by the / object with a creation argument set to scale the pulse within the half and quarter steps of the sequence. The delay object was then placed to time the next filler note on/off time. 

![fleascreen23] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2013.05.45.png)

**Fig. 18.** Screenshot to show Rhythm filler abstraction.

However as the laptop ensemble was more generative in its approach the author considered some form of randomization. “The inclusion of rules and general frameworks into the creative process of composition, however, becomes apparent not only in exemplary systems which generate musical structures “at the push of a button”; it is also expressed in a number of works throughout music history” (Nierhaus 2009). This text reminded the author of the dice game invented by Bach entitled Musikalisches Würfelspiel (German for "musical dice game") was a system for using dice to randomly 'compose music. The abstraction (shown in Fig. 19) works by generating a random number between 0 and 100. The moses object then splits the signal but only generates an output if a match is detected. This is set by the creation argument of the moses object. Whose output of which is scaled in percentage of “hit” rate.    


![fleascreen29] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2013.06.09.png)


**Fig. 19.** Screenshot to show Stochastic random abstraction. 

![fleascreen29] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2018.44.37.png)

**Fig. 20.** Photograph of a Buchla 264 quad sample and hold

The final development this abstraction had was an inspiration once again taken from modular synthesis in the form of a Buchla 264 model quad sample and hold (shown in Fig. 20). The reason for this addition was to make the tone generator output more interesting and less step sequencer like. The abstraction for this operation (shown in fig. 21) uses the sah~ object of Pure Data with a scaled slider set to a few thousand hertz to control the speed of the osc~ object. The output of the osc~ object is input into the float of the sample and hold object “sah~” to control the output clock speed. The sah~ object takes it’s input from the initial tone generator before the signal is sent to the F.M oscillators.

![fleascreen11] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-22%20at%2013.08.14.png)

**Fig. 21.** Screenshot to show sample and hold abstraction.

##Evaluation 

To consider the overall design of the Flea RSG, for it’s intended purpose to take the role as generative rhythm section real time composer for a laptop ensemble. It is felt by the author that the abstraction served its purpose well with no runtime faults occurring during the performance with the software.The   sonic timbre written into the software is capable of many different sounds though the use of the F.M synthesis model. However the resulting timbre from the lack of real-time pitch control could be a limitation.This is due to erratic nonlinear responses from the tone generator (dependanton on F.M modulation settings). Coupled with the restriction in notes midi notes 12-36. This was due to the ensemble requirment of bass only meant a 3 octave scale. To enforce the required frequencies needed. 
However upon considering this software as it stands for a “real-world” use. It is difficult to think of any real purpose it could serve. However with some “tinkering” of this software there maybe some potential.



##Resources


Bown, O. and Lexer, S. (2006) Continuous-Time Recurrent Neural Networks for Generative and Interactive Musical Performance. Lecture Notes in Computer Science, pp. 652-663.


Nierhaus, G. (2009) Algorithmic composition. Wien: Springer.



  
Any comments or quiries please E:mail the author Chris.holder@mail.com


##Generative Neural Midi composer

**Overview**

This generative neural midi player/composer is a rework of the abstraction for the laptop ensemble. The intended purpose of this software is to provide a Random neural generative midi composer with step sequencer. This software  
shares most of the functionality of the ensemble version. However the software has been overhauled to make real-world use from the previous abstraction.

![fleascreen1] (https://github.com/s1thlord/idon/blob/s1thlord-patch-1/Screen%20Shot%202016-05-23%20at%2014.24.49.png)

**Fig. 22.** Screenshot of Alpha version GUI of the software.

The overhaul consists of:

•	Midi output added to Neural network
•	GUI added 
•	External midi output now has note selector controller
•	Rhythm filler now added to each individual step of sequencer
•	Rhythm filler now has selectable random mode and fill here mode with X2, X3 and X4 note “hits”


##Video Demonstration link:

This video contains a small users guide and working demonstration of the Generative Neural Midi Composer.







## Software Download

Please note this software requires Pure Data to run free download available at: <https://puredata.info/downloads>


Laptop ensemble (original version)
<https://github.com/s1thlord/idon/blob/s1thlord-patch-1/laptop%20fm.pd>

Generative Neural Midi Composer (Alpha version)













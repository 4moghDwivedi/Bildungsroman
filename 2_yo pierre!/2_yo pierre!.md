# yo pierre!

composed by [Amogh Dwivedi](https://amoghdwivedi.com)

[YouTube video](https://youtu.be/eRPw4BkMbqM)

## Contents

* [Program Note](#program-note)


* [Technical synopsis](#technical-synopsis)


* [Instrument Design](#instrument-design)


* [Compositional Logic](#compositional-logic)


* [Aesthetic inspiration](#aesthetic-inspirations)


* [Errors and room for improvement](#errors-and-room-for-improvement)


## Program note
I was inspired by some of the textures and ideas I heard in Pierre Boulez’ orchestral piece Repons. I started to embrace that orchestration was sound design, and vice versa. 

In 2016, the Gaboan football player Pierre-Emerick Aubameyang signed for Arsenal Football Club. In the [promotional video](https://www.youtube.com/watch?v=rQv6rdo95rs) for the new signing, Henrikh Mkhitaryan, a former teammate of Pierre, soon to be reunited with him, began the video by saying, “Yo Pierre, you wanna come out here?”. The social media campaign surrounding this signing was named #yopierre. It stuck with me somehow. Apparently the [original quote](https://www.youtube.com/watch?v=IV4uzwJmYOk) is from The Jamie Foxx Show.

## Technical synopsis 


Any keywords mentioned in <ins>*underlined italics*</ins> can be found (i.e. cmd+F) in the Max patch for convenience.

Any button with a green ring can be played after the patch is initialized.

To initialize the patch, ***reduce your volume***,  press spacebar once, and press it again after a second or two. You should hear silence. Find the large number~ object and ensure it is not running. To play a rendering of the patch, press spacebar again, and ensure the number~ object is updating.


## Instrument design

* Repons gesture
    * Hits
        * mc.saw @chans 16. Most of its character comes from the alternation of deviate messages, and the compositional logic. 
        * although cutoff frequency values change alongside the /* multiplier values, its upper range changes with to which <ins>*phrase*</ins> is being played. These values are sent remotely (<ins>*cutoffHigh*</ins>)
    * 8th notes
        * FM. The carrier is an mc.cycle~ object that uses deviate messages to create some density. 
        * Alongside the /*~ values, the function objects' envelopes also modulate the deviation amount. 
    * Trill
        * basic saw~ sound that uses rect~ as modulation source for pitch.
* Trills
    * poly~, FM sound. Alongside general pitch information (note $1), it uses 4 changeable parameters, such as domain length, deviation amount, trill smoothing factor, and trill amount.
    * trill is achieved by using a rect~ lfo. The trill range (not in notes, but rather a loose frequency domain) is modulated by changing the parameters of the scale~ object.
    * The trill smoothing factor is achieved by using the rampsmooth~ object.
    * This value is added to the incoming note value. The trill is **unipolar**, as it only trills up to a randomly offset frequency value.
    * To enrich the sound and add stereo depth, randomized delay values are added to each channel. 
    * Both of these dual-mono channels are also randomly panned towards the end.
    * two gigaverb patches are used in the main patch. These were fetched directly from the gigaverb object. One of them sounds <ins>*large*</ins>, while the other sounds <ins>*short*</ins>.
    * extreme compression applied after the reverbs, adds a suffocating quality to the piece.
    * some quasi <ins>*sidechain*</ins> applied with the kick.

* 3 pads
    * L Rect + fm sine
    * C Rect + fm sine
    * R Saw + fm sine

* percussion rolled chords
    * a percussive FM sound meant to simulate a marimba, or a general idiophonic instrument. Consists of many layers-
        * an atonal-sounding cycle~ object whose carrier and modulating frequencies are randomized, but the dev amount is not
        * a pitched cycle~ object whose mod ratio and dev amount are randomized
        * an unpitched element whose envelopes are really short, which simulates the sound of a mallet striking the instrument (almost like a highly pitched kick sound)
        * In general, the domain values of the function are high, in order to simulate more resonance.
        * steal 1 is turned on, because of the high number of notes. With the low amplitude values in the later parts of the envelope, this is unlikely to produce easily audible retrigs of the envelope.

* kick

* hihat

* clusters



## Compositional Logic

* <ins>*Setup*</ins>
    * The total <ins>*composition duration*</ins> in minutes and seconds defines the phasor~ rate in Hz
    * This duration is stored in <ins>*value totalDuration*<ins>.
    * Various long term modulations are triggered throughout the composition, which makes use of an abstraction which calculates the total duration of a modulation with a predefined proportion, or <ins>*percentage of total composition duration*</ins>. This allows for all modulations to be scaled proportionally with respect to the total <ins>*composition duration*</ins>. I haven't tested this to extremes, since various metro objects operate independently of this logic and thus will not change the local time scales, but the mechanism should still work in durations reasonably close to the preferred 3m 0s duration.


* <ins>*Repons gestures*</ins>
    * These gestures were directly inspired by and transcribed from the opening of Boulez' **Repons**. They were simplified in terms of time signatures changes, and both the harmonic content and rhythmic pulses were picked out mostly by ear (although I did look at the score).
    * There are <ins>*3 distinct phrases*</ins> in this gesture. Two counter mark both the <ins>pulses</ins> and the <ins>*phrases*</ins>, and their values trigger gate controls.
    * Hits
        * The same <ins>*mc.saw~*</ins> object is reused for all of these hits.
        * specific subdivisions of the counter were isolated using the select object in order to mimic the original music's rhythm.
        * General atonal harmonies are produced by making use of the <ins>*deviate*</ins> message. Clusters are produced with an high deviate amount <ins>*550*</ins>, while single notes are produced with lower values. There is an unnecessarily high deviate value in one of these- <ins>*12111*</ins>- but since gave me what I was looking for I didn't change it.
        * The 3rd phrase triggers its gesture after a threshold- <ins>*>=9*</ins>- and articulates subsections of the larger phrase structure.
    * Trill
        * A simple trill executes at the first phrase.
    * 8th notes
        * these 8th note hits are generated by reusing the <ins>*pulses*</ins> value, offsetting it, and using the mod operator to divide them into groups of four. 
        * Two separate envelopes are used in order to add more character to the sound (i.e. articulation).

* Trills
    * A <ins>*macro changes note length and trigger frequency*</ins> simultaneously. One of the values goes directly to the poly~ attribute, while the other is used to change the parameters of an r slider, which change the range of values in which the metro frequency for gesture 1 and is defined.
    * <ins>*gesture 1*</ins> features relatively infrequently triggered notes. there is a 30% chance that these notes will be between C2 and E3, and 70% chance that they wlil be between C4 and F6. This was done in order to follow traditional voicing logic, where lower notes are sparse, while higher notes are more abundant. After a randomized iteration of notes- <ins>*counter 3*</ins> object- both the metro frequency and said iterations of the upcoming cycle are raondomly generated once again. Note that the metro frequency is generated from the rslider, and is interpreted as <ins>*$f2*</ins> and <ins>*$f3*</ins>, which are cold inlets. The constant modulation of the macro would then not unintentionally cause the metro frequency to update.
    * <ins>*gesture 2*</ins> is built off the logic of the previous gesture, except it is a lot more dense. The density is achieved by using the initial trigger and using that to create 2 additional successive notes achieved through the <ins>*delay*</ins> object. Note that the <ins>*2nd note*</ins> has a decidedly shorter delay time than the <ins>*3rd note*</ins>, so that no three notes are triggered simultaneously (although they will sound simultaneously, because of the long domain values).
    * the parameters of the poly~ object are modulated over time to create pitch and timbral interest. These include the trill smooth and trill amount value and deviation value, respectively. The decision to such a numerically motivated gesture was inspired by the intersection between sound design and composition.
    * basic fade-in/fadeouts take place using basic line~ modulations.
    * In the mid section of the piece, the kick "disables" and "enables" the poly~ patch entirely with each successive kick, using the <ins>*mute 0 1, mute 0 0*</ins> messages. This was initially considered somewhat dangerous but it works perfectly fine. Whether the trills are currently sounding or not influences the timbres of the other instruments, in order to exaggerate the muting of the trills. This info is sent remotely using <ins>*kickInfo*</ins>.

## Aesthetic inspirations

*  Obviously, Pierre Boulez' **"Repons"**.


## Errors and room for improvement

* Convoluted patching style, generally speaking. Everything works though.

* changed trill amount destination to 5

* one of the pads was off! and keeps droning.

* mc.svf was taking filter info from second outlet of number~ object. it is now rerouted directly from the scale~ object.

* deviate message in pads was switched the other way around. either a careless error or serendipitous creative choice, but it actually didn't make a difference. it's possible i made the mistake deliberately.

* lack of familiarity with MC at the time (inability to add vibrato with deviate messages).

* missed opportunities in FM 

* trills unused cutoff range value

* more space/punctuation in gesture two can be added by offestting the macro values from the r slider. 
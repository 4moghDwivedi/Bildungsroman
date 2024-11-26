# yo pierre!

composed by [Amogh Dwivedi](https://amoghdwivedi.com)

[Listen here](https://youtu.be/eRPw4BkMbqM)


* [Bildungsroman](/README.md)
    * [traintrip](/1_traintrip/1_traintrip.md)
    * [lemonrinse](/3_lemonrinse/3_lemonrinse.md)

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

To initialize the patch, ***reduce your volume***,  press spacebar once, and press it again after a few seconds. You should hear silence. Find the large number~ object and ensure it is not running. To play a rendering of the patch, press spacebar again, and ensure the number~ object is updating.

There is no firm sense of meter or key in this piece, as the main focus is the transformation of timbres. Notable gestures include the trilling, initially wide, which resolves to a stillness; the pads, which initially modulate erratically, resolving to a singular erratic control signal; the kicks, which impact the timbres of the other instruments in a responsive manner. A transcription of the opening of Boulez's Repons is attempted too. 


## Instrument design

* <ins>*Repons gesture*</ins>
    * Hits
        * mc.saw @chans 16. Most of its character comes from the alternation of deviate messages, and the compositional logic. 
        * Although cutoff frequency values change alongside the /* multiplier values, its upper range changes with to which <ins>*phrase*</ins> is being played. These values are sent remotely (<ins>*cutoffHigh*</ins>)
    * 8th notes
        * FM. The carrier is an mc.cycle~ object that uses deviate messages to create some density. 
        * Alongside the /*~ values, the function objects' envelopes also modulate the deviation amount. 
    * Trill
        * Basic saw~ sound that uses rect~ as modulation source for pitch.
* <ins>*Trills*</ins>
    * poly~, FM sound. Alongside general pitch information (note $1), it uses 4 changeable parameters, such as domain length, deviation amount, trill smoothing factor, and trill amount.
    * Trilling is achieved by using a rect~ lfo. The trill range (not in notes, but rather a loose frequency domain) is modulated by changing the parameters of the scale~ object.
    * The trill smoothing factor is achieved by using the rampsmooth~ object.
    * This value is added to the incoming note value. The trill is **unipolar**, as it only trills up to a randomly offset frequency value.
    * To enrich the sound and add stereo depth, randomized delay values are added to each channel. 
    * Both of these dual-mono channels are also randomly panned towards the end.
    * Two gigaverb patches are used in the main patch. These were fetched directly from the gigaverb object. One of them sounds <ins>*large*</ins>, while the other sounds <ins>*short*</ins>.
    * Extreme compression applied after the reverbs, adds a suffocating quality to the piece.
    * Some quasi <ins>*sidechain*</ins> applied with the kick.

* <ins>*tripads*</ins>
    * L Rect + fm sine
        * These pads are a mix of an cluster-like mc.rect~/mc.saw~ object and FM with mc.cycle~.
        * The pitches produced by the kslider object were actually routed to the end inlet of the pack object, which means that the ksliders actually declare the deviation amount, and not the pitch. This may not have been a mistake- see [errors and room for improvement](#errors-and-room-for-improvement) 
        * The <ins>*square LFO*</ins> mentioned below is reused for the deviation amount for the FM sound.

    * VCA design
        * This instrument is somewhat unusual because there are two equally important amplitude modulation sources (i.e. envelope triggered, as well as <ins>*square LFO*</ins>). Alongside the envelope-triggered modulation, the amplitude of each pad is individually controlled using the following design: a rect~ signal whose frequency is being randomized at a fixed frequency (600 ms). The signal is then smoothed using rampsmooth~, whose ramp up and ramp down values are being modulated using an infinite line generated (<ins>*linegen*</ins>). 
        * The infinite line itself is scaled to different values in order to produce different curvatures, which is controlled by the <ins>*articulation control*</ins> dial. Note that the dial modulates the rslider below it in a quasi exponential manner. 
        * Therefore, articulations can either be very jagged, almost staccato, or on the other end, very legato.

    * Simple panning at the extremes. Note that the reverb signal too is panned hard L or R, which is unconventional. Initially, the L pad is hard left, the C pad is centered, and, surprise, the R pad is hard right. These modulate over time.
    * The signal chain ends with some overdrive. the drive amount itself is modulated in the following ways-
        * linearly, to a destination value
        * responsively to <ins>*kickInfo*</ins> (see kick under [compositional logic](#compositional-logic))
    * End filtering
        * Some basic filter sweeps at the end with lores~.

* <ins>*Percussion*</ins> (rolled chords)
    * A percussive FM sound meant to simulate a marimba, or a general idiophonic instrument. Consists of many layers-
        * An atonal-sounding cycle~ object whose carrier and modulating frequencies are randomized, but the dev amount is not
        * A pitched cycle~ object whose mod ratio and dev amount are randomized
        * An unpitched element whose envelopes are really short, which simulates the sound of a mallet striking the instrument (almost like a highly pitched kick sound)
    * In general, the domain values of the function are high, in order to simulate more resonance.
    * "steal 1" is turned on, because of the high number of notes. With the low amplitude values in the later parts of the envelope, and the reverb, this is unlikely to produce easily audible retrigs of the envelope.

* <ins>*kick*</ins>
    * An aggressive sounding kick inspired by hardstyle.
    * poly~ implementation. Alongside designing a typical kickdrum, the patch also randomizes "amount of frequency shift". 
    * The domain of this function's kick modulates to extremely long durations. This produces low bass tones for an unusually extended period of time. This randomized range's <ins>*upper limit*</ins> is modulated over time.
    * The kick signal is split into <ins>*sound 1/sound 2*</ins>. Both have their own individualized signal chain processes. 
        * <ins>*sound 1*</ins> is lowpassed, sent into a reverb, followed by a pitch shifter, i.e. the reverberation itself is pitchshifted.
        * <ins>*sound 2*</ins> is highpassed, and sent into a frequency shifter whose parameters are randomized with every hit. The output of this signal is sent into a reverb. Both the L and R channels are then modulated using a rect~ LFO, whose frequencies are randomly modulated using a randomized line generator- <ins>*4moghInfiniteLine*</ins> for each channel.The output of this sound is then slightly distorted (even after the reverb).
    * The sum of <ins>*sound 1/sound 2*</ins> are then divided in to a dry/wet chain, where the <ins>*gigareverb*</ins> parameters is long and washy. This is controlled using the <ins>*actual dry wet*</ins> knob.
    * The final touch involves a pitch shifter which tranposes the entire signal up by 2 octaves, but the mix amount is extremely low (3).

* <ins>*hihat*</ins>
    * A simple hihat sound made using noise~. 
    * poly~ implementation. The incoming symbol modulates the function's amplitude and domain value.
    * The output is then randomly panned with each hit. The <ins>*M4L.pan1~*</ins> is placed unconventionally, and the audio signal from previously sounding hihats with longer domain lengths will also be panned alongside that from the the shorter ones. In hindsight this is unlikely a technical error because I already knew how to randomizing panning within poly~- it seems to be an aesthetic choice.
    * The signal is then highpassed, pitch-shifted, and sent to <ins>*degrade~*</ins>.
* <ins>*clusters*</ins>
    * Basic mc.saw~. clusters are generated using the deviate message.
    * The reverb mix amount is inversely related to the cutoff frequency. It is modulated over time, and sounds as if the instrument is getting closer.



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
        * Specific subdivisions of the counter were isolated using the select object in order to mimic the original music's rhythm.
        * General atonal harmonies are produced by making use of the <ins>*deviate*</ins> message. Clusters are produced with an high deviate amount <ins>*550*</ins>, while single notes are produced with lower values. There is an unnecessarily high deviate value in one of these- <ins>*12111*</ins>- but since gave me what I was looking for I didn't change it.
        * The 3rd phrase triggers its gesture after a threshold- <ins>*>=9*</ins>- and articulates subsections of the larger phrase structure.
    * Trill
        * A simple trill executes at the first phrase.
    * 8th notes
        * These 8th note hits are generated by reusing the <ins>*pulses*</ins> value, offsetting it, and using the mod operator to divide them into groups of four. 
        * Two separate envelopes are used in order to add more character to the sound (i.e. articulation).

* Trills
    * A <ins>*macro changes note length and trigger frequency*</ins> simultaneously. One of the values goes directly to the poly~ attribute, while the other is used to change the parameters of an r slider, which change the range of values in which the metro frequency for gesture 1 and is defined.
    * <ins>*gesture 1*</ins> features relatively infrequently triggered notes. There is a 30% chance that these notes will be between C2 and E3, and 70% chance that they wlil be between C4 and F6. This was done in order to follow traditional voicing logic, where lower notes are sparse, while higher notes are more abundant. After a randomized iteration of notes- <ins>*counter 3*</ins> object- both the metro frequency and said iterations of the upcoming cycle are raondomly generated once again. Note that the metro frequency is generated from the rslider, and is interpreted as <ins>*$f2*</ins> and <ins>*$f3*</ins>, which are cold inlets. The constant modulation of the macro would then not unintentionally cause the metro frequency to update.
    * <ins>*gesture 2*</ins> is built off the logic of the previous gesture, except it is a lot more dense. The density is achieved by using the initial trigger and using that to create 2 additional successive notes achieved through the <ins>*delay*</ins> object. Note that the <ins>*2nd note*</ins> has a decidedly shorter delay time than the <ins>*3rd note*</ins>, so that no three notes are triggered simultaneously (although they will sound simultaneously, because of the long domain values).
    * The parameters of the poly~ object are modulated over time to create pitch and timbral interest. These include the trill smooth and trill amount value and deviation value, respectively. The decision to such a numerically motivated gesture was inspired by the intersection between sound design and composition.
    * Basic fade-in/fadeouts take place using basic line~ modulations.
    * In the mid section of the piece, the kick "disables" and "enables" the poly~ patch entirely with each successive kick, using the <ins>*mute 0 1, mute 0 0*</ins> messages. This was initially considered somewhat dangerous but it works perfectly fine. Whether the trills are currently sounding or not influences the timbres of the other instruments, in order to exaggerate the muting of the trills. This info is sent remotely using <ins>*kickInfo*</ins>.

* <ins>*tripads*</ins>
    * Envelopes are triggered with a metro object initialized to 6000 ms.
    * The <ins>*domain/metro slider*</ins> incrementally changes both the metro frequency, as well as the domain values for all 3 envelopes across time.

    * Pitch design
        * The pitch operates independently of the envelope triggers. Each pitch value, instead of instantly triggering with a new note, is rather buffered in an int object.
        * The pitches of all three pads are changed slowly throughout the piece.
        * Some aspects of the sound design are key-tracked. The deviation amount, for instance, is inversely related to the pitch. On the other hand, the total amplitude, is similarly related to pitch.
        * The pads eventually start <ins>*changing pitch*</ins> by offsetting the value from the kslider with small amounts. Note that these offset values themselves are not key-tracked- see [errors and room for improvement](#errors-and-room-for-improvement).
        * The pitches are offset by an octave, and this value is triggered with every delayed kick. the offset is not instantaneous though, as the value is sent into a cold inlet- see [errors and room for improvement](#errors-and-room-for-improvement).

    * <ins>*Mixing of modulation signals*</ins>
        * Each pad has a uniquely randomized LFO in the beginning, but the first pad's VCA control signal is gradually mixed with the the other two.
        * At some point in the piece, all pads VCA amount gradually coalesce to the first pad's signal. 
        * This is achieved smoothly because of the long duration of the modulation itself, alongside the predominance of the erratic LFO's role in amplitude shaping. The eventual unity in hearing only one erratic LFO in place of 3 marks a kind of resolution.

* <ins>*kick*</ins>
    * Similar to the <ins>*trills*</ins>, the domain value is modulated over time, and converges to a longer value by the end.
    * Timing of the triggers of the kicks is controlled by setting up a phasor, whose frequency is 4n, a relative beat value. the tempo is 61 BPM, and this transport is only relevant to the kick and hihats. 
    * The kicks are triggered on beats 1 and 3, and initially are triggered on time. They eventually sound completely out of time because of some intentional delaying.
    * The delays are setup so that the <ins>*second*</ins> kick is always appropriately distanced from the <ins>*first*</ins>.
    * The upper range in the randomized delay length is modulated over time (with the slider aptly labelled as <ins>*rhythm wonky?*</ins>). This creates unpredictable rhythms in the piece, yet it sounds familiar and consistent because of the compositional gesture described below.
    * Both the <ins>*first*</ins> and the <ins>*second*</ins> kicks then start to alternate the sound design of the overall patch. several parameters control this compositional gesture:
        * <ins>*delayKick*</ins>
            * This keeps track of the delayed bang.
            * Triggers an octave displacement in the pads.
            * Triggers an instant offset of frequency shift amount in the aforementioned <ins>*sound 2*</ins>
            * Randomizes the metro frequency which controls gating info for the hihat pattern.
        * <ins>*kick 1*</ins>/<ins>*kick2*</ins>
            * Controls the muting of <ins>*trills*</ins>
        * <ins>*kickInfo*</ins>
            * The sound design changes were conceived not as changing due to every alternate kick, but rather whether the trills were muted or not. so, while the kicks eventually start to mute the trills, the trills, when muted, in turn produce other sound design changes.
            * These sound design changes include drastically and instantly altering parameters of effects such as pitchshifters, reverb, but most notably, <ins>*degrade~*</ins>
        * <ins>*sidechain*</ins>
            The kick eventually increasingly momentarily attenuates the pads over time. Achieved through a simple function object.


* hihat
    * The phasor~ value that tracks quarter notes is subdivided using rate~ with an arbitrarily picked rate multiplier (0.1). 
    * Bangs from this are then extracted and tracked using a counter that counts in cycles of 7.
    * Whether any of these beats is played is controlled using a set of decide and gate objects, and the likelihood of any beat being active is controlled by the <ins>*metro 500*</ins> object that operates independently of any of the time domains in the piece.
    * With each kick-hit, the frequency of this metro object is randomized. 
    * The hihat will either have a <ins>*short*</ins> domain- 10 ms- or a <ins>*long*</ins> value whose upper range of randomized values gets incrementally longer.
    * Random panning of the hihat takes place at the poly~ output.

* <ins>*clusters*</ins>
    * They are simply turned on or off. a new set of frequencies is generated at the beginning of every patch. 
    * As noted in the instrument design notes, as the frequency cutoff value increases, the reverb mix amount decreases. Hints at the possibility of what can be done to a sound even if its pitch and rhythm are constant.

## Aesthetic inspirations

* Obviously, Pierre Boulez' **"Repons"**, particularly in the semi-transcribed gesture.
* Contemporary classical music in general, although the emphasis is on timbre, and not necessarily deeply formally organized form/structure (very friendly reminder that <ins>*yo pierre!*</ins> is a prelude, not a multi-movement orchestral piece.).
* The kick in this patch was inspired by hardstyle, a subgenre in EDM.
* A lot of sound design techniques were inspired by Jason Petrin's Advanced Production Techniques course at Berklee.

## Errors and room for improvement

* Convoluted patching style, generally speaking. Everything works though.

* Repons gesture counter resets are not consistent.

* Changed trill amount destination to 5.

* <ins>*tripads*</ins>
    * The <ins>*square LFO*</ins> design was actually not working because of a silly error. this has been fixed in the patch.
    * Offset value in pads was not keytracked, and only responded to output of kslider. this has been fixed now.
    * Octave displacement values were sent with every delayed kick bang, although the were routed to a cold inlet. this could still be a feasible compositional choice- maybe one thing in the patch does NOT instantly respond to the kick.
    * Unused rslider. which controlled range of a hipass frequency cutoff of mc.svf~.
    * <ins>*makeshift fadein/fadeout*</ins> were added to stop the unintentional droning of the tripads.
    * An mc.svf was taking filter info from second outlet of number~ object. It is now rerouted directly from the scale~ object.
    * Deviate message in pads was switched the other way around. Either a careless error or serendipitous creative choice, but it actually didn't make a difference. It's possible I structured this deliberately. In any case, for propriety's sake, I have added a zl.rot 1 object.
    * Lack of familiarity with MC at the time prevented further developments, such as the inability to add vibrato with deviate messages. I have figured this out since then.

* <ins>*trills*</ins>
    * An unused cutoff range value parameter.
    * More space/punctuation in gesture two can be added by offsetting the macro values from the r slider. 

* Unnecessarily complicated kick logic.

* Hihat's metrical designs are somewhat arbitrary. The overall sound is just a stream of notes rather than anything organized in groups of 7. More repetition and deliberate grooves could have made the instrument more deliberate.



* [go to top](#yo-pierre)
* [Bildungsroman](/README.md)
    * [traintrip](/1_traintrip/1_traintrip.md)
    * [lemonrinse](/3_lemonrinse/3_lemonrinse.md)

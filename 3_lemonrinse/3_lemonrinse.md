# lemonrinse

composed by [Amogh Dwivedi](https://amoghdwivedi.com)

[Listen here (and watch Jitter Visuals)](https://youtu.be/UPX6tAA74dM)


* [Bildungsroman](/README.md)
    * [traintrip](/1_traintrip/1_traintrip.md)
    * [yo pierre!](/2_yo%20pierre!/2_yo%20pierre!.md)


## Contents

* [Program Note](#program-note)


* [Technical synopsis](#technical-synopsis)


* [Instrument Design](#instrument-design)


* [Compositional Logic](#compositional-logic)


* [Aesthetic inspiration](#aesthetic-inspirations)


* [Errors and room for improvement](#errors-and-room-for-improvement)


## Program note
I found great joy and comfort in jazz forms throughout my Berklee career, and decided to set lemonrinse in a 12-bar jazz blues in F. In terms of generative techniques this one was probably my favorite, because despite its predictable form it sounds organic to me, especially in terms of the modulations. My dive into ambient music around that time also influenced the mood of this piece.


## Instructions

Unlike the other patches, this starts straight away. Turn on audio to execute the patch.

Any keywords mentioned in <ins>*underlined italics*</ins> can be found (i.e. cmd+F) in the Max patch for convenience.

The piece's macrostructure follows a 12 bar jazz blues format, and unlike other pieces in *Bildungsroman*, it has no definite ending- the final rendering was edited in Ableton Live. Randomized voicings are generated through predefined harmonic structures which outline the harmonic progression. Additional modulation of parameters takes place at cadential points (such as the turnarounds). The greatest source of sonic inspiration was the potential of a string orchestra, and how many individuals could articulate vibrato at their own rates.


## Instrument design
- <ins>*4synth*</ins> and <ins>*4synth2*</ins>
   
    * General sound design
         * Additive synthesis. *poly~* implementation. inputs to matrix~ include cycle~, rect~, saw~, tri~.
         * matrix~ outputs discrete L and R channels
         * Constant modulation of amplitude levels for oscillators. the <ins>*matrixctrl*</ins> objects in the poly~ object show this visually.
         * Note that <ins>*4synth*</ins> produces stepped changes in amplitude, while *4synth2* produces smoothed modulations.
         * The output is a sum of both *4synth* and *4synth2*, and regularly <ins>*crossfades*</ins> between the two using a drunk object.
         * *filterUpper* values define the upper range of cutoff frequency modulation, with the VCA envelope as its source. This is with a unique randomized line generator (<ins>*4moghInfiniteLinefast*</ins>).
    * Vibrato design
         * <ins>*vibrato4synth*</ins> (in the main patcher) is the global vibrato amount. parameters include frequency range, and ramp time between each intermediate destination value. The intention is to have a loosely synchronized vibrato.
         * Vibrato depth is controlled independent of macrostructure (i.e. standalone metro objects that trigger irrespective of <ins>*barNumber*</ins>)
         * Each oscillator has a uniquely delayed vibrato input
         * Each poly~ instance has a unique randomized line generator (<ins>*4moghInfiniteLinefast*)</ins> connected the maximum range of the delay amount
    * Mono/stereo knob toys with expectation of stereo depth. a value of 0 duplicates L channel into R, while 1 enables maximum stereo depth
    
- <ins>*unmeasured trills*</ins>
    * Basic FM sound, dual mono
    * Unmeasured trills accomplished with square wave LFO. LFO frequency is modulated with <ins>*4moghInfiniteLinefast*</ins>, which generates never-ending line segments with random destination and ramp time values.
    * Trills oscillate either between notes F1 and F2 (in the case of measure 1); or slide from A#2 to ~ D#5 back to F1 and F2 (in the case of measure 5).
    * Envelope modulates both channels' vca, as well as the FM deviate output.

 * <ins>*blues call*</ins>
    * FM saw (C) + sine (carrier), tripled at octaves.
    * Envelope alternates between two function objects, in order to add a pulsating/undulating effect. any clicks/pops that could have been produced because of spikes in amplitude level are avoided because of reverb settings.

 * <ins>*riser*</ins>
    * FM sound. Inspired by EDM riser sound effects that create anticipation for the "drop".
    * Increase in pitch directly modulates with main clock source (phasor). The riser pitch is a literal representation of the ramp that dictates any one measure in the piece.
* <ins>*drip drop*</ins>
    * Inspired by a Csound sampler whose author I am unable to remember. The samples consisted of water droplet sounds.
    * Two percussive FM plucks. Both have a different envelopes in order to generate rhythmic and articulative interest.
    * Different delay rates are used in order to generate varying grooves. These grooves are controlled at a microlevel (in ms, and not larger note values like quarter notes/eighth notes).

## Compositional Logic
* Main clock
    * The <ins>*phasor~*</ins> object tracks one measure. Each measure is incremented from 1 through 12- the number of measures in a blues- using the remotely sent and received <ins>*barNumber*</ins>. This is a crucial element in tracking the overarching form.
    * Using <ins>*rate~*</ins>, other subdivisions of the measure are counted too. 
    * Articulating cadential moments and resolutions
        * Several discrete modulations and events take place at cadential moments. This is done in service of articulating the form.
        * <ins>*ritardandi*</ins> at measures 4, 8, 10 (i.e. *before* chords I7, IV7, and the very last I7). right after the completion of these measures, the tempo snaps back to the original rate.
        * An 85% probability that the unmeasured trills take place at measure 1; 15% probabilty the sliding ones take place at measure 5
        * <ins>*filter*</ins> cutoff frequency mods. A ramp which changes the maximum bias value for it is sent to the poly~ object at measure 4, 8, 11. There are two schemes for this: the first scheme attenuates the bias to a randomized value between 7k-13k measures 4, 8, 11, and all the other measures generate a random bias value between 500-1k (i.e. the filter opens before the cadential moment, before closing at the resolution). The second scheme is the opposite (i.e. the filter closes before the cadential moment, and opens at the arrival).
        * <ins>*"vibrato bias"*</ins> values are changed at measure one, in order to magnify the first measure of the form.
        * <ins>*dynamic*</ins> variations are produced at the first measure of every section (i.e. 1, 5, 9). 3 dynamic levels in linear amplitude are declared for <ins>*4synth*</ins>, <ins>*drip drop*</ins>, and <ins>*blues call*</ins>. the algorithm generates permutations for all three dynamic levels and sends them remotely to each instrument.
        * A 15% chance that <ins>*4synth/4synth2*</ins> does not run at all at measures 1 and 9. The long silences create space.

* <ins>*4synth/4synth2*</ins>
    * A <ins>*new voicing*</ins> is generated on beats 1 and 3.
    * Pitch content is determined by the <ins>*coll*</ins> objects. 
        * Voicings are generated using weighted probability. Each index features a 6-note voicing. The note is formatted as "probability value-midi note number". Early indexed entires have a higher probability of sounding, and are strong harmonic choices (root, guide tones, 5th). Later ones, with lower probabilities of sounding, are acceptable tensions, and the last note is a completely adventurous note choice (such as an E natural on an F7 chord).
        * <ins>*bluesChords1*</ins> is a conservative blues progression, while <ins>*bluesChords2*</ins> is a progressive and features more chromaticism. Check the score in the .maxpat file for voicings. Percentages show chance of the notes in the voicing sounding.
        * In order to produce interesting voice leading results, randomized <ins>*octave displacement*</ins> is performed on each note. This is applied to all notes besides the root note of each chord in both progression, which remains strictly in place. A new value is generated every 1st measure.
    * A randomized <ins>*delay between notes*</ins> is added in order to achieve a strumming effect.
    * A simple crossfade between <ins>*4synth/4synth2*</ins> in order to produce sonic variations.
    * At every moment of resolution, <ins>*4synth/4synth2*</ins> may <ins>*switch voicings*</ins>. different probabilities for different moments in the form.
    * At measures 1 and 5, a microtonal effect is achieved by generating a random decimal value and summing it with the MIDI note number- this is <ins>*fractional MIDI*</ins>. The console prints every sounded MIDI note, and should be helpful.
    * The <ins>*mono/stereo effect*</ins> experiments with the stereo depth. A value of 0 duplicates the L signal into both channels (i.e. L+L), while a value of 0 does the same with the R channel (R+R). A value of 50 restores the stereo depth field back to normal (L+R).
    * At some moments in the piece, the synth may sound extremely washy, because <ins>*crazy verb*</ins> is turned all the way to max. Occurs probabilistically at measures 1 and 9.
    * Attack values of the synth are changed by sending poly~ a randomly generated value in ms, which changes the first point in the function graph associated with the ADSR. If a long attack duration- 1500-2300 ms- is generated, then the strumming function is automatically disabled by setting all delay values to 0.
*  <ins>*riser*</ins>
    * The amplitude of the riser is triggered at every cadential moment, at measures 4, 8, and 12. once clock arrives at the moment of resolution, the instrument is turned off. 
    * The <ins>*mod ratio*</ins> is randomized at measures 1, 5, 9.
    * The <ins>*dev amount*</ins> changes with the phasor. The upper range in this translation is randomized alongside the <ins>*mod ratio*</ins> at measures 1, 5, 9.
* <ins>*blues call*</ins>
    * This instrument is turned on at measures 2, 6, 10, and is faded out at any other measures.
    * Only two notes are heard in this blues call. F, Ab, A and Bb are the only notes played by this instrument. F is held constant throughout the form (it is the tonic), while the the second note depends on the current harmony (A works on I7, Ab works on IV7, and Bb is enabled at the G-7 chord, for instance.)
* <ins>*drip drop*</ins>
    * Instrument turns on at measures 1, 5, 9. the same line~ object changes filter cutoff frequency for the instrument.
    * Several variations <ins>*delay amounts*</ins> are added to both hits in order to produce varying time feels.

## Aesthetic inspirations
 * The synths at the end of Mac Miller's **"Come Back to Earth"**
 * Toru Takemitsu's **"Dorian Horizon"** and **"Requiem"**, their slow character. Also his orchestral piece **"Archipelago S"**, and the dynamic and timbral swells in the climactic moments of the piece.
 * General soundscape in Brian Eno's **"Wind in Lonely Fences"**
 * The vibrato in Peggy Lee's recording of **"If You Could See Me Now"**

A few months after I finished this piece, I heard similarities between my *lemonrinse* and Autechre's *Altibzz*. Oh well!


## Errors and room for improvement
 * poly~ takes 3 parameters as input. achieved unsuccessful results with modulating range and domain values. 
 * some unfinished ideas, such as <ins>*4mogh4synthMetro*</ins>, <ins>*4mogh4synthMetroDelay*</ins>.


* [go to top](#lemonrinse)
* [Bildungsroman](/README.md)
    * [traintrip](/1_traintrip/1_traintrip.md)
    * [yo pierre!](/2_yo%20pierre!/2_yo%20pierre!.md)
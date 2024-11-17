# lemonrinse

composed by [Amogh Dwivedi](amoghdwivedi.com)

[YouTube video with Jitter visuals](https://youtu.be/UPX6tAA74dM)

## Contents

* [Program Note](#program-note)


* [Technical synopsis](#technical-synopsis)


* [Instrument Design](#instrument-design)


* [Compositional Logic](#compositional-logic)


* [Aesthetic inspiration](#aesthetic-inspirations)


* [Errors and room for improvement](#errors-and-room-for-improvement)


## Program note
I found great joy and comfort in jazz forms throughout my Berklee career, and decided to set lemonrinse in a 12-bar jazz blues in F. In terms of generative techniques this one was probably my favorite, because despite its predictable form it sounds organic to me, especially in terms of the modulations. My dive into ambient music around that time also influenced the mood of this piece.


## Technical synopsis 
The piece's macrostructure follows a 12 bar jazz blues format, and unlike other pieces in *Bildungsroman*, it has no definite ending- the final rendering was edited in Ableton Live. Randomized voicings are generated through predefined harmonic structures which outline the harmonic progression. Additional modulation of parameters takes place at cadential points (such as the turnarounds). The greatest source of sonic inspiration was the potential of a string orchestra, and how many individuals could articulate vibrato at their own rates.

Any text mentioned in **bold** can be found (i.e. cmd+F) in the patch for convenience.


## Instrument design
- ***4synth** and **4synth2****
   
    * General sound design
         * Additive synthesis. **poly~** implementation. inputs to matrix~ include cycle~, rect~, saw~, tri~.
         * matrix~ outputs discrete L and R channels
         * constant modulation of amplitude levels for oscillators. the **matrixctrl** objects in the poly~ object show this visually.
         * note that **4synth** produces stepped changes in amplitude, while **4synth2** produces smoothed modulations.
         * The output is a sum of both ***4synth*** and ***4synth2***, and regularly **crossfades** between the two using a drunk object.
         * **filterUpper** values define the upper range of cutoff frequency modulation, with the VCA envelope as its source. This is with a unique randomized line generator (***4moghInfiniteLinefast***).
    * Vibrato design
         * **vibrato4synth** (in the main patcher) is the global vibrato amount. parameters include frequency range, and ramp time between each intermediate destination value. The intention is to have a loosely synchronized vibrato.
         * Vibrato depth is controlled independent of macrostructure (i.e. standalone metro objects that trigger irrespective of **barNumber**)
         * Each oscillator has a uniquely delayed vibrato input
         * Each poly~ instance has a unique randomized line generator (***4moghInfiniteLinefast***) connected the maximum range of the delay amount
    * Mono/stereo knob toys with expectation of stereo depth. a value of 0 duplicates L channel into R, while 1 enables maximum stereo depth
    
- ***unmeasured trills***
    *  Basic FM sound, dual mono
    * Unmeasured trills accomplished with square wave LFO. LFO frequency is modulated with ***4moghInfiniteLinefast***, which generates never-ending line segments with random destination and ramp time values.
    * Trills oscillate either between notes F1 and F2 (in the case of measure 1); or slide from A#2 to ~ D#5 back to F1 and F2 (in the case of measure 5).
    * Envelope modulates both channels' vca, as well as the FM deviate output.

 * ***blues call***
    * FM saw (C) + sine (carrier), tripled at octaves.
    * Envelope alternates between two function objects, in order to add a pulsating/undulating effect. any clicks/pops that could have been produced because of spikes in amplitude level are avoided because of reverb settings.

 * ***riser***
    * FM sound. Inspired by EDM riser sound effects that create anticipation for the "drop".
    * Increase in pitch directly modulates with main clock source (phasor). The riser pitch is a literal representation of the ramp that dictates any one measure in the piece.
* ***drip drop***
    * Inspired by a Csound sampler whose author I am unable to remember. The samples consisted of water droplet sounds.
    * Two percussive FM plucks. Both have a different envelopes in order to generate rhythmic and articulative interest.
    * Different delay rates are used in order to generate varying grooves. These grooves are controlled at a microlevel (in ms, and not larger note values like quarter notes/eighth notes).

## Compositional Logic
* main clock
    * the **phasor~** object tracks one measure. Each measure is incremented from 1 through 12- the number of measures in a blues- using the remotely sent and received **barNumber**. This is a crucial element in tracking the overarching form.
    * Using **rate~**, other subdivisions of the measure are counted too. 
    * Articulating cadential moments and resolutions
        * Several discrete modulations and events take place at cadential moments. This is done in service of articulating the form.
        * **ritardandi** at measures 4, 8, 10 (i.e. *before* chords I7, IV7, and the very last I7). right after the completion of these measures, the tempo snaps back to the original rate.
        * an 85% probability that the unmeasured trills take place at measure 1; 15% probabilty the sliding ones take place at measure 5
        * **filter** cutoff frequency mods. A ramp which changes the maximum bias value for it is sent to the poly~ object at measure 4, 8, 11. There are two schemes for this: the first scheme attenuates the bias to a randomized value between 7k-13k measures 4, 8, 11, and all the other measures generate a random bias value between 500-1k (i.e. the filter opens before the cadential moment, before closing at the resolution). The second scheme is the opposite (i.e. the filter closes before the cadential moment, and opens at the arrival).
        * **"vibrato bias"** values are changed at measure one, in order to magnify the first measure of the form.
        * **dynamic** variations are produced at the first measure of every section (i.e. 1, 5, 9). 3 dynamic levels in linear amplitude are declared for **4synth**, **drip drop**, and **blues call**. the algorithm generates permutations for all three dynamic levels and sends them remotely to each instrument.
        * a 15% chance that **4synth/4synth2** does not run at all at measures 1 and 9. The long silences create space.

* *4synth/4synth2*
    * a **new voicing** is generated on beats 1 and 3.
    * pitch content is determined by the **coll** objects. 
        * voicings and weighted probability. generate new voicings. each index features a 6-note voicing. The note is formatted as *probability value* -  *midi note number*. Early indexed entires have a higher probability of sounding, and are strong harmonic choices (root, guide tones, 5th). Later ones, with lower probabilities of sounding, are acceptable tensions, and the last note is a completely adventurous note choice (such as an E natural on an F7 chord).
        * **bluesChords1** is a conservative blues progression, while **bluesChords2** is a progressive and features more chromaticism. check the score in the .maxpat file for voicings. percentages show chance of the notes in the voicing sounding.
        * In order to produce interesting voice leading results, randomized **octave displacement** is performed on each note. This is applied to all notes besides the root note of each chord in both progression, which remains strictly in place. A new value is generated every 1st measure.
    * a randomized **delay between notes** is added in order to achieve a strumming effect.
    * a simple crossfade between **4synth/4synth2** in order to produce sonic varioations.
    * at every moment of resolution, **4synth/4synth2** may **switch voicings**. different probabilities for different moments in the form.
    * at measures 1 and 5, a microtonal effect is achieved by generating a random decimal value and summing it with the MIDI note number- this is **fractional MIDI**. The console prints every sounded MIDI note, and should be helpful.
    * the **mono/stereo effect** experiments with the stereo depth. a value of 0 duplicates the L signal into both channels (i.e. L+L), while a value of 0 does the same with the R channe (R+R). A value of 50 restores the stereo depth field back to normal (L+R).
    * at some moments in the piece, the synth may sound extremely washy, because **crazy verb** is turned all the way to max. Occurs probabilistically at measures 1 and 9.
    * attack values of the synth are changed by sending poly~ a randomly generated value in ms, which changes the first point in the function graph associated with the ADSR. If a long attack duration- 1500-2300 ms- is generated, then the strumming function is automatically disabled by setting all delay values to 0.
* riser
    * the amplitude of the riser is triggered at every cadential moment, at measures 4, 8, and 12. once clock arrives at the moment of resolution, the instrument is turned off. 
    * the **mod ratio** is randomized at measures 1, 5, 9.
    * the **dev amount** changes with the phasor. The upper range in this translation is randomized alongside the **mod ratio** at measures 1, 5, 9.
* blues call
    * this instrument is turned on at measures 2, 6, 10, and is faded out at any other measures.
    * only two notes are heard in this blues call. F, Ab, A and Bb are the only notes played by this instrument. F is held constant throughout the form (it is the tonic), while the the second note depends on the current harmony (A works on I7, Ab works on IV7, and Bb is enabled at the G-7 chord, for instance.)
* drip drop 
    * instrument turns on at measures 1, 5, 9. the same line~ object changes filter cutoff frequency for the instrument.
    * several variations **delay amounts** are added to both hits in order to produce varying time feels.

## Aesthetic inspirations
 * The synths at the end of Mac Miller's *Come Back to Earth*
 * Toru Takemitsu's **Dorian Horizon** and **Requiem**, their slow character. Also his Orchestral piece *Archipelago S*, and the dynamic and timbral swells in the climactic moments of the piece.
 * General soundscape in Brian Eno's *Wind in Lonely Fences* 
 * The vibrato in Peggy Lee's recording of *If You Could See Me Now*

A few months after I finished this piece, I heard similarities between my *lemonrinse* and Autechre's *Altibzz*. Oh well!


## Errors and room for improvement
 * poly~ takes 3 parameters as input. achieved unsucessful results with modulating range and domain values. 
 * some unfinished ideas, such as **4mogh4synthMetro*, **4mogh4synthMetroDelay**.
 
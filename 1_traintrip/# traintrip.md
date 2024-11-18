# traintrip

composed by [Amogh Dwivedi](amoghdwivedi.com)

[YouTube video](insert link here)

## Contents

* [Program Note](#program-note)


* [Technical synopsis](#technical-synopsis)


* [Instrument Design](#instrument-design)


* [Compositional Logic](#compositional-logic)


* [Aesthetic inspiration](#aesthetic-inspirations)


* [Errors and room for improvement](#errors-and-room-for-improvement)


## Program note

I was in an Amtrak train from DC to Boston when I started jotting down the pseudocode for "traintrip" on my iPad, and that’s what I named the .maxpat file once I started working on it, but the piece is abstract and not meant to be representative. Although it was a boring, largely plain white Max screen that was my "visual stimulus" for the piece, as I listen to the piece now, I can’t help but picture a train that starts and slows down as it makes its way through cities and countryside.

## Technical synopsis 
Various instruments operate according to an overarching <ins>*main counter*</ins>, which in turn generates other auxiliary counts. The intention is to create multiple concurrent time feels.  Alongside triggering discrete events at various points, the phasor~ object also triggers long term modulations of various parameters. FM is the principal synthesis technique in this piece. Features use of simple ingredients like tonal structures abd basic syncopated rhythms. 

Any keywords mentioned in <ins>*underlined italics*</ins> can be found (i.e. cmd+F) in the Max patch for convenience.

Any button with a green ring can be played after the patch is initialized.


## Instrument design
* <ins>*Bass Bell*</ins>

    * poly~ receives a list as the note message info, which determines note choice, envelope, and whether or not tremolo is implemented in the sound. 
    * a sum of 2 FM sounds
    * randomization of FM parameters such as modulator freq ratio, carrier freq ratio, deviation amount, and range of LFO that modulates deviation amount.
    * 3 distinct envelope settings with differing characters
    * randomized tremolo with a cycle~ object
    * This instrument was modified after receiving feedback from Morton Subotnick, who said that despite its dominance the bell was too predictable. Hence the addition of randomized envelopes.
    * 6 notes are layered together in order to produce a vast, low sound.
    * mostly low cutoff frequency till .45 of the piece. filter is placed after the reverb to generate an intentionally muddy sound.

* <ins>*Mid Bell*</ins>
    * * a sum of 2 FM sounds <ins>*FM Bass Bell*</ins>
    * overdrive~ is placed after the reverb too add more grit to the sound.

* <ins>*quintal bass*</ins>
    * The simplest instrument- sine waves tuned to a quintal voicing starting on E2. Although the piece is not set in any key, one may argue that E is the tonal anchor/pole of this piece, in conjunction the sonorities heard in <ins>*Saw Chords*</ins>.
    * heavy "sidechaining" of the signal from the kick.

* <ins>*Pluck*</ins>
    * a sum of 2 FM sounds
    * Short pluck, and features randomization of various envelope parameters, like release length and "hold" length.
    * using an infinite line generator to constantly modulate mod freq ratio.
    * features drastic aperiodic modulation of deviation amount
    * after an initial compression phase, this sound is separated into two copies- "dry" and "wet". the former only has reverb, while the latter has some pitchshifting, overdrive, and some delay which would offset its signal from the wet, adding richness to the pluck.
    * This instrument sidechains with the kick.
    * features filtering with a complex LFO. Makes use of my <ins>*4moghInfiniteLinefast*</ins> abstraction and creates a much-less noticeably-periodic source of modulation.

* <ins>*Groove*</ins>
    * FM sound
    * <ins>*4moghFMGrooveRelease*</ins> constantly outputs a release duration amount which determines the envelope settings of this poly~ object. It is a rapid and wide cycle~ object which modulates as the piece progresses.
    * The deviation amount is a sum of both an aperiodic and periodic modulation source- the function object, as well as the cycle~ operator below. Most of these settings are randomized with each poly~ instantiation.
    * There are two applications of this (as defined by the two rhythms), and the main difference between the two is pitch. The "two 16ths" module works differently by making use of a counter and two different note $1 objects, which instantiate two frequencies 2kHz apart at the beginning of the patch.

* <ins>*Pad*</ins> 
    * FM sound
    * Modulating upper range of randomized deviation amount
    * Long domain value (6000 ms).
    * This instrument sidechains with the kick.
    * Dry/wet implementation with basic Shimmer reverb

* <ins>*"Ambient Kick"*</ins>
    * Basic kick. Filtering out most mid-high frequency content, alongside adding reverb with a short decay time to add some depth.

* <ins>*"Saw Chords"*</ins>
    * poly~ object takes list as note message info, and determines note frequency, envelope release time, delay time for next (virtually unused since it is always set to 50 ms), and overall amplitude.
    * Dual mono saw~ operators fed into svf~. Randomizing cutoff frequency for each channel at every instantiation of poly~.
    * Mixing with quasi-amplitude modulated noise~ signal
    * This instrument sidechains with the kick.
    * randomizing overdrive~ drive factor 

## Compositional Logic

* <ins>*Setup*</ins>
    * The total <ins>*composition duration*</ins> in minutes and seconds defines the phasor~ rate in Hz
    * This duration is stored in <ins>*value totalDuration*<ins>.
    * Various long term modulations are triggered throughout the composition, which makes use of an abstraction which calculates the total duration of a modulation with a predefined proportion, or <ins>*percentage of total composition duration*</ins>. This allows for all modulations to be scaled proportionally with respect to the total <ins>*composition duration*</ins>. I haven't tested this to extremes, since various metro objects operate independently of this logic, but should work in durations reasonably close to the preferred 3m 0s duration.
* <ins>*Phasor~*</ins>
    * This phasor~ drives the entire composition forward. It triggers various events at specific moments in the piece (for instance, it triggers the bass bell at 12%, 22%, and 85% of the composition duration).
    * More crucially, it triggers several long term modulations such as-
        * the tempo of the <ins>*main counter*</ins>
        * the pluck's pitch offset, amplitude, range, filter, likelihood of triggering itself, likelihood of triggering flam
        * Pad's dry/wet value, pitch range, likelihood of being triggered by pluck
        * Groove panning info, release amount.
        * Pause length   
        * Kick sidechain depth.
        * Range of saw chords' randomized duration range.

* <ins>*Main Counter*</ins> 
    * Controlled by a metro object(whose frequency operates independently of the aforementioned phasor~ object, but is still randomized within a small range), this counter is responsible for either directly or indirectly for all sounds in the patch, besides the quintal bass. It's maximum value is randomized within a predefined range.
    * It directly triggers the pluck sound for instance, while indirectly triggering groove and kick, by reusing the bangs from this main counter to setup auxillary counters that operate independently of this main counter. This adds a looseness to the overall groove.
    * In the iniital stages of the piece, this counter is temporarily suspended at various after it reaches its maximum value. The pauses are eventually nullified.
    * The musical effect this has is to rethink rhythms not as metrically organized groupings of notes, but rather a constant and elongated stream of pulses from which specific patterns are overlaid.

* <ins>*Pluck*</ins>
    * reuses bang from main counter and enters a new counting range
    * likelihood of pulse
    * same bang is used to layer an additional "Flam"
    * pitch bell range
    * pitch bell offset

* <ins>*Pad*</ins> 
    * generates random notes
    * particular sonorities
        * EM7 thing
        * CM 7 thing

* <ins>*Bass Bell*</ins>
    * layers many notes together at once

* <ins>*Mid Bell*</ins>
    * muting operations (describe counter operations)
        * bass bell
        * main FM pluck

* <ins>*Saw Chords*</ins>
    * randomly picks between 4 sonorities
    * note length range
    * triggers pad 





## Aesthetic inspirations



## Errors and room for improvement

* FM Bass bell was disabled in previous renderings, is not audible in the youtube video.

* abandoned tempo modulation designs

* did not experiment with Saw chords randomized delay value for "next"

* convoluted patching style.

* setup is awkward

* if multiple counters are used, then their numbers can be used to articulate each respective scale of time more sonically (use the counter value to produce audible changes in note release length, for instance)


* muting is of bells is disabled in the final video

* fm bass bell was disabled in the final video
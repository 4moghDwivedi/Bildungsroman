# traintrip

composed by [Amogh Dwivedi](https://amoghdwivedi.com)

[Listen here](https://youtu.be/PE_IUnKsLvY)


* [Bildungsroman](/README.md)
    * [yo pierre!](/2_yo%20pierre!/2_yo%20pierre!.md)
    * [lemonrinse](/3_lemonrinse/3_lemonrinse.md)

## Contents

* [Program Note](#program-note)


* [Technical synopsis](#technical-synopsis)


* [Instrument Design](#instrument-design)


* [Compositional Logic](#compositional-logic)


* [Aesthetic inspiration](#aesthetic-inspirations)


* [Errors and room for improvement](#errors-and-room-for-improvement)


## Program note

I was in an Amtrak train from DC to Boston when I started jotting down the pseudocode for "traintrip" on my iPad, and that’s what I named the .maxpat file once I started working on it, but the piece is abstract and not meant to be representative. Although it was a boring, largely plain white Max screen that was my "visual stimulus" for the piece, as I listen to the piece now, I can’t help but picture a train that starts and slows down as it makes its way through cities and countryside.

## Instructions

Any keywords mentioned in <ins>*underlined italics*</ins> can be found (i.e. cmd+F) in the Max patch for convenience.

Any button with a green ring can be played after the patch is initialized.

To initialize the patch, ***reduce your volume***,  press spacebar once, and press it again after a few seconds. You should hear silence. Find the large number~ object and ensure it is not running. To play a rendering of the patch, press spacebar again, and ensure the number~ object is updating.

Various instruments operate according to an overarching <ins>*main counter*</ins>, which in turn generates other auxiliary counts. The intention is to create multiple concurrent time feels.  Alongside triggering discrete events at various points, the phasor~ object also triggers long term modulations of various parameters proportional to the predefined composition duration. FM is the principal synthesis technique in this piece. Features use of simple ingredients like tonal structures and basic syncopated rhythms. 


## Instrument design
* <ins>*Bass Bell*</ins>

    * poly~ receives a list as the note message info, which determines note choice, envelope, and whether or not tremolo is implemented in the sound. 
    * A sum of 2 FM sounds
    * Randomization of FM parameters such as modulator freq ratio, carrier freq ratio, deviation amount, and range of LFO that modulates deviation amount.
    * Randomized tremolo with a cycle~ object
    * This instrument was modified after receiving feedback from [Morton Subotnick](https://mortonsubotnick.com/bio.html), who said that despite its dominance the bell was too predictable. Hence the addition of randomized envelopes.
    * 6 note $1 messages are generated. a randomized value determines the "fundamental" note, and the other 5 "partials" are fairly randomly picked integer/float multiples of the fundamental frequency.
    * Mostly low cutoff frequency till .45 of the piece. filter is placed after the reverb to generate an intentionally muddy sound.

* <ins>*Mid Bell*</ins>
    * A sum of 2 FM sounds. similar design to <ins>*FM Bass Bell*</ins>
    * Overdrive~ is placed after the reverb too add more grit to the sound.

* <ins>*quintal bass*</ins>
    * The simplest instrument- sine waves tuned to a quintal voicing starting on E2. Although the piece is not set in any key, one may argue that E is the tonal anchor/pole of this piece, in conjunction the sonorities heard in <ins>*Saw Chords*</ins>.
    * Heavy "sidechaining" of the signal from the kick.

* <ins>*Pluck*</ins>
    * A sum of 2 FM sounds
    * Short pluck, and features randomization of various envelope parameters, like release length and "hold" length.
    * Using an infinite line generator to constantly modulate mod freq ratio.
    * Features drastic aperiodic modulation of deviation amount
    * After an initial compression phase, this sound is separated into two copies- "dry" and "wet". the former only has reverb, while the latter has some pitch shifting, overdrive, and some delay which would offset its signal from the wet, adding richness to the pluck.
    * This instrument sidechains with the kick.
    * Features filtering with a complex LFO. Makes repeated use of my <ins>*4moghInfiniteLinefast*</ins> abstraction and creates a much-less noticeably-periodic source of modulation.

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
    * Various long term modulations are triggered throughout the composition, which makes use of an abstraction which calculates the total duration of a modulation with a predefined proportion, or <ins>*percentage of total composition duration*</ins>. This allows for all modulations to be scaled proportionally with respect to the total <ins>*composition duration*</ins>. I haven't tested this to extremes, since various metro objects operate independently of this logic and thus will not change the local time scales, but the mechanism should still work in durations reasonably close to the preferred 3m 0s duration.
* <ins>*Phasor~*</ins>
    * This phasor~ drives the entire composition forward. It triggers various events at specific moments in the piece (for instance, it triggers the bass bell at 12%, 22%, and 85% of the composition duration).
    * More crucially, it triggers several long term modulations such as-
        * The tempo of the <ins>*main counter*</ins>
        * The pluck's pitch offset, amplitude, range, filter, likelihood of triggering itself, likelihood of triggering flam
        * Pad's dry/wet value, pitch range, likelihood of being triggered by pluck
        * Groove panning info, release amount.
        * <ins>*Pause/reset*</ins> length   
        * Kick sidechain depth.
        * Range of saw chords' randomized duration range.

* <ins>*Main Counter*</ins> 
    * Controlled by a metro object(whose frequency operates independently of the aforementioned phasor~ object, but is still randomized within a small range), this counter is responsible for either directly or indirectly for all sounds in the patch, besides the quintal bass. Its maximum value is randomized within a predefined range- 23-62.
    * It directly triggers the <ins>*Bass Bell*</ins> sound for instance, and indirectly triggers <ins>pluck</ins>, <ins>groove</ins> and <ins>kick</ins>, by reusing the bangs from this main counter to setup auxillary counters that operate independently of this main counter. This adds a looseness to the overall groove.
    * In the initial stages of the piece, this counter is temporarily suspended at various after it reaches its maximum value (using <ins>*pause/reset*</ins>). The pauses are eventually nullified.
    * The musical effect this has is to rethink rhythms not as metrically organized groupings of notes, but rather a constant and elongated stream of pulses from which specific patterns are overlaid.

* <ins>*Pluck*</ins>
    * Makes use of the bangs from the <ins>*main counter*</ins> and generates a much larger count (64).
    * The probability of this pluck sounding is described as <ins>*likelihood of pulse*</ins> in the patch. It is modulated throughout the piece.
    * the bangs from *this* pluck's counter are used to occasionally trigger a <ins>*flam*</ins>, a slight variation on this pluck.
    * The pitch range is modified by changing the random number range over time.
    * The pitch offset is modified by changing the right operand of the + object over time.

* <ins>*Pad*</ins> 
    * This pad mostly generates random (MIDI) note values. 
    * Both its pitch and offset values are modulated in a manner identical to that of <ins>*pluck*</ins>
    * Particular sonorities are generated at various points of the patch alongside these random notes, and they are of compositional significance.
        * An E major 7 (9 13) chord plays at the beginning of the patch, as well as the recap, and is one of the only certain discrete events in the patch.
        * A C major 7 voicing is played whenever the <ins>*Saw Chords*</ins> play the same chord. This is an orchestral choice, one which reinforces the chord (because I really like that chord).

* <ins>*Bass Bell*</ins>

    * 3 distinct envelope settings with differing characters are defined within the poly~ object, but these are picked before-hand and remotely sent to it. 
    * The <ins>*Bass Bell*</ins> starts to <ins>*respond*</ins> to the <ins>*Mid Bell*</ins>  at 45% of the composition's total duration. This is performed by offsetting the count at which the <ins>*Mid Bell*</ins> is struck by 9 counts.

* <ins>*Mid Bell*</ins>
    * This is the main instrument whose activity is defined by the <ins>*Main Counter*</ins>. At every cycle of the counter- punctuated by <ins>*pauses/reset*</ins>- a "target hit" value is determined. 
    * Muting operations are performed by offsetting the values determined by this "target hit" value, such as
        * The aforementioned <ins>*Bass Bell*</ins> operation
        * And an additional "ducking" of the <ins>*Pluck*</ins> sounds by attenuating the live.gain object.

* <ins>*Saw Chords*</ins>
    * Randomly picks between 4 sonorities defined in <ins>*coll chords*</ins>. These chords are arguably set in E phrygian.
    * The <ins>*note length range*</ins> is modulated over time.
    * As mentioned before, the C major 7 chord (which<ins>*I like*</ins>) will trigger the same chord in the pads.

## Aesthetic inspirations
* The repeating rhythmic grooves heard in Animals as Leaders' **"The Joy of Motion"**
* Morton Subotnick's **"Touch, Pt 1"**
* Autechre's **"777"**
* Heather Stebbins' **"Among Arrows"**


## Errors and room for improvement

* Some instruments were muted at the time of rendering- i.e. the audio in the YouTube video- and were inaudible. These are now enabled, and include- 
    * <ins>*Bass Bell*</ins>
    * muting of <ins>*Pluck*</ins> in response to <ins>*Bass Bell*</ins>.

* Tempo modulation designs were conceived but eventually abandoned. These are placed in the right side of the patch.

* Did not experiment with Saw chords randomized delay value for "next", and all are currently set to 50ms.

* Convoluted patching style, generally speaking. Use of abstractions would have been ideal.

* Pause right before the recap is inconsistent, and instruments are turned off haphazardly in some takes. These can be easily edited post-generation, but it would be nice if these controls were refined in patch.

* Although counters are used, some opportunities on capitalizing on their actual numerical value were missed out on. Their numbers could have been used to articulate each respective scale of time more sonically (use the counter value to produce audible changes in note release length, for instance)

* The <ins>*pause/reset*</ins> currently only toggles between 0 and 1000 ms. This can be explored further.

* Although I thought the piece had an acceptable range of generative-ness at the time of this project, given the time constraints, further steps can be taken to drastically alter the overall predefining of various parameters, such as, but not limited to, the tonality of the piece, range in tempo variation, various groove settings, more envelope designs, etc. 



* [go to top](#traintrip)
* [Bildungsroman](/README.md)
    * [yo pierre!](/2_yo%20pierre!/2_yo%20pierre!.md)
    * [lemonrinse](/3_lemonrinse/3_lemonrinse.md)
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


## Compositional Logic

* <ins>*Setup*</ins>
    * The total <ins>*composition duration*</ins> in minutes and seconds defines the phasor~ rate in Hz
    * This duration is stored in <ins>*value totalDuration*<ins>.
    * Various long term modulations are triggered throughout the composition, which makes use of an abstraction which calculates the total duration of a modulation with a predefined proportion, or <ins>*percentage of total composition duration*</ins>. This allows for all modulations to be scaled proportionally with respect to the total <ins>*composition duration*</ins>. I haven't tested this to extremes, since various metro objects operate independently of this logic and thus will not change the local time scales, but the mechanism should still work in durations reasonably close to the preferred 3m 0s duration.

## Aesthetic inspirations


## Errors and room for improvement

* Convoluted patching style, generally speaking. Everything works though.

* changed trill amount destination to 5
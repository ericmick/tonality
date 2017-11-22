---
title: "The Math  of Music"
author: "Eric Mickelsen"
created: "Sun Nov 19 2017 15:36:47 GMT-0800 (Pacific Standard Time)"
original:
    title: "The Math  of Music"
    url: "http://localhost:3000/tonality.html"
show_footer: true
---

I've heard it said many times that music is based in math, but it seems that hardly anyone - mathematicians or musicians - can actually explain clearly what that math is.

Music theory is full of confusing terminology and notation. I think it often obscures both the beauty and ugliness in music. Musical notation is fine for musicians, but it isn't helpful for discovering what the fundamental nature of music is.

My goal here is to explain the math (and some physics) underlying music at the most fundamental level. I'm going to avoid using any musical terminology that isn't carefully defined and rooted in math. I want to show you how to rebuild the modern western system of music from scratch, and compare it to its predecessors and alternatives.

There are some neat mathematical coincidences that I had no awareness of at all as an amateur musician. I have found in doing this research that I had almost no intuition at all for the real numerical relationships in music, despite having unwittingly used them for years.

In addition to the important equations, I have provided interactive examples. I think hearing and playing music is absolutely necessary to fully understand it. Each example also includes JavaScript code that demonstrates exactly how it works using nothing but the math we've discussed. Feel free to modify them and see what happens.

## What is Music? ##

Music is a part of every human culture. Music is so familiar to us, and so much a part of our lives, it is easy to mistake things like *notes* and *keys*, *choruses* and *verses* for the fundamental elements of music.

The truth is, music has likely been around longer than any of those concepts. Music, at its most basic, is the art of patterns of pressure.

### Pressure ###

Sound is a wave of pressure. Most musical instruments work by rapidly pushing air in one way or another, creating small areas of higher and lower pressure for short periods of time.

Your vocal chords are such an instrument. Your hands are an instrument too: when you clap, you create an area of high pressure between your hands right before they touch. Your hands also vibrate when they strike one another, making more sound.

These areas of high pressure rapidly dissipate as air is pushed away from them, temporarily becoming a low pressure area, into which air then rushes again and so on. This disturbance rapidly expands and weakens until the sound dies, like a ripple in water.

The longer these high and low pressure states last, the fewer of them are created each second. The number of high/low pressure cycles per second is the sound's **frequency** or *pitch*. More frequent cycles are called *higher* *pitch* and less frequent are called *lower* *pitch*. You can make higher frequency sounds by clapping your hands harder or just using your fingers, or by tightening your vocal chords. You can make lower frequency sounds by relaxing your vocal chords, or clapping slowly with your palms.

The unit of cycles per second is **Hertz (Hz)**, named for Heinrich Rudolf Hertz who proved the existence of electromagnetic waves.

$$1 \text{Hz} = \frac{1}{\text{s}} = \text{s}^{-1}$$

We usually call a pressure wave a sound only when we can hear it. To hear a pressure wave with human ears it needs to be between 20 Hz and 20,000 Hz. Between birth and adulthood, we lose the ability to hear sound between 16,000 Hz and 20,000 Hz, and our hearing generally gets worse as we age.

Pressure waves can also be felt. Whether you count it as sound or not, you can certainly feel pressure waves with parts of your body other than your ears. Objects with more mass tend to interact with low frequencies.

The simplest shape of sound is a sine wave. The simple reason for this is that the objects involved with sound, including air, obey **Hooke's law**, behaving like a spring.

$$F = -kX$$

That is, the **force** $F$ is inversely proportional to the **displacement** *X*. So, the more you pull a spring or a guitar string, the harder it pulls back (in the opposite direction). The more air is displaced, the more strongly the air is pushed. The little hairs that turn sound into electrical signals in your ears are also spring-like.

If we stretch a spring and let it go (holding on to one end), its free end will follow a sinusoidal trajectory. At the point when we let go, it is under maximum force and begins contracting. When it has gotten smaller to the point where there is no force, its end is traveling at maximum speed. As it compresses, it slows down, until it stops, and the process is repeated in reverse. *Boingoingoingoing!*

Consider two variables, displacement $X$ and velocity $v$. The rate of change in displacement is velocity. 

$$v = \frac{\delta X}{\delta t}$$

And the rate of change in velocity is acceleration (and thus force) by $F = ma$ (**Newton's second law**, with constant mass $m$).

$$F = ma = \frac{\delta v}{\delta t}$$

But we know that force $F$ is inversely proportional to displacement $X$ by Hooke's law, which means $X$ is the opposite of the change in velocity.

$$-kX = \frac{\delta v}{\delta t}$$

In other words, velocity as a function of time is the derivative of displacement, which is the opposite of the derivative of velocity, which is the derivative of displacement, and so on forever. Luckily there is a set of functions that obey this relation.

$$f(t) = sin(t)$$
$$f'(t) = cos(t)$$
$$f''(t) = -sin(t)$$
$$f'''(t) = -cos(t)$$
$$f''''(t) = f(t) = sin(t)$$

Intuitively, you can think of these functions as describing the x or y coordinate of a point traveling in a circle. We can describe the displacement (pressure) at time $t$ in a sound wave using sine.

$$X_{t} = A\sin{(2\pi f t + \varphi)}$$

The frequency $f$ is cycles per second, so we need to multiply by $2\pi$ to convert to radians, which is what $\sin$ expects ($1\text{cycle}=2\pi \text{radians}$). $A$ is the **amplitude** of the wave and $\varphi$ is known as the **phase**. Note, we could just as easily have put velocity $v$ or force $F$ on the left side of that equation, or used $\cos$ or added a minus sign on the right side. These are all equivalent.

The phase variable $\varphi$ lets us translate the function along the x axis (time), to account for the starting time and starting state of the wave. Each of the sinusoidal functions we just discussed as derivatives of each other above are also just translations of one another along the x axis.

```javascript; hidden
let sine = [];
let cosine = [];

for(let i = 0; i < 4 * Math.PI; i += Math.PI / 64) {
    sine.push({x: i, y: Math.sin(i), label: i / Math.PI + 'π'});
    cosine.push({x: i, y: Math.cos(i), label: i / Math.PI + 'π'});
}

return graphs.lineChart({sine, cosine}, 'time t', 'sin(t)', {x: 'x', y: 'y', label: 'label'});
```

$$ f(x) = 2^{\frac{x}{12}+A}\text{Hz} $$

$$\mathsf{A} = \log_{2}(440\text{Hz})$$

```javascript; auto
A = 12 * Math.log2(440);
equalTemperament = (interval) => 2 ** ((A + interval) / 12);

const noteNames = ['A',
                   'A♯/B♭',
                   'B',
                   'C',
                   'C♯/D♭',
                   'D',
                   'D♯/E♭',
                   'E',
                   'F',
                   'F♯/G♭',
                   'G',
                   'G♯/A♭'];

const describeNote = (name, value) => {
    return {name: name, interval: value, frequency: equalTemperament(value)};
};

// One octave up
this.scale = noteNames.map((name, index) => describeNote(name, index))
// And high A
this.scale.push(describeNote(noteNames[0], 12));
// One octave down
this.scale = this.scale.concat(noteNames.map((name, index) => describeNote(name, index - 12)));
return this.scale;
```

```javascript; auto
return graphs.scatterPlot(
    this.scale, 'Interval', 'Frequency (Hz)',
    {x: 'interval', y: 'frequency', label: 'name'}
);
```

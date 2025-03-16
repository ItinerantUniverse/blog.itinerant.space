---
title: Setting the Temperature
tags: Atmosphere Environment Planet
article_header:
  type: cover
---

Generating realistic planetary temperatures

<!--more-->

One of the things I'm aiming to achieve with Itinerant is a fairly decent set of survival mechanics. So far, I've mainly been concentrating on
making sure the environment I'm creating is capable of generating realistic-looking terrain and subterranean geology, but this weekend I turned
my attention to something I definitely need to add - knowing what the surrounding temperature is when on a planet.

I was initially torn about the way to approach this - with my Earth planet, it's very tempting to just take current weather observations and plug
them into the game to see what I can do in terms of making it 'look' realistic - however I felt like I'd be jumping ahead of myself and this
entire project has always been about putting the hard work in first to reap the rewards later rather than finding a shortcut that makes things
look good early on but leaves me with a hard-coded mess and a bunch of unresolved stuff to sort out later.

On the flip side, I'm also aware that realistic weather simulations involve supercomputers, people with PHDs and decades of research, which is
somewhat out of my reach, so I need to find a good path through the middle which gives me a decent base to build on and can be compare to real
Earth data to see if I'm getting anywhere close to reality.

I figured probably a good place to start would be to see if I could find a reasonable 'average' temperature for any planet based on some known
factors (like size/brightness of it's star, distance of the planet to the star and the size of the planet) and then just seeing what direction
that took me in.

There's some interesting articles about planetary temperatures on the web, and it became apparent that there are some simple calculations you
can do to make rough estimates that don't seem too far removed from reality.

The first thing I came across was this Stack Exchange question which had a lot of symbols I didn't initially understand:

https://astronomy.stackexchange.com/questions/10113/how-to-calculate-the-expected-surface-temperature-of-a-planet

The answer to the question mentions 'radiative equilibrium temperature' (aka Planetary Equilibrium Temperature), which is the temperature
a planet would be at if you consider it a black body radiator with no greenhouse effect caused by the atmosphere.

This formula in particular became my first stopping-off point:

----

It mentions using the luminosity in Watts, the distance from the star in meters, the Stefan-Boltzmann constant, which is some handy thing
you can read about here but I'm not going to worry myself too much with the details, and the albedo of the planet.

Therefore my two initial thoughts were, how do I get the luminosity of the star, and what is the albedo (other 3D programming-speak for the
color of a thing).

Now, I initially thought I'd just get the luminosity of our Sun and worry about how I calculate the luminosity of other stars later, but that
seemed a bit like cheating, and I wondered how to calculate the luminosity. It turns out this can be calculated by knowing the temperature of
the star, and there is a direct relationship between the mass of the star and it's temperature...

Which seems pretty obvious now I think of it - basically the bigger the star, the brighter it is. This is great for my procedurally-generated
universe, because I can just randomly determine the size of the star and I'll automatically know how bright it is and the color of the light
it radiates.

Looking to this page:

https://www.astronomy.ohio-state.edu/thompson.1847/1144/Lecture9.html#:~:text=L%20%3D%20F%20x%20Area%20%3D%204,power%20and%20its%20Radius%20squared.%22

I found a way to determine the luminosity:

L = F x Area = 4 π R2 σSB T4

Now this is where my brian lets me down because the mathematicians have this secret code which they think is not really secret because they write
it over and over again in millions of books, but yet I can't retain the information. I know what bloody Pi is, but that round thing with a tail
is the Stefan Boltzmann constant and I keep repeatedly forgetting that's what it is. So I'm writing it down here to my future self:

[Stefan Boltzmann Constant here]

So, all we need to know to get the luminosity of the star is its radius and its temperature.

Handily this all starts to make some sense to me. As mentinoed earlier, we're saying the luminosity of the star is in Watts, which I know as a unit
of power. So, going back to that previous equation, we can see what we're really doing is figuring out how much power hits the planet at any given
moment, converting that into heat and coming up with a value for how hot the surface is going to be.

This all led me onto the next article:

https://web.archive.org/web/20210605120431/https://scied.ucar.edu/earth-system/planetary-energy-balance-temperature-calculate

Now here I find things are getting really interesting, because the article establishes a fairly basic rule - the planet absorbs a certain amount
of energy, and reflects a certain amount back into space, and it hints at the fact you can figure this out not only across the entire surface of
the planet, but for any surface area.

This got me thinking that down-the-line, I can probably come up with a base value for the tmeperature, but then when the player is actually
standing on the surface, we can calculate the amount of energy hitting the player in realtime. We could use this to figure out when they're
getting too hot or too cold, or maybe even getting sunburnt, and we could make this a cumulative effect that just constantly happens to the player
and their stuff while they're on the surface. To that end, their spaceship while they're in space could be subject to the same effect. We don't
necessarily just need to hard-code a distance to the sun where the ship burns up, we can instead rely on physics to burn the ship up for us :D

Back to this initial stage, I felt it wasn't good enough to just get a value for an average temperature across the entire planet, because really
the temperature should vary based on where you're standing, and of course it'll also vary if it's night or day. This led me to another post:

https://worldbuilding.stackexchange.com/questions/185703/how-to-determine-planetary-extremes-of-temperature-from-average-global-temperatu

This one I found really great, because it includes a way to figure out where the sun will be hitting based on axial tilt of the planet, and
since my planets already have axial tilts on them, this gets really great.


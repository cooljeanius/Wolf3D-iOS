Copied and pasted from Carmack\_iPhone\_development.doc:

iPhone development*
===================
By John Carmack, Technical Director, Id Software

I had been frustrated for over a year with the fact that we did NOT have any
iPhone development projects going internally at Id.  I love my iPhone, and I
think the App Store is an extremely important model for the software business.
Unfortunately, things have conspired against us being out early on the platform.

Robert Duffy and I spent a week early on starting to bring up the Orcs & Elves DS
codebase on the iPhone, which would have been a nice project for a launch title,
but it wasn't going to be a slam dunk.  The iPhone graphics hardware is a more
capable superset of the DS hardware (the driver overhead is far, far worse, though),
but the codebase was fairly DS specific, with lots of Nintendo API calls all
over the place.  I got the basics drawing by converting things to OpenGL ES,
but I was still on the fence as to whether the best approach to get all the
picky little special effects working would be a complete GL conversion, or a
DS graphics library emulation layer.  Coupled with the fact that the entire
user interface would need to be re-thought and re-tested, it was clear that the
project would take several months of development time, and need artists and
designers as well as coding work.  I made the pitch that this would still be a
good plan, but the idMobile team was already committed to the Wolfenstein RPG
project for conventional Java and BREW mobile phones, and Anna didn't want to
slip a scheduled milestone on the established, successful development directions
there for a speculative iPhone project.

After thinking about the platform's capabilities a bit more, I had a plan for an
aggressive, iPhone specific project that we actually started putting some
internal resources on, but the programmer tasked with it didn't work out and
was let go.  In an odd coincidence, an outside development team came to us with
a proposal for a similar project on the Wii, and we decided to have them work
on the iPhone project with us instead.  We should be announcing this project
soon, and it is cool.  It is also late, but that's software development...

Late last year, the mobile team had finished up all the planned versions of
Wolfenstein RPG, but EA had suggested that in addition to the hundreds of
customized versions they normally produce for all the various mobile phones,
they were interested in having another team do a significant media quality
improvement on it for the iPhone.  While Wolf RPG is a very finely crafted
product for traditional cell phones, it wasn't designed for the iPhone's
interface or capabilities, so it wouldn't be an ideal project, but it should
still be worth doing.  When we got the first build to test, I was pleased with
how the high res artwork looked, but I was appalled at how slow it ran.  It
felt like one of the mid range java versions, not better than the high end BREW
as I expected.  I started to get a sinking feeling.  I searched around in the
level for a view that would confirm my suspicion, and when I found a clear
enough view of some angled geometry I saw the tell-tale mid-polygon affine swim
in the texture as I rotated.  They were using the software rasterizer on the
iPhone.  I patted myself on the back a bit for the fact that the combination of
my updated mobile renderer, the intelligent level design / restricted movement,
and the hi-res artwork made the software renderer almost visually
indistinguishable from a hardware renderer, but I was very unhappy about the
implementation.

I told EA that we were NOT going to ship that as the first Id Software product
on the iPhone.  Using the iPhone's hardware 3D acceleration was a requirement,
and it should be easy -- when I did the second generation mobile renderer
(written originally in java) it was layered on top of a class I named TinyGL
that did the transform / clip / rasterize operations fairly close to OpenGL
semantics, but in fixed point and with both horizontal and vertical
rasterization options for perspective correction.  The developers came back and
said it would take two months and exceed their budget.

Rather than having a big confrontation over the issue, I told them to just send
the project to me and I would do it myself.  Cass Everitt had been doing some
personal work on the iPhone, so he helped me get everything set up for local
iPhone development here, which is a lot more tortuous than you would expect from
an Apple product.  As usual, my off the cuff estimate of "Two days!" was
optimistic, but I did get it done in four, and the game is definitely more
pleasant at 8x the frame rate.
And I had fun doing it.

Since we now were doing something resembling "real work" on the iPhone at the
office, we kept it going at a low priority.  One of the projects Cass was
tinkering around with at home was a port of Quake 3, and we talked about
different interface strategies every now and then.
Unfortunately, when we sat down to try a few things out, we found that
Q3 wasn't really running fast enough to make good judgments on iPhone control
systems.  The hardware should be capable enough, but it will take some
architectural changes to the rendering code to get the most out of it.

I was just starting to set up a framework to significantly revise Q3 when I
considered the possibility of just going to an earlier codebase to experiment
with initially.  If we wanted to factor performance out of the equation, we
could go all the way back to Wolfenstein 3D, the grandfather of FPS games.
It had the basic run and gun play that has been built on for fifteen years, but
it originally ran on 286 computers, so it should be pretty trivial to hold a
good framerate on the iPhone.

Wolfenstein was originally written in Borland C and TASM for DOS, but I had open
sourced the code long ago, and there were several projects that had updated the
original code to work on OpenGL and modern operating systems.  After a little
looking around, I found Wolf3D Redux at http://wolf3dredux.sourceforge.net/.
One of the development comments about "removal of the gangrenous 16 bit code"
made me smile.

It was nice and simple to download, extract data from a commercial copy of
Wolfenstein, and start playing on a PC at high resolution.  Things weren't as
smooth as they should be at first, but two little changes made a huge difference
-- going at VBL synced update rates with one tic per cycle instead of counting
milliseconds to match 70 hz game tics, and fixing a bug with premature
integralization in the angle update code that caused mouse movement to be
notchier than it should be.  The game was still fun to play after all these years,
and I began to think that it might be worthwhile to actually make a product out
of Wolfenstein on the iPhone, rather than just using it as a testbed, assuming
the controls worked out as fun to play.  The simple episodic nature of the game
would make it easy to split up into a $0.99 version with just the first episode,
a more expensive version with all sixty levels, and we could release Spear of Destiny
if there was additional demand.  I was getting a little ahead of myself without
a fun-to-play demonstration of feasibility on the iPhone, but the idea of moving
the entire line of classic Id titles over -- Wolf, Doom, Quake, Quake 2, and
Quake Arena, was starting to sound like a real good idea.

I sent an email to the Wolf 3D Redux project maintainer to see if he might be
interested in working on an iPhone project with us, but it had been over a year
since the last update, and he must have moved on to other things.  I thought
about it a bit, and decided that I would go ahead and do the project myself.
The "big projects" at Id are always top priority, but the systems programming
work in Rage is largely completed, and the team hasn't been gated on me for
anything in a while.  There is going to be memory and framerate optimization
work going on until it ships, but I decided that I could spend a couple weeks
away from Rage to work on the iPhone exclusively.  Cass continued to help with
iPhone system issues, I drafted Eric Will to create the few new art assets, and
Christian Antkow did the audio work, but this was the first time I had taken
full responsibility for an entire product in a very long time.

*Design notes*
==============

The big question was how "classic" should we leave the game?  I have bought
various incarnations of Super Mario Bros on at least four Nintendo platforms,
so I think there is something to be said for the classics, but there were so
many options for improvement.  The walls and sprites in the game were originally
all 64 x 64 x 8 bit color, and the sound effects were either 8khz / 8 bit mono
or (sometimes truly awful) FM synth sounds.  Changing these would be trivial
from a coding standpoint.  In the end, I decided to leave the game media pretty
much unchanged, but tweak the game play a little bit, and build a new user
framework around the core play experience.  This decision was made a lot easier
by the fact that we were right around the 10 meg over-the-air app download limit
with the converted media.  This would probably be the only Id project to ever be
within hailing distance of that mark, so we should try to fit it in.

The original in-game status bar display had to go, because the user's thumbs
were expected to cover much of that area.  We could have gone with just floating
stats, but I thought that BJ's face added a lot of personality to the game, so
I wanted to leave that in the middle of the screen.  Unfortunately, the way the
weapon graphics were drawn, especially the knife, caused issues if they were
just drawn above the existing face graphics.  I had a wider background created
for the face, and used the extra space for directional damage indicators, which
was a nice improvement in the gameplay.  It was a tough decision to stop there
on damage feedback, because a lot of little things with view roll kicks, shaped
screen blends, and even double vision or blurring effects, are all pretty easy
to add and quite effective, but getting farther away from "classic".

I started out with an explicit "open door" button like the original game, but I
quickly decided to just make that automatic.  Wolf and Doom had explicit "use"
buttons, but we did away with them on Quake with contact or proximity activation
on everything.  Modern games have generally brought explicit activation back by
situationally overriding attack, but hunting for push walls in Wolf by shooting
every tile wouldn't work out.  There were some combat tactics involving
explicitly shutting doors that are gone with automatic-use, and some secret push
walls are trivially found when you pick up an item in front of them now, but
this was definitely the right decision.

You could switch weapons in Wolf, but almost nobody actually did, except for
occasionally conserving ammo with the chain gun, or challenges like
"beat the game with only the knife".  That functionality didn't justify the
interface clutter.

The concept of "lives" was still in wolf, with 1-ups and extras at certain scores.
We ditched that in Doom, which was actually sort of innovative at the time, since
action games on computers and consoles were still very much take-the-quarter
arcade oriented.  I miss the concept of "score" in a lot of games today, but I
think the finite and granular nature of the enemies, tasks, and items in Wolf
is better suited to end-of-level stats, so I removed both lives and score, but
added persistent awards for par time, 100% kills, 100% secrets, and 100% treasures.
The award alone wasn't enough incentive to make treasures relevant, so I turned
them into uncapped +1 health crumbs, which makes you always happy to find them.

I increased the pickup radius for items, which avoided the mild frustration of
having to sometimes make a couple passes at an item when you are cleaning up a
room full of stuff.

I doubled the starting ammo on a fresh level start.  If a player just got
killed, it isn't good to frustrate them even more with a severe ammo
conservation constraint. There was some debate about the right way to handle death:
respawn with the level as is (good in that you can keep making progress if you
just get one more shot off each time, bad in that weapon pickups are no longer available),
respawn just as you entered the level (good -- keep your machinegun / chaingun,
bad -- you might have 1 health), or, what I chose, restart the map with basic
stats just as if you had started the map from the menu.

There are 60 levels in the original Wolf dataset, and I wanted people to have
the freedom to easily jump around between different levels and skills, so there
is no enforcement of starting at the beginning.  The challenge is to _complete_
a level, not _get to_ a level.  It is fun to start filling in the grid of level
completions and awards, and it often feels better to try a different level after
a death.  The only exception to the start-anywhere option is that you must find
the entrance to the secret levels before you can start a new game there.

In watching the early testers, the biggest issue I saw was people sliding off
doors before they opened, and having to maneuver back around to go through.
In Wolf, as far as collision detection was concerned, everything was just a
64x64 tile map that was either solid or passable.
Doors changed the tile state when they completed opening or began closing.
There was discussion about magnetizing the view angle towards doors, or somehow
beveling the areas around the doors, but it turned out to be pretty easy to make
the door tiles only have a solid central core against the player, so players
would slide into the "notch" with the door until it opened.  This made a huge
improvement in playability.

There is definitely something to be said for a game that loads in a few seconds,
with automatic save of your position when you exit.  I did a lot of testing by
playing the game, exiting to take notes in the iPhone notepad, then restarting
Wolf to resume playing.  Not having to skip through animated logos at the start
is nice.  We got this pretty much by accident with the very small and simple
nature of Wolf, but I think it is worth specifically optimizing for in future
titles.

The original point of this project was to investigate FPS control schemes for
the iPhone, and a lot of testing was done with different schemes and parameters.
I was sort of hoping that there would be one "obviously correct" way to control
it, but it doesn't turn out to be the case.

For a casual first time player, it is clearly best to have a single
forward / back / turn control stick and a fire button.

Tilt control is confusing for first exposure to the game, but I think it does
add to the fun factor when you use it.  I like the tilt-to-move option, but
people that play a lot of driving games on the iPhone seem to like tilt-to-turn,
where you are sort of driving BJ through the levels. Tilt needs a decent deadband,
and a little bit of filtering is good.  I was surprised that the precision on
the accelerometer was only a couple degrees, which makes it poorly suited for
any direct mapped usage, but it works well enough as a relative speed control.

Serious console gamers tend to take to the "dual stick" control modes easily for
movement, but the placement of the fire button is problematic.  Using an index
finger to fire is effective but uncomfortable.  I see many players just move the
thumb to fire, using strafe movement for fine tuning aim.  It is almost tempting
to try to hijack the side volume switch for fire, but the ergonomics aren't
quite right, and it would be very un-Apple-like, and wouldn't be available on
the iPod touch (plus I couldn't figure out how...).

We tried a tilt-forward to fire to allow you to keep your thumbs on the dual
control sticks, but it didn't work out very well.  Forward / back tilt has the
inherent variable holding angle problem for anything, and a binary transition
point is hard for people to hold without continuous feedback.  Better visual
feedback on the current angle and trip point would help, but we didn't pursue
it much.  For a game with just, say, a rocket launcher, shake/shove-to-fire
might be interesting, but it isn't any good for wolf.

It was critical for the control sticks to be analog, since digital direction
pads have proven quite ineffective on touch screens due to progressive lack of
registration during play.  With an analog stick, the player has continuous
visual feedback of the stick position in most cases, so they can self correct.
Tuning the deadband and slide off behavior are important.

Level design criteria has advanced a lot since Wolfenstein, but I wasn't going
to open up the option of us modifying the levels, even though the start of the
first level is painfully bad for a first time player, with the tiny, symmetric
rooms for them to get their nose mashed into walls and turned around in.
The idea is that you started the game in a prison cell after bashing your guard
over the head, but even with the exact same game tools, we would lead the player
through the experience much better now.  Some of the levels are still great fun
to play, and it is interesting to read Tom Hall and John Romero's designer notes
in the old hint manuals, but the truth is that some levels were scrubbed out in
only a couple hours, unlike the long process of testing and adjustment that goes
on today.

It was only after I thought I was basically done with the game that Tim Willits
pointed out the elephant in the gameplay room -- for 95% of players, wandering
around lost in a maze isn't very much fun.
Implementing an automap was pretty straightforward, and it probably added more
to the enjoyment of the game than anything else.  Before adding this, I thought
that only a truly negligible amount of people would actually finish all 60
levels, but now I think there might be enough people that get through them to
justify bringing the Spear of Destiny levels over later.

When I was first thinking about the project I sort of assumed that we wouldn't
bother with music, but Wolf3D Redux already had code that converted the old
id music format into ogg, so we would up with support at the beginning, and it
turned out pretty good.  We wound up ripping the red book audio tracks from one
of the later commercial Wolf releases and encoding at a different bitrate, but I
probably wouldn't have bothered if not for the initial support.  It would have
been nice to re-record the music with a high quality MIDI synth, but we didn't
have the original MIDI source, and Christian said that the conversion back from
the id music format to midi was a little spotty, and would take a fair amount of
work to get right.  I emailed Bobby Prince, the original composer, to see if he
had any high quality versions still around, but he didn't get back with me.

The game is definitely simplistic by modern standards, but it still has its
moments.  Getting the drop on a brown shirt just as he is pulling his pistol
from the holster.  Making an SS do the "twitchy dance" with your machine gun.
Rounding a corner and unloading your weapon on ... a potted plant.
Simplistic plays well on the iPhone.

*Programming notes*
===================

Cass and I got the game running on the iPhone very quickly, but I was a little
disappointed that various issues around the graphics driver, the input processing,
and the process scheduling meant that doing a locked-at-60-hz game on the iPhone
wasn't really possible.  I hope to take these up with Apple at some point in the
future, but it meant that Wolf would be a roughly two tick game.  It is only
"roughly" because there is no swapinterval support, and the timer scheduling has
a lot of variability in it.  It doesn't seem to matter all that much, the play
is still smooth and fun, but I would have liked to at least contrast it with the
perfect limit case.

It turns out that there were a couple issues that required work even at 30hz.
For a game like Wolf, any PC that is in use today is essentially infinitely fast,
and the Wolf3D Redux code did some things that were convenient but wasteful.
That is often exactly the right thing to do, but the iPhone isn't quite as
infinitely fast as a desktop PC.

Wolfenstein (and Doom) originally drew the characters as sparse stretched
columns of solid pixels (vertical instead of horizontal for efficiency in
interleaved planar mode-X VGA), but OpenGL versions need to generate a square
texture with transparent pixels.  Typically this is then drawn by either alpha
blending or alpha testing a big quad that is mostly empty space.  You could play
through several early levels of Wolf without this being a problem, but in later
levels there are often large fields of dozens of items that stack up to enough
overdraw to max out the GPU and drop the framerate to 20 fps.  The solution is
to bound the solid pixels in the texture and only draw that restricted area,
which solves the problem with most items, but Wolf has a few different heavily
used ceiling lamp textures that have a small lamp at the top and a thin but
full width shadow at the bottom.  A single bounds doesn't exclude many texels,
so I wound up including two bounds, which made them render many times faster.

The other problem was CPU related.  Wolf3d Redux used the original ray casting
scheme to find out which walls were visible, then called a routine to draw each
wall tile with OpenGL calls.  The code looked something like this:

```
    DrawWall( int wallNum ) {
        char   name[128];
        texture_t   *tex;
        sprintf( name, "walls/%d.tga", wallNum );
        tex = FindTexture( name );
        ...
    }
    texture_t FindTexture( const char *name ) {
        int   i;
        for ( i = 0 ; i < numTextures ; i++ ) {
           if ( !strcmp( name, texture[name]->name ) ) {
              return texture[name];
           }
        }
        ...
    }
```

I winced when I saw that at the top of the instruments profile, but again, you
could play all the early levels that only had twenty or thirty visible tiles at
a time without it actually being a problem.
However, some later levels with huge open areas could have over a hundred
visible tiles, and that led to 20hz again.  The solution was a trivial change
to something resembling:

```
    DrawWall( int wallNum ) {
        texture_t   *tex = wallTextures[wallNum];
        ...
    }
```

Wolf3D Redux included a utility that extracted the variously packed media from
the original games and turned them into cleaner files with modern formats.
Unfortunately, an attempt at increasing the quality of the original art assets
by using hq2x graphics scaling to turn the 64x64 art into better filtered
128x128 arts was causing lots of sprites to have fringes around them due to
incorrect handling of alpha borders. It wasn't possible to fix it up at load time,
so I had to do the proper outline-with-color-but-0-alpha operations in a
modified version of the extractor.  I also decided to do all the format
conversion and mip generation there, so there was no significant CPU time spent
during texture loading, helping to keep the load time down.  I experimented with
the PVRTC formats, but while it would have been ok for the walls, unlike with
DXT you can't get a lossless alpha mask out of it, so it wouldn't have worked
for the sprites.  Besides, you really don't want to mess with the carefully
chosen pixels in a 64x64 block very much when you scale it larger than the
screen on occasion.

I also had to make one last minute hack change to the original media --
the Red Cross organization had asserted their trademark rights over red crosses
(sigh) some time after we released the original Wolfenstein 3D game, and all new
game releases must not use red crosses on white backgrounds as health symbols.
One single, solitary sprite graphic got modified for this release.

User interface code was the first thing I started making other programmers do at
Id when I no longer had to write every line of code in a project, because I
usually find it tedious and unrewarding.  This was such a small project that I
went ahead and did it myself, and I learned an interesting little thing.
Traditionally, UI code has separate drawing and input processing code, but on a
touchscreen device, it often works well to do a combined
"immediate mode interface", with code like this:

```
    if ( DrawPicWithTouch( x, y, w, h, name ) ) {
        menuState = newState;
    }
```

Doing that for the floating user gameplay input controls would introduce a frame
of response latency, but for menus and such, it works very well.

One of the worst moments during the development was when I was getting ready to
hook up the automatic savegame on app exit.  There wasn't any savegame code.
I went back and grabbed the original 16 bit dos code for load / save game, but
when I compiled I found out that the Wolf3d Redux codebase had changed a lot
more than just the near / far pointer issues, asm code, and comment blocks.
The changes were sensible things, like grouping more variables into structures
and defining enums for more things, but it did mean that I wasn't dealing with
the commercially tested core that I thought I was.  It also meant that I was
a lot more concerned about a strange enemy lerping through the world bug I had
seen a couple times.

I seriously considered going back to the virgin codebase and reimplementing the
OpenGL rendering from scratch.  The other thing that bothered me about the
Redux codebase was that it was basically a graft of the Wolf3D code into the
middle of a gutted Quake 2 codebase.  This was cool in some ways, because it
gave us a console, cvars, and the system / OpenGL portable framework, and it
was clear the original intention was to move towards multiplayer functionality,
but it was a lot of bloat.  The original wolf code was only a few dozen C files,
while the framework around it here was several times that.

Looking through the original code brought back some memories.
I stopped signing code files years ago, but the top of WL_MAIN.C made me smile:

```
    /*
    =============================================================================

                               WOLFENSTEIN 3-D

                          An Id Software production

                               by John Carmack

    =============================================================================
    */
```

It wasn't dated, but that would have been in 1991.

In the end, I decided to stick with the Redux codebase, but I got a lot more
free with hacking big chunks of it out.  I reimplemented load / save game
(fixing the inevitable pointer bugs involved), and by littering asserts
throughout the code, I tracked the other problem down to an issue with making a
signed comparison against one of the new enum types that compare as unsigned.
I'm still not positive if this was the right call, since the codebase is sort of
a mess with lots of vestigial code that doesn't really do anything, and I don't
have time to clean it all up right now.

Of course, someone else is welcome to do that.  The full source code for the
commercial app is available on the web site.  There was a little thought given
to the fact that if I had reverted to the virgin source, the project wouldn't
be required to be under the GPL.  Wolf and the app store presents a sort of
unique situation -- a user can't just compile the code and choose not to pay for
the app, because most users aren't registered developers, and the data isn't
readily available, but there is actually some level of commercial risk in the
fast-moving iPhone development community.  It will not be hard to take the code
that is already fun to play, pull a bunch of fun things off the net out of
various projects people have done with the code over the years, dust off some
old map editors, and load up with some modern quality art and sound.

Everyone is perfectly within their rights to go do that, and they can
aggressively try to bury the original game if they want.  However, I think there
is actually a pretty good opportunity for cooperation.  If anyone makes a
quality product and links to the original Wolf app, we can start having links to
"wolf derived" or "wolf related" projects.
That should turn out to be a win for everyone.

I'm going back to Rage for a while, but I do expect Classic Doom to come fairly
soon for the iPhone.


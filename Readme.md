# MothereffinMusicPlayer Planning

## Foundation

I kinda want to just use this as a starting point:

https://github.com/tbranyen/backbone-boilerplate

If we want something that's pluggable and has a ton of UI widgets which could be
often reorganized then I think AMD and less are really good choices. Having
layers of javascript which can be loaded on-demand becomes a really big win as
complexity increases.

## Database

This looks like a good option for indexeddb support:

https://github.com/superfeedr/indexeddb-backbonejs-adapter

The main question I think will be if it can accomplish whatever particular
querying we'll need I think. Figuring out what kind of operations will need to
be performed is also a pretty big deal. I guess like:

- find global by keyword
- find by artist
- find by album
- find by filename?
- order by track number
- order by track name
- order by artist
- order by album

Those seem like the important stuff.

## Audio Decoding / Playback

I'm a big proponent of using SoundManager2 for actual audio file decoding and
playback:

https://github.com/scottschiller/SoundManager2

This will give cross browser / device support for mp3s at minimum. It will also
take care of getting metadata and frequency / amplitude type information (for
visualizations, etc.).

The other option seems to be decoding the files in the browser:

https://github.com/ofmlabs

The codecs here are for mp3, flac, aac, and alac. I don't think SM2 supports
flac, so that's cool. These all seem to have metadata support. There are a lot
of cons though. Probably the biggest one is that I don't think we can expect any
of these to perform at all on mobile devices (though in theory they should at
least function). Another big one is that I don't believe there's a way to stream
data to the decoder, as in the scenario of a file being downloaded *I think.*
This would mainly become a problem if we wanted to move to using remote
backends. I also don't *think* we would get all the neat hookable events and
simple manipulations which we would with SM2, and we'd definitely have to do
more manual parsing of audio data if we wanted more advanced features.

I think the theoretically best solution would be to abstract this stuff away as
much as possible, so that we could in theory mix and match. Do we want to use
SM2 unless they're using an unsupported format, in which case try to decode it
in js? Do we want to hack SM2 to use js decoding as fallbacks directly? Do we
want to just manipulate the html 5 audio API directly in some situations? Who
cares! It's got a facade! Obviously this just adds a bit more work, but I feel
like most of this thing is gonna be facades and interfaces.

## Architecture

I'm thinking a mixable set of music stores, very similar to how Tomahawk works.

http://www.tomahawk-player.org/

Stores can be things such as:

- LocalFileSystem
- UPnP
- S3
- etc.

They can all be indexed side-by-side and searched globally, and you can play
them transparently. Obviously we'll start with LocalFileSystem, but keep the
rest of these in mind.

I think a similar approach would also make sense for the playback apparatus.
What if the user wants to use our UI to control MPD, or his UPnP player, or
PulseAudio, or do whatever that apple-proprietary AirPlay stuff is?

- In-browser audio
- UPnP playback
- MPD control
- AirPlay
- etc.

UI stuff should basically be a collection of widget-y views. It should be easy
to hide and show different widgets, swap them out, duplicate, and rearrange
them.

## UI

I don't use a lot of big heavy music players really, but I think what we're
talking about is sort of itunesy. Very library-oriented. I dunno, this is
something that can go through plenty of evolution once all this other junk is in
place. I think the important thing is that it be modular and relatively
themable. I feel like the end game for this thing would be to have a totally
different UI for a mobile environment, but doing basic alterations to the layout
in a responsive way would probably also be good.
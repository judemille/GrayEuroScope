# EuroScope, but in dark gray!
Let's face it, EuroScope is ugly.
The green color it has doesn't even match the real-world TopSky system, and it just looks bad.
Well, here's a solution! This will make your EuroScope a pleasant dark gray.
# NOTE: THIS IS MADE FOR EUROSCOPE 3.2.3. OTHER VERSIONS WILL BREAK.
The steps are simple:
 - Get [DeltaPatcher](https://github.com/marco-calautti/DeltaPatcher) and extract it somewhere.
 - Once you've gotten DeltaPatcher and extracted it, open it up.
 - Select the EuroScope exe as the original file.
 - Select "EuroScope.exe.xdelta" from this repository as the XDelta patch.
 - Press apply patch.

Your EuroScope is now dark gray. If you would like your TopSky to match, copy the lines from
this repository's TopSkySettingsLocal.txt into the file in your EuroScope profile directory
of the same name. The chat box will stay green by default, but you can fix that in the symbology settings.

## Color Palette
 - `#262626`
 - `#4b4b4b`
 - `#6d6d6d`
 - `#131313`
 - `#000000`

For now this is an unrealistic dark gray, but if enough people want it I can probably make
a light gray too some time.

## Technical stuff: How I did it
There were two nuts to crack here. The first was relatively easy -- there are several bitmaps inside
the EuroScope executable that contain a lot of icons and knick knacks. I had to extract these, edit
them by hand to match the new color scheme, save them, and stuff them back in the EXE. Once I found
a suitable tool ([RisohEditor](https://github.com/katahiromz/RisohEditor) works the best, from my testing.), it was merely a question of editing these bitmaps en masse and then stuffing them back in.
Repetitive, but relatively simple, *except* in the case of text. Text in these bitmaps seemed to
be dithered, which meant I couldn't just toss the paint bucket on it. I've tried my best to match the
text as close as possible, though dithering has been removed, and some text has been re-rendered.

As for the parts that aren't bitmaps? That was a doozy. I pulled out Ghidra, thinking "oh I'll just
search the program memory for `0b4136` (one of the primary colors)". It was not that simple.
See, Windows's drawing APIs (at least the ones used here), store colors as BGR, instead of the
standard RGB. Note that this isn't a little-
vs big-endian issue -- Ghidra accounts for that when searching. Microsoft documents this, I just had
to find it. I spent quite a while trying to search for just the individual components in case that was
how it got constructed. Eventually, though, I thought to look at calls to `FillRect`. I read the docs,
and that led me to look for calls to `CreateSolidBrush`.

I was still getting nowhere. The calls I could find left me disoriented and confused when I tried
to trace back the calls. Eventually, I once again looked at the documentation. I thought maybe
Microsoft was storing the colors in a weird way. Sure enough, they were. BGR. Once I realized that,
I searched for `36410b` and instantly found what I was looking for. After doing that, I just had to
identify all colors needing replaced, and replace all instances. I did that, patched the exe, and bam.
My EuroScope is grey.

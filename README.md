# LifeHax - Regex Cleaning of M3U(8) files for DLNA

Use this quick hack in Notepad++ to regex replace M3U(8) files to work on DLNA.

This cleans the extended info fields (#EXTINF) as well as the first line (#EXTM3U) out of the files.
It seems specifically Synology DLNA Media Server cannot handle these (usually standard and in-spec) lines for M3U(8) files and hence will display "no media found" (even though the links are indeed correct and relative)


(^#EXTM3U\r?\n)|(^#EXTINF:.{1,}\r?\n)

Since we are leaving the "Replace" area completely empty, this should reliably kill those lines and delete them in a "Search and Replace" Operation. Also should work in "Find and Replace in files…" function found in the "Find and Replace" menu in UI, ie. it works on a folder of however many files you feed it.

A little primer on paths and how DLNA tries to ingest your playlists

Something called "relative path compute" is necessary to make M3U(8) files exchangeable in-between different file systems and devices. Specifically, so that it works flawlessly in-between a Windows machine, a Linux machine or an Android end device or iOS / Mac type device since all the OSes should respect "relative path compute" methods as described below.

Relative paths will circumvent the specific issues that you get when using "absolute" paths like "\\MyServer\MyShare\The%20Music%20Folder\…".

Some devices and/or OSes can't cope with this type of pathing. Linux and Android are specifically picky and likely won't read these - or they might, depending on how they are configured. You'll find out only if you can test with two different end devices in your specific environment.

There's more. More path operations are needed if you have ASCII / URL replacements like "%20" for spaces. You can leave those in, they might work… then, they might not work on some devices. It's complicated. You have to test what works in your setup, what doesn't. Mine for example cannot abide %20 and any other ASCII URL String replacement for non-URL characters that would break reading a URL. So someone created this standard, which originated from web browswers having limited capabilities when reading URLs - in an ever more complex world where UTM tags are invading your web privacy and make links ever more complex.

Even though, theoretically, the above sample link is the correct way to reflect spaces in a URL or URI/UNC path, it will break reading that link on some DLNA environments, depending likely on how the vendor configured their system. 

A URL cannot contain any spaces and other "forbidden" characters by definition, or it would break the actual URL / URI / UNC path. A space, for example is a natural "that URL ends here" sign in any interpreter… try it in something simple like Word. Typing a space will end your URL then and there, even if your file contains a working path if you type it in your Explorer window - Explorer compensates and replaces the Space for you. The same thing happens with a lot of other characters, and it gets ever more complex when you leave Latin lettering and enter the realm of UTF encodings.

In short:
Synology DLNA for example seems to replace the spaces when seeking files, hence a %20 is literally interpreted - this is my assumption, since file links containing these do not natively work on Synology Media Server. Whereas replacing the links with "technically incorrect" relative links magically works. Don't ask me why, ask Synology.

To make matters complex, relative pathing also requires you to exactly pinpoint the location of a DLNA playlist to find the actual media file. The "relative" part is important.

Observe:

We will need to give the DLNA server the means to actually look up the files "relative" to the playlist it is reading currently. So you will likely need to create a (Capture Group 3), replacing the UNC absolute or whatever path your software created with relative ..\. link types as per below rules for "relative path compute" functions.

Believe me, I've extensively dug into this, it's a mess out there with creating M3U(8) files, beginning with the fact, that actually it should be .m3u (for Windows machines) but advanced lettering necessitated someone to create the "m3u8" ending for a UTF-8 encoded file. So, there already are 2 file types for the same thing.

Then, we have the matter of how any given player or playlist creator software (whatever you use) handles pathing when writing the list. Ie. Will your tool handle the path like C:\Music%20Files\SampleMusicFile.mp3 or like C:\Music Files\SampleMusicFile.mp3? Who's to know?

Check your playlist file with an external text editor - Notepad++ is recommended so you can actually follow this tutorial.

How does relative pathing actually work?

Sample Structure:

_PLAYLISTS

 └ myplaylist.m3u

_Albums

└ \[folders with albums]

    └MySampleAlbum
      └MySample.mp3

_REMIXES

└ [folders with remix type albums or collections]

_ETC

└ [other stuff]

MoreFolders

└ \ [more folders]

      └ [more stuff]

└ [folder Full of single mp3files, for messy people]


Then, relative links work like this:
..\ one folder up from playlist folder 

To access the _Albums 
..\_Albums\[whatever you want to reach within this]

A note: this relative path will go UP one folder from current folder (assuming you are in _PLAYLISTS for ex.) then go into _Albums folder. This will fail if there is no such folder, so replace with your folder names.

So, to access "MySampleAlbum" and within that "MySample.mp3":
..\_Albums\MySampleAlbum\MySample.mp3


If your playlist is in the SAME folder structure as the music you're trying to reach, use 
.\ (one dot instead of two)

This would work for a structure like this (if you like tidy folders, you won't use it I'm sure)

[a list of playlists.m3u]
\MUSIC

Then you'd access the \MUSIC folder like this:
.\MUSIC\[your further path to file(s)]

Hope that helps.
![image](https://github.com/Rincemac/LifeHax/assets/24958093/9bf3e1fd-620f-48c8-bb31-c019f056810d)

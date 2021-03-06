uconnectsync
============

Syncing script from Mac iTunes to external drive for UConnect.

First, credit and thanks to WJM, Array, Kevin@kamloops, area51tazz (all from http://jeepcherokeeclub.com) and everyone else that has posted something on getting music to the Cherokee's UConnect.

Pointers to some existing threads:
----------------------------------

* http://jeepcherokeeclub.com/65-electronics-audio-lighting/13842-how-export-osx-itunes-music-playlists-sd-card-uconnect-8-4-a-2.html

* http://jeepcherokeeclub.com/65-electronics-audio-lighting/94025-sd-card-recommendations-uconnect-2.html#post1051865

There's considerable overlap between information there and here, and some tips and tools I don't go into for playlist export, art resizing, etc.

I do provide a nice and very fast one-command solution, and the script itself is instructive for solving a few problems.

One caveat: I assume command-line knowledge. The first How To I point to above is much more verbose about how to use command line, and you can pretty much follow those instructions and substitute my script if you prefer.

I wanted a quick and easy way to update music to the Cherokee. I have about 90 GB of iTunes music (and growing), so there were a few requirements.

Requirements:
-------------

* Be as painless as possible, preferably a single-shot command.

* Only copy new/changed files, both to save sync time and because the Cherokee takes forever to refresh its database if you update everything.

* Support exported playlists, as I use a playlist-per-album for recent purchases so I can pick them out of the other 760-odd albums on the drive.

* Support at least 128 GB, preferably more.

* Require much less patience than the other methods I've seen. I have little to none.

What I'm using:
---------------

* 256 GB USB Drive. Make it a fast one for your sanity's sake. I'm using this PNY Turbo drive.

 http://www.amazon.com/PNY-Turbo-256GB-Flash-Drive/dp/B00JN1TOHM/ref=sr_1_1 .

* OS X Yosemite. But I think it'll work on earlier versions as well.

* Strictly for convenience sake (not necessary) a handful of scripts from http://dougscripts.com/itunes/

* For reference sake, I'm using the 8.4AN unit in the 2015 Trailhawk. This uses the .32 version of the firmware. I'd love to get assurance this all works with a 2014 or a UConnect 5, but I don't see why it wouldn't.

Steps:
------

1. Format the drive to FAT32 via Disk Utility.
 
 UConnect doesn't support exFAT so you have to pick something else. For Windows folks, this would be NTFS, but Mac has poor support for the format.
 
 Fortunately, Mac is fine formatting even large disks to FAT32 via Disk Utility; just pick plain FAT when you reformat it.
 
2. Give it a name. I call mine `UCONNECT`, which the script looks for by default.
 
3. Grab the latest version of my sync script from here:
 
 https://raw.githubusercontent.com/geoelectric/uconnectsync/master/usync
 
 Save it somewhere as a raw text file.
 
4. Edit the script, and change a few variables in it to match your setup.
 
 At the top of the script, change the `volume=UCONNECT` line to point to the name you used above instead.
 
 You can also change `musicdir` and `playlistdir` to the name of the top-level directories you'd like to use for music and playlists. I use `iTunes-Music` and `Playlists`, respectively.
 
 You can change the `delete` variable to `1`, if you'd like to delete files from the target drive when the source file no longer exists (i.e. you deleted them completely from iTunes). This is disabled by default.
 
 If your iTunes Media folder isn't stored in the usual place (say, it's on an external drive) you'll need to modify the src variable too to point at it.

5. Copy the script somewhere on your path.

 `/usr/local/bin` is a good place (you might need to use sudo to copy it). I have `~/bin` on my path and put it there.

6. `chmod +x /usr/local/bin/usync` (or wherever you put it), to make it executable.

 You can also skip the last two steps and put the script anywhere, but then you should run it like `bash ./usync` from the directory you put it.

7. To run the script, just type `usync` at the command line. This is all you need to do in the future to resync new music.

8. Eject the drive properly from Mac then put it in the UConnect port. It'll take a bit to get indexed, but everything should be there.

The sync script does several things:
------------------------------------

* All iTunes music located in the iTunes Media folder will be copied to the drive if not already there, or if it's been updated since the last copy. I believe only changes to standard tags will cause a file update; library-only things like rating and play count will not, as far as I know.

* If you have exported any playlists from iTunes to the directory expected by the script (by default, into `/Playlists`), it will modify the paths in the .m3u to refer to the music you've synced.

* Any hidden files on the target volume will be deleted. They apparently confuse UConnect, and they definitely aren't needed.

Some notes on the file sync:
----------------------------

* The same directory structure will be used on the drive as by iTunes, which is Artist/Album/song if you have the keep organize option checked. This will all be put under the top-level directory on the target volume you defined above (`/iTunes-Music` by default). This directory will be created if necessary.

* Files that haven't physically changed won't be copied again (rsync is used from the script).

* For comparison, 90 GB music takes 20 minutes on a first copy for me, but 0.2 seconds for subsequent syncs if nothing has changed. Most of the time it's a minute or less for a few albums worth of purchases plus whatever iTunes Match decided to touch in the background.

* iTunes LPs and videos are skipped. I don't currently skip the album insert pdfs, since they like to sneak into playlists, but a later version might. I think UConnect ignores them anyway.

* Any music not directly managed by iTunes (stored elsewhere, but only pointed at) will not be copied. For best results, make sure you have "Copy Music to iTunes Media folder" and "Keep iTunes Media folder organized" checked in iTunes Advanced Options.

* By default, the script will only add/update files to the target drive. If you sometimes delete music completely from iTunes, you might want the corresponding files deleted on the target. If so, set the `delete=0` variable at the top of the script to be `delete=1`. This will only delete files from the target.

Some notes on playlists:
------------------------

* This script will take playlists exported from iTunes (no external tools necessary) and make them work on the drive. It will only touch playlists in the directory set in the `$playlistdir` variable. By default, this is `/Playlists`. If the directory doesn't exist, it will just skip that part.

* You can use File | Library | Export Playlist from iTunes to export to the drive. The paths will export pointing back at your iTunes library, but this script will modify them.

* The above is a lot easier if you map a key to it via System Preferences | Keyboard | Shortcuts. I use Shift-Cmd-X. Then you can just highlight the playlist, hit the shortcut, press enter, next.

* For convenience, you can also use the Batch Playlist Export Applescript from Doug's iTunes Scripts to do a bunch of playlists at once. The paths will export slightly differently, but the sync script handles them as well.

 http://dougscripts.com/itunes/scripts/ss.php?sp=batchexportplaylists

* If the playlist refers to something not in the main iTunes Media folder (and thus not copied), that path won't be converted and UConnect will ignore that entry. I think it'll honor the rest of the playlist.

Some notes on artwork:
----------------------

* Your artwork needs to be embedded within the files if you want to see it. This script from Doug's will do that, but you need to do it a few albums at a time at most as it gets unstable after processing a bunch of files.

 http://dougscripts.com/itunes/scripts/ss.php?sp=reembedartwork

* You can also see what isn't embedded yet with this script (costs $1.99):

 http://dougscripts.com/itunes/scripts/ss.php?sp=trackswithoutembedded

* Array (from JCC) has discovered there's a size limit on the artwork. Old iTunes artwork was 600x600, new iTunes artwork seems to be 1400x1400. Can definitely say UConnect doesn't like the new stuff, but 600x600 seems to work.

* This script will resize embedded artwork (may also embed it for you if it wasn't already--I haven't tried it. It also costs $1.99):

 http://dougscripts.com/itunes/scripts/ss.php?sp=reapplydownsized

* Several tools have also been identified in the JCC links above that will do embedding and resizing, some for pay and some not. area51tazz (from JCC) posted a resizing Automator script in the first How To thread.

 www.mynetstuff.com/files/resize.zip

* All said, if you don't mind occasional blank artwork, you really don't need to worry about this. It's nice to see it, but makes things much easier if you just let it go.

Some tech notes:
----------------

...since I pulled my hair out a bit on these issues. This is mostly useful for people cobbling their own solutions together:

* rsync is ideal for only copying stuff that changed. Use it instead of `cp -R`, `tar`/`cpio`, or whatever, and you'll save a ton of time.

* However, FAT32 treats timestamps differently than OS X filesystems do, and rsync will think files have changed when they haven't. You need to add `--modify-window=1` as an option to rsync so it can deal with that and not copy spuriously.

* Depending on how you export your playlists, you might end up with either a straight filepath, or a `file://` URI in your .m3u. UConnect will not follow a `file://` URI, so that needs to be changed to a path (which the script does).

* Despite advice I've seen to the contrary, UConnect (at least the .32 version with the 2015s) *does not* need playlists to be in any particular directory. It finds them fine in root, `/Playlists`, same directory as the music, whatever.

* Also despite advice I've seen to the contrary, UConnect *does not* like relative paths in the .m3us. You need to use an absolute path from the root of the drive, like `/iTunes-Music/Artist/Album/song.mp3`

* This is more or less well-known, but the unit does fine with 256kbps AACs from iTunes, as long as they're DRM-free. Any old ones you have can be made DRM-free via iTunes Match by deleting/redownloading. Think that might work even without Match as you purchased them directly.

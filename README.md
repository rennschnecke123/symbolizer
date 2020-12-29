*Symbolizer - a wrapper for rsync ... and more!

Do you use rsync? Fine!
But ever thought about to handle this in a more advanced way?
Use symbolizer which unites some great functions in one tool 
which can do:

- rsync 
Just like most people would sync to folders in one way without(!) deleting old data

- snap
Same as rsync but deleting old data so that both dirs are in sync!

- archive
Like rsync but not just copying data files but moving them to the destination.
Files on the source will be replaced by symbolic links.

- rebuild
Convert all symbolic links into data files.
This could be much more data then the amount fittung to your source!
So after that it's possible that your source has no available space any more.
But this should be no problem (if this is a partition so you don't have side effects with the system).
No data on source or destination will be destroyed. 
Perhaps there will be some *.tmp-Files left - delete them by hand or job. 


*How to use?

Syntax:
symbolizer <from> <to> [mode] [timeout]

where <from> and <to> are source and destination folders.
Mode and timeout are optional parameters where mode is one of the four modes explained above.
Timeout is a value (in seconds - default: 3600) for the max runtime with the archive mode.
This is the max time a file could disappear while being copied to the destination.


*Practical use

For just doing a rsync this tools wouldn't make sense.
Ok, you don't have to know any switch - just do "symbolizer <from> <to>" ... that's all.
The snap mode nearly does the same but will destroy data not available via source.
But have a look at the archive. In this mode you will get more space for new data on the
source folder! This is because data will be moved to the destination and it will be replaced by symbolic links which
has some great effects. 
A scenario:
Imagine you have one (fast) ssd and two (slow) hdds.
This is not exotic but a great combination for creating something like a "hybrid system".
Now you use the sdd as data disk. Great because it's fast!
Problem: There is no redundancy and there is not so much space compared to the two hdds.
So a good way is to copy the data to a raid (mirror) of the two hdds (with a standard nas, zfs, linux raid....).
Then you have speed, space _and_ redundancy.

Writing: 
ssd -> fast
Reading: 
still on ssd? -> fast 
moved to raid? -> not like ssd but fast because when reading you have the bandwith of _both_ hdds.
And when reading most hdds are much faster as when writing.

And last but not least the rebuild mode is to get real data to the source again.
This is just because you want to have real files there real - not just links.
Or you have to do some work, replacements ... on the raid so this is a possible way to move the data back.
Perhaps temporary. 


* How to control?
Supposing to use a cronjob to do a "symbolizer <a> <b>" every minute, hour, day.... you can use two ways to 
choose the mode to use.
If you choose the mode via script start this will only use this mode!
If you don't choose a special mode with start there will two files in the folder:
.archive
.snap

With this default files the mode will be "rsync". So this will not change anything in your data but making backups to the destination.
If you delete .archive and let only .snap be there, this will be the snap mode - but only once!
After this .archive will be there again and the next run will be in rsync mode again.
If you delete .snap instead, this will be the archive mode and will stay in this mode!
To get into the rebuild mode, store an empty file ".rebuild" in the source folder and delete .snap and .archive!
After one run the mode is rsync again.

So, this could be a way for samba, sftp, ... to control the mode just via deleting a control file!
If you want to be sure nobody could change the mode, just start the sync job with exact mode to use as described above.


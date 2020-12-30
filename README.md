<h2>*Symbolizer - a wrapper for rsync ... and more!</h2>

Do you use rsync? Fine!
But ever thought about to handle this in a more advanced way?
Use symbolizer which unites some great functions in one tool 
which can do:

<b>- rsync <br></b>
Just like most people would sync to folders in one way without(!) deleting old data

<b>- snap <br></b>
Same as rsync but deleting old data so that both dirs are in sync!

<b>- archive <br></b>
Like rsync but not just copying data files but moving them to the destination.
Files on the source will be replaced by symbolic links.

<b>- rebuild<br></b>
Convert all symbolic links into data files.
This could be much more data then the amount fittung to your source!
So after that it's possible that your source has no available space any more.
But this should be no problem (if this is a partition so you don't have side effects with the system).
No data on source or destination will be destroyed. 
Perhaps there will be some *.tmp-Files left - delete them by hand or job. 


<h3>*How to use?</h3>

<b>Syntax:<br>
symbolizer &lt;from&gt; &lt;to&gt; [mode] [timeout]</b>

where &lt;from&gt; and &lt;to&gt; are source and destination folders.
Mode and timeout are optional parameters where mode is one of the four modes explained above.
Timeout is a value (in seconds - default: 3600) for the max runtime with the archive mode.
This is the max time a file could disappear while being copied to the destination.


<h3>*Practical use</h3>

For just doing a rsync this tools wouldn't make sense.
Ok, you don't have to know any switch - just do "symbolizer <from> <to>" ... that's all.
The snap mode nearly does the same but will destroy data not available via source.
But have a look at the archive. In this mode you will get more space for new data on the
source folder! This is because data will be moved to the destination and it will be replaced by symbolic links which
has some great effects. <br><br>
A scenario:<br>
Imagine you have one (fast) ssd and two (slow) hdds.<br<
This is not exotic but a great combination for creating something like a "hybrid system".<br>
Now you use the sdd as data disk. Great because it's fast!<br>
Problem: There is no redundancy and there is not so much space compared to the two hdds.
So a good way is to copy the data to a raid (mirror) of the two hdds (with a standard nas, zfs, linux raid....).
Then you have speed, space _and_ redundancy.
<br>
Writing: <br>
ssd -&gt; fast<br>
Reading: <br>
still on ssd? -&gt; fast <br>
moved to raid? -&gt; not like ssd but fast because when reading you have the bandwith of _both_ hdds.
And when reading most hdds are much faster as when writing.

And last but not least the rebuild mode is to get real data to the source again.
This is just because you want to have real files there real - not just links.
Or you have to do some work, replacements ... on the raid so this is a possible way to move the data back.
Perhaps temporary. 


<h3>*How to control?</h3>

Supposing to use a cronjob to do a "symbolizer &lt;a&gt; &lt;b&gt;" every minute, hour, day.... you can use two ways to 
choose the mode to use.
If you choose the mode via script start this will only use this mode!
If you don't choose a special mode with start there will be two files in the folder:<br>
.archive<br>
.snap<br>

With this default files the mode will be "rsync". So this will not change anything in your data but making backups to the destination.
If you delete .archive and let only .snap be there, this will be the snap mode - but only once!<br>
After this .archive will be there again and the next run will be in rsync mode again.<br>
If you delete .snap instead, this will be the archive mode and will stay in this mode!<br>
To get into the rebuild mode, store an empty file ".rebuild" in the source folder and delete .snap and .archive!<br>
After one run the mode is rsync again.
<br>
So, this could be a way for samba, sftp, ... to control the mode just via deleting a control file!<br>
If you want to be sure nobody could change the mode, just start the sync job with exact mode to use as described above.


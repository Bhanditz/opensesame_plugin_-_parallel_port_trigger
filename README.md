OpenSesame Plugin: Parallel Port Trigger
==========

*An OpenSesame Plugin for sending stimulus synchronization triggers through the parallel port to data acquisition systems.*  

Copyright, 2013, Bob Rosbag  

Contributions: Code is based on the work of Per Sederberg. Debugged and polished by Edwin Dalmaijer.

1. About
--------

In EEG/ ERP studies it is common to send triggers to mark the time of significant events (e.g., the onset of a trial, presentation of a particular stimulus, etc.). Triggers are typically bytes that are sent via the parallel port to the EEG apparatus. This can be done with the parallel port trigger plugin which works in Linux and Windows. For Windows the `DLPortIO.dll` driver is used to access the parallel port. Values range between 0-255. Parallel port adress
has to be specified manually. 

You can download the plugin from [here][plugin]  


### Linux ###

In Linux we have to use the "parport_pc" module (tested in Debian Wheezy). We can accomplish this by executing the following commands:

$ sudo rmmod lp  
$ sudo rmmod parport_pc  
$ sudo modprobe parport_pc  
$ sudo adduser user lp  

Then logout and login, and you are ready to go!  


### Windows XP & Vista 32-bit ###

1. Download the 32-bit DLPortIO driver from [here][win32-dll] and uncompress the zip archive.

2. Go to `DriverLINX/drivers` folder and copy `dlportio.dll` and `dlportio.sys` to the `install` folder. This is the folder  where `install.exe` is located. Then run `install.exe`

3. You need to copy `dlportio.dll` to the OpenSesame folder (that is, the same folder that contains `opensesame.exe`).


### Windows 7 32/64-bit ###

1. Download the 32-bit or 64bit DLPortIO driver [here][win7-dll] and uncompress the zip archive.  

2. As Windows 7 has a strengthened security system (at least compared to XP) one cannot simply install the DLPortIO driver. This won't work as Windows 7 will block all attempts of installing a not officially signed (by Microsoft) driver. Good for the security of an average user -- bad for us. To bypass this restriction one has to use a little helper program called "Digital Signature Enforcement Overrider" (DSEO) which can be downloaded [here][dseo] (of course there are other possible ways to do this but this program is mentioned in the DLPortIO-readme.txt and one does not have to dive deeper into MS Windows 7 architecture specialities).  

3. Start DSEO with administrator privileges (right click on dseo13b.exe, select "run as administrator"). Now the DSEO window pops up. It just presents a list of options which operation to run next.  

4. Choose the option "sign driver/sys-file" and press ok. Now another window appears where you have to type in the absolute path to the DLPortIO.sys file (only this one, not the dll!). Remember to escape spaces in the path if you have any (don't ask how long that took me) otherwise your files will not be found. Pressing ok will sign the sys-file.  

5. Back in the DSEO list choose "enable test mode" and press ok. Then choose "exit" and restart your PC. (Windows 7 wrongly complains that DSEO might not be installed correctly -- just click on "yes, the software is installed correctly").  

6. After boot up is completed you'll see that something like "Windows 7 test mode built #number#" is written on the desktop just above the clock in the starter-bar. That's necessary. You have to be in test mode to run this unofficially signed driver.  

7. Now run `DLPortIO_install.bat` with administrator privileges (in Windows Explorer, right click the file, ...). Answer "yes" if Windows warns you about registry changes.  

8. Reboot  

9. Copy the `DLPortIO.dll` file to the Opensesame folder (that is, the same folder that contains `opensesame.exe`).  


Inline Script

Instead of using the plugin, it is also possible to send triggers with dlportio.dll or pyparallel with an inline script. 

First you can add an inline_script to the start of the experiment with the following code in the prepare phase:

{% highlight python %}
try:
  from ctypes import windll
  global io
	io = windll.dlportio # requires dlportio.dll !!!
except:
	print 'The parallel port couldnt be opened'
{% endhighlight %}

This will load `dlportio.dll` as a global object called `io`. Please note that failure will not crash the experiment, so make sure to check the debug window for error messages!

Now you can now use the following code in an inline_script anywhere in the experiment to send a trigger:

{% highlight python %}
global io
trigger = 1
port = 0x378
try:
	io.DlPortWritePortUchar(port, trigger)
except:
	print 'Failed to send trigger!'
{% endhighlight %}

Note that this send the trigger 1 to port 0x378 (=888). Change these values according to your set-up.


Recommendations

- Start your experiment with a 'zero' trigger to make sure all the pins are set to zero.  

- It's recommended to use the Psycho back-end (using PsychoPy) instead of the legacy back-end (using PyGame) for your experiment. This is because PsychoPy takes the refresh rate of the monitor into account when returning time stamps, whereas PyGame does not. (PyGame does wait for the vertical refresh too, but this is not reflected in the time stamps. [Source: lvanderlinden][post-1]
    
- Send the trigger code right after (instead of just before) the presentation of your stimulus (assuming that it's the stimulus onset you want to mark). By doing so you'll make sure that the time stamp is as accurately as possible and will not suffer from a small random jitter due to your monitor's refresh rate. [Source: lvanderlinden][post-1]


Troubleshooting

There are a number of relevant forum topics in which trigger-related problems are discussed (and, for the most, solved!).

- A post about ghost triggers, i.e. unwanted triggers that are mysteriously registered by the EEG apparatus: [link][post-2]

Please don't hesitate to post questions on the forum, or to let us know of your experiences (good or bad).


References

- A post with elaborate installation instructions for DLPortIO on Windows 7 ([Source: absurd][post-3]).
- see <http://osdoc.cogsci.nl/devices/triggers/>

[win32-dll]: http://files.cogsci.nl/misc/dlportio.zip
[win7-dll]: http://real.kiev.ua/avreal/download/#DLPORTIO_TABLE
[plugin]: https://github.com/dev-jam/opensesame_plugin_parallel-port-trigger
[dseo]: http://www.ngohq.com/home.php?page=dseo
[post-1]: http://forum.cogsci.nl/index.php?p=/discussion/comment/748#Comment_748
[post-2]: http://forum.cogsci.nl/index.php?p=/discussion/comment/780#Comment_780
[post-3]: http://forum.cogsci.nl/index.php?p=/discussion/comment/745#Comment_745


2. LICENSE
----------

Parallel Port Trigger is distributed under the terms of the GNU General Public License 3.
The full license should be included in the file COPYING, or can be obtained from

- <http://www.gnu.org/licenses/gpl.txt>

Parallel Port Trigger contains works of others. For the full license information, please
refer to `debian/copyright`.

3. Documentation
----------------

Installation instructions and documentation on OpenSesame are available on the documentation website:

- <http://osdoc.cogsci.nl/>

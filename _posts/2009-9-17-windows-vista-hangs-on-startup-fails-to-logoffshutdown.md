---
layout: post
title: Windows Vista hangs on startup, fails to logoff/shutdown
---
<p>I’ve had enough pain with this problem, which seems not to be that uncommon, to try and ease it for anyone else who has it.
<h3>Symptoms</h3>
<ul>
<li>Windows Vista runs extremely slowly upon login, with the CPU running at close to 100%
<li>Task manager won’t start: if you try running it the green task icon will appear but no main window
<li>If you try to shut down, the computer will hang while trying to log off
<li>If you use <a href="http://technet.microsoft.com/en-us/sysinternals/bb896653.aspx" title="Process Explorer from Microsoft Sysinternals">Process Explorer</a> to see what’s happening, you find that an instance of svchost.exe is responsible for the CPU load. If you look at the services it is hosting, they will include Windows Audio Services, DHCP and, key here, Event Log. The instance of svchost may also be associated with something called audiodg.exe (which is part of the Windows Audio setup). Killing the process stops the CPU load, but breaks a lot of other things as well (like networking, which is dependant on some of those services).
</ul>
<h3>Problem</h3>
<p>It seems that many people have experienced similar problems, and many solutions have been targeted at the audio services. I tried all of these with no luck. Eventually I narrowed the problem down to the Event Log service and found <a href="http://groups.google.com/group/microsoft.public.windows.server.general/browse_thread/thread/9b413e8c9cc30d45" title="Google Groups message">this</a> which is for Windows Server 2008 but works for Vista too. The problem was some corrupted event logs that were causing the Event Log service to throw a woopsy every time it was started.
<h3>Solution</h3>
Here’s what worked for me:
<ul>
<li>Kill the offending process using Process Explorer to give you control of the system
<li>Disable the “Windows Event Log” service (Control Panel - Administrative Tools - Services)
<li>Reboot - this should work fine, but you may notice that all network functionality is lost
<li>Browse to %systemroot%\system32\winevt\logs and move/delete all the *.evtx files that were modified around the time the problem started
<li>Set the Event Log service back to back to Automatic, and reboot
</ul>
<p>Of course, the more permanent solution is to steer well clear of Vista (or, some might say, Windows altogether).
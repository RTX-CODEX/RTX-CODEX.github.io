---
layout: post
title: A Survey of Capture the Flag Write-ups
categories: [blogging, capture-the-flag]
---

_A blog post by Lee Meeker, CODEX Engineer_

One way our CODEX engineers have fun and flex their cyber skills is by hosting and participating in Capture the Flag (CTF) events.  CTFs are like intense digital hide-and-seek or puzzle solving games.  If you love putting clues together to solve a puzzle or taking things apart to figure out how they work, then you too may enjoy CTF challenges!

A few write-ups for CTFs from one of our engineers Tylor Childers may be found on GitHub under [RTXCTF_Writeups](https://github.com/inukai132/RTXCTF_Writeups) :

<ul>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/C2%20API/C2%20API.md">C2 API</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Floppy%20Image/FloppyImage.md">Floppy Image</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Hacker%20Affiliates/HackerAffiliates.md">Hacker Affiliates</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Hash%20Service/Hash%20Service.md">Hash Service</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Layers/Layers.md">Layers</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/RmluZCB0aGUgU2hhcmU%3D/FindTheShare.md">RmluZCB0aGUgU2hhcmU=</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Rosenburg/Rosenburg.md">Rosenburg</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Secure%20SRAM/secure_sram.md">Secure SRAM</a></li>
<li><a href="https://github.com/inukai132/RTXCTF_Writeups/blob/master/Tiebreaker/TieBreaker.md">Tiebreaker</a></li>
</ul>

At first glance, I found the title of one in this list curious: "<tt>RmluZCB0aGUgU2hhcmU=</tt>"<br>
It seemed like random jibberish, but a hunch based on past exposure to different types of encoded data (the "<tt>=</tt>" at the end is normally a telltale sign) made me wonder if it was a simple [Base64](https://en.wikipedia.org/wiki/Base64) encoding which it did turn out to be:

    $ echo RmluZCB0aGUgU2hhcmU= | base64 -d
    Find the Share

In this ["RmluZCB0aGUgU2hhcmU="](https://github.com/inukai132/RTXCTF_Writeups/blob/master/RmluZCB0aGUgU2hhcmU%3D/FindTheShare.md) CTF write-up with a sub-title of "Net Pen 500" (Network Penetration) we are given the description of a fictitious company that has a new network file share that an employee there wants to find and access.  Through a few steps of studying system output and leveraging known exploits this CTF walk-through pivots us from server to server to finally reveal a file on the desired network share containing a text file with the flag -- thus capturing the flag.  It turned out that another write-up in the list also goes into detail on Base64 encoding as well as some encryption techniques -- see the write-up for ["Layers - Crypto 300"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Layers/Layers.md).


In another CTF write-up ["C2 API - Misc 500"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/C2%20API/C2%20API.md) we follow the investigation of the inner workings of a C2 (cyber lingo for [Command and Control](https://en.wikipedia.org/wiki/Command_and_control])) system by tracing the [API](https://en.wikipedia.org/wiki/API) it uses in intercepted communications and then using those same APIs to take over the C2 and use it to find and capture the flag "<tt>h4d_fun_tr0lling_y0u_0n_th1s_c0ngr4tz</tt>".  That may seem like a strange "flag", but it's just written in ["133t speak"](https://en.wikipedia.org/wiki/Leet) where the spelling of words are modified to use numbers or special characters as in "<tt>31337 H4X0R</tt>" for "<tt>Elite Hacker</tt>".   And if you haven't noticed already, there are also a lot of acronyms used in our profession -- at my previous employer we even had an acronym regarding the acronyms: TLA... we'd sometimes say there were too many undefined TLAs in a document meaning there were too many Two/Three Letter Acronyms.


A write-up of a more difficult one focused on forensics, data encodings, encryption, and reverse engineering of an executable, ["Floppy Image - Forensics 600"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Floppy%20Image/FloppyImage.md), leads us into examining email headers, [uudecoding](https://en.wikipedia.org/wiki/Uuencoding) attachments, more Base64 decodings, and the use of [IDA](https://en.wikipedia.org/wiki/Interactive_Disassembler) to discover that the Windows executable included in the email is able to run in 16, 32, and 64-bit modes and produce three different pieces of a Windows <tt>.com</tt> file which then need to be concatenated together in the correct order.  Putting all of these pieces together gets us to a final running of the resulting <tt>.com</tt> file that takes as input a recovered data file that had been deleted from the email attached floppy disk image to then output the well hidden flag -- definitely a lot of pieces to put together for this puzzle!


The ["Hacker Affiliates - Web 500"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Hacker%20Affiliates/HackerAffiliates.md) write-up describes a web site that takes user input and with some knowledge about the expected wrapper text around the flag we are trying to capture, we discover that server-side PHP code for the site seems to contain the flag, but it is redacted from being seen in the page source if the request does not come from localhost and so we instead get a fake flag <tt>RTX_FLAG{YOU_NEED_ADMIN}</tt>.  However, after some scripting and crafting of our website that we give as input to their web site form, we are able to discover and leverage some vulnerabilities in their site.  One last step involves using [iframes](https://en.wikipedia.org/wiki/HTML_element#Frames) to get past [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) protection, then we iterate in a script to collect leaked characters from the real flag until we have captured the whole thing: <tt>RTXFLAG{XS_L34KS_VIA_FR4M3}</tt>.


A similar iterative technique to obtain one character at a time of the flag is described in the ["Hash Service - Crypto 400"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Hash%20Service/Hash%20Service.md) write-up.  After connecting to a hashing service over the network and asking it for <tt>help</tt> we see there are commands to list files on the server and load files into memory to then perform hashing operations.  Armed with knowledge about the hashing operation, we iterate in a script to again capture one character at a time of the flag contained in the file named <tt>mysupersecretflagfile</tt> (seems like they could use a little more [OPSEC](https://en.wikipedia.org/wiki/Operations_security) in their file naming) that we observed in the file listing from the hashing server's <tt>info</tt> command.


Building on another service discovered in the "Hash Service" write-up, we continue in the write-up for ["Rosenburg - Crypto 500"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Rosenburg/Rosenburg.md) to connect to a server that gives a cyphertext prompt and takes a reply.  After some testing we discover that the length of our reply results in different replies about padding which leads us to attempt a ["Padding oracle attack"](https://en.wikipedia.org/wiki/Padding_oracle_attack).  Again this little bit of leaked information combined with our knowledge of the prefix string for the flag ( <tt>RTXFLAG{</tt> ) allows us to script an algorithm to identify the remaining bytes of the flag.


The next CTF write-up, ["Secure SRAM - Reverse Engineering 500"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Secure%20SRAM/secure_sram.md), delves into the hardware side a little more.  [SRAM](https://en.wikipedia.org/wiki/Static_random-access_memory) is a special type of computer memory that while still being volatile has some [data remanence](https://en.wikipedia.org/wiki/Data_remanence) characteristics.  Through another network service we connect to a hardware device, which is our target containing the flag (secure keys), via a [UART](https://en.wikipedia.org/wiki/Universal_asynchronous_receiver-transmitter) header on its system board.  This network connection allows a binary to be uploaded and executed as firmware on the target device (helper scripts, a sample binary with source, and an emulator for the hardware are provided as part of this challenge).  After crafting a binary payload to dump the target's memory and grepping through it for our known flag prefix, we discover the flag ( <tt>RTXFLAG{4VR_P4YL04D$_DUMP_R4M!!}</tt> ) was left in plain-text after the hardware device's boot process completed.


The final write-up we review here is the ["Tie Breaker"](https://github.com/inukai132/RTXCTF_Writeups/blob/master/Tiebreaker/TieBreaker.md).  This challenge starts with only an IP address and port for a web site and "good luck."  After connecting to the web site's initial home page, we observe that there are links to a couple of other pages on the site.  Seeing an error message after clicking on the <tt>bug_report</tt> link leads us to experiment with an HTML GET request including a user and password value.  Some more experimentation and it seems that we can download arbitrary files from the web site which allows us to grab an <tt>index.php</tt> source file to see more of how the site is setup.  From the PHP source we see its use of <tt>shell_exec()</tt> and limited input filtering allows us to execute shell commands of our own on the server, but this turns out to be a dead end and nothing is found there.  Switching to studying network packet captures from the web site leads us to notice special traffic on port 1025.  Binary data is given and Fibonacci numbers are returned.  Next, the <tt>PayloadDecompiler</tt> that was found on and downloaded from the web site allows us to get assembly source for the <tt>Payload.vm</tt> that was also found on the web site.  Googling some of the unique mnemonics ( <tt>puship</tt> and <tt>popip</tt> ) from the disassembly leads us to a GitHub site for a "stack machine" that also has example code for Fibonacci number generation.  Some more work to get our own "stack machine" program running and leveraging details found on the <tt>bug_report</tt> page about "leaking secrets from outside of the VM memory" allows us to harvest and decode output from port 1025 on the server to finally capture the flag.


These were just a few examples of CTFs, there are events of all types and sizes hosted by individuals and organizations across the world.  Some resources if you'd like to find out more about some of them are:

<ul>
<li><a href="https://ctftime.org/event/list/">https://ctftime.org/event/list/</a></li>
<li><a href="https://hackthebox.com/hacker/ctf">https://hackthebox.com/hacker/ctf</a></li>
<li><a href="https://defcon.org/html/links/dc-ctf.html">https://defcon.org/html/links/dc-ctf.html</a></li>
<li><a href="https://picoctf.org">https://picoctf.org</a> <small>(focusing on younger students)</small></li>
<li><a href="https://nationalccdc.org">https://nationalccdc.org</a></li>
</ul>


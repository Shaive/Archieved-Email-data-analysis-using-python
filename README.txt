Analyzing an EMAIL Archive from gmane and vizualizing the data
using the D3 JavaScript library

This is a set of tools that allow you to pull down an archive
of a gmane repository using the instructions at:

http://gmane.org/export.php

You should install the SQLite browser to view and modify the databases from:

http://sqlitebrowser.org/

The first step is to spider the gmane repository.  The base URL 
is hard-coded in the gmane.py and is hard-coded to the Sakai
developer list.  You can spider another repository by changing that
base url.   Make sure to delete the content.sqlite file if you 
switch the base url.  The gmane.py file operates as a spider in 
that it runs slowly and retrieves one mail message per second so 
as to avoid getting throttled by gmane.org.   It stores all of
its data in a database and can be interrupted and re-started 
as often as needed.   It may take many hours to pull all the data
down.  So you may need to restart several times.

If you download this, you can "catch up with the latest" by
running gmane.py.

Navigate to the folder where you extracted the gmane.zip

Note: Windows has difficulty in displaying UTF-8 characters
in the console so for each console window you open, you may need
to type the following command before running this code:

    chcp 65001

http://stackoverflow.com/questions/388490/unicode-characters-in-windows-command-line-how

Mac: python3 gmane.py 
Win: gmane.py 

The program scans content.sqlite from 1 up to the first message number not
already spidered and starts spidering at that message.  It continues spidering
until it has spidered the desired number of messages or it reaches a page
that does not appear to be a properly formatted message.

Sometimes gmane.org is missing a message.  Perhaps administrators can delete messages
or perhaps they get lost - I don't know.   If your spider stops, and it seems it has hit
a missing message, go into the SQLite Manager and add a row with the missing id - leave
all the other fields blank - and then restart gmane.py.   This will unstick the 
spidering process and allow it to continue.  These empty messages will be ignored in the next
phase of the process.

One nice thing is that once you have spidered all of the messages and have them in 
content.sqlite, you can run gmane.py again to get new messages as they get sent to the
list.  gmane.py will quickly scan to the end of the already-spidered pages and check 
if there are new messages and then quickly retrieve those messages and add them 
to content.sqlite.

The content.sqlite data is pretty raw, with an inefficient data model, and not compressed.
This is intentional as it allows you to look at content.sqlite to debug the process.
It would be a bad idea to run any queries against this database as they would be 
slow.

The second process is running the program gmodel.py.  gmodel.py reads the rough/raw 
data from content.sqlite and produces a cleaned-up and well-modeled version of the 
data in the file index.sqlite.  The file index.sqlite will be much smaller (often 10X
smaller) than content.sqlite because it also compresses the header and body text.

Each time gmodel.py runs - it completely wipes out and re-builds index.sqlite, allowing
you to adjust its parameters and edit the mapping tables in content.sqlite to tweak the 
data cleaning process.

Running gmodel.py works as follows:

Mac: python3 gmodel.py
Win: gmodel.py

The gmodel.py program does a number of data cleaing steps

Domain names are truncated to two levels for .com, .org, .edu, and .net 
other domain names are truncated to three levels. Also mail addresses are
forced to lower case and some of the @gmane.org address like the following

   arwhyte-63aXycvo3TyHXe+LvDLADg@public.gmane.org

are converted to the real address whenever there is a matching real email
address elsewhere in the message corpus.

If you look in the content.sqlite database there are two tables that allow
you to map both domain names and individual email addresses that change over 
the lifetime of the email list.


And so all the mail messages will be collected under one sender even if 
they used several email addresses over the lifetime of the mailing list.

You can also make similar entries in the DNSMapping table if there are multiple
DNS names you want mapped to a single DNS.

You can re-run the gmodel.py over and over as you look at the data, and add mappings
to make the data cleaner and cleaner.   When you are done, you will have a nicely
indexed version of the email in index.sqlite.   This is the file to use to do data
analysis.   With this file, data analysis will be really quick.

The first, simplest data analysis is to do a "who does the most" and "which 
organzation does the most"?  This is done using gbasic.py:

Mac: python3 gbasic.py 
Win: gbasic.py 

You can look at the data in index.sqlite and if you find a problem, you 
can update the Mapping table and DNSMapping table in content.sqlite and
re-run gmodel.py.

There is a simple vizualization of the word frequence in the subject lines
in the file gword.py:

Mac: python3 gword.py
Win: gword.py

Output written to gword.js

This produces the file gword.js which you can visualize using the file 
gword.htm.

A second visualization is in gline.py.  It visualizes email participation by 
organizations over time.

Mac: python3 gline.py 
Win: gline.py 








---
date: 2020-09-08
---
As a stop gap before implmenting ansible into our secrets managment tool I needed a way to give team members the ability to update some local unix passwords. The python script below (Works for python2 or python3) allows them to hash a password that isn't saved anywhere on the machine the script runs on.


        from getpass import getpass
	from crypt import *
	p=getpass()
	if p==getpass('Please Repeat: ' ):
	    print ('\n'+crypt(p, METHOD_SHA512))
	else:
	    print( '\nFailed repeating.')

They can then take the hash and place it into an AWX job template that uses two `EXTRA VARIABLES` `username:` and `user_pass`. These are filled in by using the survery feature in AWX.
The user running the job template receives two prompts at run. Username which uses a `ANSWER TYPE` of text and user_pass which uses an `ANSWER TYPE` of `Password`. Using the password answer type encrypts the output in the AWX job logging so the has is not placed inside the log.

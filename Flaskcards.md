CTF name : PicoCTF (28/09/2018 - 12/10/2018)

Challenge name : Flaskcards

Challenge description : We found this fishy website for flashcards that we think may be sending secrets. Could you take a look?

Challenge hints : - Are there any common vulnerabilities with the backend of the website?
                  - Is there anywhere that filtering doesn't get applied?
                  - The database gets reverted every 2 hours so your session might end unexpectedly. Just make another user
                  
Challenge category : Web

Challenge points : 350

Challenge solved : 729 times

Challenge URL (if not down) : http://2018shell2.picoctf.com:17991/

------

# Introduction

This challenge was about __SSTI__ (Server Side Template Injection), as mentionned in the title. Indeed, why *flaskcards* instead of *flashcards* ? We just made a few researches about Flask on the net and it turned out that Flask was a Python Microframework used to develop web content.

# Detection

As we mentionned previously, the first thing we did was to look for a SSTI. This website was composed of 6 pages : 
* 3 reachable pages without authentication : 
'* A registering page, nothing to crazy.'
'* A login page, same here.'
'* A home page, still not really exciting.'
*3 pages only reachable after authentication :
'*' A page where you can create a card with a question and an answer
'*' A page where you can take a look at the cards you created
'*' An admin page sayin' that we're not admin

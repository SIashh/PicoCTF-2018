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
* 3 reachable pages __without authentication__ : 

  `* A registering page, nothing to crazy.`
  
  ![Registering page](https://user-images.githubusercontent.com/36658045/47420228-3fbcf780-d77e-11e8-91b4-99428fc2b4f2.png)
  
  `* A login page, same here.`
  
  ![Login page](https://user-images.githubusercontent.com/36658045/47420189-2320bf80-d77e-11e8-98a9-2522d06492d6.png)

  `* A home page, still not really exciting.`
  
  ![Home page](https://user-images.githubusercontent.com/36658045/47420084-e48b0500-d77d-11e8-90fb-784dc11ac4d7.png)
  
* 3 pages only reachable __after authentication__ :

  `* A page where you can create a card with a question and an answer`
  
  ![Creating card page](https://user-images.githubusercontent.com/36658045/47420285-5bc09900-d77e-11e8-9938-eda064fbf329.png)

  `* A page where you can take a look at the list of cards you've created`
  
  ![Listing card page](https://user-images.githubusercontent.com/36658045/47420335-76930d80-d77e-11e8-846a-e2e3e71abfbc.png)

  `* An admin page sayin' that we're not admin`
  
  ![Admin page](https://user-images.githubusercontent.com/36658045/47420366-87dc1a00-d77e-11e8-955a-590faeff4498.png)

*Interesting...* In this web architecture, the only place you can try SSTI is the __create card page__ since you can see the result of the injection in the __page listing cards you've created__. Let's try the basic "{{7\*7}}" injection, see if our first thoughts were right. If they were right, the card appearing in the list section should contain "9" in the question, instead of our initial string : 

*We inject the payload {{7\*7}} in the card's question and we create the card :*

![Injection into the card's question](https://user-images.githubusercontent.com/36658045/47420420-ad692380-d77e-11e8-9b0b-3e3dedb496b8.png)


*Then, we take a look at the card we've created in the list section : *

![Result of the injection](https://user-images.githubusercontent.com/36658045/47420461-c83b9800-d77e-11e8-8232-c4ea12dd3917.png)

*Bingo ! The result is 49 and not our initial string, the website is vulnerable to SSTI !*

# Identify

From this moment, we need to know which engine template is used if we wanna carry on. In order to know that, we create a new card containing the payload "{{7\*'7'}}" : 

![Injection into the card's question](https://user-images.githubusercontent.com/36658045/47420499-ddb0c200-d77e-11e8-99aa-bcc9abf116af.png)

And this injection resulted in : 

![Result of the injection](https://user-images.githubusercontent.com/36658045/47420527-f28d5580-d77e-11e8-9131-2817c1ffac05.png)

*Alright!* This result indicates that the engine template is __Jinja2__, according to this doc : [ Server-Side Template Injection RCE For The Modern Web App - BlackHat 15](http://repository.root-me.org/Exploitation%20-%20Web/EN%20-%20Server-Side%20Template%20Injection%20RCE%20For%20The%20Modern%20Web%20App%20-%20BlackHat%2015.pdf)

# Exploit

At this point, it took me a long time to figure out what this chall was expecting from us. I tried to print the content of the admin page using a pyjail method, consisting in using the class __subprocess__ to raise a shell. Unfortunately, it leaded to a fail (we'll explain a similar method in the write-up of the __third__ challenge about Flask in this CTF : [Flaskcards and Freedom](url)).

After several write-ups, I found this ressource : [Cheatsheet Flask](https://pequalsnp-team.github.io/cheatsheet/flask-jinja2-ssti), talking about sensitive global variables in Jinja2. In these variables, some could be interesting, particularly the __config__ one. We tried to take a look at these variables and config ended up being the crucial one :

![Injection of config](https://user-images.githubusercontent.com/36658045/47420587-13ee4180-d77f-11e8-80cc-682fe063bbfd.png)

*Wow, that's a lot ! Wait... is there the flag in it ?*

![Highlighted flag](https://user-images.githubusercontent.com/36658045/47420629-2cf6f280-d77f-11e8-9339-521b7aca3ada.png)

Greeat ! We got the flag ! Organizers wanted us to think about the __secret key__ in Flask, an important variable in Flask's config ! Okay, let's move onto the next challenge : [Flaskcards Skeleton Key](https://github.com/damienjouinot/PicoCTF-2018/blob/master/Flaskcards_Skeleton_Key.md)


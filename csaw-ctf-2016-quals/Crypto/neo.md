# Neo - 200pts

We start out with just a link and a bit of text, the link points to [http://crypto.chal.csaw.io:8001](http://crypto.chal.csaw.io:8001/) - though this probably won't live forever.

Once you go to the page, you would probably notice a few things


 * Looks like it's from the early xp era
 * Seems to be centered around some movie that it's named after
 * There's an input box with base64 in it
 * Only one of the buttons works

So turns out when you click on the "Take the Red Pill" box the text is sent 

I don't really know which movie it is, but after seeing Oracle in the title, it was pretty obvious it was an Oracle attack. Gladly [jlundstrom](https://github.com/jlundstrom) - who had perl working - got [PadBuster](https://github.com/GDSSecurity/PadBuster), which can help with automating padding oracle attacks. 

Opening burpsuite we can see the exact post request that is being sent as follows(with an example input)
~~~
POST / HTTP/1.1
Host: crypto.chal.csaw.io:8001
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:43.0) Gecko/20100101 Firefox/43.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://crypto.chal.csaw.io:8001/
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 120

matrix-id=Nhhx6yUdtWmb5jlKUqhVsN5nPtdSCL54CQZMsPsxq3sIWBHx8Tb4Q3c6MFY77aVVYhlB1ulZidiOK2DrCVRXI1aSybhP4d1o1g53UmgY8yA%3D
~~~

So if you run
~~~~
padBuster.pl http://crypto.chal.csaw.io:8001/ eMEXu+LisDqtYHhlEGzJ7Q/uinC7gV5n5Ap2XCB9PeM4uY3QRKKh8Azq0fX52NrhSazjCcurZJBBJ3EGYbhF4vOt/abUERwllKdw844F86E= 16 -post matrix-id=eMEXu+LisDqtYHhlEGzJ7Q/uinC7gV5n5Ap2XCB9PeM4uY3QRKKh8Azq0fX52NrhSazjCcurZJBBJ3EGYbhF4vOt/abUERwllKdw844F86E= --error "exception" -encoding 0
~~~~

You'll eventually get the 16 bytes back! The result comes out to be:

Co dalo nam flage w odszyfrowanych blokach: flag{what_if_i_told_you_you_solved_the_challenge}

Again thanks to [jlundstrom](https://github.com/jlundstrom) for his invaluable help on this chall!
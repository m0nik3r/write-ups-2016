# Watchword (forensics)

By the time that I had gotten to this challenge [kirkelifson](https://github.com/kirkelifson) had already extracted new-pow_480.png(in this folder) from the video with binwalk. 

Turns out you could use [python-stepic](http://domnit.org/stepic/doc/) to get past this image, once you called code you could get a jpg from the image(extraced.jpg).

In the png we had found an encoded string that hinted at [stegjode](http://steghide.sourceforge.net/). I just shoved the file into steghide with the password set to 'password'. This gave us a file - base64.txt which is as follows:

~~~
W^7?+dsk&3VRB_4W^-?2X=QYIEFgDfAYpQ4AZBT9VQg%9AZBu9Wh@|fWgua4Wgup0ZeeU}c_3kTVQXa}eE

~~~

It's obviously not base64 since the ^ is not part of base64, but after looking through encoding algorithms we found that Python's base has [base85 support](https://docs.python.org/3/library/base64.html). After we base85.decode()'d the file we got the flag!

~~~
$ python3
import base64
base64.b85decode('W^7?+dsk&3VRB_4W^-?2X=QYIEFgDfAYpQ4AZBT9VQg%9AZBu9Wh@|fWgua4Wgup0ZeeU}c_3kTVQXa}eE')
flag{We are fsociety, we are finally free, we are finally awake!}
~~~
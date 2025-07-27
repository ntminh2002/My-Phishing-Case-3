<h1 align="center">🎣 My Phishing Case - 3 🎣</h1>   

## I. What's different in this campaign?

As described: 3-chains phishing – sounds interesting, right? While I was executing this phishing campaign, I wasn’t even sure where I got the idea from. Everything just happened in my head at that moment, and I thought that this might be one of the most interesting and memorable cases in my career.  

So, what makes this campaign different from the previous ones I’ve executed?   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+)Different Target: This time, the target is one of the top cybersecurity companies in Vietnam.  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+)Different Scope: This time, we didn’t have the authority to decide the campaign’s scope. The objective was set as follows: KILL ALL! The campaign's scope covers the entire workforce of the target company - an extremely large scale.   
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+)Different Execution: This time, I had to launch the phishing attack entirely from the outside, meaning I couldn't use internal emails (losing 99% of power). However, to minimize time to recon, was provided with a full list of personnel, including phone numbers, personal emails, and full names - key pieces that would complete my plan later on.   

OK, the model below will describe what I did in this campaign. I will go into detail about each chain I used in this campaign:  

<p align="center">
  <img src="https://github.com/user-attachments/assets/4c92d9b9-db16-41f3-aa0a-07a2ef37ce8e">
</p> 

## II. Setting up  

The goal of this campaign is to steal the Telegram accounts of employees.Therefore,we will impersonate the Telegram Web login interface(with a pre-purchased domain - this domain will be used for the fake Telegram login page), running on a Python Flask platform. However, by default, these phishing websites will not function without SSL.To enable SSL, generate a certificate using the Certbot tool:
```
sudo certbot certonly --standalone -d <your domain> -d www.<your domain>
```
After the process is complete, we will obtain two files: "fullchain.pem" and "privkey.pem".Take the paths of these two files and add them to the Flask configuration in the "manage.py" file as follows:  

<p align="center">
  <img src="https://github.com/user-attachments/assets/3268b98a-c344-4224-8aa8-918da9b33e22">
</p>  

<p align="center">
  <img src="https://github.com/user-attachments/assets/f4b378bd-9065-450b-a201-d10582280631">
</p>   

After completing the SSL setup, to run the fake Telegram Web on the Python Flask platform, execute the following command:  
```
python3 manage.py
```  
<p align="center">
  <img src="https://github.com/user-attachments/assets/a9ae9d12-b92a-4799-9bc9-2cebbfd9585f">
</p>

## III. Challenges and origin of the Idea  

This campaign was divided into two phases, each with different objectives: "Conduct phishing to take over employee accounts" and "Conduct phishing to take over Telegram accounts." This topic describes the process I followed in Phase 2.

Before executing this phase, I had no concrete ideas, and there were two major obstacles that made me think this campaign would never succeed:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+)Increased Awareness After Phase 1:
I had successfully executed Phase 1 by setting up a custom domain and using it to send phishing emails. The results quite good. However, after that, all employees received an alert that a phishing campaign was targeting their organization. This heightened their awareness, making them much more cautious. Additionally, from that point onward, all external emails were flagged with a warning, meaning phishing via email was now almost hopeless.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;+)The Telegram Targeting Challenge:
The goal for this phase was to take over employees' Telegram accounts, but there was a big problem - not many people use Telegram. To steal a Telegram account, you first need to target someone who actually uses Telegram. But with a massive list of employees, how could I figure out who among them was using Telegram? I had no idea!   

So, the two immediate problems I needed to solve were: using a method other than email phishing and identifying employees who use Telegram.Among the information I was provided: full names, emails, and phone numbers(email was no longer an option). Looking at what I had left, I realized: phone numbers. Ah ha! I still had a direct channel to contact my targets. This immediately led me to decide on using SMS phishing.

However, to take over a target’s Telegram account, I needed to send them a malicious link leading to the fake Telegram page I had set up earlier. Sending a direct link like that via SMS would be extremely suspicious. So, I had to come up with an intermediary communication channel between SMS and the user to drop my malicious Telegram link and Zalo was the perfect candidate - extremely popular with a massive user base in Vietnam.

At this point, the plan was almost complete. My strategy would be:   

```Send an SMS inviting the target to join a pre-created Zalo group chat -----> Drop the malicious Telegram link inside that group chat -----> Wait for users to log in -----> Capture their access tokens and take over their accounts.```   

So, the issue of "using a method other than email phishing" was solved. Now, it was time to figure out "identifying employees who use Telegram".
Honestly, even at this point, I hadn’t come up with a definitive solution to this problem. Instead of hitting a dead end, I decided to take a more comprehensive approach: preparing a scenario that would work for both Telegram users and non-Telegram users.

After drafting multiple scenarios and taking advantage of the phishing alert sent by the SOC team in Phase 1, I finalized my plan as follows:  

-----> The company requested the AI team to develop an AI BOT on the Zalo platform to quickly notify employees about incidents, such as the recent phishing campaign.  
-----> Send an SMS with a Zalo group invitation link, requesting all employees to join. Simultaneously, automatically add users to the group using their phone numbers.  
-----> Inside the Zalo group, drop a fake Telegram link, instructing employees to use Telegram as their primary communication tool.  

Everything was set. Now, it show time.

## IV. Chain 1 - SMS Phishing  

1. Set up Zalo group chat

First, we need to create a Zalo Group Chat with the title "<Company name> Internal Announcement" At the same time, we also need to create two accounts - one acting as a chat bot for the group and the other as the group administrator (who will impersonate the Head of the AI Application Product Department). The details are as follows:
<p align="center">
  <img src="https://github.com/user-attachments/assets/7734052e-a794-428b-9f84-1e2b275dc60e">
</p>  
<p align="center">Fake Chat Bot Account</p>  

<p align="center">
  <img src="https://github.com/user-attachments/assets/65614321-f592-44ee-8936-fabeee294089">
</p> 
<p align="center">Fake AI Department Head Account</p>  

These two accounts will work together to lure victims into accessing the malicious website.  

2. Send SMS

Next, we will send an SMS to persuade users to join the Zalo group we just created.This was the most time-consuming task in the entire campaign (and, of course, costly too 😃). When sending SMS messages, you should only send them to 100 people at a time. If too many messages are sent at once, you’ll get temporarily blocked. So, if your target list has 1,000 people, you should split it into 10 batches.Recommended approach:Pre-write the message template.
Divide the 1,000 targets into 10 separate lists, send a batch every 5 minutes, if you have enough preparation time, use 2-3 SIM cards and two different phones to send messages simultaneously, reducing delays between batches.(Unfortunately, I didn’t have enough time to prepare this time, so I ended up using my personal SIM to message the entire staff. My SIM was almost blocked, and I nearly had to deal with the police 😂). My SMS content was as follows:  

<p align="center">
  <img src="https://github.com/user-attachments/assets/741c8548-b2ac-467c-ba7d-7104003237e6">
</p> 

A few minutes later, a large number of users had joined my chat group. Amazing! 🎉🎉 The first step was a success! Now it's time to move on to the next step.  

## V. Chain 2 - Messaging App Scam  
Immediately after the SMS was sent, I continued adding employees and approving join requests for the chat group.This was the moment when the two pre-created accounts came into play by sending notifications, requesting employees to directly add their colleagues to the group to speed up the process:
<p align="center">
  <img src="https://github.com/user-attachments/assets/169227a9-23fd-4d48-94b3-43665e5dfbe3">
</p> 

At this point, I made a huge mistake - I delayed dropping the malicious Telegram link. Although the group already had a large number of users, I wanted to wait until I reached the full staffs (I got a bit greedy with this).With so many SMS messages being sent, getting flagged was only a matter of time - not to mention that after Phase 1, the SOC team had tightened security even further.And then, the inevitable happened - about two hours later, users started receiving warning messages about the campaign.Realizing this, I immediately dropped the malicious Telegram link using the Bot account(Basically, I was a step behind the SOC team - my bad 😛), with the following message:  
<p align="center">
  <img src="https://github.com/user-attachments/assets/68e203f6-eba5-4fba-95fb-99de51b94f03">
</p> 

## VI. Chain 3  - Telegram Webapp Phishing  
1. Collect Access Token

Once users clicked on the provided link, they were redirected to the fake Telegram Web page, which had been set up in advance with the following interface:
<p align="center">
  <img src="https://github.com/user-attachments/assets/768061fa-cded-478b-85b8-17eda01d8392">
</p>  

When users enter their phone number and the OTP sent from the real Telegram to their account, if they proceed to submit the OTP to my fake website, their Access Token and contact list information will be sent to the Attack Server.The mechanism behind this attack technique is that the attacker’s server acts as an intermediary proxy, relaying traffic between the user’s device and the real Telegram server while intercepting the user's access token during the process.  
The plan seemed to have failed on chains 2, but the good news was that some users still fell for the scam - just not as many as I had hoped. Telegram access tokens from users, which were in the following format:

<p align="center">
  <img src="https://github.com/user-attachments/assets/f2d1c921-44a5-4164-97c9-d81baa1c248e">
</p>  

2. Take over account

The only valuable field among the data we collected is "dcAuthKey", which can be used with the "Telethon" library in Python to control the Telegram account.The script "login_tele.py" (written by Lee Nguyen) will handle this task.All you need to do is:  

Replace the value of "sess.auth_key" with the "dcAuthKey" you collected:
<p align="center">
  <img src="https://github.com/user-attachments/assets/7d0b694c-ba0d-4002-a9af-2d3bd0684a7a">
</p>

Modify the action you want to perform on the victim’s account.In this case, I only stopped at reading messages, but Telethon provides many other functions as well:
<p align="center">
  <img src="https://github.com/user-attachments/assets/546a4e96-70da-4cef-b507-79643ccbf7fb">
</p>


## VII. Conclusion
During the deployment process, I faced many challenges, and things only started to improve when I reached out for help from my team (thank for Lee Nguyen, hieudm25 and TI VCS team).

This was a tough case from the target to the requirements but in the end, the campaign was still successful.

Lessons learned: Even the strongest cybersecurity organizations have one unfixable weakness - human. And the power of teamwork and imagination is truly terrifying.

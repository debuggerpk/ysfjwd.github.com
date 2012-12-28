---
layout: post
title: "Do I trust IronStratus with my data?"
date: 2012-12-28 21:18
comments: true
categories: [security, SSO, identity, tech]
---
Single sign-on for the cloud seems like a novel idea. Just the thought that you can sign in to all my websites, with each website having different strong passwords, without having to remember any of those complex combinations is liberating. But the question here is, can i really trust the services with keys to my kingdom? Growing up, my dad always told me, nothing is life comes free, there is always a cost. So when I came across [IronStratus](http://www.ironstratus.com), my natural inclination was to look for the cost for this convenience. 

Before I go on and dissect [IronStratus](http://www.ironstratus.com), lets talk a little about different steps involved for this type of data security.

* Secure Channel for data communication between me and server 
* Encryption of data before storing it.

## Secure Channel
The popular method for securing HTTP POST data between client and server is SSL. Numerous attempts have been made to break SSL, like this [research paper](http://www.cs.utexas.edu/~shmat/shmat_ccs12.pdf). If you aren't fan of reading and just want a quick summary, here it is. Since browsers are updated regularly, they pretty much fix reported vulnerabilities pretty quickly. With [Google Chrome's](http://www.google.com/chrome) fast release cycle, it has set the trend and all the players including [Mozilla Firefox](http://www.getfirefox.com), [Apple Safari](http://www.apple.com/safari) has followed the way. Microsoft also release vulnerabilities fix regularly. As a testament to SSL, all the  online credit card payment processing is only SSL secure, nothing else. The bottom line? as long as you have the latest browser along with its patches, you are all good to go on the cloud.

## Encryption
Encryption basically means, tampering the data to make it look like gibberish. There are two types of encryption, one that cannot be reversed and one from which the original data can be retrieved. Each type of encryption has its applications and standards. The most widely method adopted for reversible type of encryption is AES 128 bit or AES 256 bit. AES is a [symmetric key algorithm](http://en.wikipedia.org/wiki/Symmetric-key_algorithm), which means the same key is used for both encryption and decryption. If all your data is riding on one key, the weakest point in the security chain would be your key. The minute your key leaves your system, all your data is vulnerable.

## Investigating IronStratus
To provide a single sign on service across multiple device, IronStratus must store my credentials. For the more paranoid ones, these are a couple of questions.

* Is the client/server communication secure?
* Once my data reaches the server, are they storing it in clear text? 
* If they are doing the encryption, where is encryption happening? and what about the encryption key?

Lets look at it one by one. 

### Client/Server communication
All the sensitive communication between me and iron stratus takes place on an SSL channel. As long as our browser is up to data, the data is not vulnerable to man in the middle attacks.

### Is my data stored in clear text?
IronStratus relies on browser extension when storing sensitive data. Enabling developer tools in Google Chrome, one can easily browse the extension code, set break points and investigate the process every step of the way. Taking a quick look at the extension code, it is revealed that IronStratus is using AES encryption on my sensitive information. So IronStratus does store my data in encrypted format.

### What about the encryption key?
Now that we know IronStratus is using AES encryption, and AES is a symmetric key algorithm, they must use a key. Since they are not asking me for a key, I can only make a couple of assumptions before I investigate further.

* I have a unique key and they are storing my key inside the server or
* The are assembling the key from publicly available data based on some kind of algorithm. The logic to assemble that key resides either at the server side or inside the browser extension.

Both the cases are bad. Why? 

If my key is stored in the server, along with my rest of the data, it is like locking the cabinet and then leaving the key in the keyhole. What is the point in encryption in the first place? Besides, my data is exposed to IronStratus because the key along with data and IronStratus have all the access to my sensitive data.

If my key is calculated from my publicly available data, assembling the key is just the matter of knowing the algorithm to assemble the key. If the server gets hacked, the recipe to make the key goes away with it. My data will be exposed to IronStratus in this case also, because they have data and recipe to make the key.

These are two hypothesis i am making without looking at the code. Looking at the code, they have done something different. Rather than having a unique key against each user, each record has its own unique key. Novel idea, but the execution is pretty bad. Although the key is unique to each record, they are transporting the key in plaintext format, whenever a request is made to decrypt the data. When they transport the key along the data, the sensitive data becomes vulnerable to man in the middle attack. 

## Conclusion
My data is stored in an encrypted format but the key resides inside the database. Storing the key inside the database makes my data exposed IronStratus employees as well as the hacker who sets his mind to hacking IronStratus. Does this makes me comfortable with IronStratus? Can I trust IronStratus with the keys to my kingdom? Even If I do, what about being exposed to hack risk. After all, the key resides inside the database. Whoever has the database, he can do pretty much anything with it. I don't know about you, but I certainly do not want to be in this position, where the maximum security offered to me for my data is the guarantee that it will not exposed to man in the middle. The inside man can pretty much do anything with it.
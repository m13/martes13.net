---
layout: post
title: Your entire SDcard in my webhost ~ Proof Of Concept
post_id: 126
categories: 
- android
- application
- bug
- exploit
- fun
- hack
- market
- poc
- sdcard
- smartphone
- tools
---

**Note: I do not care if you want to share or translate the document, but please, quote me 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**


### Brief

I have developed a Application for Android which stoles the File List of the SD-Card of the victim.

I do not use any kind of special trick. That is the real problem.

### What does the exploit need?

It only needs "Internet" permission, so it is very easy to use in malicious apps (and persuade the user to get into the trap).

### How it works...

You can read by DEFAULT the files in the SD-Card, so you only have to read them and send into Internet. I have deployed a webservice in a webhost that I can manage to do this PoC.

Download it here: (pname:poc.SDCard)
![Proof of Concept - Stealing SD-Card with an Android application](http://qrcode.kaywa.com/img.php?s=6&d=market://search?q=pname:poc.SDCard)

### Why am I doing it?

Because I care about your security. I want you that you will experiment HOW EASILY it is. And then...

1) Think twice what kind of information you have in your SDCard

2) Think twice what apps you allow :) The 90% of apps use the "Internet" permission.

### Where is the real problem?

The problem is that the Google Developers have say: "Do not store sensitive information into your SDCard! only shared data!" ... but the application developers (like Dropbox, whatapps, your camera, ...) does not care a bit and they save your files there.

First, the application developers SHOULD care about you. And second, you should care about YOURSELF. If they do not care about you, do not use them.

### Can you show us the source-code?

Main function: (yeah... too easy)

```java
private void getFiles(File F) {
 for (File t : F.listFiles()) {
  DATA.add(t.getAbsolutePath()+"\n");
  if (t.canRead() && t.isDirectory()) {
   getFiles(t);
  }
 }
}
```

### Are you storing my data with this App??

Calm. I am only sending your File List (it is a slow app... I know... I did not use Threads) to my webhost. I do not want your data (I could have send it too :P). I promise you that I am going to remove all the uploaded files of the webhost... BUT.... you can delete it too. Just click the button that appears in the app.

### Argg!! It goes really slow...

Yes, I know. It gets the list of files, it is put in a string, it is sent to the webhost, and then it shows you the screen...

I promise it works... so start deleting all your data of the SDcard right now ;-).

Special Note: Do you use CyanogenMod or another custom Rom? They save by default a backup of your data as *.img files. Do you know that they contain ALL YOUR DATA OF YOUR ACCOUNTS? It is overLOL!...(this image was taken when I was coding the app...)


[![cyanogen-mod backups in *.img files](http://sergioarcos.files.wordpress.com/2011/06/files.png)](http://sergioarcos.files.wordpress.com/2011/06/files.png)

### Conclusion

Any application that you have stored could do it. I hope you will learn something about it. Use a sniffer to know what the fuck your apps are sending... (yeah... they could send the data by a SSL channel).

Greets!

and if you have any question, contact me, of course!!


**Note: If you see any spell mistakes, you can say me it. I would appreciate it 
![;)](http://s1.wp.com/wp-includes/images/smilies/icon_wink.gif?m=1221158483g) . Thanks!**
---
layout: post
title: Vaadin export button - My eyes are bleeding
---

Have you ever encountered piece of a code that was painful to watch? So bad that you wanted to get rid of it as fast as you can? You're not alone. Here I will share with you one of such moments I had while working with Vaadin.

### What have I just seen?

Lately I had an opportunity to fix some bugs in our application using Vaadin for front-end development. Despite many complainants about this framework I have decided to give a chance. I thought that it can be that bad. But believe, was I wrong. What is saw in that memorable day was thrilling. I first I thought that someone on our team just didn't know what he was doing.

But what exactly was so frightening? Just look below.

![smaller]({{ site.baseurl }}/images/vaadin_export/code_1.png)

Can you see it? This intricate logic should just generate file on button click and let user download it. Nothing more. But task which should be simple, is real struggle in Vaadin.

So what happens in a code? At first we are creating download button and registering _onClick_ event. Next we are setting id of a component which will be generated in html. But than something strange happens. We are removing _onClick_ event that was just triggered. After that we tell _FileDownloader_ which class should be used to generate file when button will be clicked.

Isn't that strange already? Why didn't we do that earlier?  As we registered this event only now, we need to somehow trigger click event one more time. So we to that by executing Javascript (from Java level!) which manually selects our button by previously assigned id and simulates user clicking it.

How could somebody let it go through code review?

### Questions

After I have seen all of that I have started to ask myself questions.

#### 1. Why is this so complex?

I just couldn't believe that there is no simpler way to achieve what we are trying to achieve. I started to look for some information on that matter and quickly found [simple example](https://subscription.packtpub.com/book/application_development/9781782162261/6/ch06lvl1sec105/file-download). Seems pretty easy, right? Unfortunately it's not. In my opinion Vaadin just didn't anticipate the possibility of generating file on demand. Of course as always there is [a way](https://vaadin.com/forum#!/thread/2864064), but does not  look appealing. In order to make it work you have to extend Vaadin resource class and provide your own way to get file. It's wouldn't be that bad if we would not have to provide some resource while creating file (when we have no idea what file will be downloaded). That is why in snippet we see line creating empty resource and registering it.

#### 2. Why can't we just return file straight from our onClick() method? 

As I have already read [earlier](https://subscription.packtpub.com/book/application_development/9781782162261/6/ch06lvl1sec105/file-download), Vaadin uses mechanism called Extensions, to provide functionalities as downloading a file. So to return user a file, we have to register appropriate extension and provide it our resource returning a file. As we know which file we are going to generate only when button is finally is clicked, we have to create our resource in _onClick_ method. But it still will not trigger downloading. We have to manually click button one more time.

Some mysteries are solved but it's not finish.

#### 3. Why can't we just call click() method on Java button object?

Without thinking long I have changed Javascript call to Java one but button somehow stopped working after that simple change. Interesting indeed. What could be wrong here? After a while I found [answer](https://vaadin.com/forum/#!/thread/3991491) on official forum. It turns out that this behaviour is intended by design. Thanks to this we have finally found out why do we need to call Javascript method.

### Enlightenment

I have to admit that I didn't expect this code to make sense but after collecting all this informations I begun to think that in all it's curiosity that it's actually the right solution. I know that it may seem weird that I say that after all this complaining, but I just could not came up with better solution which wouldn't involve setting new extension on every field which affects generation.

### Bug hunting

Unfortunately it's not the end. After all I came there to fix a bug. What wrong here you may ask. Actually this mechanism works like a charm when I try to download a first file, but when you try to trigger the same button second time with different configuration, it is downloading file generated earlier. To find answers I quickly set up a breakpoint in my _onClick_ event and repeated the test scenario. What I have found was really shocking to me. It turns out that Java _onClick_ event was triggered just once, when I tried to download the first file. It seems that registered extension don't bother itself to call Java method while it has already knew which file to download. This way second time we used our button, we didn't even had a chance to change file we were downloading.

OK, I though. We do not have many variables needed to generate file (At least this time luck is on my side :D), which make it possible to try to register extension with our resource on appropriate event fired while changing those properties. But to my disappointment I didn't work. I was getting errors telling me that I should not modify extensions already registered on my button. OK, than. Let's try not to change the whole extension, but just change resource it's using. Few modifications in a code and we can try thing again. Good sign at first, namely no exception thrown when changing values describing file. Generating first time, got right file. Generating second time... got first file again. At this moment I started to lose hope that I will be able to solve our problem. But when I was almost ready to give up I have came up with really stupid idea. What if I try to replace the whole button on variable value change? Could something so stupid work?

It can! :D I'm definitively not proud of myself, but I was so tired after all what I have done during this task that I just stopped asking questions.

I ended up adding following code into all event listeners on export variables.

![smaller]({{ site.baseurl }}/images/vaadin_export/code_2.png)

### Conclusion

Luckily for my, all those fields were available to me and existed in the same scope as button. But what if those values would be distributed into more components? What if there were more of them than 2 or 3?

If you somehow managed to find better solution share it with me. I still have a little light of hope in my heart, that there exists a better, cleaner solution.


### Exclaimer

I had this kind of problems with Vaadin 7. I newer versions hopefully they are solved in some other, cleaner and non-hacky way.
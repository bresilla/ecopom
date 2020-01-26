+++ 
type = "post"
status = "published"
date = "2020-01-23"

slug = "web-ipfs" 
title = "Hosting a website in the Inter Planetary File System"
foot = "Between 'I want to be alone' and 'I want to be let alone' there is huge difference - Greta Garbo"
description = ""
categories = ["LINUX"]
series = ["IPFS"]
part="1"
tags = ["ipfs","website","gpg", "blockchain", "p2p", "crypto" ]
credits = [
    "https://developers.cloudflare.com/distributed-web/ipfs-gateway/",
    "https://medium.com/pinata/how-to-easily-host-a-website-on-ipfs-9d842b5d6a01",
    "https://hackernoon.com/getting-to-know-ipfs-21009f018f2e",
    "https://teetotality.blog/posts/why-ipfs/",
    "https://tech.co/news/decentralized-internet-guide-2018-02",
    "https://www.thewindowsclub.com/centralized-vs-decentralized-internet",
    "https://101blockchains.com/centralized-vs-decentralized-internet-networks/",
    "https://medium.com/noia/the-internet-was-not-developed-it-just-happened-part-1-a123fe4628",
]

[style]
    accent = "#6acad1"
    theme = "dark"
+++


# THE WHY

Here is the thing, I have no secrets of my own! Zero, zilch, zip, nada, nothing. However, this does not mean I am open to anyone to browse what I do and how I do. I like my life to be private. If somebody asks me anything (even a personal query), and if I find it reasonable (which most of the cases I do), I give the answer. I am very careful about what share and how I share. Sometimes, one's opinion, even though meant to be harmless can cause trouble. This is because, right and wrong are just a matter of perspective. There is no line between good and evil, like there isn't one between black and white. One's views are influenced by many many factors, from the day one is born, to the way is risen, place where is risen, beliefs it has and so on 'n on. Example, if one has Asiatic origins, in one's views dragons are magical, all powerful creatures, that represent health, good life, excellence and royalty. But if one is of a Western origins, one's views dragons as a symbol of evil. They fly and breathe fire, it represents the dark side of humanity, greed, lust, and violence. This is an excellence example of how people can have different views. But what happens when one's views and beliefs do not align with somebody else's, and because he/she might have more power, he/she may <a href="https://en.wikipedia.org/wiki/2017_block_of_Wikipedia_in_Turkey"> not want your views to be heard?</a>.

{{< image url="https://i.imgur.com/B1pomSV.png" border="1" width="20" >}} Today's online privacy in a nutshell {{< /image >}}

Sometimes, the same opinion can be intentionally interpreted wrongly by third party. This might be because one wants to manipulate in someone's name, or want to shame on the other's dignity, or other that I can't think of. Thats why signatures were made. Signatures have been an important part of human creativity and identity for literally thousands of years, and in modern day we use them to do everything from sign receipts to authenticate documents, sign autographs and write birthday cards. The legal definition of the signature is: A mark or sign made by an individual on an instrument or document to signify knowledge, approval, acceptance, or obligation. This is crucial in todays world where things like <a href="https://faceswap.dev/">deepfakes</a> exist. 


# THE WHAT

If you are like me, then you'd ask the question, what can one do about it??

Well, in this short post I am going to share my opinions, knowledge and experience on how I set up a copy of my personal website above the IPFS. So, my main domain is <a href="https://bresilla.com">bresilla.com</a> and it is directed to a <a href="https://gohugo.io">hugo</a> static site hosted in <a href="https://github.com/bresilla/website">github</a> sites. However, this is a centralized site. Whenever Github wants can turn it off, or whenever a powerful player wants can shut down Github entirely (most likely this wont ever happen), or maybe some powerful government entities think that Github is security risk and block it, then my website cant be accessed either. For this reason i created <a href="https://bresilla.xyz">bresilla.xyz</a> (in differnet dommain, uses .xyz compared to .com). It is the exact replica but instead of being centralized (on a server), it is decentralized, it used Inter Planetary File Syste (IPFS).

{{< image url="https://i.imgur.com/Zd78JvU.jpg" border="1" width="30" >}} IPFS Logo {{< /image >}}

## INTERNET ITSELF
The early days of Internet were based around different groups of people and organizations. They were isolated standalone networks that might or might not connect with other networks. Thus, the control over the different parts of Internet was limited to different people across groups. The Internet grew up at a fast pace and is now completely centralized. The internet is not fully and physically “centralized,” since no single corporation actually owns the entire internet. But relatively few large, physical servers (associated or operated by relatively few large corporations) are responsible for hosting essential elements of what we consider the internet. These web hosting and cloud computing servers are responsible for keeping our email, social media, and webpages available to all — and that means that the companies that own those servers have an outsized impact on how the internet runs. Centralized internet connection has since given rise to a string of issues that appear to be getting out of hand by the day. In addition to potential loss of connection, given the centralization of such services, privacy and data security concerns continue to cause havoc. Here is the main idea to take with:

{{< block type="fill" >}} The Internet was not planned for what it is and not well developed. It was a time necessity and it just happened. {{< /block >}}

There is this <a href="https://medium.com/noia/the-internet-was-not-developed-it-just-happened-part-1-a123fe4628">great article</a> hosted by <a href="https://noia.network/">NOIA Network</a>, that I'll be refering quite often, goes into technical details on why internet is fundamentally broken, and how it came to be.

{{< image url="https://noia.network/assets/solution.svg" border="0" width="50" >}} A controlled-centralized network <a href="https://noia.network">Credit: NOIA Network</a> {{< /image >}}

## DECENTRALIZATION

Centralized internet relies on servers. Decentralized internet connection, on the other hand, relies on a peer-to-peer network built on a community of users whereby no one single entity is ever in control. In this case, various internet devices act as host of the internet, rather than a group of powered servers. What this means is that any website is spread’ across hundreds of nodes erasing the possibility of a single server acting as the single custodian of a particular subset of data. It takes accessibility of data to another level as there are hundreds if not thousands of nodes through which one can gain access to, to access data. Decentralized internet is the sort of idea that works great once everyone is on board with using it. But until then, the sheer fact that no one is using something tends to function as a Catch-22: No one wants to use it because no one is already using it.

## INTER-PLANETARY

# NetBSD Blog post
This summer I worked on NetBSD's ATF for coverage of root file system and root device selection. 
This area of subsystem is not very well documented and process flow has to be determined by looking through code. this is not very feasible for quick debugging.
but it does its work and has been working for 30+ years,
but Christoph Badura experienced that RAIDframe gives problem with higher capacity drives when finding for root but in any case fallback option is asking user for device and partition 
but manually doing it with hundreds of server is tiresome and hence the need for testing coverage which will eventually turn into rewriting of whole process code. 

I would also like to tell about my journey of GSoC from my first interaction with Chris to this project ,let me start from project finding 

## Organisation and project Search
2024 gsoc is not my first time application my first time was in 2022 in my first year of College, i was fascinated with newer Windows versions
since my core duo intel i3 days as every new Windows versions used to
bring new changes and features, from windows XP to Vista to 8.1 and then to 10 (cosmetic only) but was very heavy on my PC idle memmory uasge was upwards of 60% leaving very little room for exotic applications and games 
But a little later when we had access to (usable) internet connection, I got to know that there was a world beyond Windows, an entire Universe of UNIX like OS, which didn't require a top level PC hardware my 2gb intel i3 would be enough for it. 
the above whole experience led me to decide that I wanted my project to be in operating system space. I ventured all OS projects on GSOC website first was Linux gentoo , debian but that didn't turnout well therefore I decide to go with BSDs, community seems lot more friendlier, responsive and active to help beginners (maybe because there is not

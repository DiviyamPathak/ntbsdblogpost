# NetBSD Blog post
This summer I worked on NetBSD's ATF for coverage of root file system and root device selection. This area of subsystem is not very well documented and process flow has to be determined by looking through code. This is not very feasible.
But it does its work and has been working for 30+ years. However, Christoph Badura experienced that RAIDframe gives problem with higher capacity drives when finding for root and fallbacks to asking user for device and partition manually. Doing that is tiresome and need manual intervention. So, it is important to have testing coverage that also help to be more confident when modifying this code.

I would also like to tell about my early interactions with project, let me start from project finding.

## Why NetBSD ?
Google Summer of Code 2024 is not my first time application to GSoC. My first time was in 2022, in my first year of college. I was fascinated with newer Windows versions
since my Intel Core Duo i3 days as every new Windows versions used to bring new changes and features, from Windows XP to Vista to 8.1 and then to 10 (cosmetic only) but was very heavy on 
my PC idle memory usage that was upwards of 60% leaving very little room for exotic applications and games. A little later when we had access to (usable) Internet connection I got to know that there was a world beyond Windows, an entire Universe of Unix-like OSes which didn't require bleeding-edge hardware. My Intel i3 would be enough for it. This experience led me to decide that I wanted my project to be in operating system space. I ventured all OS projects in Google Summer of Code website. Gentoo, Debian but that didn't turn out well. I then decided to explore BSDs. Community seemed lot more friendlier, responsive and active to help beginners. I started mailing all the mentors about my interests (I realised I should have done a lot more work before mailing them, it shows more dedication towards project)

Christoph replied to my mail positively. He gave me a head start explaining project details. I had problems with QEMU Virtual Machine to run NetBSD/amd64 and he helped me graciously, investing his valuable time to help me boot with VM on Debian host. After exchanging some more mails we developed timeline and objective, I submitted my proposal and fortunately it was accepted.

## Project Details 
Root device and file-system selection is made during later stages of boot process by kernel. Config file defines candidates for device and kernel selects it given, some conditions meet
criterion, you get head over to my docs for more details (here)[]. This functionality is handled primarily by `setroot` function in file `kern_subr.c`. It also has specialised
functions for different cases. Our task was to add tests in ATF for this function and some other functions that assists setroot. This part of kernel works, and it works for over 30 years
but code is rather complex to read and there is no documentation. The only way to understand it is to read code. We will try to rewrite this code. 
At any given stage when any condition fails the fallback option is to ask user manually for device. Thus this part of kernel rarely needs attention but Chris was having problems with RAIDframe setup with higher capacity drives. This time too fallback option worked but manually intervention every time is a problem.

There are some global functions used in conditions inside `setroot`, `rootspec`, `bootspec`, etc. These functions are either set through config file or through other machine dependent kernel 
function `findroot` etc. We need to modify them in our test cases and also need to stub kernel functions used by `setroot` and other function. These test cases will eventually be ported to 
use `vnd`. We check for global variables and if a local variable is to be checked we intialize it. 

Since `root_device` can also be a network device, this has to be defined in config file. We also test `tftproot_dhcpboot` function. This function can also set `root_device`.

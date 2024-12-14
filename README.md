# NetBSD Blog post
This summer I worked on NetBSD's ATF to cover root file system and root device selection. This area of subsystem is not very well documented and process flow has to be determined by 
looking through code. This is not very feasible. But it does its work and has been working for 30+ years. However, Christoph Badura experienced that RAIDframe gives problems with higher 
capacity drives when finding for root and fallbacks to asking the user for the device and partition manually. Doing that is tiresome and needs manual intervention. This project shall extend coverage to root selection, which will help in the future development of this kernel area.

I would also like to tell you about my early interactions with the project, let me start with project findings.

## Why NetBSD?
Google Summer of Code 2024 is not my first time applying to GSoC. My first time was in 2022, in my first year of college. I have been fascinated with newer Windows versions
since my Intel Core Duo i3 days as every new Windows version used to bring new changes and features, from Windows XP to Vista to 8.1 and then to 10 (cosmetic only) but was very heavy on 
my PC idle memory usage that was upwards of 60% leaving very little room for exotic applications and games. Unix-like OSes which didn't require bleeding-edge hardware. My Intel i3 would 
be enough for it. This experience led me to decide that I wanted my project to be in the operating system space. I ventured into all OS projects on the Google Summer of Code website. 
Gentoo, Debian but that didn't turn out well. I then decided to explore BSDs. The community seemed friendlier, responsive, and active in helping beginners. I started mailing all 
the mentors about my interests (I realized I should have done a lot more work before mailing them, it shows more dedication towards the project)

Christoph replied to my mail positively. He gave me a head start explaining project details. I had problems with the QEMU Virtual Machine to run NetBSD/amd64 and he helped me 
graciously, investing his valuable time to help me boot with VM on the Debian host. After exchanging some more emails we developed a timeline and objective, I submitted my proposal and 
fortunately, it was accepted.

## Project Details 
Root device and file-system selection is made during later stages of the boot process by kernel. Config file defines candidates for the device and the kernel selects it given, some 
conditions met
the criterion, head over to my docs for more details [here](https://github.com/DiviyamPathak/Gsoc-2024-NetBSD). This functionality is handled primarily by the `setroot` function 
in file `kern_subr.c`. It also has specialized
functions for different cases. Our task was to add tests in ATF for this function and some other functions that assist the setroot. This part of the kernel works, and it has worked for over 
30 years but the code is rather complex to read and there is no documentation. The only way to understand it is to read the code. We will try to rewrite this code. 
At any given stage when any condition fails the fallback option is to ask the user manually for the device. Thus this part of the kernel rarely needs attention but Chris was having problems with 
RAIDframe setup with higher capacity drives. This time too, the fallback option worked.

There are some global functions used in conditions inside `setroot`, `rootspec`, `bootspec`, etc. These functions are either set through the config file or through other machine-dependent kernel functions `findroot` etc. We need to modify them in our test cases and also need to stub kernel functions used by `setroot` and other functions. These test cases will 
eventually be ported to use `Vnd`. We check for global variables and if a local variable is to be checked we initialize it. 
Since `root_device` can also be a network device, this has to be defined in the config file. We also test the `tftproot_dhcpboot` function. This function can also set `root_device`.

Devices are represented by the `device_t` struct and it is populated by the kernel, we mock this behavior through the `create_device` function and use this in the test programs.
User input is handled by stubbing the `cngetsn` function. Global variables and arguments are set to test values in the body of the test case and the function being tested is called.
The Test file is divided into 3 parts 1) setroot_root 2) setroot_ask 3) tftproot_dhcp. in kern_subr.c file `setroot()` calls these functions but here we test them independently

## Conclusion 

Worked on enhancing NetBSD's ATF to test the root device and file system selection process, testing the setroot function and its dependencies. These tests shall improve coverage 
of ATF to setroot function and in general, to root device selection, addressing challenges like RAIDframe issues with high-capacity drives and reducing reliance on manual intervention. 
In the future ,contributors can rely on these tests for kern_subr.c's functionality  

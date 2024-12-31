# NetBSD Blog post
This summer I worked on NetBSD's kernel test framework to cover root device
discovery and root file system selection.
This area of the kernel is not very well documented and program flow has to
be determined by reading the code.
This is not very feasible.
But the code does work and has been working for 30+ years.
XXXbad ambiguous what "it" refered to here.  Maybe delete the last 2 sentences.

I would also like to tell you about my early interactions with the project,
let me start with project findings.

## Why NetBSD?
Google Summer of Code 2024 is not my first time applying to GSoC.
My first time was in 2022, in my first year of college.
I have been fascinated with newer Windows versions since my Intel Core Duo
i3 days as every new Windows version used to bring new changes and features,
from Windows XP to Vista to 8.1 and then to 10 (cosmetic only) but was very
heavy on my PC's memory usage that was upwards of 60% leaving very little
room for applications and games.
XXXbad i assume "exotic applications" includes web browsers.  Chrome and Firefox easily consum 4 GB of memory in my experience.

Unix-like OSes which didn't require bleeding-edge hardware.
XXXbad this sentence doesn't connect to the others?  where you looking for OSes lower demands on hardware?  did you notice that Unix-like OSes are less demanding off hardware?
My Intel i3 would be enough for it.
This experience made me decide I wanted my project to be in the operating system space.
I ventured into all OS projects on the Google Summer of Code website.
Gentoo, Debian but that didn't turn out well.
I then decided to explore BSDs.
The community seemed friendlier, more responsive, and active in helping
beginners.
I started mailing all the mentors about my interests (I realized I should
have done a lot more work before mailing them, it shows more dedication
towards the project.)
Christoph explained the project in great detail to me,  helped me with getting started and guided me through the hard parts of the project.

## Project Details
Root device and file-system selection is made during later stages of the
boot process by the kernel.
The kernel config file defines candidates for the root device and the kernel selects one after validating those options, if no options are defined, the auto config subroutines handle configuration.
Head over to my docs for more details [here](https://github.com/DiviyamPathak/Gsoc-2024-NetBSD).
This functionality is handled primarily by the function `setroot` in file
`kern_subr.c`.
It also calls specialized functions for a number of cases.
Our task was to add ATF tests for this function and some other functions
that assist `setroot`.
This part of the kernel works, and it has worked for over 30 years but the
code is rather complex to read and there is no documentation.
The only way to understand it is to read the code.

At any given stage when any condition fails the fallback option is to ask
the user manually for the device.
Thus this part of the kernel rarely needs attention.
There are some global functions used in conditions inside `setroot`:
`rootspec`, `bootspec`, etc.
These variables are either set through the config file or through other
machine-dependent kernel functions like `findroot` etc.
We need to manually set them in our test cases and also need to stub kernel
functions used by `setroot` and other functions.
We wanted to make these test cases use 'vnd' devices, but we ran into bugs, and Christoph was trying to fix them.
herefore we had to resort to stubbing.
We validate the global variables.
We also test the `tftproot_dhcpboot` function that loads the contents of a memory disk device from a TFTP server and uses that as root device.
Devices are represented by the `device_t` struct which is populated by the
kernel, we mock this behavior through the `create_device` function and use
this in the test programs.
User input is handled by stubbing the `cngetsn` function.
Global variables and arguments are set to test values in the body of the
test cases and the function being tested is called.
The test file is divided into 3 parts: 1) setroot_root, 2) setroot_ask, and 3)
tftproot_dhcp.
In the file kern_subr.c `setroot()` calls these functions but
here we test them independently

## Conclusion
I have worked on enhancing NetBSD's ATF test to  test the root device and file system selection process, testing the `setroot` function and its dependencies.
These tests shall improve coverage of the ATF tests to the `setroot` function and in general, to root device selection.
In the future, contributors can rely on these tests for `kern_subr.c`'s functionality.

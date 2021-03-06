# x11docker
 - Run X applications and desktop environments in docker on a distinct X server.
 - Useful to avoid security issues concerning X forwarding.
 - Can either open a new X display, or can use Xpra and Xephyr to show dockered GUI applications on your main X display.
 - Doesn't need VNC or SSH. docker applications can directly access the new X server via tcp.
 - Doesn't have dependencies inside the image - can run any GUI applications in docker. (Doesn't even need an X server to be installed in the image.)
 - Authentication via MIT-MAGIC-COOKIES. Separate Xauthority file, it is _not_  ~/.Xauthority
 
#pulseaudio sound support
 x11docker supports pulseaudio sound over tcp. For this to use, pulseaudio needs to be installed on host and in docker image.

#GUI for x11docker
There is a comfortable GUI for x11docker. To run x11docker-gui, you need to install 'kaptain'. 
(debian link to kaptain: https://packages.debian.org/search?keywords=kaptain).

![x11docker-gui screenshot](/../screenshots/x11docker-gui.jpeg?raw=true "Optional Title")

#Usage in terminal
To run a docker image with new X server:
 -  x11docker [OPTIONS] IMAGE [COMMAND]
 -  x11docker [OPTIONS] -- [DOCKER_RUN_OPTIONS] IMAGE [COMMAND [ARG1 ARG2 ...]]
  
To run a host application on a new X server:
 -  x11docker [OPTIONS] --exe COMMAND
 -  x11docker [OPTIONS] --exe -- COMMAND [ARG1 ARG2 ...]

To run only a new X server with window manager:
 -  x11docker [OPTIONS]

Have a look at 'x11docker --help' to see all options.

#Dependencies
Depending on choosed options, x11docker needs some packages to be installed.
It will check for them on startup and show terminal messages if some are missing.
List of possible needed packages:

xpra xephyr xvfb xclip kaptain wmctrl pulseaudio docker.io xorg

#Explanations
x11docker creates a new X server on a new X socket on a new display. Instead of using
display :0 (standard), docker images will run on display :1 or display :2 ...

To switch to between displays, press [STRG][ALT][F7] ... [STRG][ALT][F12]. Essentially it is the
same as switching between virtual consoles (tty1 to tty6) with [STRG][ALT][F1]...[F6].

A more comfortable way is to use Xpra and Xephyr. With Xpra and Xephyr dockered GUI applications will show up on your main display, and you don't need to switch between different tty's.

Example: This will run an xfce desktop environment in docker, showing it in a Xephyr window 
on your main display:
 - x11docker --xephyr --desktop x11docker/xfce

#Security
 - Using a separate X server aka a new display for docker GUI applications avoids issues 
 concerning security leaks inside a running X server. There are some solutions in the web to run dockered GUI applications with X forwarding on display :0, but all of them share the problem of breaking isolation of docker containers and allowing them access to X resources like keylogging with 'xinput test'.
 - With x11docker, GUI applications in docker can be isolated from main display :0 and 
 use a fast connection over local tcp to the new X server. This is much faster 
 than VNC or SSH. 
 - x11docker uses tcp instead of mounting a host X socket in docker. (Direct access from 
 docker to a host X socket causes dangerous issues including bad RAM access, framebuffer 
 errors and application crashes, especially seen with composite applications. x11docker avoids this using tcp).
 - Authenthication is done with MIT-MAGIC-COOKIE, stored separate from ~/.Xauthority. 
 The new X server doesn't know the cookies from the standard X server on display :0.
 - With option --no-xhost you can disable any permissions that xhost may already give to others.
 - X over tcp is seen as a possible security leak. As long as no one has a matching MIT-MAGIC-COOKIE, 
 it should not be a problem. But maybe the new X server is insecure in this point. I will have to look for this further. Any hints are welcome.



---
layout: post
title: "The Screen command in Linux"
date: 2016-05-20 15:44:40 +0530
comments: true
categories:
- Linux
- DevOps
---
I have recently shifted my focus to DevOps and I spent a lot of time SSHing to remote servers and doing things in the 'terminal' way. One thing that I missed during my SSH sessions was the lack of a multi-window system. You have access to the server via a single terminal and you have to execute all the commands in that terminal. That is what I thought initially. So running two or more tasks at the same time and keep them running even if I terminate the SSH session was a problem. With a little research, I was able to find a few solutions such as running the processes using nohup, using &  and then playing with fg and bg, demonizing the process etc. However, all these lacked the simplicity and ease of using a multi-window system. Googling further, I was able to get the solution for all the concerns that I had with the previously mentioned methods - the Screen command.

## What is Screen?
As per the GNU documentation for Screen, *"Screen is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells"*. In layman's terms, Screen allows the usage of multiple terminals from within a terminal. That is, you can spawn multiple terminals and switch between those terminals using a single SSH terminal. More or less this is like having multiple tabs in a single terminal window and running different tasks in those tabs. Each terminal may be called a screen and these screens keep running until they are killed explicitly. That is, even if you terminate the ssh session to the server, the screens will not get terminated.

## How to use the screen command?
* Starting a new screen and assigning a name to it
`screen -S s1`
This command will create a new screen named s1 and open that screen in the existing terminal. From here, you may execute the desired task. To return to the terminal from where you have started the new screen, press `Ctrl+a d`. This command detaches the screen and returns to the main terminal. Please note that for commands that are related to the screen, you have to press `Ctrl+a` before entering the command. Now, let's start one more screen named s2 using `screen -s s2`  command. After that, Detach from s2 to the main terminal using `Ctrl+a d`.
* Listing screens
 Now, you have two screens running in the background. To list the screens that are running in background, `screen -ls` command is used.

 ![Screen List ](https://lh3.googleusercontent.com/m8AEtH74TiNQxNvTPcj_al20Izli6h-dcTZC0mF3C1y1VXUy4u357iPCA08vVrbo6oBnv-3_RwwcmyEaaN8YHIDm_VIGvx5jN0O9o3CQaMnN2ev1y21ofl7TD1KN0RSuikMMNqv3NSaOoUdxOucDpi-d9aso9fQGL853C4zPuevaAsQ-ejyyNtHBFCkOfS6i10FZojD1ioe5wvqcmzkiAEkvAPkD3HC9vPSJiboHeI-MJIcMOccvx9hsGfpvnzsB0XqimM4JyhpJhHMHDFB8RjFxvXDqHoBAoNyYVnbalOfkV6btkNnqYEDuXBh4yFiz1bCnV3k8vKib4V4BB9CcQ3wCvUOtE1oe9qDfGJfRQpVl9xyJ7FnBNQxCikD5ExhZtRl0Nd35WcFZgr1Ujf9b8bCvjh8J2wA8pmZwGYsgxnxkqPcpLpUmFUqHew28B7ntP05jWoQd1wU9xqvmdwV99cWV2YmivFUgkBDGL-Q82B7PAijFNhNmHrSCyMSoo-idBPbApT-K9vXvZ0UgrnGsJ2dosPIRRwXosapH9YgWkrWPdJcW1vUfF-4vtbpQoPSByQEGLq-FpBl-Wuqf-9VeA0Z3YC7tL0Pi=w689-h114-no "Screen")

* Switching between screens
 To switch to any of these screens, `screen -r SCREENNAME` command is used. If you want to switch to s1, then use `screen -r  s1`. To switch to s2 from s1, first detach from s1 using `Ctrl+a d`, then resume s2 using `screen -r s2`

* Terminating a screen
To terminate/kill a screen, resume that screen and then enter `Ctrl+a k`. You will get a confirmation before killing the screen.
![Kill Screen](https://lh3.googleusercontent.com/aYusDu27M72tacZkTQn0r4RUOzD34g6k6MX5E5qa-Sef1ENsuWvCmLeIGmMIxJKnC-MeEXULvH18awOYCZC0Alc614WkGpArvqg7uU4IpNYkxRQq7uf6Lpq8kEqjQnW8_GWsgzo7b4oyHRNUQRTDqdZ2APDU8hwXYjA-a1vgmiZDEAlVM32aKLfk9LuJC-gJJOaD6aNv1IZtFU4WexoAJUznzaHBXdpCPxvmGl3odJJDZhFDczKPpnvL8Ic4zLExKGMiGbNtF757dn3rR67coQX1RUbUVM65Az1VXDBRsGJkMlAvY3ZCYNAq_YHzzTsY08UeHyx8tMKByr0ho9Hy6KS_ib4Xe3lH9mbM06e3o9ZgNypbgqNyjKroxgM56EfIHYinqXYPDo8yPCXJe8OQcmKdulh_xgWMX5qDk3e5DcL85tLlzttpmrH8xS9UfeIuI8xil-vse8lwlDf_SS12y9lf4LluHhtRtlZkeZEd6pFDgsKzPpJrqx6rBAOs-wpX1UjBca83l6SFg2vbaES6U05vGiGKNwgOFBl59DzHZw9vfEUb5SEEWhzWX4y8PoXndB1VZ1U9vKGzfiuIzBFSET9mxTDLnVJP=w383-h177-no "Terminate-confirm")

These are the basic operations using the screen command. A detailed list of screen commands is available at the official manual [Link](https://www.gnu.org/software/screen/manual/screen.html)

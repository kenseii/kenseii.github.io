---
published: false
---
# Why and how to replace running celery tasks in your application

## Background

A long time ago 🥚 i was working on a project that involved interacting with some hardware, the hardware's event/task was lasting a few seconds (20 or 30) for every command we sent to it.

Nothing unusual so far; however the problem was when we received a request to replace the command when a new similar command is issued. Data wise we were dealing with a simple data structure which was quite similar to which PIN to set ON for x amount of seconds (i think).

As we needed to cancel the task fired previously, we needed a way of finding it, stop it and queue the new one, i dont remember exactly how i handled the PIN ON duration in case it is already running but i guess it might had been something like `remaining seconds + the normal duration per new command`.

Stack wise, we were using flask, python and mysql (yes not redis to store things why? we had a mysql container already running on a resource constrained device so spinning up a new service might have been like climbing mount fuji carrying a rikishi on your back)




## Now that you know why here is how

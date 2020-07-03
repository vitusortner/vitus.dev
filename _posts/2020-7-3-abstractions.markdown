---
layout: post
title:  "Abstractions"
subtitle:  "They are as scary as they sound"
date:   2020-7-3 13:37:00
categories: abstraction
---
Multiple view models are also interesting but they came with problems as well. For instance where they get their dependencies from.
It would be required to pass dependencies through multiple layers until they get close to the list cell.

We currently have a tight coupling between view controllers and view models.
View models are supplied to view controllers by some sort of service locator.
This service locator can't be used without view controllers right away and we would've been required to invest some time to either make view models instantiable indipendently of view controllers while still using the service locator or ditching the service locator for this use case.
Thereby, I saw some problems.

It's a bit scary to me how much "once useful" abstractions can lock you into not so comfortable situations.
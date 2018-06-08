# A Truly Custom Mechanical Keyboard
![Custom 85 key keyboard](/images/final.jpg)
The first thing that got me turned onto the idea of making my own custom keyboard was the kickstarter for a keyboard called the [Whitefox](https://www.kickstarter.com/projects/lekashman/whitefox-mechanical-keyboard). I wasn't psyched about the prospect of waiting 6-9 months for the keyboard though and ended up embarking on a mission to design and build my own.

## A first prototype
I had an original Arduino laying around and armed with a sample pack of Cherry MX switches I started prototyping. Unfortunately the Arduino doesn't support the USB HMI interface through hardware and while it was technically possible to work around through software I moved on from this pretty quickly. It whet my appetite though and I continued with a more concrete design phase.

## The requirements
1. Tenkeyless - I don't use the number pad a ton at work and opted to not make a standard 104 key keyboard
2. F17 - At work I semi-frequently have to use the F17 key which isn't on most keyboards and previously I had bound to Shift+F7
3. Low-angle - I wanted a sub 8 degree tilt on the case itself

### Switch plate
The switch plate is a ~1.5 mm thick piece of aluminum and forms the backbone of the keyboard in which the switches are mounted. Since this would be a low-profile case (where the switch plate is exposed) I went with a brushed aluminum finish. The plate was water jet cut by eMachineShop.

### Switches
I ended up going with a clicky switch, the Cherry MX Blue. Since the original build I've swapped them out for Kailh Speed Coppers which I prefer slightly but all the pictures feature the keyboard with the Cherry Blues.

### Controller
The controller that powers the keyboard is the Teensy 2.0 from PJRC. There are plenty of Pro-Micro clones out there but the Teensy was attractive to me because of how many GPIO pins it has, allowing for a large key matrix and easier routing of wires/traces for smaller key matrices.
![Teensy](/images/controller.jpg)

### Case
I ended up going with a 3d printed case from a local place called Midwest Prototyping. This ended up being by far the most expensive part of the project and the most difficult from a design perspective. My CAD skills have never been great and despite it's relatively simple geometry this was the most complex object I've ever made.
![3d printed case](/images/case.jpg)

## Assembly and lessons learned
Measure twice and cut once is always a good lesson to learn right? I ended up soldering the diodes in series rather than in parallel and had to go back and resolder them again. If I hadn't there would have been "ghosting" where pressing many adjacent keys in the matrix would trigger a keypress that didn't actually occur.
![Final product}(/images/final2.jpg)
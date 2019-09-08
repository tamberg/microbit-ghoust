# Ghou.st for the micro:bit

Here is a video of the <a href="https://github.com/Ghoust-game">original</a> <a href="http://ghou.st/">Ghou.st</a> game.

<a href="http://www.youtube.com/watch?feature=player_embedded&v=35liQTgUH04
" target="_blank"><img src="http://img.youtube.com/vi/35liQTgUH04/0.jpg" 
alt="The original Ghou.st game" width="240" height="180" border="10" /></a>

## Get started
- Open https://makecode.microbit.org/
- Connect the micro:bit via USB
- Save file to "USB Drive"

<img src="images/ide.png" width="512" />

## Blocks
All of the following blocks are required to make it work.

### On start
<img src="images/ghoust-onstart.png" width="512" />

### Forever
<img src="images/ghoust-forever.png" width="512" />

### On radio
<img src="images/ghoust-onradio.png" width="512" />

### On button
<img src="images/ghoust-onbutton.png" width="512" />

### On shake
<img src="images/ghoust-onshake.png" width="512" />

## Code
```
input.onButtonPressed(Button.A, function () {
    playing = 1
    basic.showIcon(IconNames.Ghost)
})
radio.onReceivedValueDeprecated(function (remoteId, remoteAlive) {
    list[parseFloat(remoteId)] = remoteAlive
    win = playing == 1 && alive == 1
    friends = 0
    for (let listAlive of list) {
        win = win && listAlive != 1
        friends = friends + listAlive
    }
    if (win) {
        basic.showIcon(IconNames.Heart)
    } else if (!(playing)) {
        basic.showNumber(friends + 1)
    }
})
input.onGesture(Gesture.Shake, function () {
    if (playing) {
        alive = 0
        basic.showIcon(IconNames.No)
    }
})
let friends = 0
let win = false
let list: number[] = []
let alive = 0
let playing = 0
radio.setGroup(1)
playing = 0
let n = 300
let id = "" + Math.randomRange(0, n)
alive = 1
for (let i = 0; i < n; i++) {
    list.push(0)
}
basic.showString(id)
basic.forever(function () {
    radio.sendValue(id, alive)
    basic.pause(500)
})
```

## How to play
- Use one micro:bit per player flashed with the above <a href="#code">code</a>.
- Reset your micro:bit to display the device ID and watch it start counting.
- Wait until all players see the same number which should equal the total number of players.
- Once everybody is ready, press the A button to become a ghoust, then try to shake other players' devices.

## How it works
<a href="#code">The code</a> implements a simple, distributed algorithm, without a central coordinator :)

### Getting a "unique" device ID
<a href="#on-start">On start</a> your device picks a random _id_ between 0 and _n_ (e.g. n = 300, n >> number of devices).

### Broadcasting your ID and status
<a href="#forever">Forever</a>, your device broadcasts (via Bluetooth radio) its _id_ and _alive_ status which is either 1 (alive) or 0 (not alive).

### Keeping track of other devices
<a href="#on-radio">On radio</a>, if a broadcast message was received, your device reads the remote device's _id_ and _alive_ status and updates its list.

### Detecting that you're out
<a href="#on-shake">On shake</a>, the device knows immediately, that it is no longer alive. All others learn it eventually, via broadcast.

### Detecting that you win
A device knows it won, if it's alive, and all other devices in its list are not alive.

## Make an enclosure

### Cut a PET bottle
Pierce a hole and cut along the label.

<img src="images/ghoust-chaya-0.jpg" width="512" />

Cut off the bottom part of the bottle.

<img src="images/ghoust-chaya-1.jpg" width="512" />

Then make a short, vertical cut as shown.

<img src="images/ghoust-chaya-2.jpg" width="512" />

### Use a laser-cut adapter
Here's a <a href="designs/ghoust-chaya.dxf">design</a> to fit the micro:bit into Chaya bottles.

<img src="images/ghoust-chaya-4.jpg" width="512" />

Connect the wires, align the adapter with the battery plug.

<img src="images/ghoust-chaya-adapter-back.jpg" width="512" />

Loosely strap the micro:bit to the adapter with a zip tie.

<img src="images/ghoust-chaya-adapter-front.jpg" width="512" />

Apply hot glue to both sides and place the battery.

<img src="images/ghoust-chaya-battery-hotglue.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-battery.jpg" width="250" />

### Add a button and a buzzer
Pierce small holes in the bottle cap to add a button and a buzzer.

<img src="images/ghoust-chaya-button-pierce.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-button-mounted.jpg" width="250" />

Cut two crocodile clip wires in half and remove the insulation.

<img src="images/ghoust-chaya-button-wire.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-button-soldered.jpg" width="250" />

Solder the wires to the pins - take care not to melt the cap.

<img src="images/ghoust-chaya-soldering.jpg" width="512" />

Put the wires through the bottle, and connect them to the micro:bit.

<img src="images/ghoust-chaya-assembly.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-wiring-top.jpg" width="250" />

Make sure the wires are connected right, run some test code to verify.

<img src="images/ghoust-chaya-top.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-test.jpg" width="250" />

### Add code for the button
In addition to <a href="#code">the code</a> above, add the following block.

<img src="images/ghoust-onpulsed.png" width="512" />

```
pins.onPulsed(DigitalPin.P2, PulseValue.High, function () {
    if (playing == 0) {
        playing = 1
        basic.showIcon(IconNames.Ghost)
    } else { // playing == 1
        control.reset() // sets playing to 0
    }
})
```

### Add code for the buzzer
TODO

### Switch on the micro:bit and start playing
Switch it on, then squeeze the top to add the micro:bit and re-assemble the bottle.

<img src="images/ghoust-chaya-11.jpg" width="250" />&nbsp;&nbsp;&nbsp;<img src="images/ghoust-chaya-done.jpg" width="250" />

That's it! You are ready <a href="#how-to-play">to play</a>.

<img src="images/ghoust-chaya-13.jpg" width="512" />

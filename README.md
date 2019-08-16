# Ghou.st for the micro:bit

Based on http://ghou.st/, https://github.com/Ghoust-game

## Get started
- Open https://makecode.microbit.org/
- Connect the micro:bit via USB
- Save file to "USB Drive"

<img src="images/ide.png" width="512" />

## Blocks
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
        basic.showNumber(friends)
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
let n = 1000
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
- Reset your micro:bit to display the player ID and watch it start counting.
- Wait until all players see the same number which should equal the total number of players.
- Once everybody is ready, press the A button to become a ghoust, then try to shake other players' devices.

## How it works
The above code is a distributed algorithm, it works peer-to-peer, without a central coordinator :)

### Getting a "unique" device ID
<a href="#on-start">On start</a> your device picks a random _id_ between 0 and _n_ (e.g. n = 1000, n >> number of devices).

### Broadcasting your ID and status
<a href="#forever">Forever</a>, your device broadcasts (via radio) its _id_ and _alive_ status which is either 1 (alive) or 0 (not alive).

### Keeping track of other devices
<a href="#on-radio">On radio</a>, if a broadcast message was received, your device reads the remote device's _id_ and _alive_ status and updates its list.

### Detecting that you're out
<a href="#on-shake">On shake</a>, the device knows immediately, that it is no longer alive. All others learn it eventually, via broadcast.

### Detecting that you win
A device knows it won, if it's alive, and all other devices in its list are not alive.

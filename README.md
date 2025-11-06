# **Interest Group Code \- IR Remote Blocks**

Welcome\! These are the custom MakeCode blocks for our infrared (IR) remote control project.

You can use these blocks to make your micro:bit respond to the remote control.

## **How to Start (This is Important\!)**

You **must** use this block in the on start section of your code. It tells the micro:bit which pin your IR sensor is connected to.

* **pin:** Where you plugged in the sensor (like P0).  
* **protocol:** Make sure this is set to NEC.

makerbit.connectIrReceiver(DigitalPin.P0, IrProtocol.NEC)

## **Our Custom Blocks\!**

We added some special blocks to make coding easier.

### **last number pressed**

This block is super useful. It only remembers the **last number** (0-9) that you pressed on the remote. If you press "EQ" or "VOL+", this block's value won't change.

basic.forever(function () {  
    let num \= makerbit.irLastNumberPressed()  
    basic.showNumber(num)  
})

### **last button pressed (name)**

This block tells you the name of the *very last button* you pressed. It returns a simple word (a "string") that you can show on the screen.

* Pressing **VOL+** returns **"UP"**  
* Pressing **VOL-** returns **"DOWN"**  
* Pressing **CH-** returns **"LEFT"**  
* Pressing **CH+** returns **"RIGHT"**  
* Pressing **Play/Pause** returns **"PLAY"**  
* Pressing **1** returns **"1"**  
* Pressing **EQ** returns **"EQ"**

basic.forever(function () {  
    let buttonName \= makerbit.irButtonName()  
    basic.showString(buttonName)  
})

## **Making Other Buttons Do Stuff**

What about buttons like EQ or CH? Here are two ways to use them.

### **Method 1: The Easy Way (Event Blocks)**

This is the best way for most projects. Just drag this block out and choose the button you want from the dropdown menu. Put the code you want to run inside it.

The micro:bit will *only* run this code when that specific button is pressed.

// This code runs when you press the "EQ" button  
makerbit.onIrButton(IrButton.Eq, IrButtonAction.Pressed, function () {  
    basic.clearScreen()  
})

// This code runs when you press the "Play/Pause" button  
makerbit.onIrButton(IrButton.PlayPause, IrButtonAction.Pressed, function () {  
    music.playMelody("C5 G4", 1200\)  
})

### **Method 2: The Advanced Way (Using if statements)**

This method is for more complex projects. You can check *which* button was pressed inside a forever loop.

To do this, you need two blocks:

1. **last button pressed (code)**: This oval block gives you the raw number code for *any* button.  
2. **code for button**: This dropdown block gives you the correct code to check against.

Here is how you can use an if statement to check if the EQ button was the last one pressed.

basic.forever(function () {  
    // Get the code for the last button pressed  
    let last\_button\_code \= makerbit.irButton()

    // Check if that code matches the code for the "EQ" button  
    if (last\_button\_code \== makerbit.irButtonCode(IrButton.Eq)) {  
        basic.showIcon(IconNames.Skull)  
    }  
})

## **Fun Demo Ideas**

Here are some cool projects you can build using these blocks. Just copy the code and paste it into MakeCode\!

### **1\. Pixel Mover (Etch-a-Sketch)**

A program where you can draw on the screen, just by moving a single pixel.

// \--- IR Remote Demo: Pixel Mover \---

let x \= 2  
let y \= 2

/\*\*  
 \* Initializes the IR receiver and plots the starting pixel.  
 \*/  
function initializeApp(): void {  
    // 1\. Connect to the IR Receiver on Pin 0  
    makerbit.connectIrReceiver(DigitalPin.P0, IrProtocol.NEC)  
      
    // 2\. Set the starting volume to low  
    music.setVolume(10)

    // 3\. Plot the starting pixel in the center  
    led.plot(x, y)  
}

/\*\*  
 \* Handles the IR datagram event.  
 \* Called every time a signal is received.  
 \*/  
function onIrSignalReceived(): void {  
    let buttonCode \= makerbit.irButton()

    // \--- Movement Controls \---

    // Move Up (2)  
    if (buttonCode \== IrButton.Number\_2) {  
        y \= Math.max(0, y \- 1\)  
        led.plot(x, y)  
    }   
    // Move Down (8)  
    else if (buttonCode \== IrButton.Number\_8) {  
        y \= Math.min(4, y \+ 1\)  
        led.plot(x, y)  
    }  
    // Move Left (4)  
    else if (buttonCode \== IrButton.Number\_4) {  
        x \= Math.max(0, x \- 1\)  
        led.plot(x, y)  
    }  
    // Move Right (6)  
    else if (buttonCode \== IrButton.Number\_6) {  
        x \= Math.min(4, x \+ 1\)  
        led.plot(x, y)  
    }

    // \--- Action Controls \---

    // Clear Screen (EQ)  
    else if (buttonCode \== IrButton.Eq) {  
        basic.clearScreen()  
        led.plot(x, y) // Re-plot the current pixel  
    }  
    // Toggle Pixel (Play/Pause)  
    // This will turn the pixel at (x, y) on or off  
    else if (buttonCode \== IrButton.PlayPause) {  
        led.toggle(x, y)  
    }  
    // Reset to Center (0)  
    else if (buttonCode \== IrButton.Number\_0) {  
        x \= 2  
        y \= 2  
        basic.clearScreen()  
        led.plot(x, y)  
    }  
      
    // Play "Nyan Cat" (CH)  
    else if (buttonCode \== IrButton.Ch) {  
        music.playMelody("B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6", 180\)  
    }  
}

// \--- Main Program \---  
initializeApp()  
makerbit.onIrDatagram(onIrSignalReceived)

### **2\. Music Maker**

Use the remote to play music and sound effects.

// \--- IR Remote Demo: Music Maker \---

/\*\*  
 \* Initializes the IR receiver and shows a "start" screen.  
 \*/  
function initializeApp(): void {  
    makerbit.connectIrReceiver(DigitalPin.P0, IrProtocol.NEC)  
    // Set the starting volume to low  
    music.setVolume(10)  
    // Show a music note to start  
    basic.showIcon(IconNames.QuarterNote)  
}

/\*\*  
 \* Handles all button presses from the remote.  
 \*/  
function onIrSignalReceived(): void {  
    let buttonCode \= makerbit.irButton()

    // \--- Single Notes \---  
    // The number buttons play single notes  
    if (buttonCode \== IrButton.Number\_1) {  
        music.playTone(262, music.beat(BeatFraction.Quarter)) // C  
        basic.showIcon(IconNames.SmallSquare)  
    } else if (buttonCode \== IrButton.Number\_2) {  
        music.playTone(294, music.beat(BeatFraction.Quarter)) // D  
        basic.showIcon(IconNames.Square)  
    } else if (buttonCode \== IrButton.Number\_3) {  
        music.playTone(330, music.beat(BeatFraction.Quarter)) // E  
        basic.showIcon(IconNames.SmallDiamond)  
    } else if (buttonCode \== IrButton.Number\_4) {  
        music.playTone(349, music.beat(BeatFraction.Quarter)) // F  
        basic.showIcon(IconNames.Diamond)  
    } else if (buttonCode \== IrButton.Number\_5) {  
        music.playTone(392, music.beat(BeatFraction.Quarter)) // G  
        basic.showIcon(IconNames.Target)  
    } else if (buttonCode \== IrButton.Number\_6) {  
        music.playTone(440, music.beat(BeatFraction.Quarter)) // A  
        basic.showIcon(IconNames.Yes)  
    } else if (buttonCode \== IrButton.Number\_7) {  
        music.playTone(494, music.beat(BeatFraction.Quarter)) // B  
        basic.showIcon(IconNames.No)  
    } else if (buttonCode \== IrButton.Number\_8) {  
        music.playTone(523, music.beat(BeatFraction.Quarter)) // High C  
        basic.showIcon(IconNames.Chessboard)  
    } else if (buttonCode \== IrButton.Number\_9) {  
        // A "fail" sound  
        music.playTone(139, music.beat(BeatFraction.Half))  
        basic.showIcon(IconNames.Sad)  
    } else if (buttonCode \== IrButton.Number\_0) {  
        // A "success" sound  
        music.playTone(523, music.beat(BeatFraction.Eighth))  
        music.playTone(587, music.beat(BeatFraction.Eighth))  
        music.playTone(659, music.beat(BeatFraction.Eighth))  
        music.playTone(698, music.beat(BeatFraction.Eighth))  
        basic.showIcon(IconNames.Happy)  
    }  
      
    // \--- Built-in Melodies \---  
      
    // Play "Nyan Cat"  
    else if (buttonCode \== IrButton.ChPlus) {  
        basic.showIcon(IconNames.Ghost)  
        music.playMelody("B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6 B5 G5 F5 C6", 180\)  
    }  
    // Play "Entertainment"  
    else if (buttonCode \== IrButton.ChMinus) {  
        basic.showIcon(IconNames.TShirt)  
        music.playMelody("C D E C C D E C E F G G E F G G G A G F E C G A G F E C C G C", 120\)  
    }  
    // Play "Ba Ding" sound effect  
    else if (buttonCode \== IrButton.PlayPause) {  
        basic.showIcon(IconNames.EighthNote)  
        music.playMelody("C5 G4", 1200\)  
    }

    // \--- Volume Controls \---  
    else if (buttonCode \== IrButton.VolUp) {  
        // Show "High Volume" icon  
        basic.showLeds(\`  
            \# . \# . \#  
            \# . \# . \#  
            \# . \# . \#  
            \# . \# . \#  
            \# . \# . \#  
            \`)  
        music.setVolume(255) // Max volume  
    } else if (buttonCode \== IrButton.VolDown) {  
        // Show "Low Volume" icon  
        basic.showLeds(\`  
            . . . . .  
            . . . . .  
            \# . \# . .  
            \# . \# . .  
            \`)  
        music.setVolume(10) // Very low volume  
    }  
      
    // Clear screen  
    else if (buttonCode \== IrButton.Eq) {  
        basic.clearScreen()  
        basic.showIcon(IconNames.QuarterNote) // Show home icon  
    }  
}

// \--- Main Program \---  
initializeApp()  
// This program uses the "datagram" event,  
// so holding a button will repeat the sound.  
makerbit.onIrDatagram(onIrSignalReceived)

## **License**

Licensed under the MIT License (MIT).

## **Supported targets**

* for PXT/microbit  
* for PXT/calliope

# What is this?
Rox Jockey is **a realtime and BPM-synced video jockey**.

By editing some presets, you can assign videos (specific frames) or images to keyboard events.
It is also possible to have geometric processing, such as scale and angle changes, and chroma key processing performed beforehand.

The assigned videos will be played back at a speed that matches the set BPM while the key event is occurring.
(The BPM can be changed at any time by hitting a specific key in a certain rhythm)

**Each of the examples below is generated from a single piece of footage:**
<video width="320" height="180" controls>
  <source type="video/mp4" src="vid/butterfly_example.mp4">
</video>
<video width="320" height="180" controls>
  <source type="video/mp4" src="vid/animated_example.mp4">
</video>

# Required runtime environment
- Windows 10 x64
- You may get an error that **VCRUNTIME140_1.dll** does not exist. In that case, please download and install vc_redist.x64.exe from [here](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0).

### Notice
- In this software, real-time processing highly depends on CPU performance, not GPU.
- This software uses a lot of memory to load frames. When you run the program, it will show you the memory size required to load the videos, so make sure you check it carefully!

# How it works
When you start RoxJockey.exe, the process proceeds as follows:

1. Read **config.json** file in the same directory.
2. Read the preset files in **presets** directory based on **config.json**.
3. Load and pre-process the footages in **footages** directory based on the preset files.
4. Assign the footages to keyboard events and generate a full-screen window.

# Configuration file (config.json)
- **It must be JSON format.** The default settings are as follows:

```json
{
  "resolution": [1920,1080],
  "bpm": {
    "hold": "lcontrol",
    "beat": "return"
  },
  "presets":[
    "tutorials/tutorial1.json",
    "tutorials/tutorial2.json"
  ]
}
```

## resolution (required)
Specifies the resolution to be displayed by **[width, height]**.

If the resolution does not match the display you are using, it will be scaled automatically.
The smaller the resolution you specify, the less memory will be used, but the quality will be rough.
Some of the preset settings are affected by the resolution you specify here.

## bpm (required)
Specifies **two keys** to be used for configuring bpm.

In the above example, you can set the BPM by repeatedly pressing **Return Key** for one beat while holding down **Left Control Key**.
Since it takes the average of the intervals between beats while the Left control key is pressed, the more repetitions there are, the more accurate the BPM becomes.

## presets (required)
Specifies presets to load from **presets** directory.

Each preset is assigned to the numeric keys in the specified order. In the example above, "tutorials/tutorial1.json" is assigned to the numeric key "1" and "tutorials/tutorial2.json" is assigned to the numeric key "2".
By pressing these numeric keys in the full screen window, you can switch presets.

# Preset file
- **It must be JSON format.**
- **Even if you are setting up a single event, please use array format.**
- **The footage you add later will be displayed in the front.**

```json
[
  {
    "footage": "A.mp4",
    "frame_segment": [100,120],
    "event":  {
      "start": ["z","down"],
      "end": ["z","up"]
    },
    "keying":false
  },
  {
    "footage": "A.mp4",
    "frame_segment": [225,245],
    "event":  {
      "start": ["a","down"],
      "end": ["a","donw"]
    },
    "keying": true,
    "power_of_beat": 0,
    "condition":{
      "angle": -90,
      "scale": 1.7,
      "border": "wrap"
    }
  }
]
```

## footage (required)
Specifies **a footage** to load from **footages** directory.

An image file can also be set.

## frame_segment (required to load a video)
Specifies the frames to be loaded by **[start frame, end frame]**.

## event (required)
Specifies **the start key event** when the frame starts to be displayed and **the key event when it ends**.

The frame will be displayed during the event. In the first event in the example above, the frame will be played only while the **"z" key** is down.
On the other hand, in the second event, once the **"a" key** is pressed, the frame will continue to play until the **"a" key** is pressed again.
When the event is restarted, the frame will start playing again from the beginning.

You can also specify the **"always"** keyword to make it always play, as shown below.
```json
"event": "always"
```

## keying (required)
Specifies **whether chroma keying will be applied**; if true, the default is to remove the green screen.

You can use the HSV color space to specify the range of colors to be extracted as follows. (The respective HSV values are **[H, S, V]**)
```json
"keying":{
  "lower_hsv": [90, 35, 39],
  "upper_hsv": [180, 100, 100],
  "mask_erode_iteration": 2
}
```

## power_of_beat (optional)
Specifies **how many beats to play the target frames to the end**. It's calculated by **2<sup>power_of_beat</sup>**.

For example, if the number is 0, the cycle will be 2<sup>0</sup>=1 beat; if the number is -2, the cycle will be 2<sup>-2</sup> = 1/4 beats; if the number is 2, the cycle will be 2<sup>2</sup>=4 beats.

The default value is **0**.

## condition (optional)
The following parameters are present.
```json
"condition":{
  "coordinates": [200,200],
  "angle": -90,
  "scale": 1.7,
  "flip": "v",
  "grayscale": false,
  "border": "wrap"
}
```

### condition/ coordinates
Specifies the coordinates of the center point of the target footage by **[x-coordinate, y-coordinate]**.

With the center of the screen as the origin, the x-coordinate is positive on the right side and the y-coordinate is positive on the top side.

### condition/ angle
Specifies **the number of degrees to rotate counterclockwise** around the center of the target footage.

It can also take a negative value.

### condition/ scale
Specifies **the ratio of scaling** around the center of the target footage.

### condition/ flip
Apply **inversion for horizontal, vertical or both**.

It can take the values **"h"**, **"v"** or **"both"** respectively.

### condition/ grayscale
Specifies whether **grayscaling will be applied**.

### condition/ border
You can set **how to fill the margins caused by the condition setting**.

Choose from the following five options.

|  border  |  result  |
| :--- | :---: |
|  transparent  |  <img src="img/border_transparent.png" alt="drawing" width="270"/>  |
|  wrap  |  <img src="img/border_wrap.png" alt="drawing" width="270"/>  |
|  reflect  |  <img src="img/border_reflect.png" width="270"/>  |
|  reflect101  |  <img src="img/border_reflect101.png" width="270"/>  |
|  replicate  |  <img src="img/border_replicate.png" width="270"/>  |

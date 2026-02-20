# Getting Started with ArduPilot, DroneKit, and Mission Planner (SITL)

This guide walks you through setting up a Python environment, installing DroneKit, running ArduPilot SITL through Mission Planner, and testing vehicle control using Python scripts.

> [!NOTE]
> We'll eventually create a clean repo containing the required .py files and reqs

> The original documents for DroneKit are [here](https://dronekit.netlify.app/). They're outdated, but the code works fine if you just write with Python3 syntax.

---

## 1. Create a New Python Virtual Environment

Open **Windows CMD** and run:

```cmd
python -m venv dronekit3
```

---

## 2. Activate the Environment

```cmd
cd dronekit3
Scripts\activate.bat
```

You should now see the environment name prefixed in your terminal, e.g.:

```
(dronekit3) C:\Users\you\py\dronekit3>
```

---

## 3. Install Required Python Packages

```cmd
pip install dronekit pymavlink future pyyaml
```

---

## 4. Patch DroneKit Deprecation Issue

DroneKit currently references a deprecated Python class. Apply this quick patch:

### Create the patch file

```cmd
edit dronekitpatch.py
```

Paste the following into the editor:

```python
"""
This script modifies the DroneKit __init__.py file to replace deprecated
'collections.MutableMapping' with 'collections.abc.MutableMapping'.
"""
from pathlib import Path

p = Path('Lib/site-packages/dronekit/__init__.py')
data = p.read_text()
data = data.replace('collections.MutableMapping', 'collections.abc.MutableMapping')

p.write_text(data)
print("Dronekit __init__.py patched successfully.")
```

Save (Ctrl+S) and exit (Ctrl+Q).

### Run the patch

```cmd
python dronekitpatch.py
```

---

## 5. Test DroneKit Installation

With the virtual environment still active:

```cmd
python
```

Inside Python:

```python
import dronekit
exit()
```

If no errors appear, DroneKit is installed correctly. 

***Don't close the terminal, we will run more python files in this environment***

---

## 6. Install Mission Planner

Download and install Mission Planner:

**https://ardupilot.org/planner/docs/mission-planner-installation.html**

---

## 7. Launch SITL in Mission Planner

1. Open **Mission Planner** from the Windows Start Menu.
2. Click **Simulation**.

3. Select **MultiRotor** firmware.


4. Return to the **Data** tab (top-right of window).

5. Open the **Actions** panel.

Mission Planner will automatically start SITL and listen on:

```
tcp:127.0.0.1:5763
```

---


### Download SpartanCode TCP Socket Plugin
 - Fab Page: https://www.fab.com/listings/48db4522-8a05-4b91-bcf8-4217a698339b
 - Github: https://github.com/CodeSpartan/UE4TcpSocketPlugin

<br>

If you download from Fab, it will be in your Epic Games Launcher, Unreal Engine Libray.
Press install to engine.

### Create a new UE5 Game with Blank Template
Enable blueprints.

### Launch Editor and Activate Plugin
1. Click Settings drop-down in the top-right of your editor window
2. Click Plugins
3. Search for 'tcp' and it should appear at the top of the list

Click enable plugin. A window will pop-up up prompting you to restart UE Editor, click **Restart Now**

### Download the following Unreal Assets:
```
bp_tcpRelay.uassest
bp_pythonPawn.uasset
bpi_relay.uasset
```

Create a `custom` folder in the `Content` folder in the Content Drawer. In Explorer, place these blueprints in you Unreal Project/content folder

You can find the folder in explorer easily by right clicking the custom folder in Unreal Engine and just clicking `Show in Explorer`

> For example on windows in the default Unreal Engine save path it looks like: <br>
 C:\Users\<username>\Documents\Unreal Projects\<projectName>\Content\custom

Back in Unreal Editor, they should appear in your content drawer.


Drag bp_pythonPawn and bp_tcpRelay into the level.

We have to fix a few things in the blueprints for it to work.

# Press Edit bp_pythonPawn in the outliner on the right

Open Class Settings and look to the right of the window.

Under interfaces, there should be a dropdown next to Implemented Interfaces that says add.
Click that and type in bpi and click on the Bpi Relay interface.

Now, on the left side of the window, there should be something that says EventGraph that you need to open (double click).

Zoom in on the bottom right of the nodes to see something that says `Invalid Message Node`.
Delete that node, and right click to add a new node. Type in Relay TCP data and click on it.
Where the invalid message node was connected, connect the nodes on the left to the Relay TCP data with the corresponding shapes.

Now zoom out and zoom in near the top left where it says `Get Actor of Class`. 
The class under Select Class needs to be bp_tcpRelay.

Now, press Compile at the top left of the window. It should be successful. Close the window.

# Press Edit bp_tcpRelay

Same as bp_pythonPawn, add the Bpi Relay interface in Class Settings

In the EventGraph, the `Invalid Message Node` that needs to be replaced is in the middle right.
The `Get Actor of Class` is in the top right, and the class to be selected is bp_pythonPawn.

Click compile and close the window.






Put both dronekit_unreal.py and tcp_relay.py in your dronekit13 folder.

Run 

```
python dronekit13.py

```

It should say, 

```

Vehicle Connected.
Waiting for location local_frame to be available...
```

Now you can go back to Unreal Engine and click the play button in the top tool bar. Going back to the terminal, we should see:

```
Location local_frame is now available.
Listening on 127.0.0.1:1234
Connected. Client address: ('127.0.0.1', 58057)
```

---

## 9. Manual Arm and Takeoff in Mission Planner

1. In Mission Planner **Actions** panel, change the Mode dropdown to **GUIDED** and then click **Arm/Disarm**.


> [!IMPORTANT]
> **After arming, you have ~10 seconds to take off before auto‑disarm.**
> This is a default safety parameter in the vehicles. If you don't takeoff by then, just rearm and try again.

2. Right‑click the map → **Takeoff** → enter altitude.

---


After entering an altitude, the pythonPawn in the game in Unreal Engine should be in the air.

Now, right clicking on the map and clicking `Fly Here` should reflect changes in the Unreal Engine environment.
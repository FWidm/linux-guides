# linux-guides
a small collection of how i got my hw to run

## VXE Mouse

#### Step 1: Identify Your Device's Attributes

First, you need to find the unique Vendor and Product IDs for your device.

1.  Plug in your device.
2.  Open a terminal and run `lsusb` to find the Vendor ID (`idVendor`) and Product ID (`idProduct`). Look for a line that matches your device description.

```bash
lsusb
# Example output:
# Bus 003 Device 005: ID 3554:f58a Compx VXE NordicMouse 1K Dongle
```

In this example, the `idVendor` is `3554` and the `idProduct` is `f58a`.

#### Step 2: Create or Edit a Udev Rule File

Udev rules are stored in `/etc/udev/rules.d/`. Create a new file with a `.rules` extension. It's best practice to give it a name with a numerical prefix less than `60` (e.g., `50-mydevice.rules`) to ensure it runs before default system rules.

```bash
sudo nano /etc/udev/rules.d/50-vxe-mouse.rules
```

#### Step 3: Write the Udev Rule

Inside the file, add the following lines. Replace the `idVendor` and `idProduct` with the values you found in Step 1.

```udevrules
# Permissions for the VXE NordicMouse 1K Dongle
SUBSYSTEM=="hidraw", ATTRS{idVendor}=="3554", ATTRS{idProduct}=="f58a", MODE="0666", TAG+="uaccess"
SUBSYSTEM=="hiddev", ATTRS{idVendor}=="3554", ATTRS{idProduct}=="f58a", MODE="0666", TAG+="uaccess"
```

  * `SUBSYSTEM`: Specifies the kernel subsystem (e.g., `hidraw` for raw HID reports).
  * `ATTRS`: Specifies an attribute to match, such as `idVendor` and `idProduct`. Using both is crucial for a unique match.
  * `MODE="0666"`: Sets the permissions to allow read and write access for all users.
  * `TAG+="uaccess"`: Marks the device as accessible for the currently logged-in user, which is a modern requirement on many Linux distributions.

#### Step 4: Apply the Rules

Save the file and then tell the udev daemon to reload its rules.

```bash
sudo udevadm control --reload-rules
```

For the new rule to take effect, you must unplug and then replug your device.

#### Step 5: Verify 

Go to the software hub https://v3-hub.atkgear.com/ and check if it works, if not check `chrome://device-log/` for errors like `[07:24:18] Failed to open '/dev/hidraw3': FILE_ERROR_ACCESS_DENIED`

source: https://www.reddit.com/r/linux_gaming/comments/1feizmm/comment/lqhja85/

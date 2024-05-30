### Step-by-Step Guide to Debug React Native App Using Phone’s Hotspot

#### **Requirements:**
- Your development machine (PC/laptop).
- An Android device with a mobile hotspot feature.

#### **Steps:**

### 1. Set Up the Mobile Hotspot

1. **Enable the hotspot on your Android device:**
   - Go to **Settings** > **Network & Internet** > **Hotspot & tethering** > **Wi-Fi hotspot**.
   - Turn on the **Wi-Fi hotspot**.

2. **Connect your PC/laptop to the mobile hotspot:**
   - On your PC/laptop, connect to the Wi-Fi network created by your phone’s hotspot.

### 2. Configure React Native App to Use the Local IP Address

1. **Find your phone's IP address:**
   - Go to **Settings** > **About phone** > **Status** > **IP address**.
   - Alternatively, you can find it using the ADB command when the phone is connected via USB:
     ```sh
     adb shell ip -f inet addr show wlan0
     ```
   - Note the IP address, which usually looks like `192.168.43.x`.

2. **Open the Developer Menu on your React Native app:**
   - Shake your device or
   - Run `adb shell input keyevent 82` in the terminal if the device is connected via USB.

3. **Go to Dev Settings.**

4. **Tap on Debug server host & port for device.**

5. **Enter your machine’s IP address and the port number (default is 8081):**
   - Your entry should look like this: `192.168.43.x:8081`.

### 3. Set Up ADB Over Wi-Fi Using the Hotspot

1. **Connect your phone to your PC/laptop via USB.**

2. **Verify the connection:**
   ```sh
   adb devices
   ```

3. **Enable TCP/IP mode on the device:**
   ```sh
   adb tcpip 5555
   ```

4. **Disconnect the USB cable from your device.**

5. **Connect to your device over Wi-Fi using the phone's hotspot IP address:**
   ```sh
   adb connect 192.168.43.x:5555
   ```

6. **Verify the connection:**
   ```sh
   adb devices
   ```

### 4. Start the Metro Bundler

1. **Run the Metro Bundler on your development machine:**
   ```sh
   npx react-native start
   ```

### 5. Install and Run the App

1. **Install the app:**
   ```sh
   npx react-native run-android
   ```

2. **Reload the app:**
   - From the terminal, you can reload the app by pressing `r`.
   - Or use the In-app developer menu and select the **Reload** option.

### **Summary of Commands:**
- Connect device and verify:
  ```sh
  adb devices
  ```
- Enable TCP/IP mode:
  ```sh
  adb tcpip 5555
  ```
- Connect over Wi-Fi using the hotspot:
  ```sh
  adb connect 192.168.43.x:5555
  ```
- Verify connection:
  ```sh
  adb devices
  ```
- Run the app:
  ```sh
  npx react-native run-android
  ```

By following these steps, you should be able to debug your React Native app on the same device that is providing the hotspot. This setup allows you to develop and test your app wirelessly, even without a separate Wi-Fi network.

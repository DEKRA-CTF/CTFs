### 0x00 Challenge: Lamp
**Description:**
>They have sent us a BLE capture made with an unidentified sniffer. To see the ATT attributes correctly, you have to identify and change the correct protocol in wireshark. Once done, you will be able to identify when the light bulb is turned on or off.
>
>The flag is the date of the first time the light bulb was turned on in the attached capture.
>
>Flag format: DEKRA{YYYY-MM-DD_HH:MM:SS}
>
>Capture: https://drive.google.com/file/d/1UTE4WmvABFKlnNsi5eRJbcJQwSsFJSdN/view?usp=sharing

### 0x01 Write-up:
The unidentifier sniffer is a nordic_BLE. First of all you need to configure the wireshark properly.

![](https://i.imgur.com/Lk7iZAp.png)

After that is easy to filter to ATT protocol and find the first WRITE request.

![](https://i.imgur.com/PXDdrCa.png)

Flag: DEKRA{2019-12-02_14:45:39}


# JBL-Live-PRO-on-Ubuntu-22.04

## Introduction

> I had many problems using my JBL Live Pro+ on Ubuntu 22.04, here is the things I did in order to use it.

## Headset connecting and then instant disconnect

```bash
sudo rfkill unblock all
sudo hciconfig hci0 down
sudo rmmod btusb
sudo modprobe btusb
sudo hciconfig hci0 up
```
Then, reboot.

> Using these different commands you will have your headset connected but you probably won't be able to use it because it is not detected as an output device.

## Headset connected but not detected as an output device

1. Replace Pulseaudio with Pipewire

Add the ppa repository :

```bash
sudo add-apt-repository ppa:pipewire-debian/pipewire-upstream
```

then,

```bash
sudo apt update
```

Install the following dependency, otherwise we face the issue “Bluetooth headset won’t connect after installing pipewire” :

```bash
sudo apt install libspa-0.2-bluetooth
```

* Install the client libraries :

```bash
sudo apt install pipewire-audio-client-libraries
```

* Reload the daemon :

```bash
systemctl --user daemon-reload
```

* Disable pulseaudio :
```bash
systemctl --user --now disable pulseaudio.service pulseaudio.socket
```

* Mask pulse audio (Ubuntu > 20.04) :

```bash
systemctl --user mask pulseaudio
```

* Enable pipewire-media-session-service :

```bash
systemctl --user --now enable pipewire-media-session.service
```

* Verify that pipewire is now running through :

```bash
pactl info
```

You'll get the following output :

```
Server name: PulseAudio (on PipeWire 0.3.56)
```

If you want to rollback all the changes :
```bash
systemctl --user unmask pulseaudio
systemctl --user --now disable pipewire{,-pulse}.{socket,service}    
systemctl --user --now enable pulseaudio.service pulseaudio.socket
```

2. Install these libraries

```bash
sudo apt install gstreamer1.0-pipewire libpipewire-0.3-{0,dev,modules} libspa-0.2-{bluetooth,dev,jack,modules} pipewire{,-{audio-client-libraries,pulse,bin,locales,tests}}
```

3. Reboot

4. Disconnect and reconnect your bluetooth device

5. Unmute the different sound devices on Alsamixer (not sure that this step changed something but I did it)

```bash
alsamixer
```
Select your sound card with F6, switch between your different audio sources, if an audio source has MM instead of 00 it means it's muted, type 'm' to unmute it. 

6. rfkill the bluetooth if it still doesn't work

```bash
sudo rfkill block bluetooth && sleep 0.1 && sudo rfkill unblock bluetooth;
```
After this step my JBL Live pro+ were detected as an audio source but the sound was strange.

7. Reboot

If it doesn't work after the reboot create a bash file with these commands :

```bash
sudo rfkill unblock all
sudo hciconfig hci0 down
sudo rmmod btusb
sudo modprobe btusb
sudo hciconfig hci0 up


sudo rfkill block bluetooth && sleep 0.1 && sudo rfkill unblock bluetooth;
```

then :

```bash
source name_of_your_file
```

## Conclusion

I hope it worked for you or on another bluetooth device.



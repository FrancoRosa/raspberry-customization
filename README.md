# raspberry-customization
>> Config steps for a clean/blank boot with logo and other snippeds

## Configure ssh and wifi headless

### Enable SSH access

1. After flashing the SD card with Raspbian OS, you should use your file browser and go to the boot partition.
2. There you should create an empty _ssh_ file.
  ```touch ssh```
3. Then, connect the RPi to a router by its Ethernet port, then try the following command to check if it is connectec
  ```ping raspberrypi.local```
4. If it worked, you can use your ssh client to connect. The default password is ```raspberry``` 
  ```ssh pi@raspberrypi.local```

### Enable wifi connection
If it is easier for you to use the wifi at home, follow this steps (Requirements RPi 3 or superior or RPi Zero)
1. Use your file browser and go to the boot partition.
2. There you should create an empty a new file _wpa_supplicant.conf_
  ```nano wpa_supplicant.conf```
3. There, replace the ssid and psk(password) with your actual network name and credentials, you should not erase the double quotes
```
country=US
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="NETWORK-NAME"
    psk="NETWORK-PASSWORD"
}
```
4. Save the changes pressing ```crtl+o```. Then, ```ctrl+x``` to exit.
5. Finally, boot your Rpi and repeat step _3_ of the SSH instructions.

## Hide RPi Boot messages

Open a terminal on RPi or log with ssh for this tasks

### Remove rainbow screen
Open “/boot/config.txt” as root.
```
sudo nano /boot/config.txt
```
Then add below line at the end of the file.
```
disable_splash=1
```

### Remove text message under splash image:
Open “/usr/share/plymouth/themes/pix/pix.script” as root.
```bash
sudo nano /usr/share/plymouth/themes/pix/pix.script
```
Then, comment the following lines:

```bash
message_sprite = Sprite();
message_sprite.SetPosition(screen_width * 0.1, screen_height * 0.9, 10000);
       my_image = Image.Text(text, 1, 1, 1);
       message_sprite.SetImage(my_image);
```

### Replace Splash Image
For this procedure you need the custom logo somewhere on your RPi, on your terminal type ```sftp pi@raspberrypi.local``` to use the FTP client then ```put logo.png```, finally

```
sudo cp ~/logo.png /usr/share/plymouth/themes/pix/splash.png
```
Now you should see your custom logo in the next boot

## Customize linux commands
You may edit the _.bashrc_ file for this task
```
nano .bashrc
```
At the end of this file append the commands followed by the actions, for instance.
```
alias ok='echo it works!'
alias off='vcgencmd display_power 0'
alias on='vcgencmd display_power 1'
```

This configuration will work after boot.

## Run commands just after boot

Edit the following file
```
sudo nano /etc/rc.local
```
Then, add the command you want to run ( just before #Print the IP address), for instance
```
vcgencmd display_power 0
```
Then save and reboot to see the changes

## Credits:
- This short manual was inspired on the following link. https://scribles.net/customizing-boot-up-screen-on-raspberry-pi/
- Create a img file and shrink it, step by step, here: https://ep.gnt.md/index.php/how-to-clone-raspberry-pi-sd-card-on-linux-and-shrink-it-to-actual-size/

## Bonus
- Copy img file from sd to file (https://mtyka.github.io/raspberrypi/2019/07/25/shrink-raspberrypi-image.html)
```
sudo dd bs=4M if=/dev/sdb of=myimage.img conv=fsync status=progress
```
- Shrink img file https://ostechnix.com/pishrink-make-raspberry-pi-images-smaller/

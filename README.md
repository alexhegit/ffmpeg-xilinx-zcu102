# ffmpeg-xilinx-zcu102
Enable ffmpeg on Xilinx ZCU102
The BSP/rootfs inlcude the ffmpeg and libx264(NEON). You can use it estimate the software solution of ffmpeg with H.264 encoder/decoder.

## Enviroment
Platform:     Xilinx zcu102 EVB
Tools/BSP:    [Xilinx petalinux v2018.2][1]

## Create the BSP
Please refer to [UG1144 - PetaLinux Tools Documentation: Reference Guide (ver2018.2)][2] for more details.
1. Create the petalinux project with the zcu102 BSP
2. Add ffmpeg and other tools into rootfs
   Chose the package groups by "petalinux-config -c rootfs" 
   - chose the multimedia package group
   - chose the opencv package group
   - chose x264 (H.264 software en/decoder)
   - chose any other package as you need
3. Enlarge the MAX size(>250MB or more) of initramfs.(page51 Managing Image Size ).
4. Build and create the images files.


## ffmppeg/ffplay Test
1. Copy the images(BOOT.bin & image.ub) from /sdcard to SD card. 
2. Copy estimate video source files to SD card
3. Insert the SD card to zcu102
4. Connect Monitor to zcu102 DP port
5. Connect USB camera to zcu102
6. Boot zcu102 with sd boot mode. Login as "root" with passwd "root"
The X11 GUI will show in the monitor. 
7. Mount SD partion 1 as need

  $mount /dev/mmcblk0p1 /mnt
  
  $cd /mnt
  
8. Set DISPLAY device of X11
  
  $export DISPLAY=:0.0
  
9. Play the video files.
  
  e.g.
  
  $ffplay -loop 0 -threads 2 hd0047_1080p@30fps_vb1M.ts
  
10. Play live video from USB camera
  
  e.g.
  
  $ffplay -f video4linux2 -framerate 30 -video_size hd720 /dev/video0



You can use ffmpeg to create the test video file with different format.
e.g. 
force covert source video file to 1080p@30fps baseline level, cbr v10Mbit/s.

$ffmpeg -i hd0047_1080p@30fps.mov -c:v libx264 -profile:v baseline -level 4 -an -y -x264-params "nal-hrd=cbr" -b:v 10M -minrate 10M -maxrate 10M  hd0047_1080p@30fps_vb10M.ts


## Test Report

Test files is 1080p@30fps, x264 Basline/level 4 in different bitrate, no audio. DP is output port  


Input                      |Output|Resolution@fps	| X264 profile/level | Bitrate(kb/s)	| Audio	| CPU loading(%)
---------------------------|------|---------------------|--------------------|------------------|-------|--------------
hd0047_1080p@30fps_vb1M.ts | DP   | 1080p@30fps	        | Baseline/level 4   | 1119	         | No   | 35~47	
hd0047_1080p@30fps_vb3M.ts | DP   | 1080p@30fps	        | Baseline/level 4   | 3272	         | No   | 40~55	
hd0047_1080p@30fps_vb5M.ts | DP   | 1080p@30fps  	| Baseline/level 4   | 5434	         | No   | 40~60	
hd0047_1080p@30fps_vb8M.ts | DP   | 1080p@30fps  	| Baseline/level 4   | 8689	         | No   | 40~67	
hd0047_1080p@30fps_vb10M.ts| DP   | 1080p@30fps  	| Baseline/level 4   | 10862	         | No   | 45~70	
							




[1]: https://www.xilinx.com/products/design-tools/embedded-software/petalinux-sdk.html
[2]: https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_2/ug1144-petalinux-tools-reference-guide.pdf

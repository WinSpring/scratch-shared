xrandr
xrandr --output HDMI1 --auto --right-of LVDS1

## To turn of the laptop's monitor (suppose it is LVDS) off, in order to prolong its life:
xrandr --output LVDS --off
## to bring it back:
xrandr --output LVDS --auto

## set brightness:
xrandr --prop --verbose
xrandr --output LVDS1 --brightness 0.8 (between 0.0 and 1.0)

## change screen resolution:
xrandr --output <OUTPUT> --mode 1024x768
xrandr --output LVDS-1 --auto --primary --output HDMI-1 --mode 1920x1080 --rate 75 --right-of LVDS-1

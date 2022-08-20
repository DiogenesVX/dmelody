# dmelody
A simple audio player which uses mpv as a back-end.
DMelody provides a simple GUI for managing your audio files and listening to your favorite music, it uses yad for the interface and mpv to play and interact with the playlist.
I created it for myself to use with sway on Debian. If someone finds it useful for his/her situation, I'd be happy :).
It was tested on Debian Testing, it might as well run on your version/distribution.

# What you can do with dmelody
   1. Create a playlist (select a directory or several directories to be added to the playlist).
   2. Get exif data (artist, title, album, genre, duration and file name).
   3. Play - to play the playlist and Play Sel - to play the selected track.
   4. Show currently playing track.
   5. Play previous, next, toggle pause, toggle repeat the current track.
   6. Seek forward/backward by 10 senconds and 2 minutes.
   7. Display artist, title and duration directly in your swaybar.

# Screenshot
![Alt text](https://github.com/DiogenesVX/dmelody/blob/main/dmelody.png)

# Usage
  1. For correct work, place the dmelody script into /opt/dmelody/
  2. The current version is set up to work with pipewire and wireplumber so you might want to set up your audio system accordingly, otherwise you would have to make changes in the dmelody script.
  3. In order to have the artist, title and duration displayed directly in swaybar (as you can see in the screenshot), you need to add the following to your sway config:
  
          bar {
            position bottom
            separator_symbol @
            workspace_buttons no 
            status_command while ~/.config/sway/swaybin/swaytray; do sleep 1; done
          }

  create the directory ~/.config/sway/swaybin
  
  create a file ~/.config/sway/swaybin/swaytray
  
  add the following lines to ~/.config/sway/swaybin/swaytray
       
        POSITION=$(echo '{ "command": ["get_property_string", "time-pos"] }' | socat - /tmp/mpvsocket | jq .data | tr '"' ' ' | cut -d'.' -f 1)

        REMAINING=$(echo '{ "command": ["get_property_string", "time-remaining"] }' | socat - /tmp/mpvsocket | jq .data | tr '"' ' ' | cut -d'.' -f 1)

        MPVARTIST=$(echo '{ "command": ["get_property", "filtered-metadata"] }' | socat - /tmp/mpvsocket | jq ".data.Artist")

        MPVTITLE=$(echo '{ "command": ["get_property", "filtered-metadata"] }' | socat - /tmp/mpvsocket | jq ".data.Title")

        POSITIONSHOW=$(printf '%d:%02d:%02d' $(($POSITION/3600)) $(($POSITION%3600/60)) $(($POSITION%60)))

        REMAININGSHOW=$(printf ' %d:%02d:%02d\n' $(($REMAINING/3600)) $(($REMAINING%3600/60)) $(($REMAINING%60)))

        if [ $(pgrep mpv) ]; then
            beforeplay=[
            playstatus=▶
            middleplay=-
            afterplay=]
        fi
        
        echo "$beforeplay $playstatus $MPVARTIST $middleplay $MPVTITLE $POSITIONSHOW $REMAININGSHOW $afterplay"

  4. Launch the script the way you like (either with a key combination, or with a .desktop launcher or simply /opt/dmelody/dmelody)
  
 Make sure you have the following packages installed:
 
      jq
      sed
      yad
      mpv
      bash
      grep
      gawk
      socat
      procps
      psmisc
      pipewire
      coreutils
      findutils
      wireplumber
      libnotify-bin
      playerctl (optional)
      libimage-exiftool-perl
      fonts-noto-color-emoji

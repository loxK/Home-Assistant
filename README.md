#My HA setup:

This is my current Home Automation setup.  Starting small and gradually growing as funds allow...

##House:

3 storey townhouse, laid out (basically) like...


Aspect -    Back               Front

2nd floor - En Suite           Master Bedroom
          - Master Bedroom     Master Bedroom

1st floor - Family bathroom    Bedroom 4
          - Bedroom 2          Bedroom 3

Gnd floor - Living room        WC
          - Living room        Kitchen


##Hardware:

Raspberry Pi 3

Z-wave.me Razberry module

Wetek Openelec (Kodi)

Amazon Echo Dot

Asus wifi router with USB NAS attached

Philips Hue Bridge

Philips Hue Tap

3 x Philips Hue colour bulbs (bedroom)

2 x Fibaro FGD-212 dimmers (living room and one yet to be fitted!)

Fibaro FGMS-001 multi-sensor (yet to be fitted)

Comag 10" tablet

Usual home theatre stuff - TV/Blu-Ray/AV Receiver

##Software:

Home Assistant

HAdashboard

Kodi

Yatse (+ call plugin)

Alexa app (for config of echo dot)

Hue app (for config of hue lights/bridge)

Custom Alexa skill

Owntracks

Pushbullet

##What it does:

 - Controllable from my phone over the internet, or via local network.

 - Controls living room lights and three lights in the bedroom ( 1 x ceiling, 2 x bedside).  The bedroom lights are multicoloured and can be set to preset scenes or controlled individually.  The living room lights are white but can be set to preset brightness or controlled to any desired level.

 - Tracks our phones using owntracks and therefore knows whether or not anybody is at home.

 - Notifies us of key events via pushbullet and/or notifications on screen via kodi.

 - Bedroom lights can also be controlled by Hue Tap which has 4 buttons (3 pre-programmed scenes and off).

 - Automatically pause media during phone call using Yatse.

 - Automatically turn living room lights on when it gets dark and somebody is home.

 - Automatically turn living room lights on if the house is empty and somebody arrives home in the dark.

 - Automatically set living room lights to 'dim' when playing media (except music) and it is dark.

 - Automatically set living room lights to 'normal' when media (except music) pauses or stops.

 - Voice control for all the lights.

 - Voice control Kodi.

##My configuration:

See in the repo for all of my non-sensitive configuration files.  I've been playing with HASS for a while now and have made a fair few changes, a few quick notes:

In my configuration.yaml I have removed history component, discovery component and conversation component. Most people seem to leave these in but I removed them because:

 - The history component paints lovely pretty bars of the state that your components have been in recently.  I didn't need this, and found that it was quite slow to render, so it's gone.  I find the logbook a far more useful tool anyway.
 - The discovery component never discovered anything for me, so having it running in the background seemed a waste of resources.
 - The conversation component is superlfuous with my amazon echo implementation, so again I've reclaimed some resources.
 
I have changed my logging database to mysql.  I read on a blog post once that it massively improves the speed of HASS.  In all honesty I did not notice such a change to the performance of HASS, but I've done it now so I'm not changing it back unless I have a specific reason to. 

Everybody has their own way of organising files, and they can get confusing, so I decided on an organisational method for mine from the start and this is how I work it:

In the '.homeassistant/' folder there are some files that can't be moved, so they obviosuly stay there.  Any that don't have to be there (and don't have to be in a specific location elswewhere, ie local icons in ./homeassistant/www/) go in a folder called config/

When I'm adding an entry to my configuration.yaml I ask myself whether it will take more than two lines.  If it takes only one or two lines, I put it directly in configuration.yaml

Example:
```
#####Fine:

sun:        <---------  One line

ttl:
  - platform: Google <-------- Two lines
  

#####Not fine:

media_player:
  - platform: kodi
    host: 192.168.0.123  <----- Three lines or more
```

	
	
Where the entry will take three lines or more, I use an include and place the include file in the config directory.  I use the exact name of the component for the include, eg:
```
media_player: !include config/media_player.yaml
```

In cases where the new include file becomes large (I favour keeping them in bite-sized chunks of around 50 lines or fewer), I split that file in to as many as are needed and place them in a folder named exactly after the component, eg:
```
media_player: !include_dir_list config/media_player/
```

All of which, for me, leads to an easy to manage configuration system that looks something like this...

```
/path/to/.homeassistant/
        |
        |- configuration.yaml
		|- google_calendars.yaml
        |- known_devices.yaml
        |- secrets.yaml
		|- zwave_device_config.yaml
        |...[log files etc]
        |
        |-----/www/
        |     |
        |     |...[local pictures here]
        |
        |-----/deps/
        |     |
        |     |...[dependencies]
        |
        |-----/config/
        |     |
		|     |- alarm_control_panel.yaml
        |     |- device_sun_light_trigger.yaml
        |     |- emulated_hue.yaml
		|     |- google.yaml
        |     |- hdmi_cec.yaml
        |     |- http.yaml
		|     |- input_boolean.yaml
        |     |- light.yaml
        |     |- logbook.yaml
        |     |- media_player.yaml
        |     |- mqtt.yaml
		|     |- notify.yaml
        |     |- recorder.yaml
        |     |- weblink.yaml
        |     |
        |     |-----/alert/
        |     |     |
        |     |     |...[File per alert]		
        |     |
        |     |-----/automation/
        |     |     |
        |     |     |...[Folder per room/group]
        |     |           |
		|     |           |...[File per automation]
        |     |
        |     |-----/camera/
        |     |     |
        |     |     |...[Folder per type]
        |     |           |
		|     |           |...[File per camera]		
        |     |
        |     |-----/customize/
        |     |     |
        |     |     |...[File per group/set]
        |     |
        |     |-----/groups/
        |     |     |
        |     |     |-----/cards/
        |     |     |     |
        |     |     |     |...[file for each UI card]
        |     |     |
        |     |     |-----/views/
        |     |           |
        |     |           |...[file for each UI view tab]
        |     |
        |     |-----/scene/
        |     |     |
        |     |     |...[folder for each room/group]
        |     |           |
		|     |           |...[File per scene]
        |     |
        |     |-----/script/
        |     |     |
        |     |     |...[folder for each room/group]
        |     |           |
		|     |           |...[File per script]		
		|     |
        |     |-----/sensor/
        |     |     |
        |     |     |...[file for each group/set]
        |     |
        |     |-----/zones/
        |           |
        |           |...[file for each zone]
		|
		|-----/tts/
        |     |
        |     |...[text_to_speech files]		
	
```				

...which you can browse through in this repo.

##Useful links/resources etc:

[Home Assistant](http://home-assistant.io)

[Owntracks](http://owntracks.org/)

[Yatse](http://yatse.tv/redmine/projects/yatse)

[HAdashboard](http://home-assistant.io/docs/ecosystem/hadashboard/dash_config/)

[Bruh's website](http://www.bruhautomation.com/) and [Youtube](https://www.youtube.com/c/bruhautomation1)

[HA examples](https://home-assistant.io/cookbook/) espescially [CCOSTAN](https://github.com/CCOSTAN/Home-AssistantConfig) and [Bruh](https://github.com/bruhautomation/BRUH3-Home-Assistant-Configuration)


##Things to do:

Put some images on here

Timeline of automations / conditions to trigger

Buy more stuff!

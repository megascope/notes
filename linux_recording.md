Recording What You Here

Linux using pulseaudio 

```
pacmd list-sink-inputs # see inputs

parec --format=s16le -d auto_null.monitor | lame -r --quiet -q 3 --lowpass 17 --abr 192 - "/tmp/af.mp3"

parec --format=s16le -d auto_null.monitor | oggenc -b 128 -o /tmp/pm.ogg --raw -
```

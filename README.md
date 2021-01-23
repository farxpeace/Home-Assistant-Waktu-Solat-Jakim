# Home Assistant untuk Waktu Solat mengikut masa dari Jakim
Menggunakan api dari https://api.azanpro.com/ untuk mendapatkan waktu solat mengikut tetapan Jakim

### configuration.yaml
```yaml
sensor:
  - platform: time_date
    display_options:
      - 'time'
      - 'date'
      - 'date_time'
      - 'date_time_utc'
      - 'date_time_iso'
      - 'time_date'
      - 'time_utc'
      - 'beat'
  - platform: rest
    resource: https://api.azanpro.com/times/today.json?zone=sgr01&format=24-hour
    name: Jakim Waktu Solat Daily
    json_attributes:
      - zone
      - prayer_times
      - locations
    value_template: '{{ value_json.zone }}'
  - platform: template
    sensors:
        jakim_ha_date:
            friendly_name: 'Jakim HA Date'
            value_template: '{{as_timestamp(strptime(states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["date"], "%d-%m-%Y")) | timestamp_custom("%Y-%m-%d", true)}}'
        jakim_date:
            friendly_name: 'Jakim Date'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["date"] }}'
        jakim_timestamp:
            friendly_name: 'Jakim Unix Timestamp'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["datestamp"] }}'
        jakim_imsak:
            friendly_name: 'Imsak'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["imsak"] }}'
        jakim_subuh:
            friendly_name: 'Subuh'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["subuh"] }}'
        jakim_syuruk:
            friendly_name: 'Syuruk'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["syuruk"] }}'
        jakim_zohor:
            friendly_name: 'Zohor'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["zohor"] }}'
        jakim_asar:
            friendly_name: 'Asar'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["asar"] }}'
        jakim_maghrib:
            friendly_name: 'Maghrib'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["maghrib"] }}'
        jakim_isyak:
            friendly_name: 'Isyak'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["prayer_times"]["isyak"] }}'
        jakim_location:
            friendly_name: 'Jakim Zone Location'
            value_template: '{{ states.sensor.jakim_waktu_solat_daily.attributes["locations"] }}'
```

### automation.yaml
Tukar mp3 dengan pilihan anda. Tukar entity_id dengan chromecast anda. Saya gunakan 2 output iaitu MPD dan chromecast
``
entity_id: media_player.living_room_speaker
``
``
entity_id: media_player.mpd
``
```yaml
- id: '1610912946292'
  alias: Azan - 01 - Subuh
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_subuh") == states("sensor.time")}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.9'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Telah masuk waktu subuh bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - delay: 00:00:10
  - service: media_extractor.play_media
    data:
      entity_id: media_player.living_room_speaker
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_subuh.mp3
      media_content_type: music
  - service: media_extractor.play_media
    data:
      entity_id: media_player.mpd
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_subuh.mp3
      media_content_type: music
  mode: single
- id: '1610915963986'
  alias: Azan - 02 - Zohor
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_zohor") == states("sensor.time")}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.5'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Telah masuk waktu zohor bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - delay: 00:00:10
  - service: media_extractor.play_media
    data:
      entity_id: media_player.living_room_speaker
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  - service: media_extractor.play_media
    data:
      entity_id: media_player.mpd
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  mode: single
- id: '1610916009800'
  alias: Azan - 03 -  Asar
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_asar") == states("sensor.time")}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.5'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Telah masuk waktu asar bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - delay: 00:00:10
  - service: media_extractor.play_media
    data:
      entity_id: media_player.mpd
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  - service: media_extractor.play_media
    data:
      entity_id: media_player.living_room_speaker
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  mode: single
- id: '1610916034660'
  alias: Azan - 04 - Maghrib
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_maghrib") == states("sensor.time")}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.8'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Telah masuk waktu maghrib bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - delay: 00:00:10
  - service: media_extractor.play_media
    data:
      entity_id: media_player.living_room_speaker
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  - service: media_extractor.play_media
    data:
      entity_id: media_player.mpd
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  mode: single
- id: '1610916052986'
  alias: Azan - 05 - Isyak
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_isyak") == states("sensor.time")}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.4'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Telah masuk waktu isyak bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.mpd
      message: Telah masuk waktu isyak bagi rumah ini dan kawasan kawasan yang sewaktu
        dengannya
      language: id
  - delay: 00:00:10
  - service: media_extractor.play_media
    data:
      entity_id: media_player.living_room_speaker
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  - service: media_extractor.play_media
    data:
      entity_id: media_player.mpd
      media_content_id: https://xxxxxx.duckdns.org/local/sounds/azan_zohor_asar_maghrib_isyak.mp3
      media_content_type: music
  mode: single

- id: '1611057450253'
  alias: Azan - 05 - Isyak - 15 Minit sebelum
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_isyak") == (as_local(now()).strftime("%s")
      | int + (25*60)) | timestamp_custom("%H:%M", false)}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.6'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Azan Isyak akan berkumandang dalam masa 15 minit lagi
      language: id
  - delay: 00:00:10
  mode: single
- id: '1611216229092'
  alias: Azan - 03 - Asar - 15 Minit sebelum
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_asar") == (as_local(now()).strftime("%s")
      | int + (25*60)) | timestamp_custom("%H:%M", false)}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.6'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Azan Asar akan berkumandang dalam masa 15 minit lagi
      language: id
  mode: single
- id: '1611216272536'
  alias: Azan - 04 - Maghrib - 15 Minit sebelum
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_maghrib") == (as_local(now()).strftime("%s")
      | int + (25*60)) | timestamp_custom("%H:%M", false)}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.6'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Azan Maghrib akan berkumandang dalam masa 15 minit lagi
      language: id
  mode: single
- id: '1611216332786'
  alias: Azan - 02 - Zohor - 15 Minit sebelum
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_zohor") == (as_local(now()).strftime("%s")
      | int + (25*60)) | timestamp_custom("%H:%M", false)}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.6'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Azan Zohor akan berkumandang dalam masa 15 minit lagi
      language: id
  mode: single
- id: '1611216388029'
  alias: Azan - 01 - Subuh - 15 Minit sebelum
  description: ''
  trigger:
  - platform: template
    value_template: '{{states("sensor.jakim_subuh") == (as_local(now()).strftime("%s")
      | int + (25*60)) | timestamp_custom("%H:%M", false)}}'
  condition: []
  action:
  - service: media_player.volume_set
    data:
      volume_level: '0.6'
    entity_id: media_player.living_room_speaker
  - service: tts.google_translate_say
    entity_id: media_player.living_room_speaker
    data:
      entity_id: media_player.living_room_speaker
      message: Azan Subuh akan berkumandang dalam masa 15 minit lagi
      language: id
  mode: single
```


### automation.yaml
Dan kita perlu restart HA setiap hari pada jam 1 malam. Jadi sensor akan automatik ambil data baru.
```yaml
- id: '1610916523303'
  alias: Restart HA everyday at 01:00:00
  description: ''
  trigger:
  - platform: time
    at: 01:00:00
  condition: []
  action:
  - service: homeassistant.restart
    data: {}
  mode: single
```

### Demo video dan gambar
* [Part 1](https://www.facebook.com/farxpeace/posts/10159654361284162) - https://www.facebook.com/farxpeace/posts/10159654361284162
    * Menggunakan Remote 433Mhz dan Smart Switch. Anak-anak mudah untuk buka tutup lampu sendiri kerana kita boleh letakkan Remote rendah sikit.
    * Door Sensor untuk detect keadaan pintu sama ada tertutup atau terbuka. Seandainya tertutup, Exhaust Fan akan hidup secara automatik dan akan padam sendiri selepas 5 minit.
* [Part 2](https://www.facebook.com/farxpeace/posts/10159657148119162) - https://www.facebook.com/farxpeace/posts/10159657148119162
    * Cuba buat home automation dengan menggunakan HA
* [Part 3](https://www.facebook.com/farxpeace/posts/10159704171579162) - https://www.facebook.com/farxpeace/posts/10159704171579162
    * Masukkan HA dalam Raspberry Pi 4 Model B. 

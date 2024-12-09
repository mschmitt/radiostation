# Personal Radio Station (Meta Repository)

I'm working on a special-interest streaming radio station. This will affect the following (not all yet created) repositories, modeling the workflow from music download to delivery.

## Download and Cleanup

https://github.com/mschmitt/radiostation-track-prep

Interactively on a workstation:

* Manual:
  * Save music, create folder on Nextcloud
  * Ensure proper tagging
  * Screenshot the download site with license info to `proof.png` in the album folder.
* Scripted:
  * Add our local tags (Creative Commons notice, license, proof of license) (complete, `embed-proof-*.sh`)
    
## Playlist Preparation

Interactively in Nextcloud Music:

* Listen, add tracks to the station playlist
* Nextcloud Music is the principal location of tracks (in browsable/playable form) and for playlist maintenance

## Playlist Export (Status: Work in progress; develop at home and move to colocation)

`mschmitt/radiostation-playlist-prep`

Runs in the same place as Liquidsoap:

* Scripted: (all work in progress, working title: `nextcloud.py`)
  * Todo: configuration file with paths, URLs etc.
  * Use the subsonic API to export the station playlist from Nextcloud
  * Download each track to a holding directory, slugify/normalize filenames
  * Write playlist file
  * Signal to Liquidsoap that the playlist was updated (UNIX socket or TCP socket? Permissions?)
* Yet unclear whether this will run manually (triggered how?) or as a systemd timer, or both
 
## Back-end Delivery (Status: complete, move from home to housed server)

`mschmitt/radiostation-liquidsoap`

  * Liquidsoap:
    * Act as the Icecast source (complete, `station.liq`)
    * Embed Metadata into Stream on track changes (complete)
    * Example `vars.liq` in repository
    * Example systemd unit
 
## Front-end Delivery (Status: complete)

`mschmitt/radiostation-icecast-delivery`

Icecast has very little to configure, most of which will be done manually.

* Manual:
  * Webpage with player and AJAX thingy for metadata. (complete, `index.html` etc.)
* Scripted:
  * Icecast:
    * Hook stealing of the MDomain SSL certificate from Apache into Icecast startup (complete, `/etc/systemd/system/icecast2.service.d/override.conf`)
  * Probe Metadata from Stream and save the JSON object for insertion into the player page (complete, `metadata-prober`)
    * Example systemd unit
* All of this will be published non-anonymized


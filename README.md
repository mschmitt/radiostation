# Personal Radio Station (Meta Repository)

The document reflects the architecture of my special-interest streaming radio station. The workflow is modeled repository-by-repository from music download to delivery.

## Download and Cleanup

https://github.com/mschmitt/radiostation-track-prep

Interactively on a workstation:

* Manually:
  * Save music, create folder on Nextcloud
  * Ensure proper tagging
  * Screenshot the download site with license info to `proof.png` and save the download link to `url.txt` in the album folder.
  * For Jamendo, which doesn't display a summary of album licensing, there's `jamendo_album_summary.py`.
* Scripted:
  * Add our local tags (Creative Commons notice, license, proof of license, download url.)

Note the station does not play directly out of Nextcloud, but Nextcloud and Nextcloud Music are used for track and playlist management only.
    
## Playlist Preparation

Interactively in Nextcloud Music:

* Listen, add tracks to the station playlist
* Nextcloud Music is the principal location of tracks (in browsable/playable form) and for playlist maintenance

Note the station does not play directly out of Nextcloud, but Nextcloud and Nextcloud Music are used for track and playlist management only.

## Playlist Export

https://github.com/mschmitt/radiostation-exporter

Runs where Liquidsoap runs:

* Use the subsonic API to export the station playlist from Nextcloud Music
* Download each track to a holding directory, slugify/normalize filenames
* Automatic quality control (Metadata check) for each track.
* Write playlist file
* Purge unused tracks from the holding directory (this also affects tracks with bad QA)
* Calculate replay gain (`collectiongain`)
* Signal to Liquidsoap that the playlist was updated
* Yet unclear whether this will run manually forever or as a systemd timer, or both

## Liquidsoap

https://github.com/mschmitt/radiostation-liquidsoap

  * Liquidsoap:
    * Acts as the Icecast source
    * Embeds Metadata into Stream on track changes
    * **TODO**: Liquidsoap's randomness doesn't feel sufficiently random. Maybe hard-randomize the playlist on every export? But only if we can define and implement criteria for what we consider "random enough".
 
## Delivery

https://github.com/mschmitt/radiostation-delivery, https://github.com/mschmitt/radiostation-website

* Manually:
  * Icecast configuration (credentials, HTTPS)
* Scripted:
  * Webpage with player and AJAX thingy for metadata
    * **FIXME**: Instead of the old "unknown" Javascript dance, `he.js` throws the entire script away if it encounters an empty metadata object.
  * Icecast systemd unit modified (by default drop-in) to steal the MDomain SSL certificate from Apache
  * Periodically probe Metadata from Stream and save the JSON object for insertion into the player page.
    * **FIXME**: Emty metadata object is *not* a safe fallback. (See above re. `he.js`)

## TODO / DRAFT: Scalability / independence from my shared infrastructure

* Classic distributed
  * Central server (*icecast.${domain}*) acts as the hidden icecast master (playlist export, quality control, Liquidsoap)
  * Provides stream only to icecast frontend relays
  * *${domain}* served by relays
  * Relays are semi-disposable VPSes and connect on demand
  * Web server on each relay, pulling metadata from... Not sure where
  * All relays stream in sync
  * Round-Robin DNS (let's not prematurely optimize for this)
  * Don't care if you stream from a different server in the round robin than the one giving you the metadata, as the relays are in sync
  * Tasks to automate on relay:
    * Install dependencies (webserver, icecast)
    * Configure webserver for Letsencrypt (read back up on how I previously did this in a DNS Round Robin - but let's not prematurely optimize for this)
    * Configure webserver cert stealing for icecast
    * Configure icecast (passwords, relay)
    * Prometheus

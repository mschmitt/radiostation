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
 
## Player

https://github.com/mschmitt/radiostation-delivery, https://github.com/mschmitt/radiostation-website

* Manually:
  * Icecast configuration (credentials, HTTPS)
* Scripted:
  * Webpage with player and AJAX thingy for metadata
    * **FIXME**: Instead of the old "unknown" Javascript dance, `he.js` throws the entire script away if it encounters an empty metadata object.
  * Icecast systemd unit modified (by default drop-in) to steal the MDomain SSL certificate from Apache
  * Periodically probe Metadata from Stream and save the JSON object for insertion into the player page.
    * **FIXME**: Empty metadata object is *not* a safe fallback. (See above re. `he.js`)

## CDN

https://github.com/mschmitt/radiostation-cdn

* Notes on resource usage:
  * Bandwidth: 112 kbps per listener = 100 Mbps for almost 1000 listeners
  * Not relevant for anything in the current implementation:
    * Storage: 1.5 GB for 200 MP3s = 8 GB for 1000 MP3s
    * CPU: Less than 1 core for Vorbis transcoding
    * RAM: Unclear

* Relay architecture
  * *${domain}* served by icecast relays only, former single server turns into a hidden icecast main server, *radio.${domain}*
  * Relays are semi-disposable VPSes and connect on demand
  * Web server on each relay reverse-proxies the entire root hierarchy to the main server
  * Opted for *acme-tiny* instead of *mod_md* to better support cluster operation towards Letsencrypt

## Public Relations

https://github.com/mschmitt/radiostation-lightningtalk

* Prepared, but never given.

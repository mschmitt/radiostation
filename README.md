# Personal Radio Station (Meta Repository)

I'm working on a special-interest streaming radio station. This will affect the following (not yet created) repositories, modeling the workflow from music download to delivery.

## Download and Cleanup (Status: almost-complete, automatic QA to be done)

`mschmitt/radiostation-track-prep`

Happens interactively on a workstation:

* Manual:
  * Save music, create folder on Nextcloud
  * Ensure proper tagging
  * Screenshot the download site with license info to `proof.png` in the album folder.
* Scripted:
  * Add our local tags (Creative Commons notice, license, proof of license) (complete, `embed-proof-*.sh`)
  * Some QA to help avoid unclean and missing tags

## Playlist Preparation (Status: Nothing to do)

Interactively in Nextclous Music:

* Listen, add tracks to the station playlist
* Nextcloud Music is the pricipal location of tracks and playlist

## Playlist Export (Status: Work in progress; develop at home and move to housed server)

`mschmitt/radiostation-playlist-prep`

Happens where Liquidsoap runs and outputs to Icecast:

* Scripted: (all work in progress, working title: `nextcloud.py`)
  * Use the subsonic API to export the station playlist from Nextcloud
  * Download each track to a holding directory, slugify/normalize filenames
  * Write playlist file
  * Signal to Liquidsoap that the playlist was updated (UNIX socket or TCP socket? Permissions?)
 
## Back-end Delivery (Status: Move from home to housed server)

`mschmitt/radiostation-liquidsoap`

  * Liquidsoap:
    * Act as the Icecast source (complete, `station.liq`)
    * Embed Metadata into Stream on track changes (complete)
    * Example `vars.liq` in repository
 
## Front-end Delivery (Status: complete)

`mschmitt/radiostation-icecast-delivery`

Icecast has very little to configure, most of which will be done manually.

* Manual:
  * Webpage with player and AJAX thingy for metadata. (complete, `index.html` etc.)
* Scripted:
  * Icecast:
    * Hook stealing of the MDomain SSL certificate from Apache into Icecast startup (complete, `/etc/systemd/system/icecast2.service.d/override.conf`)
  * Probe Metadata from Stream and save the JSON object for insertion into the player page (complete, `metadata-prober`)


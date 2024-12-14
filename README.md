# Personal Radio Station (Meta Repository)

The document reflects the architecture of my special-interest streaming radio station. The workflow is modeled repository-by-repository from music download to delivery.

## Download and Cleanup

https://github.com/mschmitt/radiostation-track-prep

Interactively on a workstation:

* Manually:
  * Save music, create folder on Nextcloud
  * Ensure proper tagging
  * Screenshot the download site with license info to `proof.png` and save the download link to `url.txt` in the album folder.
* Scripted:
  * Add our local tags (Creative Commons notice, license, proof of license, download url.)
* **FIXME**: Started using extended attributes to mark files as done, but xattrs aren't synced through Nextcloud. Find another way.

Note the station does not play directly out of Nextcloud, but Nextcloud and Nextcloud Music are used for track and playlist management only.
    
## Playlist Preparation

Interactively in Nextcloud Music:

* Listen, add tracks to the station playlist
* Nextcloud Music is the principal location of tracks (in browsable/playable form) and for playlist maintenance

## Playlist Export

https://github.com/mschmitt/radiostation-exporter

Runs where Liquidsoap runs:

* Use the subsonic API to export the station playlist from Nextcloud Music
* Download each track to a holding directory, slugify/normalize filenames
* Write playlist file
* Signal to Liquidsoap that the playlist was updated
* Yet unclear whether this will run manually forever or as a systemd timer, or both
* **TODO**: Automatic quality control (Metadata check) for each individual track.

## Liquidsoap

https://github.com/mschmitt/radiostation-liquidsoap

  * Liquidsoap:
    * Acts as the Icecast source
    * Embeds Metadata into Stream on track changes
 
## Delivery

https://github.com/mschmitt/radiostation-delivery, https://github.com/mschmitt/radiostation-website

* Manually:
  * Icecast configuration (credentials, HTTPS)
* Scripted:
  * Webpage with player and AJAX thingy for metadata
  * Icecast systemd unit modified (by default drop-in) to steal the MDomain SSL certificate from Apache
  * Periodically probe Metadata from Stream and save the JSON object for insertion into the player page

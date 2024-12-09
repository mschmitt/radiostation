# Personal Radio Station (Meta Repository)

I'm working on a special-interest streaming radio station. This will affect the following (not yet created) repositories, modeling the workflow from music download to delivery.

## Download and Cleanup

`mschmitt/radiostation-track-prep`

Happens interactively on a workstation:

* Manual:
  * Save music, create folder on Nextcloud
  * Ensure proper tagging
  * Screenshot the download site with license info to `proof.png` in the album folder.
* Scripted:
  * Add our local tags (Creative Commons notice, license, proof of license)
  * Some QA to help avoid unclean and missing tags

## Playlist Preparation

Interactively in Nextclous Music:

* Listen, add tracks to the station playlist
* Nextcloud Music is the pricipal location of tracks and playlist

## Playlist Export

`mschmitt/radiostation-playlist-prep`

Happens where Liquidsoap runs and outputs to Icecast:

* Scripted:
  * Use the subsonic API to export the station playlist from Nextcloud
  * Download each track to a holding directory, slugify/normalize filenames
  * Write playlist file
  * Signal to Liquidsoap that the playlist was updated (UNIX socket or TCP socket? Permissions?)
  * Liquidsoap:
    * Act as the Icecast source
    * Embed Metadata into Stream on track changes
 
## Delivery

`mschmitt/radiostation-icecast-delivery`

Icecast has very little to configure, most of which will be done manually.

* Sripted:
  * Icecast:
    * Hook stealing of the MDomain SSL certificate from Apache into Icecast startup
  * Other:
    * Probe Metadata from Stream and save the JSON object for insertion into the player page

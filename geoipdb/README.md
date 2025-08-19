# README

These tools make use of location info from https://db-ip.com and
https://simplemaps.com/data/world-cities. 

The `make-*` scripts here all write their output to stdout.  Generally the recommended procedure is to create a file with a `-new.csv` or `-new.json` and if it succeeds, move it over the corresponding file without the `-new`.

The process is as follows:
1. `worldcities.csv` is downloaded from simplemaps.com by hand and processed by `make-location-cities` into `location-cities.csv`.  The latter then is checked into git.  This only needs to be done very rarely because cities don't tend to move.
2. Servers found in this repository can be automatically added or removed in `server-cities.csv` using the `make-server-cities` script.  That then needs to be hand-edited to add a comma and a string to find the nearest city to the server as found in `location-cities.csv`.  The string can have 2 or 3 comma-separated components: country-code, city name, and optionally a state or province if needed to identify the city uniquely.  Since this reads the old `server-cities.csv` to reuse previously added cities, the initial bootstrap can be done by creating that file empty.
3. A `corrections-new.json` file can then be created by `make-corrections`.  That takes as input the `corrections.json` with the previous output (just for looking up old ip addresses in case a DNS lookup fails), an `exceptions.json` for hand-edited corrections, the files generated in steps 1 and 2. 
4. The tool from https://github.com/iglov/mmdb-editor can then be used to apply the corrections from `corrections.json` (renamed from `corrections-new.json`) to `iplocation-base.mmdb` (see below for where the latter comes from) to make a new `iplocation.mmdb`.

Much of the above process is automated through github actions.  To bootstrap that, a `geoipdb` release tag needs to be created by hand in this repository and a `corrections.json` and `iplocation-base.mmdb.gz` need to be uploaded to that tag through the web interface.  The latter is downloaded from https://db-ip.com/db/download/ip-to-city-lite and renamed.  After the initial bootstrap, another github action downloads and releases a new version of the `mmdb` monthly and renames it to that name.

A redirect to the release download area is configured at https://geoipdb.openhtc.io, so stratum 1s can download from https://geoipdb.openhtc.io/iplocation.mmdb.gz.

# py-mapzen-whosonfirst-maxmind

Python tools for working with MaxMind GeoIP data and Who's On First documents

## Setup

```
sudo pip install -r requirements.txt .
```

## Important

It's probably still too soon.

## Usage

_This section is incomplete._

This package is meant to be used in concert with the following other (not-Perl) packages:

* https://github.com/whosonfirst/p5-Whosonfirst-MaxMind-Writer
* https://github.com/whosonfirst/go-whosonfirst-mmdb

The first two steps are to prepare the raw MaxMind GeoLite2 data and to establish concordances with Who's On First. These two tools will/should probably be merged in to one but today they are not...

You would use the `wof-mmdb-build-concordances` in this package to call the Mapzen Places API to build up a concordances for all the Geonames ID listed in a GeoLite2 IP database:

```
/usr/local/py-mapzen-whosonfirst-maxmind/scripts/wof-mmdb-build-concordances --apikey mapzen-*** --countries /usr/local/data/whosonfirst-data/meta/wof-country-latest.csv GeoLite2-Country-CSV_20170801/GeoLite2-Country-Locations-en.csv > /usr/local/maxmind-data/201711/GeoLite2-City-CSV_20171003/wof-geonames.csv
```

See the way we're passing in the path to a CSV (meta) file with countries? You shouldn't have to do that but today... you have to do that.

Then we use tools in the `p5-Whosonfirst-MaxMind-Writer` and `go-whosonfirst-mmdb` packages to transform the concordances in to a proper `mmdb` IP lookup database, with Who's On First data:

```
/usr/local/go-whosonfirst-mmdb/bin/wof-mmdb-prepare -concordances /usr/local/maxmind-data/201711/GeoLite2-City-CSV_20171003/wof-geonames.csv /usr/local/maxmind-data/201711/GeoLite2-City-CSV_20171003/wof-geonames-lookup.json

/usr/local/p5-Whosonfirst-MaxMind-Writer/scripts/build-wof-mmdb.pl -s /usr/local/maxmind-data/201711/GeoLite2-City-CSV_20171003/GeoLite2-City-Blocks-IPv4.csv -d cities.mmdb -l maxmind-data/201711/GeoLite2-City-CSV_20171003/wof-geonames-lookup.json
```

Finally you can test the database with tools in the `go-whosonfirst-mmdb` package.

```
/usr/local/go-whosonfirst-mmdb/bin/wof-mmdb -db cities.mmdb 88.190.229.170  | python -mjson.tool
{
    "88.190.229.170": {
        "mz:is_ceased": -1,
        "mz:is_current": -1,
        "mz:is_deprecated": 0,
        "mz:is_superseded": 0,
        "mz:is_superseding": 0,
        "mz:latitude": 48.859116,
        "mz:longitude": 2.331839,
        "mz:max_latitude": 48.9016495,
        "mz:max_longitude": 2.416342,
        "mz:min_latitude": 48.815857,
        "mz:min_longitude": 2.22372773135544,
        "mz:uri": "https://whosonfirst.mapzen.com/data/101/751/119/101751119.geojson",
        "wof:country": "FR",
        "wof:id": 101751119,
        "wof:name": "Paris",
        "wof:parent_id": 102068177,
        "wof:path": "101/751/119/101751119.geojson",
        "wof:placetype": "locality",
        "wof:repo": "whosonfirst-data",
        "wof:superseded_by": [],
        "wof:supersedes": []
    }
}
```

## See also

* https://github.com/whosonfirst/p5-Whosonfirst-MaxMind-Writer
* https://github.com/whosonfirst/go-whosonfirst-mmdb
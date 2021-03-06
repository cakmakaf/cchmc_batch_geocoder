# cchmc_batch_geocoder

> A docker container for geocoding, assigning census tract, and deprivation index to addresses

![](https://img.shields.io/github/tag-date/cole-brokamp/cchmc_batch_geocoder.svg?colorB=blue&label=version&style=flat-square)
![](https://img.shields.io/microbadger/image-size/degauss/cchmc_batch_geocoder.svg?logo=docker&style=flat-square)
![](https://img.shields.io/github/license/cole-brokamp/cchmc_batch_geocoder.svg?style=flat-square)
![](https://img.shields.io/docker/automated/degauss/cchmc_batch_geocoder.svg?label=build%20process&logo=docker&style=flat-square)
![](https://img.shields.io/travis/cole-brokamp/cchmc_batch_geocoder.svg?logo=travis&style=flat-square)
<!---![](https://img.shields.io/docker/build/degauss/cchmc_batch_geocoder.svg?label=build%20status&logo=docker&style=flat-square)-->

## Quick Start

This DeGAUSS container condenses the sequence of (1) geocoding street addresses with a [custom geocoder based on 2015 TIGER/Line address range files ](https://github.com/cole-brokamp/geocoder), (2) joining the geocodes to a 2010 census tract shapefile from NHGIS using the epsg:5072 projection, and (3) adding census tract level data from the [community deprivation index](https://github.com/cole-brokamp/dep_index) all into a single image.

To run, navigate to the directory containing a CSV file with a column called `address` and call:

```
docker run --rm=TRUE -v $PWD:/tmp degauss/cchmc_batch_geocoder my_address_file.csv
```

## Results and Diagnostic Output

The container tries to simplify interpretation of the geocoding results with some new columns:

- `bad_address`: `TRUE` for Cincinnati foster & institutional addresses, "foreign", "verify", "unknown", and missing addresses
- `PO`: `TRUE` if a Post Office (PO) box
- `precise_geocode`: `TRUE` if geocoding result had a precision method of “street” or “range” and a score of > 0.5

If `precise_geocode` is `FALSE`, this means that the address was geocoded but probably not well enough to accurately place it in a census tract. The `lat` and `lon` columns and the corresponding census tract variables (like `fips_tract_id`, `dep_index`, etc…) for these are set to missing since we cannot accurately place them at a coordinate and in a census tract.

The addresses that are not successfully geocoded are still in the output file, but all moved to the top. This allows for quick examination of these addresses for errors. After edits are made, rerun the container. The successful geocodes are cached locally in a folder called `geocoding_cache` so that the geocoding process is never repeated, but instead read from disk. This makes the process of manually editing problematic addresses and rerunning the edited file through the container very quick.

## DeGAUSS

To find more information on how to install Docker and use DeGAUSS, see the [DeGAUSS README](https://github.com/cole-brokamp/DeGAUSS) or our publications in [JAMIA](https://colebrokamp-website.s3.amazonaws.com/publications/Brokamp_JAMIA_2017.pdf) or [JOSS](https://colebrokamp-website.s3.amazonaws.com/publications/Brokamp_JOSS_2018.pdf).


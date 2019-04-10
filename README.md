# Canboatjs

[![npm version](https://img.shields.io/npm/v/@canboat/canboatjs.svg)](https://www.npmjs.com/@canboat/canboatjs)
[![Build Status](https://travis-ci.org/sbender9/canboatjs.svg?branch=master)](https://travis-ci.org/sbender9/canboatjs)

Pure javascript NMEA 2000 decoder and encoder

Canboatjs is a port of the canboat project (https://github.com/canboat/canboat) to javascript and it is intended to be used with the [Signal K Node Server](https://github.com/SignalK/signalk-server-node)

# Contributors
Canboajs js is built on the backs of a few people whithout whom it would not be possible.
- Kees Verruijt <kees@verruijt.net>
- Jouni Hartikainen <jouni.hartikainen@iki.fi>
- Teppo Kurki <teppo.kurki@iki.fi>

# Features

- Read directly from an Actisense NGT-1, canbus devices, Digital Yachts' iKonvert and Yacht Devices YDWG-02
- Supports input in canboat analyzer json format
- Take input in canboat analyzer json format and convert to binary N2K format and output to supported devices

# PGN Descriptions
The details about the PGN's recognized by Canboatjs come from the canboat project in [pgns.json](https://github.com/canboat/pgns). If you want to add or update PGN details, please make changes to the pgn.h file in canboat and submit a pull request there. Include sample data and raise an issue here so that I can include your changes in Canboatjs.


# Command Line Programs

## analyzerjs
This program is similar to the canboat `analyzer` command-line. It takes input in the actisense serial format and outputs canboat json for mat.

## to-pgn
This program takes input in the canboat json format and outputs actisense serial format.

## candumpanalyzer
This program takes input in the candump format and outputs canboat json format

# Use in your own software

## Installation

- `npm install @canboat/canboatjs`

## Create the parser

```js
const FromPgn = require('@canboat/canboatjs').FromPgn

const parser = new FromPgn()

parser.on('warning', (pgn, warning) => {
  console.log(`[warning] ${pgn.pgn} ${warning}`)
})

```

## Parse input from the Actisense NGT-1 or iKonvert string formats

```js
const json = parser.parseString('2017-03-13T01:00:00.146Z,2,127245,204,255,8,fc,f8,ff,7f,ff,7f,ff,ff')
if ( json ) {
  console.log(JSON.stringify(json))
}

```

Output:
```json
{
   "description" : "Rudder",
   "dst" : 255,
   "prio" : 2,
   "pgn" : 127245,
   "fields" : {
      "Reserved1" : "62",
      "Direction Order" : 0,
      "Instance" : 252
   },
   "src" : 204,
   "timestamp" : "2017-03-13T01:00:00.146Z"
}
```

## Parse input from the YDWG-02

```js
const json = parser.parseString('16:29:27.082 R 09F8017F 50 C3 B8 13 47 D8 2B C6')
if ( json ) {
  console.log(JSON.stringify(json))
}
```

Output:
```json
{
   "src" : 127,
   "pgn" : 129025,
   "description" : "Position, Rapid Update",
   "timestamp" : "2019-04-10T20:29:27.082Z",
   "dst" : 255,
   "prio" : 2,
   "fields" : {
      "Latitude" : 33.0875728,
      "Longitude" : -97.0205113
   }
}
```

## Generate Actisense format from canboat json

```js
const pgnToActisenseSerialFormat = require('./index').pgnToActisenseSerialFormat

const string = pgnToActisenseSerialFormat({
   "dst" : 255,
   "prio" : 2,
   "pgn" : 127245,
   "fields" : {
      "Reserved1" : "62",
      "Direction Order" : 0,
      "Instance" : 252
   },
   "src" : 204,
})

if ( string ) {
  console.log(string)
}
```

Output:

`2019-04-10T12:00:32.733Z,2,127245,0,255,8,fc,f8,ff,7f,ff,7f,ff,ff`

## Generate iKconvert format from canboat json

```js


const pgnToiKonvertSerialFormat = require('./index').pgnToiKonvertSerialFormat

const string = pgnToiKonvertSerialFormat({
   "dst" : 255,
   "prio" : 2,
   "pgn" : 127245,
   "fields" : {
      "Reserved1" : "62",
      "Direction Order" : 0,
      "Instance" : 252
   },
   "src" : 204,
})

if ( string ) {
  console.log(string)
}
```

Output:  `!PDGY,127245,255,/Pj/f/9///8=`

## Generate YDGW-02 format from canboat json



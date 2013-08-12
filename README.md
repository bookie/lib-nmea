# NMEA C Library

NMEA encode/decode library. This library bases on the NMEA protocol defined by [NovAtel](http://www.novatel.com/) in its OEM4 familay.

Please reference to [OEM4 Family of Receivers - Command and Log Reference Manual](http://www.novatel.com/assets/Documents/Manuals/om-20000047.pdf) for details.


## Operation
This library import/export information from individual NMEA message from/into its assoicated structure. 

## Decoding Flow
1. Read NMEA sentenses from file
2. Exclude all binary characters in the sentenses
3. Identify the NMEA message
4. Skip to next sentense if the NMEA message contains no UTC (e.g. GPGLL, GPGSA, etc..)
5. Verify the checksum of the sentense
6. Record the timestamp from UTC
7. Read all fields in the sentence, and decode them to associated structures
8. Step to next sentence
9. Repeat from _step 7_ until UTC has been updated from the message
10. Repeat from _step 6_ until end of file

## Supported NMEA Message Type
* GGA - Fix Information
* GLL - Geographic Position
* GRS - Range Residuals for Each Satellite
* GSA - DOP and Active Satellites
* GSV - Satellites in View
* RMC - Recommended Minimum Data
* VTG - Vector track an Speed over the Ground
* ZDA - UTC Date and Time
* TXT - Text Information

## API

* TOC
{:toc}

### Seek next message block in file
```c
uint32 nmea_file_seek_next_blk(FILE *pfile);
```

#### Parameters

pfile
: **FILE object** - a FILE object that identify the stream.

#### Return

Number of character in next message block.

### Extract one NMEA message from file and copy to buffer
```c
uint32 nmea_file_extract_msg(char *str, FILE *pfile);
```

#### Parameters

str
: **string** - char array to hold the message.

pfile
: **FILE object** - FILE input stream.

#### Return

Number of character in the message.

### Compute the checksum of the message
```c
uint8_t nmea_chksum_msg(const char *str, uint32_t size);
```

#### Parameters

str
: **string** - char array to hold the message.

size
: **integer** - size of the message.

#### Return

The checksum of the message.

## Snippet of NMEA Library

```c
struct nmea_date
{
  uint8_t dd;   // Date
  uint8_t mm;   // Month
  uint8_t yy;   // Year
};

struct nmea_utc
{
  uint8_t hh;   // Hour
  uint8_t mm;   // Minute
  float   ss;   // Decond
};

enum nmea_rmc_pos_status
{
    A,          // Data valid
    V,          // Data invalid
};

struct nmea_rmc
{
  struct nmea_utc           utc;      // UTC of position
  enum nmea_rmc_pos_status  postat;   // Position status
  double                    lat;      // Latitude
  double                    lon;      // Longitude
  float                     spdknt;   // Speed over ground in knots
  float                     trktrue;  // Track made good, degree True
  struct nmea_date          date;     // Date
  float                     magvar;   // Magnetic variation, degree
};

/*
 * Decode the RMC message into a structure
 * Return true if decode success
 */
static bool libnmea_dec_rmc(struct *nmea_rmc, const char *str);

```

## Remove Binary Characters

In case there are binary messages embedded in the NMEA text file,
use the following command to remove them:
```bash
strings <NMEA file> |  sed -nb 's/^.*\($GP\)/\1/p' 
```



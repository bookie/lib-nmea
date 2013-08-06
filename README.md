# NMEA C Library

NMEA encode/decode library. This library base on the NMEA protocol defined by [NovAtel](http://www.novatel.com/) in its OEM4 familay.

Please reference to [OEM4 Family of Receivers - Command and Log Reference Manual](http://www.novatel.com/assets/Documents/Manuals/om-20000047.pdf) for details.


## Operation
This library import/export information from individual NMEA message from/into its assoicated structure. 

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



Decoupage
=========

RESTful API for time series data cutouts.  Each cutout of data is 
composed of a header and data.  Various backends can be supported, the
initial target is MongoDB with or without Cube.

## Motivation

Fast data retrieval mechanism that allows for simple server side caching.

## API Example

www.nicedata.com/kelpie/system/decoupage/cutouts/valuesxyz/simple/2013-01-02T08:00:00Z/17hours/1second

The slugs

* valuesxyz: data set of interest
* 2013-01-02T08:00:00Z: the start time
* 17hours: duration of the data
* simple: kind of data (simple, or statistics)
* 1minute: the interval of interest (see cloudwatch api)

The response will be either a map of time:value pairs or an array of values.  In the
case of the response containing an array of values the header will contain the sampling interval.

##Cutouts

There are exactly two kinds of time series data that can be represented in
cutouts.  

* A map of time to value pairs 
* An array of data values. 

A cutout can be of the following sizes:

* 1 second
* 5 seconds
* 30 seconds
* 1 minute
* 5 minutes
* 30 minutes
* 1 hour
* 3 hours
* 6 hours
* 12 hours
* 1 day
* 2 days
* 5 days
* one week
* four weeks
* one month
* three months
* one year

These sizes are deliberately human scaled.  In addition power 2 sizes are available, e.g.

1, 2, 4, 8 ...timeUnitOfMeasure

if time unit of measure is omitted then seconds is assumed


##Data Values
Anything that can be represented in JSON, so pretty much anything.  
For the majority of use cases the Data Values will be Numbers.

## Data Response Headers

### Map Of Values Header

This is for use when the data is non uniformly sampled.

    {
        "type": "map",
        "cutout": {
            "startTime": "2013-01-02T08:00:00Z",
            "size": "1hour"
        },
        "requestTime": "2013-01-02T12:21:00Z'",
    }
 
Pretty simple: the type of data, the cutout dimensions, and the time that 
the request was fulfilled.  The dimensions are start time and cutout size.
The requestTime is the time on the server when the response was completed. 
This can be used by the client to setup Comet type requests, more later.

### Array Header

This is for use when the data is uniformly sampled

    {
        "type": "array",
        "cutout": {
            "startTime": "2013-01-02T08:00:00Z",
            "size": "1hour"
        },
        "interval": 25ms,
        "lastTime": "2013-01-02T08:52:47Z",
        "requestTime": "2013-01-02T12:21:00Z'",
    }

Pretty simple again.  As in the Map header: 
* the type of data
* the cutout dimensions
* the time that the request was fulfilled.  

In addition:
* sampling interval
* time of the last data value

## The Data

The data is either a map or an array.  In both cases the format will either
contain just the data, or statistics on the data in the interval of 
interest; max, min, number of values, mean, standard deviation.




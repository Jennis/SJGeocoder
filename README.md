# SJGeocoder

## CLGeocoder, but with local search results built-in

SJGeocoder is a drop-in `CLGeocoder` replacement that also performs an `MKLocalSearch` for the query and gives back a combined list of results. Copy the files into your project, do a `#import "SJGeocoder.h"`, and use SJGeocoder in place of CLGeocoder. The only difference is that you can use the `initWithSearchSource:` constructor, which takes a `SJGeocoderSearchSource` parameter:

- `SJGeocoderSearchSourceGeocoder` to just do a geocode,
- `SJGeocoderSearchSourceLocalSearch` to just do a local search, or
- `SJGeocoderSearchSourceAny` to do 'em both

(`init` just calls `init:SJGeocoderSearchSourceGeocoder`, so it's just like a normal CLGeocoder.)

## Example

In use, SJGeocoder is identical to CLGeocoder, except that instead of returning just a list of geocode results, the list also contains local search results.

	NSString *query = @"Apple Store";
	CLLocationCoordinate2D brisbaneCenter = CLLocationCoordinate2DMake(-27.573663, 153.019974);
	CLRegion *brisbane = [[CLRegion alloc] initCircularRegionWithCenter:brisbaneCenter radius:1000 * 300 identifier:@"Brisbane"];
	CLGeocoder *geocoder = [[SJGeocoder alloc] initWithSearchSource:SJGeocoderSearchSourceAny];
	[geocoder geocodeAddressString:query
						  inRegion:brisbane
				 completionHandler:^(NSArray *placemarks, NSError *error) {
					 // placemarks is an array of CLPlacemark objects
				 }
	];

## Notes

We check at runtime whether we can perform an `MKLocalSearch` (since it was only introduced in iOS 6.1). If we're on iOS < 6.1, we just do a geocode.

The completion handler returns an array of SJPlacemark objects because of an issue with MKLocalSearch -- a CLGeocoder's results (CLPlacemark objects) have names, but an MKLocalSearch's results (MKMapItems that have a CLPlacemark property) don't have names (I have a Radar out on this). Returning SJPlacemarks just makes sure that whatever SJGeocoder gives back, it has a name. You shouldn't need to think about SJPlacemark objects, since they just inherit from CLPlacemark, but just in case, that's why they exist.


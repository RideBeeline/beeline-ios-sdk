# Beeline iOS SDK

Beeline iOS SDK for bicycle routing

Latest version: `3.33.0`

## Requirements

- **Xcode 16.4**
- **iOS 15+**

Please note, we longer support the simulator running on Intel Mac's.

## Access

Contact us for credentials tech@beeline.co

## Installation

### Setup

Our SDK is distributed as a pre-built XCFramework.

1. Create a directory in your project named BeelineShared

1. Create a BeelineShared/BeelineShared.version file containing a single line with the version you wish to target

```
3.33.0
```

1. Add an environment variable named `TOKEN_GITHUB` for the access token we provided

1. Create a script to install the XCFramework into your project

```
#!/bin/sh

version=$(head -n 1 BeelineShared/BeelineShared.version)

if [ -z $version ] ; then
  echo "No version number defined in BeelineShared/BeelineShared.version"
  exit 1
fi

if [ -z $TOKEN_GITHUB ] ; then
  echo "No TOKEN_GITHUB environment variable set"
  exit 1
fi

echo "Installing BeelineShared version: $version"

dir=BeelineShared
build_dir=$dir/build
cache_dir=$dir/cache
rm -r $build_dir
rm -r $cache_dir
mkdir $build_dir
mkdir $cache_dir

release_assets_url=https://api.github.com/repos/RideBeeline/beeline-app-android/releases/tags/beeline-shared-$version
artifact_name=BeelineShared.xcframework.zip
asset_url=$(curl -L -H "Authorization: token $TOKEN_GITHUB" "$release_assets_url" | jq ".assets[] | select(.name==\"$artifact_name\") | .url" | sed 's/\"//g')
echo $asset_url
curl -o $cache_dir/$artifact_name -L -H "Authorization: token $TOKEN_GITHUB" -H "Accept: application/octet-stream" "$asset_url"
unzip -qq $cache_dir/$artifact_name -d $build_dir/

```

4. Run the script `./install_beeline_shared.sh`

5. Add the BeelineShared.xcframework to your project

 Drag and drop the following frameworks into the **Frameworks and Libraries** section of your application’s Xcode project:
 - `BeelineShared/build/BeelineShared.xcframework`

6. Verify with a successful build

## Beeline Routing

Beeline routing provides cycling and motorcycling optimised routes.

The Beeline routing API will return up to three routes.
Please note that in some cases, especially for shorter routes, routes can be close to identical. In this case less than three will be returned.

### Example setup

Note, the `instance` and `key` parameters will be provided to you by Beeline.

```swift
import BeelineShared

let apiClient = BeelineRoutingApiClient(
  instance: SimpleProperty(RoutingInstance.production),
  userAgent: "<YOUR USER AGENT>",
  clientType: "<YOUR APP NAME>",
  clientVersion: context.appVersion,
  key: { _ in "KEY_PROVIDED_BEELINE" },
  unexpectedErrorHandler: { error in },
)

let routeProvider: RouteProvider = BeelineRouting(
  routingApi: apiClient,
  userIdProvider: { nil }
)

```

### Example request for a single route

This will provide the best available route

```swift
let parameters = RouteParameters(
    vehicle: .bike,
    type: .oneWay,
    start: Coordinate(latitude: 51.50072, longitude: -0.12462),
    startBearing: nil,
    waypoints: [],
    end: Coordinate(latitude: 51.50206, longitude: -0.14009),
    nudgeWaypoints: nil,
    restrictions: [],
    isLiveSpeedsAndClosuresEnabled: false,
    category: null,
    provider: null,
    rideId: null,
    userId: null,
    generateDistance: null
)

// Error handling omitted
let routes = try await routeProvider.routes(parameters: parameters)

```

### Displaying a Beeline route on a map

You can get a list of coordinates that describes the track of the route
```swift
let coordinates = routes[0].course.track
```

## Beeline Navigation

Once you have a route you can start navigation.
If you go off reroute, it will reroute automatically.

```swift
let locationProvider: LocationProvider = DefaultLocationProvider()

let orientationProvider = NativeOrientationFuser(
  locationProvider: locationProvider,
  geomagneticsProvider: ObjectiveWMMMagneticsProvider(),
  sensorsReadingsProvider: IOSSensorsReadingsProvider(),
  bridge: NativeOrientationFuserBridgeFactory.shared.create(),
  onError: { error in }
)

let navigator = TrackNavigator(
  dispatcher: CoroutineDispatcher,
  locations: locationProvider.filteredLocationsFlow,
  routeProvider: routeProvider,
  rerouteBehavior: DefaultRerouteBehavior(
    isAutoRerouteEnabled: { true },
    isAutoRerouteEnabledWhenStartingOffRoute: false,
  ),
  offRouteTolerance: NavigationSettings.OFF_ROUTE_TOLERANCE,
  nudgeTolerance: NavigationSettings.NUDGE_TOLERANCE,
  trafficRerouteCheckInterval: NavigationSettings.TRAFFIC_REROUTE_CHECK_INTERVAL,
  trafficRerouteMinimumDeviationDistance: NavigationSettings.TRAFFIC_REROUTE_MINIMUM_DEVIATION_DISTANCE,
  rideId: nil,
  userId: nil,
  initialRoute: routes[0],
  originalRoute: routes[0],
  initialStepSequentialIndex: 0
)

navigator.start()

// Listen for navigator updates via navigator.snapshotFlow


navigator.stop()
```

# Beeline iOS SDK

Beeline iOS SDK for bicycle routing

Latest version: `1.1.0`

## Requirements

- **Xcode 12.0**
- **iOS 12+**
- **Alamofire ~> 5**
- **RxSwift ~> 6**
- **Carthage 0.38.0**

## Access

Contact us for credentials tech@beeline.co

## Installation

### Carthage

1. Add the following dependencies to your `Cartfile`.
```
# Required
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineLocation/BeelineLocation.json" ~> 1.1
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineRouting/BeelineRouting.json" ~> 1.1

# Optional for navigation
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineFusion/BeelineFusion.json" ~> 1.1
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineNavigation/BeelineNavigation.json" ~> 1.1
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineNavigationUI/BeelineNavigationUI.json" ~> 1.1
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineOrientation/BeelineOrientation.json" ~> 1.1

# If not already specified
github "marcbaldwin/Alto" ~> 1.5
github "Alamofire/Alamofire" ~> 5.0
github "ReactiveX/RxSwift"  ~> 6.0
github "RxSwiftCommunity/RxSwiftExt" ~> 6.0
```

2. Build frameworks

Depending on your setup you might need to use this [script](https://github.com/Carthage/Carthage/issues/3019#issuecomment-665136323)
```
carthage update --platform ios
```

3. Add frameworks to your project

 Drag and drop the following frameworks into the **Frameworks and Libraries** section of your application’s Xcode project:
 - `Carthage/Build/iOS/Alto.framework`
 - `Carthage/Build/iOS/Alamofire.framework`
 - `Carthage/Build/iOS/RxSwift.framework`
 - `Carthage/Build/iOS/RxSwiftExt.framework`
 - `Carthage/Build/iOS/RxSwiftExt.framework`
 - `Carthage/Build/iOS/RxCocoa.framework`
 - `Carthage/Build/iOS/BeelineFusion.framework`
 - `Carthage/Build/iOS/BeelineLocation.framework`
 - `Carthage/Build/iOS/BeelineNavigation.framework`
 - `Carthage/Build/iOS/BeelineNavigationUI.framework`
 - `Carthage/Build/iOS/BeelineOrientation.framework`
 - `Carthage/Build/iOS/BeelineRouting.framework`

4. Add the following paths to your `input.xcfilelist` for the carthage copy-frameworks run script phase
```
$(SRCROOT)/Carthage/Build/iOS/Alto.framework
$(SRCROOT)/Carthage/Build/iOS/Alamofire.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineFusion.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineLocation.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineNavigation.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineNavigationUI.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineOrientation.framework
$(SRCROOT)/Carthage/Build/iOS/BeelineRouting.framework
$(SRCROOT)/Carthage/Build/iOS/RxSwift.framework
$(SRCROOT)/Carthage/Build/iOS/RxCocoa.framework
$(SRCROOT)/Carthage/Build/iOS/RxSwiftExt.framework
$(SRCROOT)/Carthage/Build/iOS/RxRelay.framework
```

5. Add the following paths to your `output.xcfilelist` for the carthage copy-frameworks run script phase
```
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Alto.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Alamofire.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineFusion.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineLocation.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineNavigation.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineNavigationUI.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineOrientation.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/BeelineRouting.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/RxSwift.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/RxCocoa.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/RxSwiftExt.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/RxRelay.framework
```

6. Verify with a successful build

## Beeline Routing

Beeline routing provides cycling optimised routes.

The Beeline routing API will return up to three routes. Each route is categorised as follows:
- **Fast**

 *Prioritises the shortest, simplest route. May contain fast traffic and tricky junctions.*
- **Balanced**

 *Fairly direct, using well-rated roads and paths where possible.*
- **Quiet**

 *Keeps you on well-rated roads from the Beeline community. It may feel longer, but you'll ride great roads.*

Please note that in some cases, especially for shorter routes, routes can be close to identical. In this case less than three will be returned. They will be prioritised in this order:
- balanced
- fast
- quiet

## Beeline Routing Integration

### Example setup

Note, the `instance` and `key` parameters will be provided to you by Beeline.

```swift
import BeelineRouting

let beelineRouting = BeelineDirectionsProvider(
  key: "YOUR_KEY",
  instance: "YOUR_INSTANCE",
  userId: "optional-user-id"
)
```

### Example request for a single route

This will provide the best available route

```swift
let start = CLLocationCoordinate2D(latitude: 51.50072, longitude: -0.12462)
let end = CLLocationCoordinate2D(latitude: 51.50206, longitude: -0.14009)

let parameters = RouteParameters(
    vehicle: .bike,
    start: start,
    end: end,
    sessionId: "optional-session-id"
)

beelineRouting.route(parameters: parameters)
    .subscribe(
        onSuccess: { route in
            print(route)
        },
        onFailure: { error in
            print(error)
        }
    )
    .disposed(by: disposeBag)
```

### Example request for multiple route options

This will provide up to three routes

```swift
let start = CLLocationCoordinate2D(latitude: 51.50072, longitude: -0.12462)
let end = CLLocationCoordinate2D(latitude: 51.50206, longitude: -0.14009)

let parameters = RouteParameters(
    vehicle: .bike,
    start: start,
    end: end
)

beelineRouting.routes(parameters: parameters)
    .subscribe(
        onSuccess: { routes in
            print(routes)
        },
        onFailure: { error in
            print(error)
        }
    )
    .disposed(by: disposeBag)
```

### Displaying a Beeline route on a map

You can get a list of coordinates that describes the track of the route
```swift
let coordinates = route.course.track
```

## Beeline Navigation

Once you have a route you can start navigation.

Create a `CompassView` and integrate it into your UI.
```swift
let compassView = CompassView()
compassView.screen.arrowColor = .white
compassView.screen.borderColor = .white
compassView.screen.textColor = .white
compassView.progressView.borderColor = .white
compassView.progressView.fillColor = .white
```

Bind your `CompassView` to the `Navigator` for your route.
```swift
let locationProvider: LocationProvider = DefaultLocationProvider()

let orientationProvider: OrientationProvider = FusedOrientationProvider(
  orientationFuser: NativeOrientationFuser(),
  locationProvider: locationProvider
)

let navigator = TrackNavigator(
  directionsProvider: beelineRouting,
  config: .default,
  rerouteBehavior: DefaultRerouteBehavior(),
  route: route
)

let measurementPreference = DefaultMeasurementPreference(distanceUnit: .kilometres)

let compassViewModel = VirtualCompassViewModel(
  navigator: navigator,
  orientationProvider: orientationProvider,
  measurementFormatter: measurementFormatter
)

compassView.bind(to: compassViewModel, disposeBag: disposeBag)

navigator.start()

// Ensure you stop the navigator to avoid memory leaks and stop location updates
navigator.stop()
```

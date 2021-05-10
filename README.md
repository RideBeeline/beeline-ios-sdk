# Beeline iOS SDK

Beeline iOS SDK for bicycle routing

## Requirements

- **Xcode 12.0**
- **iOS 12+**
- **RxSwift ~> 5**
- **Moya ~> 14**

## Access

Contact us for credentials tech@beeline.co

## Installation

### Carthage

Add the `BeelineRouting` dependency to your `Cartfile`.

```
binary "https://s3-eu-west-1.amazonaws.com/succeed.likeab.ee/sdk/ios/BeelineRouting/BeelineRouting.json" ~> 0.0.1

# If not already specified
github "ReactiveX/RxSwift" ~> 5.0
github "Moya/Moya" ~> 14.0
```

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

### Example setup

Note, the `instance` and `key` parameters will be provided to you by Beeline.

```swift
let beelineRouting = BeelineRoutingApiClient(
  key: "YOUR_KEY",
  instance: "YOUR_INSTANCE"
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
    end: end
)

beelineRouting.route(parameters: parameters, userId: "abcd")
    .subscribe(
        onSuccess: { route in
            print(route)
        },
        onError: { error in
            print(error)
        }
    )
    .disposed(by: disposeBag)
```

### Example request for multiple route options

This will provide the best available route

```swift
let start = CLLocationCoordinate2D(latitude: 51.50072, longitude: -0.12462)
let end = CLLocationCoordinate2D(latitude: 51.50206, longitude: -0.14009)

let parameters = RouteParameters(
    vehicle: .bike,
    start: start,
    end: end
)

beelineRouting.routes(parameters: parameters, userId: "abcd")
    .subscribe(
        onSuccess: { routes in
            print(routes)
        },
        onError: { error in
            print(error)
        }
    )
    .disposed(by: disposeBag)
```

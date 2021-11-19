# Changelog

## Summary
 - 1.4.0 Orientation fix, demo mode, colour improvements
 - 1.3.0 Transparent progress bar background colour
 - 1.2.2 Arrow colour
 - 1.2.1 CLLocationManager fix
 - 1.2.0 Lanscape support
 - 1.1.0 NavigationUI
 - 1.0.0 Routing

### 1.4.0 Orientation fix, demo mode, colour improvements
*Released on 18/11/2021*

#### Description
- Fixes orientation when device is in landscape mode
- Disables arrow rotation when in demo mode
- Adds property to change junction indicator colour

### 1.3.0 Transparent progress bar background colour
*Released on 08/11/2021*

#### Description
Removes the `NavigationView.fillBackgroundColor` property. Instead uses a clear background to show whatever is beneath it.

### 1.2.2 Arrow colour
*Released on 14/10/2021*

#### Description
Adds the ability to change the arrow's colour via the `CompassView.screen.arrowColor` property.

### 1.2.1 CLLocationManager fix
*Released on 20/09/2021*

#### Description
Fixes an issue that could result in no locations being received if multiple `CLLocationManager` were instaniated.

### 1.2.0 Lanscape support
*Released on 03/09/2021*

#### Description
Supports landscape orientation. No changes are required for this to work.


### 1.1.0 NavigationUI
*Released on 02/09/2021*

#### Description
Initial NavigationUI SDK.


### 1.0.0 Routing
*Released on 26/07/2021*

#### Description
Initial routing SDK.

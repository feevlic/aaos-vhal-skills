---
name: android14-release
description: Android 14 Automotive OS skill for VHAL.
---

# Android 14 Automotive OS Release Skill for VHAL

<system_directives>
<role>
You are an elite Staff-Level Android Automotive OS (AAOS) platform engineer and architecture expert. Your primary function is to flawlessly guide developers in implementing, debugging, and orchestrating Vehicle Hardware Abstraction Layer (VHAL) properties against the canonical Android 14 specifications.
</role>

<operational_constraints>

1. **Absolute Truth Protocol:** The `# Properties` section below is the mathematical boundary of your knowledge. Do NOT hallucinate, infer, or guess property names, IDs, or data types. If a property isn't listed, state unequivocally that it is unsupported or unavailable in Android 14.
2. **Framework Compliance:** When generating application-level code, ALWAYS invoke properties using framework constants (e.g., `android.car.VehiclePropertyIds.PERF_VEHICLE_SPEED`). NEVER hardcode Hex or Int IDs into Kotlin/Java codebases.
3. **Strict Validation:** Actively police `Access` rules and `Protection Level` scopes. If a developer attempts to write to a `READ` property, or requests a `signature|privileged` capability within standard app code, enforce architectural boundaries and warn them immediately.
4. **Log Decoding Mastery:** Use the mapped `ID (Int)` and deeply mapped `<Data Enums>` to rapidly decode ambiguous AOSP logs, dumpsys output, and raw integers. Convert numerical chaos into human-readable states instantly.
   </operational_constraints>
   </system_directives>

## Implementation Patterns (Best Practices)

<code_generation_rules>
When generating application-level code for Android Automotive, you must strictly adhere to the following lifecycle-aware, performant, and reactive patterns. Prioritize Kotlin Coroutines and `Flow` over raw callbacks to prevent memory leaks and ensure main-thread safety.
</code_generation_rules>

### 1. Reactive Property Observation (`callbackFlow`)

Wrap `CarPropertyEventCallback` in a `callbackFlow` to create a safe, lifecycle-aware stream of vehicle updates.

```kotlin
import android.car.VehiclePropertyIds
import android.car.hardware.CarPropertyValue
import android.car.hardware.property.CarPropertyManager
import kotlinx.coroutines.channels.awaitClose
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.callbackFlow

fun CarPropertyManager.observeProperty(
    propertyId: Int,
    areaId: Int = 0,
    rate: Float = CarPropertyManager.SENSOR_RATE_ONCHANGE
): Flow<CarPropertyValue<*>> = callbackFlow {
    val callback = object : CarPropertyManager.CarPropertyEventCallback {
        override fun onChangeEvent(value: CarPropertyValue<*>) {
            trySend(value) // Safe async dispatch
        }

        override fun onErrorEvent(propId: Int, zone: Int) {
            // Log or handle hardware-level property errors
        }
    }

    try {
        registerCallback(callback, propertyId, rate)
    } catch (e: SecurityException) {
        close(e) // Missing permissions
    } catch (e: IllegalArgumentException) {
        close(e) // Property not supported on this vehicle
    }

    // Automatically unregister when the flow collector cancels/stops
    awaitClose {
        unregisterCallback(callback, propertyId)
    }
}
```

### 2. Lifecycle-Aware UI Collection

When collecting property flows in a UI component (Fragment/Activity), always use `repeatOnLifecycle` to automatically pause VHAL communication when the app goes into the background. This saves system resources and limits battery drain from frequent sensor polling.

```kotlin
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.lifecycleScope
import androidx.lifecycle.repeatOnLifecycle
import kotlinx.coroutines.launch

// Inside Fragment or Activity
viewLifecycleOwner.lifecycleScope.launch {
    viewLifecycleOwner.repeatOnLifecycle(Lifecycle.State.STARTED) {
        // Example: Listen to vehicle speed at normal rate
        carPropertyManager
            .observeProperty(VehiclePropertyIds.PERF_VEHICLE_SPEED, rate = CarPropertyManager.SENSOR_RATE_NORMAL)
            .collect { value ->
                val speed = value.value as Float
                // Update UI safely here
            }
    }
}
```

### 3. Safe Synchronous Reads & Writes

Use safe wrapper functions for explicit get/set calls. Always catch `PropertyNotAvailableException` and `SecurityException`. Property values might be briefly unavailable during system boot, component sleep states, or due to bad HAL implementations.

```kotlin
import android.car.hardware.property.PropertyNotAvailableException

fun readVehicleSpeedSafe(carPropertyManager: CarPropertyManager): Float? {
    return try {
        val propValue = carPropertyManager.getProperty<Float>(
            VehiclePropertyIds.PERF_VEHICLE_SPEED,
            /* areaId= */ 0
        )
        propValue?.value
    } catch (e: PropertyNotAvailableException) {
        null // Sensor warming up or temporarily disabled
    } catch (e: SecurityException) {
        null // Handle missing runtime/system permissions
    }
}
```

### General Rules for Code Generation:

1. **Framework Constants:** Always map HAL properties to `android.car.VehiclePropertyIds.<PROPERTY_NAME>`. Never use hardcoded Integer or Hex strings in application code.
2. **Property Types:** Adhere strictly to the `Data Type` defined in this document (e.g., if a property is `int32[]`, cast it to `Integer[]` or `IntArray` safely).

## ADB & VHAL Debugging Commands (Mocking / Testing)

<adb_testing_instructions>
To test app implementations without driving a physical vehicle, you must proactively suggest the `cmd car_service` ADB interfaces to spoof or read VHAL data. Use the Hex or Integer IDs included in this document to form accurate terminal commands for the developer.
</adb_testing_instructions>

**Inject/Spoof a VHAL Property Event:**
This forces the system to believe the HAL just updated a sensor value, which will trigger your app's `callbackFlow`.

```bash
# adb shell cmd car_service inject-vhal-event [PROPERTY_ID] [ZONE_ID] [VALUE]
adb shell cmd car_service inject-vhal-event 0x1120040A 0 1 # Spoofs ABS_ACTIVE to True
```

**Get Current VHAL Property Value:**

```bash
# adb shell cmd car_service get-property-value [PROPERTY_ID] [ZONE_ID]
adb shell cmd car_service get-property-value 0x11401020 0 # Reads DRIVER_DISTRACTION_WARNING
```

**List all Car Service Properties Available:**

```bash
adb shell dumpsys car_service --services CarPropertyService --list
```

## Vehicle Area ID Reference (Bitmasks)

<area_computation_protocol>
Many vehicle properties are zoned (e.g., they apply uniquely to specific doors, seats, or windows). App developers must combine these constants using Bitwise OR (`|`) to query or modify specific areas using `CarPropertyManager`. When interacting with Zoned properties, ALWAYS consult this reference map to construct the correct `AreaId` integer. Never attempt to guess area IDs.
</area_computation_protocol>

### VehicleArea

- `GLOBAL = 0x01000000`
- `WINDOW = 0x03000000`
- `MIRROR = 0x04000000`
- `SEAT = 0x05000000`
- `DOOR = 0x06000000`
- `WHEEL = 0x07000000`
- `VENDOR = 0x08000000`
- `MASK = 0x0f000000`

### VehicleAreaDoor

- `ROW_1_LEFT = 0x00000001`
- `ROW_1_RIGHT = 0x00000004`
- `ROW_2_LEFT = 0x00000010`
- `ROW_2_RIGHT = 0x00000040`
- `ROW_3_LEFT = 0x00000100`
- `ROW_3_RIGHT = 0x00000400`
- `HOOD = 0x10000000`
- `REAR = 0x20000000`

### VehicleAreaMirror

- `DRIVER_LEFT = 0x00000001`
- `DRIVER_RIGHT = 0x00000002`
- `DRIVER_CENTER = 0x00000004`

### VehicleAreaSeat

- `UNKNOWN = 0x0000`
- `ROW_1_LEFT = 0x0001`
- `ROW_1_CENTER = 0x0002`
- `ROW_1_RIGHT = 0x0004`
- `ROW_2_LEFT = 0x0010`
- `ROW_2_CENTER = 0x0020`
- `ROW_2_RIGHT = 0x0040`
- `ROW_3_LEFT = 0x0100`
- `ROW_3_CENTER = 0x0200`
- `ROW_3_RIGHT = 0x0400`

### VehicleAreaWheel

- `UNKNOWN = 0x0`
- `LEFT_FRONT = 0x1`
- `RIGHT_FRONT = 0x2`
- `LEFT_REAR = 0x4`
- `RIGHT_REAR = 0x8`

### VehicleAreaWindow

- `FRONT_WINDSHIELD = 0x00000001`
- `REAR_WINDSHIELD = 0x00000002`
- `ROW_1_LEFT = 0x00000010`
- `ROW_1_RIGHT = 0x00000040`
- `ROW_2_LEFT = 0x00000100`
- `ROW_2_RIGHT = 0x00000400`
- `ROW_3_LEFT = 0x00001000`
- `ROW_3_RIGHT = 0x00004000`
- `ROOF_TOP_1 = 0x00010000`
- `ROOF_TOP_2 = 0x00020000`

# Properties

### Property Name: ABS_ACTIVE

- **ID (Int)**: 287310858
- **Hex Value**: 0x1120040A
- **AIDL Summary**: ABS is active.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DYNAMICS_STATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_LEAD_VEHICLE_MEASURED_DISTANCE

- **ID (Int)**: 289411093
- **Hex Value**: 0x11401015
- **AIDL Summary**: Measured distance from leading vehicle when using Adaptive Cruise Control (ACC) or Predictive Cruise Control in millimeters.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [100000]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 200000
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_TARGET_TIME_GAP

- **ID (Int)**: 289411092
- **Hex Value**: 0x11401014
- **AIDL Summary**: Current target time gap for Adaptive Cruise Control (ACC) or Predictive Cruise Control in milliseconds.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_STATES (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1200]}
- **Data Type**: int32

### Property Name: ANDROID_EPOCH_TIME

- **Data Type**: unknown

### Property Name: AP_POWER_STATE_REPORT

- **ID (Int)**: 289475073
- **Hex Value**: 0x11410A01
- **AIDL Summary**: Property to report power state of application processor.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleApPowerStateReport::WAIT_FOR_VHAL, 0]}
- **Data Type**: int32

### Property Name: AP_POWER_STATE_REQ

- **ID (Int)**: 289475072
- **Hex Value**: 0x11410A00
- **AIDL Summary**: Property to control power state of application processor.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_ENABLED

- **ID (Int)**: 287313920
- **Hex Value**: 0x11201000
- **AIDL Summary**: Enable or disable Automatic Emergency Braking (AEB).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_STATE

- **ID (Int)**: 289411073
- **Hex Value**: 0x11401001
- **AIDL Summary**: Automatic Emergency Braking (AEB) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [ErrorState::NOT_AVAILABLE_DISABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_ENABLED

- **ID (Int)**: 287313924
- **Hex Value**: 0x11201004
- **AIDL Summary**: Enable and disable Blind Spot Warning (BSW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_STATE

- **ID (Int)**: 339742725
- **Hex Value**: 0x14401005
- **AIDL Summary**: Blind Spot Warning (BSW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [BlindSpotWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_STATE

- **ID (Int)**: 289410817
- **Hex Value**: 0x11400F01
- **AIDL Summary**: Cabin lights.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_SWITCH

- **ID (Int)**: 289410818
- **Hex Value**: 0x11400F02
- **AIDL Summary**: Cabin lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Data Type**: int32

### Property Name: CLUSTER_DISPLAY_STATE

- **ID (Int)**: 289476405
- **Hex Value**: 0x11410F35
- **AIDL Summary**: Property to change the state of the cluster display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Default Value**: {int32Values: [0, -1, -1, -1, -1, -1, -1, -1, -1]}
- **Data Type**: int32

### Property Name: CLUSTER_NAVIGATION_STATE

- **ID (Int)**: 292556600
- **Hex Value**: 0x11700F38
- **AIDL Summary**: Property to inform the current navigation state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: CLUSTER_REPORT_STATE

- **ID (Int)**: 299896630
- **Hex Value**: 0x11E00F36
- **AIDL Summary**: Property to reports the current display and ClusterUI statue.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: CLUSTER_REQUEST_DISPLAY

- **ID (Int)**: 289410871
- **Hex Value**: 0x11400F37
- **AIDL Summary**: Property to request to change the cluster display state to show some ClusterUI.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: CLUSTER_SWITCH_UI

- **ID (Int)**: 289410868
- **Hex Value**: 0x11400F34
- **AIDL Summary**: Property to start the ClusterUI in cluster display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: CREATE_USER

- **ID (Int)**: 299896585
- **Hex Value**: 0x11E00F09
- **AIDL Summary**: Property to create a new user for multi-user management.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Data Type**: unknown

### Property Name: CRITICALLY_LOW_TIRE_PRESSURE

- **ID (Int)**: 392168202
- **Hex Value**: 0x1760030A
- **AIDL Summary**: Critically low tire pressure.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_TIRES
- **Protection Level**: signature|privileged
- **Areas**:
  - **Area ID**: Constants::WHEEL_FRONT_LEFT
  - **Default Value**: {floatValues: [137.0]}
  - **Area ID**: Constants::WHEEL_FRONT_RIGHT
  - **Default Value**: {floatValues: [137.0]}
  - **Area ID**: Constants::WHEEL_REAR_RIGHT
  - **Default Value**: {floatValues: [137.0]}
  - **Area ID**: Constants::WHEEL_REAR_LEFT
  - **Default Value**: {floatValues: [137.0]}
- **Data Type**: float

### Property Name: CRUISE_CONTROL_COMMAND

- **ID (Int)**: 289411090
- **Hex Value**: 0x11401012
- **AIDL Summary**: Write Cruise Control (CC) commands.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES (Write)
- **Protection Level**: signature|privileged
- **Areas**:
  - **Area ID**: 0
- **Data Type**: unknown

### Property Name: CRUISE_CONTROL_ENABLED

- **ID (Int)**: 287313935
- **Hex Value**: 0x1120100F
- **AIDL Summary**: Enable or disable Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_STATE

- **ID (Int)**: 289411089
- **Hex Value**: 0x11401011
- **AIDL Summary**: Current state of Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [CruiseControlState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_TARGET_SPEED

- **ID (Int)**: 291508243
- **Hex Value**: 0x11601013
- **AIDL Summary**: Current target speed for Cruise Control (CC) in meters per second.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [25.0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minFloatValue: 20.0, maxFloatValue: 35.0
- **Data Type**: float

### Property Name: CRUISE_CONTROL_TYPE

- **ID (Int)**: 289411088
- **Hex Value**: 0x11401010
- **AIDL Summary**: Current type of Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_STATES (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [CruiseControlType::STANDARD]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: CURRENT_GEAR

- **ID (Int)**: 289408001
- **Hex Value**: 0x11400401
- **AIDL Summary**: Vehicle transmission's current {@link VehicleGear}.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Data Type**: int32

### Property Name: CURRENT_POWER_POLICY

- **ID (Int)**: 286265123
- **Hex Value**: 0x11100F23
- **AIDL Summary**: Property to report a new current power policy to VHAL.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Data Type**: unknown

### Property Name: DISPLAY_BRIGHTNESS

- **ID (Int)**: 289409539
- **Hex Value**: 0x11400A03
- **AIDL Summary**: Property to represent brightness of the display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [100]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 100
- **Data Type**: int32

### Property Name: DISTANCE_DISPLAY_UNITS

- **ID (Int)**: 289408512
- **Hex Value**: 0x11400600
- **AIDL Summary**: Distance units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::MILE]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: DOOR_CHILD_LOCK_ENABLED

- **ID (Int)**: 371198723
- **Hex Value**: 0x16200B03
- **AIDL Summary**: Door child lock feature enabled.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area ID**: Constants::DOOR_2_RIGHT
- **Data Type**: int32

### Property Name: DOOR_LOCK

- **ID (Int)**: 371198722
- **Hex Value**: 0x16200B02
- **AIDL Summary**: Door lock.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Protection Level**: signature|privileged
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Default Value**: {int32Values: [1]}
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Default Value**: {int32Values: [1]}
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Default Value**: {int32Values: [1]}
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: DOOR_MOVE

- **ID (Int)**: 373295873
- **Hex Value**: 0x16400B01
- **AIDL Summary**: Door move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: DOOR_POS

- **ID (Int)**: 373295872
- **Hex Value**: 0x16400B00
- **AIDL Summary**: Door position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_REAR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
- **Data Type**: int32

### Property Name: ELECTRONIC_TOLL_COLLECTION_CARD_STATUS

- **ID (Int)**: 289410874
- **Hex Value**: 0x11400F3A
- **AIDL Summary**: Electronic Toll Collection card status.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ELECTRONIC_TOLL_COLLECTION_CARD_TYPE

- **ID (Int)**: 289410873
- **Hex Value**: 0x11400F39
- **AIDL Summary**: Electronic Toll Collection card type.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_ENABLED

- **ID (Int)**: 287313933
- **Hex Value**: 0x1120100D
- **AIDL Summary**: Enable or disable Emergency Lane Keep Assist (ELKA).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_STATE

- **ID (Int)**: 289411086
- **Hex Value**: 0x1140100E
- **AIDL Summary**: Emergency Lane Keep Assist (ELKA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [EmergencyLaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: ENGINE_COOLANT_TEMP

- **ID (Int)**: 291504897
- **Hex Value**: 0x11600301
- **AIDL Summary**: Temperature of engine coolant in celsius.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [75.0]}
- **Data Type**: float

### Property Name: ENGINE_IDLE_AUTO_STOP_ENABLED

- **ID (Int)**: 287310624
- **Hex Value**: 0x11200320
- **AIDL Summary**: Represents feature for engine idle automatic stop.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_LEVEL

- **ID (Int)**: 289407747
- **Hex Value**: 0x11400303
- **AIDL Summary**: Engine oil level.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleOilLevel::NORMAL]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_TEMP

- **ID (Int)**: 291504900
- **Hex Value**: 0x11600304
- **AIDL Summary**: Temperature of engine oil in celsius.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [101.0]}
- **Data Type**: float

### Property Name: ENGINE_RPM

- **ID (Int)**: 291504901
- **Hex Value**: 0x11600305
- **AIDL Summary**: Engine rpm.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: ENV_OUTSIDE_TEMPERATURE

- **ID (Int)**: 291505923
- **Hex Value**: 0x11600703
- **AIDL Summary**: Outside temperature in celsius.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_ENVIRONMENT
- **Default Value**: {floatValues: [25.0]}
- **Data Type**: float

### Property Name: EVS_SERVICE_REQUEST

- **Default Value**: {int32Values: [EvsServiceType::REARVIEW, EvsServiceState::OFF]}
- **Data Type**: int32

### Property Name: EV_BATTERY_DISPLAY_UNITS

- **ID (Int)**: 289408515
- **Hex Value**: 0x11400603
- **AIDL Summary**: EV battery units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::KILOWATT_HOUR]}
- **Data Type**: int32

### Property Name: EV_BATTERY_INSTANTANEOUS_CHARGE_RATE

- **ID (Int)**: 291504908
- **Hex Value**: 0x1160030C
- **AIDL Summary**: EV instantaneous charge rate in milliwatts.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: EV_BATTERY_LEVEL

- **ID (Int)**: 291504905
- **Hex Value**: 0x11600309
- **AIDL Summary**: EV battery level.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: EV_BRAKE_REGENERATION_LEVEL

- **ID (Int)**: 289408012
- **Hex Value**: 0x1140040C
- **AIDL Summary**: Regenerative braking level of a electronic vehicle.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_POWERTRAIN (Read) AND/OR Car.PERMISSION_CONTROL_POWERTRAIN (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: EV_CHARGE_CURRENT_DRAW_LIMIT

- **ID (Int)**: 291508031
- **Hex Value**: 0x11600F3F
- **AIDL Summary**: EV charge current draw limit.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_ENERGY (Read) AND/OR Car.PERMISSION_CONTROL_CAR_ENERGY (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [12.5]}
- **Data Type**: float

### Property Name: EV_CHARGE_PERCENT_LIMIT

- **ID (Int)**: 291508032
- **Hex Value**: 0x11600F40
- **AIDL Summary**: EV charge percent limit.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_ENERGY (Read) AND/OR Car.PERMISSION_CONTROL_CAR_ENERGY (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [40.0]}
- **Data Type**: float

### Property Name: EV_CHARGE_PORT_CONNECTED

- **ID (Int)**: 287310603
- **Hex Value**: 0x1120030B
- **AIDL Summary**: EV charge port connected.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY_PORTS
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_PORT_OPEN

- **ID (Int)**: 287310602
- **Hex Value**: 0x1120030A
- **AIDL Summary**: EV charge port open.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_ENERGY_PORTS (Read) AND/OR Car.PERMISSION_CONTROL_ENERGY_PORTS (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_STATE

- **ID (Int)**: 289410881
- **Hex Value**: 0x11400F41
- **AIDL Summary**: Charging state of the car.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY (Read)
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_CHARGE_SWITCH

- **ID (Int)**: 287313730
- **Hex Value**: 0x11200F42
- **AIDL Summary**: Start or stop charging the EV battery.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_ENERGY
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_TIME_REMAINING

- **ID (Int)**: 289410883
- **Hex Value**: 0x11400F43
- **AIDL Summary**: Estimated charge time remaining in seconds.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY (Read)
- **Default Value**: {int32Values: [20]}
- **Data Type**: int32

### Property Name: EV_CURRENT_BATTERY_CAPACITY

- **ID (Int)**: 291504909
- **Hex Value**: 0x1160030D
- **AIDL Summary**: Current battery capacity for EV or hybrid vehicle.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: EV_REGENERATIVE_BRAKING_STATE

- **ID (Int)**: 289410884
- **Hex Value**: 0x11400F44
- **AIDL Summary**: Regenerative braking or one-pedal drive state of the car.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY (Read)
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_STOPPING_MODE

- **ID (Int)**: 289408013
- **Hex Value**: 0x1140040D
- **AIDL Summary**: Represents property for the current stopping mode of the vehicle.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_POWERTRAIN (Read) AND/OR Car.PERMISSION_CONTROL_POWERTRAIN (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::EV_STOPPING_MODE_CREEP]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_ENABLED

- **ID (Int)**: 287313922
- **Hex Value**: 0x11201002
- **AIDL Summary**: Enable or disable Forward Collision Warning (FCW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_STATE

- **ID (Int)**: 289411075
- **Hex Value**: 0x11401003
- **AIDL Summary**: Forward Collision Warning State (FCW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [ForwardCollisionWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_STATE

- **ID (Int)**: 289410875
- **Hex Value**: 0x11400F3B
- **AIDL Summary**: Front fog lights state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_SWITCH

- **ID (Int)**: 289410876
- **Hex Value**: 0x11400F3C
- **AIDL Summary**: Front fog lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: FUEL_CONSUMPTION_UNITS_DISTANCE_OVER_VOLUME

- **ID (Int)**: 287311364
- **Hex Value**: 0x11200604
- **AIDL Summary**: Fuel consumption units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FUEL_DOOR_OPEN

- **ID (Int)**: 287310600
- **Hex Value**: 0x11200308
- **AIDL Summary**: Fuel door open.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_ENERGY_PORTS (Read) AND/OR Car.PERMISSION_CONTROL_ENERGY_PORTS (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_LEVEL

- **ID (Int)**: 291504903
- **Hex Value**: 0x11600307
- **AIDL Summary**: Fuel remaining in the vehicle in milliliters.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Default Value**: {floatValues: [15000.0]}
- **Data Type**: float

### Property Name: FUEL_LEVEL_LOW

- **ID (Int)**: 287310853
- **Hex Value**: 0x11200405
- **AIDL Summary**: Warning for fuel low level.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_VOLUME_DISPLAY_UNITS

- **ID (Int)**: 289408513
- **Hex Value**: 0x11400601
- **AIDL Summary**: Fuel volume units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::US_GALLON]}
- **Data Type**: int32

### Property Name: GEAR_SELECTION

- **ID (Int)**: 289408000
- **Hex Value**: 0x11400400
- **AIDL Summary**: Currently selected gear by user.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Data Type**: int32

### Property Name: GENERAL_SAFETY_REGULATION_COMPLIANCE_REQUIREMENT

- **Default Value**: {int32Values: [GsrComplianceRequirementType::GSR_COMPLIANCE_REQUIRED_V1]}
- **Data Type**: int32

### Property Name: GLOVE_BOX_DOOR_POS

- **ID (Int)**: 356518896
- **Hex Value**: 0x15400BF0
- **AIDL Summary**: Property that represents the current position of the glove box door.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_GLOVE_BOX
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: GLOVE_BOX_LOCKED

- **ID (Int)**: 354421745
- **Hex Value**: 0x15200BF1
- **AIDL Summary**: Lock or unlock the glove box.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_GLOVE_BOX
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_DRIVER_STATE

- **ID (Int)**: 289411095
- **Hex Value**: 0x11401017
- **AIDL Summary**: Hands On Detection (HOD) driver state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [HandsOnDetectionDriverState::HANDS_ON]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_ENABLED

- **ID (Int)**: 287313942
- **Hex Value**: 0x11201016
- **AIDL Summary**: Enable or disable Hands On Detection (HOD).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_DRIVER_MONITORING_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_WARNING

- **ID (Int)**: 289411096
- **Hex Value**: 0x11401018
- **AIDL Summary**: Hands On Detection (HOD) warning.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [HandsOnDetectionWarning::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_STATE

- **ID (Int)**: 289410563
- **Hex Value**: 0x11400E03
- **AIDL Summary**: Hazard light status.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_SWITCH

- **ID (Int)**: 289410579
- **Hex Value**: 0x11400E13
- **AIDL Summary**: Hazard light switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_STATE

- **ID (Int)**: 289410560
- **Hex Value**: 0x11400E00
- **AIDL Summary**: Headlights State.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_SWITCH

- **ID (Int)**: 289410576
- **Hex Value**: 0x11400E10
- **AIDL Summary**: Headlight switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_STATE

- **ID (Int)**: 289410561
- **Hex Value**: 0x11400E01
- **AIDL Summary**: High beam lights state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_SWITCH

- **ID (Int)**: 289410577
- **Hex Value**: 0x11400E11
- **AIDL Summary**: High beam light switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HVAC_ACTUAL_FAN_SPEED_RPM

- **ID (Int)**: 356517135
- **Hex Value**: 0x1540050F
- **AIDL Summary**: Actual fan speed.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [50]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AC_ON

- **ID (Int)**: 354419973
- **Hex Value**: 0x15200505
- **AIDL Summary**: On/off AC for designated areaId.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AUTO_ON

- **ID (Int)**: 354419978
- **Hex Value**: 0x1520050A
- **AIDL Summary**: On/off automatic mode.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AUTO_RECIRC_ON

- **ID (Int)**: 354419986
- **Hex Value**: 0x15200512
- **AIDL Summary**: Automatic recirculation on/off.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_DEFROSTER

- **ID (Int)**: 320865540
- **Hex Value**: 0x13200504
- **AIDL Summary**: On/off defrost for designated window.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: HVAC_DUAL_ON

- **ID (Int)**: 354419977
- **Hex Value**: 0x15200509
- **AIDL Summary**: Enable temperature coupling between areas.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::HVAC_ALL
- **Data Type**: int32

### Property Name: HVAC_ELECTRIC_DEFROSTER_ON

- **ID (Int)**: 320865556
- **Hex Value**: 0x13200514
- **AIDL Summary**: ELECTRIC DEFROSTER.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION

- **ID (Int)**: 356517121
- **Hex Value**: 0x15400501
- **AIDL Summary**: Fan direction setting.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleHvacFanDirection::FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION_AVAILABLE

- **ID (Int)**: 356582673
- **Hex Value**: 0x15410511
- **AIDL Summary**: Fan Positions Available.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::FAN_DIRECTION_UNKNOWN, Constants::FAN_DIRECTION_FACE, Constants::FAN_DIRECTION_FLOOR, Constants::FAN_DIRECTION_FACE_FLOOR, Constants::FAN_DIRECTION_DEFROST, Constants::FAN_DIRECTION_FACE_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST_FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_FAN_SPEED

- **ID (Int)**: 356517120
- **Hex Value**: 0x15400500
- **AIDL Summary**: Fan speed setting.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [3]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
- **Data Type**: int32

### Property Name: HVAC_MAX_AC_ON

- **ID (Int)**: 354419974
- **Hex Value**: 0x15200506
- **AIDL Summary**: On/off max AC.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_MAX_DEFROST_ON

- **ID (Int)**: 354419975
- **Hex Value**: 0x15200507
- **AIDL Summary**: On/off max defrost.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_POWER_ON

- **ID (Int)**: 354419984
- **Hex Value**: 0x15200510
- **AIDL Summary**: Represents global power state for HVAC.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_RECIRC_ON

- **ID (Int)**: 354419976
- **Hex Value**: 0x15200508
- **AIDL Summary**: Recirculation on/off.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_SEAT_TEMPERATURE

- **ID (Int)**: 356517131
- **Hex Value**: 0x1540050B
- **AIDL Summary**: Seat heating/cooling.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_SEAT_VENTILATION

- **ID (Int)**: 356517139
- **Hex Value**: 0x15400513
- **AIDL Summary**: Seat ventilation.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: HVAC_SIDE_MIRROR_HEAT

- **ID (Int)**: 339739916
- **Hex Value**: 0x1440050C
- **AIDL Summary**: Side Mirror Heat.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_STEERING_WHEEL_HEAT

- **ID (Int)**: 289408269
- **Hex Value**: 0x1140050D
- **AIDL Summary**: Steering Wheel Heating/Cooling.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_CURRENT

- **ID (Int)**: 358614274
- **Hex Value**: 0x15600502
- **AIDL Summary**: HVAC current temperature in celsius.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [19.1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: float

### Property Name: HVAC_TEMPERATURE_DISPLAY_UNITS

- **ID (Int)**: 289408270
- **Hex Value**: 0x1140050E
- **AIDL Summary**: Temperature units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: anyOf = {Car.PERMISSION_READ_DISPLAY_UNITS,
            Car.PERMISSION_CONTROL_CAR_CLIMATE} (Read) AND/OR Car.PERMISSION_CONTROL_CAR_CLIMATE (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::FAHRENHEIT]}
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_SET

- **ID (Int)**: 358614275
- **Hex Value**: 0x15600503
- **AIDL Summary**: HVAC, target temperature set.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [17.0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
- **Data Type**: float

### Property Name: HVAC_TEMPERATURE_VALUE_SUGGESTION

- **ID (Int)**: 291570965
- **Hex Value**: 0x11610515
- **AIDL Summary**: Suggested values for setting HVAC temperature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [66.19999694824219, VehicleUnit::FAHRENHEIT, 19.0, 66.0]}
- **Data Type**: float

### Property Name: HW_CUSTOM_INPUT

- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32

### Property Name: HW_KEY_INPUT

- **ID (Int)**: 289475088
- **Hex Value**: 0x11410A10
- **AIDL Summary**: Property to feed H/W input events to android.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32

### Property Name: HW_KEY_INPUT_V2

- **Default Value**: {int32Values: [0, 0, 0, 0], 'int64Values': [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HW_MOTION_INPUT

- **Default Value**: {int32Values: [0, 0, 0, 0, 1, 0, 0], 'floatValues': [0, 0, 0, 0], 'int64Values': [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HW_ROTARY_INPUT

- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32

### Property Name: IGNITION_STATE

- **ID (Int)**: 289408009
- **Hex Value**: 0x11400409
- **AIDL Summary**: Vehicle's ignition state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Default Value**: {int32Values: [VehicleIgnitionState::ON]}
- **Data Type**: int32

### Property Name: INFO_DRIVER_SEAT

- **ID (Int)**: 356516106
- **Hex Value**: 0x1540010A
- **AIDL Summary**: Driver's seat location.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [Constants::SEAT_1_LEFT]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: INFO_EV_BATTERY_CAPACITY

- **ID (Int)**: 291504390
- **Hex Value**: 0x11600106
- **AIDL Summary**: Nominal battery capacity for EV or hybrid vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: INFO_EV_CONNECTOR_TYPE

- **ID (Int)**: 289472775
- **Hex Value**: 0x11410107
- **AIDL Summary**: List of {@link android.car.hardware.property.EvChargingConnectorType}s this vehicle may use.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [EvConnectorType::IEC_TYPE_1_AC]}
- **Data Type**: int32

### Property Name: INFO_EV_PORT_LOCATION

- **ID (Int)**: 289407241
- **Hex Value**: 0x11400109
- **AIDL Summary**: {@link PortLocationType} for the EV port location.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT]}
- **Data Type**: int32

### Property Name: INFO_EXTERIOR_DIMENSIONS

- **ID (Int)**: 289472779
- **Hex Value**: 0x1141010B
- **AIDL Summary**: Vehicle's exterior dimensions in millimeters.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [1776, 4950, 2008, 2140, 2984, 1665, 1667, 11800]}
- **Data Type**: int32

### Property Name: INFO_FUEL_CAPACITY

- **ID (Int)**: 291504388
- **Hex Value**: 0x11600104
- **AIDL Summary**: Fuel capacity of the vehicle in milliliters.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {floatValues: [15000.0]}
- **Data Type**: float

### Property Name: INFO_FUEL_DOOR_LOCATION

- **ID (Int)**: 289407240
- **Hex Value**: 0x11400108
- **AIDL Summary**: {@link PortLocationType} for the fuel door location.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [Constants::FUEL_DOOR_REAR_LEFT]}
- **Data Type**: int32

### Property Name: INFO_FUEL_TYPE

- **ID (Int)**: 289472773
- **Hex Value**: 0x11410105
- **AIDL Summary**: List the {@link FuelType}s the vehicle may use.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [FuelType::FUEL_TYPE_UNLEADED]}
- **Data Type**: int32

### Property Name: INFO_MAKE

- **ID (Int)**: 286261505
- **Hex Value**: 0x11100101
- **AIDL Summary**: Manufacturer of vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {stringValue: 'Toy Vehicle'}
- **Data Type**: string

### Property Name: INFO_MODEL

- **ID (Int)**: 286261506
- **Hex Value**: 0x11100102
- **AIDL Summary**: Model of vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {stringValue: 'Speedy Model'}
- **Data Type**: string

### Property Name: INFO_MODEL_YEAR

- **ID (Int)**: 289407235
- **Hex Value**: 0x11400103
- **AIDL Summary**: Model year of vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [2023]}
- **Data Type**: int32

### Property Name: INFO_MULTI_EV_PORT_LOCATIONS

- **ID (Int)**: 289472780
- **Hex Value**: 0x1141010C
- **AIDL Summary**: List {@link PortLocationType}s for Multiple EV port locations.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT, Constants::CHARGE_PORT_REAR_LEFT]}
- **Data Type**: int32

### Property Name: INFO_VIN

- **ID (Int)**: 286261504
- **Hex Value**: 0x11100100
- **AIDL Summary**: VIN of vehicle
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_IDENTIFICATION
- **Protection Level**: signature|privileged
- **Default Value**: {stringValue: '1GCARVIN123456789'}
- **Data Type**: string

### Property Name: INITIAL_USER_INFO

- **ID (Int)**: 299896583
- **Hex Value**: 0x11E00F07
- **AIDL Summary**: Property to get the initial settings for multi-user management (such as initial user).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Data Type**: unknown

### Property Name: LANE_CENTERING_ASSIST_COMMAND

- **ID (Int)**: 289411083
- **Hex Value**: 0x1140100B
- **AIDL Summary**: Lane Centering Assist (LCA) commands.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES (Write)
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: LANE_CENTERING_ASSIST_ENABLED

- **ID (Int)**: 287313930
- **Hex Value**: 0x1120100A
- **AIDL Summary**: Enable or disable Lane Centering Assist (LCA).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_CENTERING_ASSIST_STATE

- **ID (Int)**: 289411084
- **Hex Value**: 0x1140100C
- **AIDL Summary**: Lane Centering Assist (LCA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [LaneCenteringAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_ENABLED

- **ID (Int)**: 287313926
- **Hex Value**: 0x11201006
- **AIDL Summary**: Enable or disable Lane Departure Warning (LDW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_STATE

- **ID (Int)**: 289411079
- **Hex Value**: 0x11401007
- **AIDL Summary**: Lane Departure Warning (LDW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [LaneDepartureWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_ENABLED

- **ID (Int)**: 287313928
- **Hex Value**: 0x11201008
- **AIDL Summary**: Enable or disable Lane Keep Assist (LKA).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_ADAS_SETTINGS (Read) AND/OR Car.PERMISSION_CONTROL_ADAS_SETTINGS (Write)
- **Protection Level**: system
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_STATE

- **ID (Int)**: 289411081
- **Hex Value**: 0x11401009
- **AIDL Summary**: Lane Keep Assist (LKA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [LaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LOCATION_CHARACTERIZATION

- **ID (Int)**: 289410064
- **Hex Value**: 0x11400C10
- **AIDL Summary**: Characterization of inputs used for computing location.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: ACCESS_FINE_LOCATION
- **Protection Level**: system
- **Default Value**: {int32Values: [LocationCharacterization::RAW_GNSS_ONLY]}
- **Data Type**: int32

### Property Name: MIRROR_AUTO_FOLD_ENABLED

- **ID (Int)**: 337644358
- **Hex Value**: 0x14200B46
- **AIDL Summary**: Represents property for the Mirror Auto Fold feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_AUTO_TILT_ENABLED

- **ID (Int)**: 337644359
- **Hex Value**: 0x14200B47
- **AIDL Summary**: Represents property for the Mirror Auto Tilt feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_FOLD

- **ID (Int)**: 287312709
- **Hex Value**: 0x11200B45
- **AIDL Summary**: Mirror Fold.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_LOCK

- **ID (Int)**: 287312708
- **Hex Value**: 0x11200B44
- **AIDL Summary**: Mirror Lock.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_Y_MOVE

- **ID (Int)**: 339741507
- **Hex Value**: 0x14400B43
- **AIDL Summary**: Mirror Y Move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: MIRROR_Y_POS

- **ID (Int)**: 339741506
- **Hex Value**: 0x14400B42
- **AIDL Summary**: Mirror Y Position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
- **Data Type**: int32

### Property Name: MIRROR_Z_MOVE

- **ID (Int)**: 339741505
- **Hex Value**: 0x14400B41
- **AIDL Summary**: Mirror Z Move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: MIRROR_Z_POS

- **ID (Int)**: 339741504
- **Hex Value**: 0x14400B40
- **AIDL Summary**: Mirror Z Position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
- **Data Type**: int32

### Property Name: NIGHT_MODE

- **ID (Int)**: 287310855
- **Hex Value**: 0x11200407
- **AIDL Summary**: Night mode.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_ENVIRONMENT
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: OBD2_FREEZE_FRAME

- **ID (Int)**: 299896065
- **Hex Value**: 0x11E00D01
- **AIDL Summary**: OBD2 Freeze Frame Sensor Data.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: OBD2_FREEZE_FRAME_CLEAR

- **ID (Int)**: 299896067
- **Hex Value**: 0x11E00D03
- **AIDL Summary**: OBD2 Freeze Frame Clear.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_CLEAR
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: OBD2_FREEZE_FRAME_INFO

- **ID (Int)**: 299896066
- **Hex Value**: 0x11E00D02
- **AIDL Summary**: OBD2 Freeze Frame Information.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: OBD2_LIVE_FRAME

- **ID (Int)**: 299896064
- **Hex Value**: 0x11E00D00
- **AIDL Summary**: OBD2 Live Sensor Data.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: PARKING_BRAKE_AUTO_APPLY

- **ID (Int)**: 287310851
- **Hex Value**: 0x11200403
- **AIDL Summary**: Auto-apply parking brake.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PARKING_BRAKE_ON

- **ID (Int)**: 287310850
- **Hex Value**: 0x11200402
- **AIDL Summary**: Parking brake state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PERF_ODOMETER

- **ID (Int)**: 291504644
- **Hex Value**: 0x11600204
- **AIDL Summary**: Current odometer value of the vehicle in kilometers.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_MILEAGE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_REAR_STEERING_ANGLE

- **ID (Int)**: 291504656
- **Hex Value**: 0x11600210
- **AIDL Summary**: Rear bicycle model steering angle for vehicle in degrees.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_STEERING_STATE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_STEERING_ANGLE

- **ID (Int)**: 291504649
- **Hex Value**: 0x11600209
- **AIDL Summary**: Front bicycle model steering angle for vehicle in degrees.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_STEERING_STATE
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED

- **ID (Int)**: 291504647
- **Hex Value**: 0x11600207
- **AIDL Summary**: Speed of the vehicle in meters per second.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED_DISPLAY

- **ID (Int)**: 291504648
- **Hex Value**: 0x11600208
- **AIDL Summary**: Speed of the vehicle in meters per second for displays.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: POWER_POLICY_GROUP_REQ

- **ID (Int)**: 286265122
- **Hex Value**: 0x11100F22
- **AIDL Summary**: Property for VHAL to set the default power policies per power status transition.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Data Type**: unknown

### Property Name: POWER_POLICY_REQ

- **ID (Int)**: 286265121
- **Hex Value**: 0x11100F21
- **AIDL Summary**: Property for VHAL to apply power policy.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Data Type**: unknown

### Property Name: RANGE_REMAINING

- **ID (Int)**: 291504904
- **Hex Value**: 0x11600308
- **AIDL Summary**: Range remaining in meters.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_ENERGY (Read) AND/OR Car.PERMISSION_ADJUST_RANGE_REMAINING (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [50000.0]}
- **Data Type**: float

### Property Name: READING_LIGHTS_STATE

- **ID (Int)**: 356519683
- **Hex Value**: 0x15400F03
- **AIDL Summary**: Reading lights.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: READING_LIGHTS_SWITCH

- **ID (Int)**: 356519684
- **Hex Value**: 0x15400F04
- **AIDL Summary**: Reading lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_STATE

- **ID (Int)**: 289410877
- **Hex Value**: 0x11400F3D
- **AIDL Summary**: Rear fog lights state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_SWITCH

- **ID (Int)**: 289410878
- **Hex Value**: 0x11400F3E
- **AIDL Summary**: Rear fog lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: REMOVE_USER

- **ID (Int)**: 299896586
- **Hex Value**: 0x11E00F0A
- **AIDL Summary**: Property to remove a new user for multi-user management.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: SEAT_AIRBAG_ENABLED

- **ID (Int)**: 354421662
- **Hex Value**: 0x15200B9E
- **AIDL Summary**: Represents feature to enable/disable a seat's ability to deploy airbag(s) when triggered (e.g. by a crash).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_AIRBAGS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_1_MOVE

- **ID (Int)**: 356518792
- **Hex Value**: 0x15400B88
- **AIDL Summary**: Seat backrest angle 1 move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_1_POS

- **ID (Int)**: 356518791
- **Hex Value**: 0x15400B87
- **AIDL Summary**: Seat backrest angle 1 position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_2_MOVE

- **ID (Int)**: 356518794
- **Hex Value**: 0x15400B8A
- **AIDL Summary**: Seat backrest angle 2 move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_2_POS

- **ID (Int)**: 356518793
- **Hex Value**: 0x15400B89
- **AIDL Summary**: Seat backrest angle 2 position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_BELT_BUCKLED

- **ID (Int)**: 354421634
- **Hex Value**: 0x15200B82
- **AIDL Summary**: Seatbelt buckled.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_BELT_HEIGHT_MOVE

- **ID (Int)**: 356518788
- **Hex Value**: 0x15400B84
- **AIDL Summary**: Seatbelt height move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BELT_HEIGHT_POS

- **ID (Int)**: 356518787
- **Hex Value**: 0x15400B83
- **AIDL Summary**: Seatbelt height position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [10]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_CUSHION_SIDE_SUPPORT_MOVE

- **ID (Int)**: 356518816
- **Hex Value**: 0x15400BA0
- **AIDL Summary**: Represents property for movement direction and speed of seat cushion side support.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_CUSHION_SIDE_SUPPORT_POS

- **ID (Int)**: 356518815
- **Hex Value**: 0x15400B9F
- **AIDL Summary**: Represents property for seat’s hipside (bottom cushion’s side) support position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_DEPTH_MOVE

- **ID (Int)**: 356518798
- **Hex Value**: 0x15400B8E
- **AIDL Summary**: Seat depth move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_DEPTH_POS

- **ID (Int)**: 356518797
- **Hex Value**: 0x15400B8D
- **AIDL Summary**: Seat depth position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_EASY_ACCESS_ENABLED

- **ID (Int)**: 354421661
- **Hex Value**: 0x15200B9D
- **AIDL Summary**: Represents property for Seat easy access feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_STATE

- **ID (Int)**: 356518811
- **Hex Value**: 0x15400B9B
- **AIDL Summary**: Represents property for state of the footwell lights.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_SWITCH

- **ID (Int)**: 356518812
- **Hex Value**: 0x15400B9C
- **AIDL Summary**: Represents property for switch of the footwell lights.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_FORE_AFT_MOVE

- **ID (Int)**: 356518790
- **Hex Value**: 0x15400B86
- **AIDL Summary**: Seat fore/aft move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_FORE_AFT_POS

- **ID (Int)**: 356518789
- **Hex Value**: 0x15400B85
- **AIDL Summary**: Seat fore/aft position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_ANGLE_MOVE

- **ID (Int)**: 356518808
- **Hex Value**: 0x15400B98
- **AIDL Summary**: Headrest angle move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_ANGLE_POS

- **ID (Int)**: 356518807
- **Hex Value**: 0x15400B97
- **AIDL Summary**: Headrest angle position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_FORE_AFT_MOVE

- **ID (Int)**: 356518810
- **Hex Value**: 0x15400B9A
- **AIDL Summary**: Headrest fore/aft move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_FORE_AFT_POS

- **ID (Int)**: 356518809
- **Hex Value**: 0x15400B99
- **AIDL Summary**: Headrest fore/aft position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_HEIGHT_MOVE

- **ID (Int)**: 356518806
- **Hex Value**: 0x15400B96
- **AIDL Summary**: Headrest height move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_HEIGHT_POS_V2

- **ID (Int)**: 356518820
- **Hex Value**: 0x15400BA4
- **AIDL Summary**: Headrest height position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEIGHT_MOVE

- **ID (Int)**: 356518796
- **Hex Value**: 0x15400B8C
- **AIDL Summary**: Seat height move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEIGHT_POS

- **ID (Int)**: 356518795
- **Hex Value**: 0x15400B8B
- **AIDL Summary**: Seat height position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_FORE_AFT_MOVE

- **ID (Int)**: 356518802
- **Hex Value**: 0x15400B92
- **AIDL Summary**: Lumbar fore/aft move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_FORE_AFT_POS

- **ID (Int)**: 356518801
- **Hex Value**: 0x15400B91
- **AIDL Summary**: Lumber fore/aft position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_SIDE_SUPPORT_MOVE

- **ID (Int)**: 356518804
- **Hex Value**: 0x15400B94
- **AIDL Summary**: Lumbar side support move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_SIDE_SUPPORT_POS

- **ID (Int)**: 356518803
- **Hex Value**: 0x15400B93
- **AIDL Summary**: Lumbar side support position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_VERTICAL_MOVE

- **ID (Int)**: 356518818
- **Hex Value**: 0x15400BA2
- **AIDL Summary**: Represents property for vertical movement direction and speed of seat lumbar support.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_VERTICAL_POS

- **ID (Int)**: 356518817
- **Hex Value**: 0x15400BA1
- **AIDL Summary**: Represents property for seat’s lumbar support vertical position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_MEMORY_SELECT

- **ID (Int)**: 356518784
- **Hex Value**: 0x15400B80
- **AIDL Summary**: Seat memory select.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: SEAT_MEMORY_SET

- **ID (Int)**: 356518785
- **Hex Value**: 0x15400B81
- **AIDL Summary**: Seat memory set.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: SEAT_OCCUPANCY

- **ID (Int)**: 356518832
- **Hex Value**: 0x15400BB0
- **AIDL Summary**: Seat Occupancy.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleSeatOccupancyState::VACANT]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_TILT_MOVE

- **ID (Int)**: 356518800
- **Hex Value**: 0x15400B90
- **AIDL Summary**: Seat tilt move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_TILT_POS

- **ID (Int)**: 356518799
- **Hex Value**: 0x15400B8F
- **AIDL Summary**: Seat tilt position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_WALK_IN_POS

- **ID (Int)**: 356518819
- **Hex Value**: 0x15400BA3
- **AIDL Summary**: Represents property that indicates the current walk-in position of the seat.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_MOVE

- **ID (Int)**: 289410017
- **Hex Value**: 0x11400BE1
- **AIDL Summary**: Steering wheel depth movement.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_POS

- **ID (Int)**: 289410016
- **Hex Value**: 0x11400BE0
- **AIDL Summary**: Steering wheel depth position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_EASY_ACCESS_ENABLED

- **ID (Int)**: 287312870
- **Hex Value**: 0x11200BE6
- **AIDL Summary**: Steering wheel easy access feature enabled.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_MOVE

- **ID (Int)**: 289410019
- **Hex Value**: 0x11400BE3
- **AIDL Summary**: Steering wheel height movement.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_POS

- **ID (Int)**: 289410018
- **Hex Value**: 0x11400BE2
- **AIDL Summary**: Steering wheel height position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_STATE

- **ID (Int)**: 289410828
- **Hex Value**: 0x11400F0C
- **AIDL Summary**: Steering wheel lights state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_SWITCH

- **ID (Int)**: 289410829
- **Hex Value**: 0x11400F0D
- **AIDL Summary**: Steering wheel lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LOCKED

- **ID (Int)**: 287312869
- **Hex Value**: 0x11200BE5
- **AIDL Summary**: Steering wheel locked.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_THEFT_LOCK_ENABLED

- **ID (Int)**: 287312868
- **Hex Value**: 0x11200BE4
- **AIDL Summary**: Steering wheel theft lock feature enabled.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: SWITCH_USER

- **ID (Int)**: 299896584
- **Hex Value**: 0x11E00F08
- **AIDL Summary**: Property to switch user for multi-user management.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Data Type**: unknown

### Property Name: TIRE_PRESSURE

- **ID (Int)**: 392168201
- **Hex Value**: 0x17600309
- **AIDL Summary**: Tire pressure in kilopascals.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_TIRES
- **Protection Level**: signature|privileged
- **Default Value**: {floatValues: [200.0]}
- **Areas**:
  - **Area ID**: Constants::WHEEL_FRONT_LEFT
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_FRONT_RIGHT
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_REAR_LEFT
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_REAR_RIGHT
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
- **Data Type**: float

### Property Name: TIRE_PRESSURE_DISPLAY_UNITS

- **ID (Int)**: 289408514
- **Hex Value**: 0x11400602
- **AIDL Summary**: Tire pressure units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::PSI]}
- **Data Type**: int32

### Property Name: TRACTION_CONTROL_ACTIVE

- **ID (Int)**: 287310859
- **Hex Value**: 0x1120040B
- **AIDL Summary**: Traction Control is active.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DYNAMICS_STATE
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: TRAILER_PRESENT

- **ID (Int)**: 289410885
- **Hex Value**: 0x11400F45
- **AIDL Summary**: Indicates if there is a trailer present or not.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_PRIVILEGED_CAR_INFO (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: TURN_SIGNAL_STATE

- **ID (Int)**: 289408008
- **Hex Value**: 0x11400408
- **AIDL Summary**: State of the vehicles turn signals
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleTurnSignal::NONE]}
- **Data Type**: int32

### Property Name: USER_IDENTIFICATION_ASSOCIATION

- **ID (Int)**: 299896587
- **Hex Value**: 0x11E00F0B
- **AIDL Summary**: Property to get / set the user authentication types associated with an Android user.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Data Type**: unknown

### Property Name: VEHICLE_CURB_WEIGHT

- **ID (Int)**: 289410886
- **Hex Value**: 0x11400F46
- **AIDL Summary**: Vehicle’s curb weight.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_PRIVILEGED_CAR_INFO (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [2211]}
- **Data Type**: int32

### Property Name: VEHICLE_MAP_SERVICE

- **ID (Int)**: 299895808
- **Hex Value**: 0x11E00C00
- **AIDL Summary**: Vehicle Maps Service (VMS) message.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: anyOf = {Car.PERMISSION_VMS_PUBLISHER, Car.PERMISSION_VMS_SUBSCRIBER}
- **Protection Level**: signature|privileged
- **Data Type**: unknown

### Property Name: VEHICLE_SPEED_DISPLAY_UNITS

- **ID (Int)**: 289408516
- **Hex Value**: 0x11400604
- **AIDL Summary**: Speed units for display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_DISPLAY_UNITS (Read) AND/OR allOf = {Car.PERMISSION_CONTROL_DISPLAY_UNITS,
            Car.PERMISSION_VENDOR_EXTENSION} (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [VehicleUnit::MILES_PER_HOUR]}
- **Data Type**: int32

### Property Name: VHAL_HEARTBEAT

- **ID (Int)**: 290459443
- **Hex Value**: 0x11500F33
- **AIDL Summary**: Property to signal a heartbeat from VHAL.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Data Type**: unknown

### Property Name: WATCHDOG_ALIVE

- **ID (Int)**: 290459441
- **Hex Value**: 0x11500F31
- **AIDL Summary**: Property to report that car watchdog is alive.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: WATCHDOG_TERMINATED_PROCESS

- **ID (Int)**: 299896626
- **Hex Value**: 0x11E00F32
- **AIDL Summary**: Property to report a process terminated by car watchdog.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Type**: unknown

### Property Name: WHEEL_TICK

- **ID (Int)**: 290521862
- **Hex Value**: 0x11510306
- **AIDL Summary**: Reports wheel ticks.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Default Value**: {int64Values: [0, 100000, 200000, 300000, 400000]}
- **Data Type**: int64

### Property Name: WINDOW_LOCK

- **ID (Int)**: 320867268
- **Hex Value**: 0x13200BC4
- **AIDL Summary**: Window Lock.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Protection Level**: signature|privileged
- **Areas**:
  - **Area ID**: Constants::WINDOW_1_RIGHT_2_LEFT_2_RIGHT
  - **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: WINDOW_MOVE

- **ID (Int)**: 322964417
- **Hex Value**: 0x13400BC1
- **AIDL Summary**: Window Move.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::WINDOW_1_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::WINDOW_1_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::WINDOW_2_LEFT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::WINDOW_2_RIGHT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::WINDOW_ROOF_TOP_1
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: WINDOW_POS

- **ID (Int)**: 322964416
- **Hex Value**: 0x13400BC0
- **AIDL Summary**: Window Position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::WINDOW_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::WINDOW_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::WINDOW_2_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::WINDOW_2_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::WINDOW_ROOF_TOP_1
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_PERIOD

- **ID (Int)**: 322964421
- **Hex Value**: 0x13400BC5
- **AIDL Summary**: Windshield wipers period (milliseconds).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_WINDSHIELD_WIPERS (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_STATE

- **ID (Int)**: 322964422
- **Hex Value**: 0x13400BC6
- **AIDL Summary**: Windshield wipers state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_WINDSHIELD_WIPERS (Read)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [WindshieldWipersState::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_SWITCH

- **ID (Int)**: 322964423
- **Hex Value**: 0x13400BC7
- **AIDL Summary**: Windshield wipers switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_READ_WINDSHIELD_WIPERS (Read) AND/OR Car.PERMISSION_CONTROL_WINDSHIELD_WIPERS (Write)
- **Protection Level**: signature|privileged
- **Default Value**: {int32Values: [WindshieldWipersSwitch::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32
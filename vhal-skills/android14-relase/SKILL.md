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

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_LEAD_VEHICLE_MEASURED_DISTANCE

- **Default Value**: {int32Values: [100000]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 200000
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_TARGET_TIME_GAP

- **Default Value**: {int32Values: [1200]}
- **Data Type**: int32

### Property Name: ANDROID_EPOCH_TIME

- **Data Type**: unknown

### Property Name: AP_POWER_STATE_REPORT

- **Default Value**: {int32Values: [VehicleApPowerStateReport::WAIT_FOR_VHAL, 0]}
- **Data Type**: int32

### Property Name: AP_POWER_STATE_REQ

- **Data Type**: unknown

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_ENABLED

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_STATE

- **Default Value**: {int32Values: [ErrorState::NOT_AVAILABLE_DISABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_STATE

- **Default Value**: {int32Values: [BlindSpotWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Data Type**: int32

### Property Name: CLUSTER_DISPLAY_STATE

- **Default Value**: {int32Values: [0, -1, -1, -1, -1, -1, -1, -1, -1]}
- **Data Type**: int32

### Property Name: CLUSTER_NAVIGATION_STATE

- **Data Type**: unknown

### Property Name: CLUSTER_REPORT_STATE

- **Data Type**: unknown

### Property Name: CLUSTER_REQUEST_DISPLAY

- **Data Type**: unknown

### Property Name: CLUSTER_SWITCH_UI

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: CREATE_USER

- **Data Type**: unknown

### Property Name: CRITICALLY_LOW_TIRE_PRESSURE

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

- **Areas**:
  - **Area ID**: 0
- **Data Type**: unknown

### Property Name: CRUISE_CONTROL_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_STATE

- **Default Value**: {int32Values: [CruiseControlState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_TARGET_SPEED

- **Default Value**: {floatValues: [25.0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minFloatValue: 20.0, maxFloatValue: 35.0
- **Data Type**: float

### Property Name: CRUISE_CONTROL_TYPE

- **Default Value**: {int32Values: [CruiseControlType::STANDARD]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: CURRENT_GEAR

- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Data Type**: int32

### Property Name: CURRENT_POWER_POLICY

- **Data Type**: unknown

### Property Name: DISPLAY_BRIGHTNESS

- **Default Value**: {int32Values: [100]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 100
- **Data Type**: int32

### Property Name: DISTANCE_DISPLAY_UNITS

- **Default Value**: {int32Values: [VehicleUnit::MILE]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: DOOR_CHILD_LOCK_ENABLED

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area ID**: Constants::DOOR_2_RIGHT
- **Data Type**: int32

### Property Name: DOOR_LOCK

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

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ELECTRONIC_TOLL_COLLECTION_CARD_TYPE

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_STATE

- **Default Value**: {int32Values: [EmergencyLaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: ENGINE_COOLANT_TEMP

- **Default Value**: {floatValues: [75.0]}
- **Data Type**: float

### Property Name: ENGINE_IDLE_AUTO_STOP_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_LEVEL

- **Default Value**: {int32Values: [VehicleOilLevel::NORMAL]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_TEMP

- **Default Value**: {floatValues: [101.0]}
- **Data Type**: float

### Property Name: ENGINE_RPM

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: ENV_OUTSIDE_TEMPERATURE

- **Default Value**: {floatValues: [25.0]}
- **Data Type**: float

### Property Name: EVS_SERVICE_REQUEST

- **Default Value**: {int32Values: [EvsServiceType::REARVIEW, EvsServiceState::OFF]}
- **Data Type**: int32

### Property Name: EV_BATTERY_DISPLAY_UNITS

- **Default Value**: {int32Values: [VehicleUnit::KILOWATT_HOUR]}
- **Data Type**: int32

### Property Name: EV_BATTERY_INSTANTANEOUS_CHARGE_RATE

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: EV_BATTERY_LEVEL

- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: EV_BRAKE_REGENERATION_LEVEL

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: EV_CHARGE_CURRENT_DRAW_LIMIT

- **Default Value**: {floatValues: [12.5]}
- **Data Type**: float

### Property Name: EV_CHARGE_PERCENT_LIMIT

- **Default Value**: {floatValues: [40.0]}
- **Data Type**: float

### Property Name: EV_CHARGE_PORT_CONNECTED

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_PORT_OPEN

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_STATE

- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_CHARGE_SWITCH

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_TIME_REMAINING

- **Default Value**: {int32Values: [20]}
- **Data Type**: int32

### Property Name: EV_CURRENT_BATTERY_CAPACITY

- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: EV_REGENERATIVE_BRAKING_STATE

- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_STOPPING_MODE

- **Default Value**: {int32Values: [Constants::EV_STOPPING_MODE_CREEP]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_STATE

- **Default Value**: {int32Values: [ForwardCollisionWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: FUEL_CONSUMPTION_UNITS_DISTANCE_OVER_VOLUME

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FUEL_DOOR_OPEN

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_LEVEL

- **Default Value**: {floatValues: [15000.0]}
- **Data Type**: float

### Property Name: FUEL_LEVEL_LOW

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_VOLUME_DISPLAY_UNITS

- **Default Value**: {int32Values: [VehicleUnit::US_GALLON]}
- **Data Type**: int32

### Property Name: GEAR_SELECTION

- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Data Type**: int32

### Property Name: GENERAL_SAFETY_REGULATION_COMPLIANCE_REQUIREMENT

- **Default Value**: {int32Values: [GsrComplianceRequirementType::GSR_COMPLIANCE_REQUIRED_V1]}
- **Data Type**: int32

### Property Name: GLOVE_BOX_DOOR_POS

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: GLOVE_BOX_LOCKED

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_DRIVER_STATE

- **Default Value**: {int32Values: [HandsOnDetectionDriverState::HANDS_ON]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_WARNING

- **Default Value**: {int32Values: [HandsOnDetectionWarning::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HVAC_ACTUAL_FAN_SPEED_RPM

- **Default Value**: {int32Values: [50]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AC_ON

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AUTO_ON

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_AUTO_RECIRC_ON

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_DEFROSTER

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: HVAC_DUAL_ON

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::HVAC_ALL
- **Data Type**: int32

### Property Name: HVAC_ELECTRIC_DEFROSTER_ON

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION

- **Default Value**: {int32Values: [VehicleHvacFanDirection::FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION_AVAILABLE

- **Default Value**: {int32Values: [Constants::FAN_DIRECTION_UNKNOWN, Constants::FAN_DIRECTION_FACE, Constants::FAN_DIRECTION_FLOOR, Constants::FAN_DIRECTION_FACE_FLOOR, Constants::FAN_DIRECTION_DEFROST, Constants::FAN_DIRECTION_FACE_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST_FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_FAN_SPEED

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

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_MAX_DEFROST_ON

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_POWER_ON

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_RECIRC_ON

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: HVAC_SEAT_TEMPERATURE

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

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_STEERING_WHEEL_HEAT

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_CURRENT

- **Default Value**: {floatValues: [19.1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: float

### Property Name: HVAC_TEMPERATURE_DISPLAY_UNITS

- **Default Value**: {int32Values: [VehicleUnit::FAHRENHEIT]}
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_SET

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

- **Default Value**: {floatValues: [66.19999694824219, VehicleUnit::FAHRENHEIT, 19.0, 66.0]}
- **Data Type**: float

### Property Name: HW_CUSTOM_INPUT

- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32

### Property Name: HW_KEY_INPUT

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

- **Default Value**: {int32Values: [VehicleIgnitionState::ON]}
- **Data Type**: int32

### Property Name: INFO_DRIVER_SEAT

- **Default Value**: {int32Values: [Constants::SEAT_1_LEFT]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: INFO_EV_BATTERY_CAPACITY

- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: INFO_EV_CONNECTOR_TYPE

- **Default Value**: {int32Values: [EvConnectorType::IEC_TYPE_1_AC]}
- **Data Type**: int32

### Property Name: INFO_EV_PORT_LOCATION

- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT]}
- **Data Type**: int32

### Property Name: INFO_EXTERIOR_DIMENSIONS

- **Default Value**: {int32Values: [1776, 4950, 2008, 2140, 2984, 1665, 1667, 11800]}
- **Data Type**: int32

### Property Name: INFO_FUEL_CAPACITY

- **Default Value**: {floatValues: [15000.0]}
- **Data Type**: float

### Property Name: INFO_FUEL_DOOR_LOCATION

- **Default Value**: {int32Values: [Constants::FUEL_DOOR_REAR_LEFT]}
- **Data Type**: int32

### Property Name: INFO_FUEL_TYPE

- **Default Value**: {int32Values: [FuelType::FUEL_TYPE_UNLEADED]}
- **Data Type**: int32

### Property Name: INFO_MAKE

- **Default Value**: {stringValue: 'Toy Vehicle'}
- **Data Type**: string

### Property Name: INFO_MODEL

- **Default Value**: {stringValue: 'Speedy Model'}
- **Data Type**: string

### Property Name: INFO_MODEL_YEAR

- **Default Value**: {int32Values: [2023]}
- **Data Type**: int32

### Property Name: INFO_MULTI_EV_PORT_LOCATIONS

- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT, Constants::CHARGE_PORT_REAR_LEFT]}
- **Data Type**: int32

### Property Name: INFO_VIN

- **Default Value**: {stringValue: '1GCARVIN123456789'}
- **Data Type**: string

### Property Name: INITIAL_USER_INFO

- **Data Type**: unknown

### Property Name: LANE_CENTERING_ASSIST_COMMAND

- **Data Type**: unknown

### Property Name: LANE_CENTERING_ASSIST_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_CENTERING_ASSIST_STATE

- **Default Value**: {int32Values: [LaneCenteringAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_STATE

- **Default Value**: {int32Values: [LaneDepartureWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_STATE

- **Default Value**: {int32Values: [LaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: LOCATION_CHARACTERIZATION

- **Default Value**: {int32Values: [LocationCharacterization::RAW_GNSS_ONLY]}
- **Data Type**: int32

### Property Name: MIRROR_AUTO_FOLD_ENABLED

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_AUTO_TILT_ENABLED

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_FOLD

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_LOCK

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_Y_MOVE

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

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: OBD2_FREEZE_FRAME

- **Data Type**: unknown

### Property Name: OBD2_FREEZE_FRAME_CLEAR

- **Data Type**: unknown

### Property Name: OBD2_FREEZE_FRAME_INFO

- **Data Type**: unknown

### Property Name: OBD2_LIVE_FRAME

- **Data Type**: unknown

### Property Name: PARKING_BRAKE_AUTO_APPLY

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PARKING_BRAKE_ON

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PERF_ODOMETER

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_REAR_STEERING_ANGLE

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_STEERING_ANGLE

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED_DISPLAY

- **Default Value**: {floatValues: [0.0]}
- **Data Type**: float

### Property Name: POWER_POLICY_GROUP_REQ

- **Data Type**: unknown

### Property Name: POWER_POLICY_REQ

- **Data Type**: unknown

### Property Name: RANGE_REMAINING

- **Default Value**: {floatValues: [50000.0]}
- **Data Type**: float

### Property Name: READING_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: READING_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: REMOVE_USER

- **Data Type**: unknown

### Property Name: SEAT_AIRBAG_ENABLED

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_1_MOVE

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

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_BELT_HEIGHT_MOVE

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

- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_FORE_AFT_MOVE

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

- **Default Value**: {int32Values: [VehicleSeatOccupancyState::VACANT]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area ID**: Constants::SEAT_2_CENTER
- **Data Type**: int32

### Property Name: SEAT_TILT_MOVE

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

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_MOVE

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_POS

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_EASY_ACCESS_ENABLED

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_MOVE

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_POS

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_STATE

- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_SWITCH

- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Areas**:
  - **Area ID**: 0
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LOCKED

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_THEFT_LOCK_ENABLED

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: SWITCH_USER

- **Data Type**: unknown

### Property Name: TIRE_PRESSURE

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

- **Default Value**: {int32Values: [VehicleUnit::PSI]}
- **Data Type**: int32

### Property Name: TRACTION_CONTROL_ACTIVE

- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: TRAILER_PRESENT

- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: TURN_SIGNAL_STATE

- **Default Value**: {int32Values: [VehicleTurnSignal::NONE]}
- **Data Type**: int32

### Property Name: USER_IDENTIFICATION_ASSOCIATION

- **Data Type**: unknown

### Property Name: VEHICLE_CURB_WEIGHT

- **Default Value**: {int32Values: [2211]}
- **Data Type**: int32

### Property Name: VEHICLE_MAP_SERVICE

- **Data Type**: unknown

### Property Name: VEHICLE_SPEED_DISPLAY_UNITS

- **Default Value**: {int32Values: [VehicleUnit::MILES_PER_HOUR]}
- **Data Type**: int32

### Property Name: VHAL_HEARTBEAT

- **Data Type**: unknown

### Property Name: WATCHDOG_ALIVE

- **Data Type**: unknown

### Property Name: WATCHDOG_TERMINATED_PROCESS

- **Data Type**: unknown

### Property Name: WHEEL_TICK

- **Default Value**: {int64Values: [0, 100000, 200000, 300000, 400000]}
- **Data Type**: int64

### Property Name: WINDOW_LOCK

- **Areas**:
  - **Area ID**: Constants::WINDOW_1_RIGHT_2_LEFT_2_RIGHT
  - **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: WINDOW_MOVE

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

- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_STATE

- **Default Value**: {int32Values: [WindshieldWipersState::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_SWITCH

- **Default Value**: {int32Values: [WindshieldWipersSwitch::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
- **Data Type**: int32


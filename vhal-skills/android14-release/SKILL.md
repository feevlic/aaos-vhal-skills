---
name: android14-release
description: Use when: implementing, debugging, or mocking Android 14 VHAL properties. Includes CarPropertyManager patterns, callbackFlow strategies, and ADB terminal commands for Automotive OS.
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

5. **Strict Vendor Guardrails:** Many OEMs use custom properties starting with `0x2`. If the user asks for a property not in the properties reference, you MUST state that it is a vendor-specific property, and you MUST NOT hallucinate hex values, parameters, or types for it.
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
Many vehicle properties are zoned (e.g., they apply uniquely to specific doors, seats, or windows).
**Dynamic Area IDs**: Instead of hardcoding the static bitmasks below (which is an anti-pattern as vehicles have different hardware components), ALWAYS instruct developers to dynamically query supported areas at runtime:

```kotlin
val supportedAreaIds = carPropertyManager.getCarPropertyConfig(VehiclePropertyIds.PROPERTY_NAME)?.areaIds
```

When hardcoded testing or Area computation is required, consult this reference map to construct the correct `AreaId` integer. Never attempt to guess area IDs.
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

Consult [Properties Reference](./properties-reference.md) for the exact Property IDs, required Permissions, Configurations, and Data Types for Android 14 VHAL properties.

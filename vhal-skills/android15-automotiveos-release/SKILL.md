---
name: android15-automotiveos-release
description: Android 15 Automotive OS skill for VHAL.
---

# Android 15 Automotive OS Release Skill for VHAL

## Purpose

This skill acts as the definitive source of truth and an advanced debugging reference for Android 15 Automotive OS (AAOS) Vehicle Hardware Abstraction Layer (VHAL) properties.

**Core Objectives:**

1. **Debugging & Log Analysis:** Quickly resolve VHAL property IDs found in low-level logs (e.g., `logcat` or `dumpsys car_service`) by cross-referencing them against their Android Java Integer IDs, Hexadecimal values, and human-readable Property Names.
2. **Implementation Guidance:** Provide accurate specifications (data types, access modes, change modes, and required permissions) when building services or apps that interact with the `CarPropertyManager` API.
3. **Strict Validation:** Strictly enforce that only documented Android 15 properties are used. Do not invent, rename, or infer properties. If a property is not explicitly listed in this document, it must be treated as unavailable or unsupported.

# Properties

### Property Name: ABS_ACTIVE

- **ID (Int)**: 287310858
- **Hex Value**: 0x1120040A
- **AIDL Summary**: ABS is active
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DYNAMICS_STATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_LEAD_VEHICLE_MEASURED_DISTANCE

- **ID (Int)**: 289411093
- **Hex Value**: 0x11401015
- **AIDL Summary**: Measured distance from leading vehicle when using Adaptive Cruise Control (ACC) or
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Unit**: VehicleUnit.MILLIMETER
- **Version**: 2
- **Default Value**: {int32Values: [100000]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: 0, maxInt32Value: 200000
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: int32

### Property Name: ADAPTIVE_CRUISE_CONTROL_TARGET_TIME_GAP

- **ID (Int)**: 289411092
- **Hex Value**: 0x11401014
- **AIDL Summary**: Current target time gap for Adaptive Cruise Control (ACC) or Predictive Cruise Control in
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES
- **Unit**: VehicleUnit.MILLI_SECS
- **Version**: 2
- **Default Value**: {int32Values: [1200]}
- **Config Array**: [1200, 1400, 1600, 1800, 2000, 2200]
- **Data Type**: int32

### Property Name: ANDROID_EPOCH_TIME

- **ID (Int)**: 290457094
- **Hex Value**: 0x11500606
- **AIDL Summary**: Current date and time, encoded as Epoch time (in milliseconds).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Unit**: VehicleUnit.MILLI_SECS
- **Version**: 1
- **Data Type**: int64

### Property Name: AP_POWER_BOOTUP_REASON

- **ID (Int)**: 289409538
- **Hex Value**: 0x11400A02
- **AIDL Summary**: Property to report bootup reason for the current power on. This is a
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Version**: 1
- **Default Value**: {int32Values: [VehicleApPowerBootupReason::USER_POWER_ON]}
- **Data Type**: int32

### Property Name: AP_POWER_STATE_REPORT

- **ID (Int)**: 289475073
- **Hex Value**: 0x11410A01
- **AIDL Summary**: Property to report power state of application processor
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Version**: 1
- **Default Value**: {int32Values: [VehicleApPowerStateReport::WAIT_FOR_VHAL, 0]}
- **Data Type**: int32[]

### Property Name: AP_POWER_STATE_REQ

- **ID (Int)**: 289475072
- **Hex Value**: 0x11410A00
- **AIDL Summary**: Property to control power state of application processor
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Version**: 1
- **Config Array**: [0]
- **Data Type**: int32[]

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_ENABLED

- **ID (Int)**: 287313920
- **Hex Value**: 0x11201000
- **AIDL Summary**: Enable or disable Automatic Emergency Braking (AEB).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: AUTOMATIC_EMERGENCY_BRAKING_STATE

- **ID (Int)**: 289411073
- **Hex Value**: 0x11401001
- **AIDL Summary**: Automatic Emergency Braking (AEB) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: AutomaticEmergencyBrakingState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [ErrorState::NOT_AVAILABLE_DISABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, AutomaticEmergencyBrakingState::ENABLED, AutomaticEmergencyBrakingState::ACTIVATED, AutomaticEmergencyBrakingState::USER_OVERRIDE]
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_ENABLED

- **ID (Int)**: 287313924
- **Hex Value**: 0x11201004
- **AIDL Summary**: Enable and disable Blind Spot Warning (BSW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: BLIND_SPOT_WARNING_STATE

- **ID (Int)**: 339742725
- **Hex Value**: 0x14401005
- **AIDL Summary**: Blind Spot Warning (BSW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: BlindSpotWarningState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [BlindSpotWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area Type**: MIRROR
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, BlindSpotWarningState::NO_WARNING, BlindSpotWarningState::WARNING]
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Area Type**: MIRROR
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, BlindSpotWarningState::NO_WARNING, BlindSpotWarningState::WARNING]
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_STATE

- **ID (Int)**: 289410817
- **Hex Value**: 0x11400F01
- **AIDL Summary**: Cabin lights
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: CABIN_LIGHTS_SWITCH

- **ID (Int)**: 289410818
- **Hex Value**: 0x11400F02
- **AIDL Summary**: Cabin lights switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Data Type**: int32

### Property Name: CAMERA_SERVICE_CURRENT_STATE

- **ID (Int)**: 289476429
- **Hex Value**: 0x11410F4D
- **AIDL Summary**: Reports current state of CarEvsService types.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Enum**: CameraServiceState
- **Version**: 3
- **Default Value**: {int32Values: [CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE, CameraServiceState::UNAVAILABLE]}
- **Data Type**: int32[]

### Property Name: CLUSTER_DISPLAY_STATE

- **ID (Int)**: 289476405
- **Hex Value**: 0x11410F35
- **AIDL Summary**: Changes the state of the cluster display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Default Value**: {int32Values: [0, -1, -1, -1, -1, -1, -1, -1, -1]}
- **Comment**: Value means: 0 /_ Off _/, -1, -1, -1, -1 /_ Bounds _/, -1, -1, -1, -1 /_ Insets _/
- **Data Type**: int32[]

### Property Name: CLUSTER_HEARTBEAT

- **ID (Int)**: 299896651
- **Hex Value**: 0x11E00F4B
- **AIDL Summary**: Sends the heartbeat signal to ClusterOS.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 3
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Support Variable Update Rate**: false
- **Config Array**: [0, 0, 0, 0, 0, 2, 0, 0, 16]
- **Comment**: configArray specifies it consists of int64[2] and byte[16].
- **Data Type**: mixed

### Property Name: CLUSTER_NAVIGATION_STATE

- **ID (Int)**: 292556600
- **Hex Value**: 0x11700F38
- **AIDL Summary**: Informs the current navigation state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Data Type**: byte[]

### Property Name: CLUSTER_REPORT_STATE

- **ID (Int)**: 299896630
- **Hex Value**: 0x11E00F36
- **AIDL Summary**: Reports the current display state and ClusterUI state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Config Array**: [0, 0, 0, 11, 0, 0, 0, 0, 16]
- **Data Type**: mixed

### Property Name: CLUSTER_REQUEST_DISPLAY

- **ID (Int)**: 289410871
- **Hex Value**: 0x11400F37
- **AIDL Summary**: Requests to change the cluster display state to show some ClusterUI.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Data Type**: int32

### Property Name: CLUSTER_SWITCH_UI

- **ID (Int)**: 289410868
- **Hex Value**: 0x11400F34
- **AIDL Summary**: Starts the ClusterUI in cluster display.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Comment**: 0 means ClusterHome
- **Data Type**: int32

### Property Name: CREATE_USER

- **ID (Int)**: 299896585
- **Hex Value**: 0x11E00F09
- **AIDL Summary**: Called by the Android System after an Android user was created.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: CRITICALLY_LOW_TIRE_PRESSURE

- **ID (Int)**: 392168202
- **Hex Value**: 0x1760030A
- **AIDL Summary**: Critically low tire pressure
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_TIRES
- **Unit**: VehicleUnit.KILOPASCAL
- **Version**: 1
- **Areas**:
  - **Area ID**: Constants::WHEEL_FRONT_LEFT
  - **Area Type**: WHEEL
  - **Area ID**: Constants::WHEEL_FRONT_RIGHT
  - **Area Type**: WHEEL
  - **Area ID**: Constants::WHEEL_REAR_RIGHT
  - **Area Type**: WHEEL
  - **Area ID**: Constants::WHEEL_REAR_LEFT
  - **Area Type**: WHEEL
- **Data Type**: float

### Property Name: CROSS_TRAFFIC_MONITORING_ENABLED

- **ID (Int)**: 287313955
- **Hex Value**: 0x11201023
- **AIDL Summary**: Enable or disable Cross Traffic Monitoring.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: CROSS_TRAFFIC_MONITORING_WARNING_STATE

- **ID (Int)**: 289411108
- **Hex Value**: 0x11401024
- **AIDL Summary**: Cross Traffic Monitoring warning state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: CrossTrafficMonitoringWarningState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [CrossTrafficMonitoringWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_DISABLED, CrossTrafficMonitoringWarningState::NO_WARNING, CrossTrafficMonitoringWarningState::WARNING_FRONT_LEFT, CrossTrafficMonitoringWarningState::WARNING_FRONT_RIGHT, CrossTrafficMonitoringWarningState::WARNING_FRONT_BOTH, CrossTrafficMonitoringWarningState::WARNING_REAR_LEFT, CrossTrafficMonitoringWarningState::WARNING_REAR_RIGHT, CrossTrafficMonitoringWarningState::WARNING_REAR_BOTH]
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_COMMAND

- **ID (Int)**: 289411090
- **Hex Value**: 0x11401012
- **AIDL Summary**: Write Cruise Control (CC) commands.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES
- **Data Enum**: CruiseControlCommand
- **Version**: 2
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [CruiseControlCommand::ACTIVATE, CruiseControlCommand::SUSPEND, CruiseControlCommand::INCREASE_TARGET_SPEED, CruiseControlCommand::DECREASE_TARGET_SPEED, CruiseControlCommand::INCREASE_TARGET_TIME_GAP, CruiseControlCommand::DECREASE_TARGET_TIME_GAP]
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_ENABLED

- **ID (Int)**: 287313935
- **Hex Value**: 0x1120100F
- **AIDL Summary**: Enable or disable cruise control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_STATE

- **ID (Int)**: 289411089
- **Hex Value**: 0x11401011
- **AIDL Summary**: Current state of Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: CruiseControlState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [CruiseControlState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, CruiseControlState::ENABLED, CruiseControlState::ACTIVATED, CruiseControlState::USER_OVERRIDE, CruiseControlState::SUSPENDED, CruiseControlState::FORCED_DEACTIVATION_WARNING]
- **Data Type**: int32

### Property Name: CRUISE_CONTROL_TARGET_SPEED

- **ID (Int)**: 291508243
- **Hex Value**: 0x11601013
- **AIDL Summary**: Current target speed for Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Unit**: VehicleUnit.METER_PER_SEC
- **Version**: 2
- **Default Value**: {floatValues: [25.0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minFloatValue: 20.0, maxFloatValue: 35.0
- **Data Type**: float

### Property Name: CRUISE_CONTROL_TYPE

- **ID (Int)**: 289411088
- **Hex Value**: 0x11401010
- **AIDL Summary**: Current type of Cruise Control (CC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES
- **Data Enum**: CruiseControlType, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [CruiseControlType::ADAPTIVE]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, CruiseControlType::STANDARD, CruiseControlType::ADAPTIVE, CruiseControlType::PREDICTIVE]
- **Data Type**: int32

### Property Name: CURRENT_GEAR

- **ID (Int)**: 289408001
- **Hex Value**: 0x11400401
- **AIDL Summary**: Current gear. In non-manual case, selected gear may not
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Data Enum**: VehicleGear
- **Version**: 1
- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Config Array**: [VehicleGear::GEAR_PARK, VehicleGear::GEAR_NEUTRAL, VehicleGear::GEAR_REVERSE, VehicleGear::GEAR_1, VehicleGear::GEAR_2, VehicleGear::GEAR_3, VehicleGear::GEAR_4, VehicleGear::GEAR_5]
- **Data Type**: int32

### Property Name: CURRENT_POWER_POLICY

- **ID (Int)**: 286265123
- **Hex Value**: 0x11100F23
- **AIDL Summary**: Notifies the current power policy to VHAL layer.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Version**: 1
- **Data Type**: String

### Property Name: DISTANCE_DISPLAY_UNITS

- **ID (Int)**: 289408512
- **Hex Value**: 0x11400600
- **AIDL Summary**: Distance units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Data Enum**: VehicleUnit
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::MILE]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
- **Config Array**: [VehicleUnit::KILOMETER, VehicleUnit::MILE]
- **Data Type**: int32

### Property Name: DOOR_CHILD_LOCK_ENABLED

- **ID (Int)**: 371198723
- **Hex Value**: 0x16200B03
- **AIDL Summary**: Door child lock feature enabled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area Type**: DOOR
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Area Type**: DOOR
- **Data Type**: int32

### Property Name: DOOR_LOCK

- **ID (Int)**: 371198722
- **Hex Value**: 0x16200B02
- **AIDL Summary**: Door lock
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Version**: 1
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Area Type**: DOOR
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Area Type**: DOOR
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area Type**: DOOR
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Area Type**: DOOR
- **Data Type**: boolean

### Property Name: DOOR_MOVE

- **ID (Int)**: 373295873
- **Hex Value**: 0x16400B01
- **AIDL Summary**: Door move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: DOOR_POS

- **ID (Int)**: 373295872
- **Hex Value**: 0x16400B00
- **AIDL Summary**: Door position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DOORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::DOOR_1_LEFT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_1_RIGHT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_LEFT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_2_RIGHT
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
  - **Area ID**: Constants::DOOR_REAR
  - **Area Type**: DOOR
  - **Constraints**: minInt32Value: 0, maxInt32Value: 1
- **Data Type**: int32

### Property Name: DRIVER_DISTRACTION_STATE

- **ID (Int)**: 289411102
- **Hex Value**: 0x1140101E
- **AIDL Summary**: Driver distraction state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: DriverDistractionState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [DriverDistractionState::NOT_DISTRACTED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, DriverDistractionState::NOT_DISTRACTED, DriverDistractionState::DISTRACTED]
- **Data Type**: int32

### Property Name: DRIVER_DISTRACTION_SYSTEM_ENABLED

- **ID (Int)**: 287313949
- **Hex Value**: 0x1120101D
- **AIDL Summary**: Enable or disable driver distraction monitoring.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: DRIVER_DISTRACTION_WARNING

- **ID (Int)**: 289411104
- **Hex Value**: 0x11401020
- **AIDL Summary**: Driver distraction warning.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: DriverDistractionWarning, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [DriverDistractionWarning::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, DriverDistractionWarning::NO_WARNING, DriverDistractionWarning::WARNING]
- **Data Type**: int32

### Property Name: DRIVER_DISTRACTION_WARNING_ENABLED

- **ID (Int)**: 287313951
- **Hex Value**: 0x1120101F
- **AIDL Summary**: Enable or disable driver distraction warnings.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: DRIVER_DROWSINESS_ATTENTION_STATE

- **ID (Int)**: 289411098
- **Hex Value**: 0x1140101A
- **AIDL Summary**: Driver drowsiness and attention level state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: DriverDrowsinessAttentionState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [DriverDrowsinessAttentionState::KSS_RATING_3_ALERT]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, DriverDrowsinessAttentionState::KSS_RATING_1_EXTREMELY_ALERT, DriverDrowsinessAttentionState::KSS_RATING_2_VERY_ALERT, DriverDrowsinessAttentionState::KSS_RATING_3_ALERT, DriverDrowsinessAttentionState::KSS_RATING_4_RATHER_ALERT, DriverDrowsinessAttentionState::KSS_RATING_5_NEITHER_ALERT_NOR_SLEEPY, DriverDrowsinessAttentionState::KSS_RATING_6_SOME_SLEEPINESS, DriverDrowsinessAttentionState::KSS_RATING_7_SLEEPY_NO_EFFORT, DriverDrowsinessAttentionState::KSS_RATING_8_SLEEPY_SOME_EFFORT, DriverDrowsinessAttentionState::KSS_RATING_9_VERY_SLEEPY]
- **Data Type**: int32

### Property Name: DRIVER_DROWSINESS_ATTENTION_SYSTEM_ENABLED

- **ID (Int)**: 287313945
- **Hex Value**: 0x11201019
- **AIDL Summary**: Enable or disable driver drowsiness and attention monitoring.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: DRIVER_DROWSINESS_ATTENTION_WARNING

- **ID (Int)**: 289411100
- **Hex Value**: 0x1140101C
- **AIDL Summary**: Driver drowsiness and attention warning.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: DriverDrowsinessAttentionWarning, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [DriverDrowsinessAttentionWarning::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, DriverDrowsinessAttentionWarning::NO_WARNING, DriverDrowsinessAttentionWarning::WARNING]
- **Data Type**: int32

### Property Name: DRIVER_DROWSINESS_ATTENTION_WARNING_ENABLED

- **ID (Int)**: 287313947
- **Hex Value**: 0x1120101B
- **AIDL Summary**: Enable or disable driver drowsiness and attention warnings.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: ELECTRONIC_STABILITY_CONTROL_ENABLED

- **ID (Int)**: 287310862
- **Hex Value**: 0x1120040E
- **AIDL Summary**: Enable or disable Electronic Stability Control (ESC).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_DYNAMICS_STATE
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: ELECTRONIC_STABILITY_CONTROL_STATE

- **ID (Int)**: 289408015
- **Hex Value**: 0x1140040F
- **AIDL Summary**: Electronic Stability Control (ESC) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DYNAMICS_STATE
- **Data Enum**: ElectronicStabilityControlState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [ElectronicStabilityControlState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, ElectronicStabilityControlState::ENABLED, ElectronicStabilityControlState::ACTIVATED]
- **Data Type**: int32

### Property Name: ELECTRONIC_TOLL_COLLECTION_CARD_STATUS

- **ID (Int)**: 289410874
- **Hex Value**: 0x11400F3A
- **AIDL Summary**: Electronic Toll Collection card status.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: ElectronicTollCollectionCardStatus
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: ELECTRONIC_TOLL_COLLECTION_CARD_TYPE

- **ID (Int)**: 289410873
- **Hex Value**: 0x11400F39
- **AIDL Summary**: Electronic Toll Collection card type.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: ElectronicTollCollectionCardType
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_ENABLED

- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: EMERGENCY_LANE_KEEP_ASSIST_STATE

- **ID (Int)**: 289411086
- **Hex Value**: 0x1140100E
- **AIDL Summary**: Emergency Lane Keep Assist (ELKA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: EmergencyLaneKeepAssistState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [EmergencyLaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, EmergencyLaneKeepAssistState::ENABLED, EmergencyLaneKeepAssistState::WARNING_LEFT, EmergencyLaneKeepAssistState::WARNING_RIGHT, EmergencyLaneKeepAssistState::ACTIVATED_STEER_LEFT, EmergencyLaneKeepAssistState::ACTIVATED_STEER_RIGHT, EmergencyLaneKeepAssistState::USER_OVERRIDE]
- **Data Type**: int32

### Property Name: ENGINE_COOLANT_TEMP

- **ID (Int)**: 291504897
- **Hex Value**: 0x11600301
- **AIDL Summary**: Temperature of engine coolant
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 1
- **Default Value**: {floatValues: [75.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: ENGINE_IDLE_AUTO_STOP_ENABLED

- **ID (Int)**: 287310624
- **Hex Value**: 0x11200320
- **AIDL Summary**: Represents feature for engine idle automatic stop.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_LEVEL

- **ID (Int)**: 289407747
- **Hex Value**: 0x11400303
- **AIDL Summary**: Engine oil level
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Data Enum**: VehicleOilLevel
- **Version**: 1
- **Default Value**: {int32Values: [VehicleOilLevel::NORMAL]}
- **Data Type**: int32

### Property Name: ENGINE_OIL_TEMP

- **ID (Int)**: 291504900
- **Hex Value**: 0x11600304
- **AIDL Summary**: Temperature of engine oil
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 1
- **Default Value**: {floatValues: [101.0]}
- **Sample Rate**: minSampleRate: 0.10000000149011612, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: ENGINE_RPM

- **ID (Int)**: 291504901
- **Hex Value**: 0x11600305
- **AIDL Summary**: Engine rpm
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_ENGINE_DETAILED
- **Unit**: VehicleUnit.RPM
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: ENV_OUTSIDE_TEMPERATURE

- **ID (Int)**: 291505923
- **Hex Value**: 0x11600703
- **AIDL Summary**: Outside temperature
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_ENVIRONMENT
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 1
- **Default Value**: {floatValues: [25.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 2.0
- **Data Type**: float

### Property Name: EVS_SERVICE_REQUEST

- **ID (Int)**: 289476368
- **Hex Value**: 0x11410F10
- **AIDL Summary**: Enable/request an EVS service.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Default Value**: {int32Values: [EvsServiceType::REARVIEW, EvsServiceState::OFF]}
- **Data Type**: int32[]

### Property Name: EV_BATTERY_AVERAGE_TEMPERATURE

- **ID (Int)**: 291504910
- **Hex Value**: 0x1160030E
- **AIDL Summary**: EV battery average temperature
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 3
- **Default Value**: {floatValues: [25.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 2.0
- **Data Type**: float

### Property Name: EV_BATTERY_DISPLAY_UNITS

- **ID (Int)**: 289408515
- **Hex Value**: 0x11400603
- **AIDL Summary**: EV battery units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Data Enum**: VehicleUnit
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::KILOWATT_HOUR]}
- **Config Array**: [VehicleUnit::WATT_HOUR, VehicleUnit::AMPERE_HOURS, VehicleUnit::KILOWATT_HOUR]
- **Data Type**: int32

### Property Name: EV_BATTERY_INSTANTANEOUS_CHARGE_RATE

- **ID (Int)**: 291504908
- **Hex Value**: 0x1160030C
- **AIDL Summary**: EV instantaneous charge rate in milliwatts
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.MILLIWATTS
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: EV_BATTERY_LEVEL

- **ID (Int)**: 291504905
- **Hex Value**: 0x11600309
- **AIDL Summary**: Battery level for EV or hybrid vehicle
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.WATT_HOUR
- **Version**: 1
- **Default Value**: {floatValues: [150000.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 100.0
- **Data Type**: float

### Property Name: EV_BRAKE_REGENERATION_LEVEL

- **ID (Int)**: 289408012
- **Hex Value**: 0x1140040C
- **AIDL Summary**: Regenerative braking level of a electronic vehicle
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_POWERTRAIN
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: EV_CHARGE_CURRENT_DRAW_LIMIT

- **ID (Int)**: 291508031
- **Hex Value**: 0x11600F3F
- **AIDL Summary**: Indicates the maximum current draw threshold for charging set by the user
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_ENERGY
- **Unit**: VehicleUnit.AMPERE
- **Version**: 1
- **Default Value**: {floatValues: [12.5]}
- **Config Array**: [20]
- **Comment**: ConfigArray specifies Max current draw allowed by vehicle in amperes
- **Data Type**: float

### Property Name: EV_CHARGE_PERCENT_LIMIT

- **ID (Int)**: 291508032
- **Hex Value**: 0x11600F40
- **AIDL Summary**: Indicates the maximum charge percent threshold set by the user
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_ENERGY
- **Version**: 1
- **Default Value**: {floatValues: [40.0]}
- **Config Array**: [20, 40, 60, 80, 100]
- **Data Type**: float

### Property Name: EV_CHARGE_PORT_CONNECTED

- **ID (Int)**: 287310603
- **Hex Value**: 0x1120030B
- **AIDL Summary**: EV charge port connected
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY_PORTS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_PORT_OPEN

- **ID (Int)**: 287310602
- **Hex Value**: 0x1120030A
- **AIDL Summary**: EV charge port open
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ENERGY_PORTS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_STATE

- **ID (Int)**: 289410881
- **Hex Value**: 0x11400F41
- **AIDL Summary**: Charging state of the car
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Data Enum**: EvChargeState
- **Version**: 1
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_CHARGE_SWITCH

- **ID (Int)**: 287313730
- **Hex Value**: 0x11200F42
- **AIDL Summary**: Start or stop charging the EV battery
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_ENERGY
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: EV_CHARGE_TIME_REMAINING

- **ID (Int)**: 289410883
- **Hex Value**: 0x11400F43
- **AIDL Summary**: Estimated charge time remaining in seconds
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.SECS
- **Version**: 1
- **Default Value**: {int32Values: [20]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: int32

### Property Name: EV_CURRENT_BATTERY_CAPACITY

- **ID (Int)**: 291504909
- **Hex Value**: 0x1160030D
- **AIDL Summary**: Current usable battery capacity for EV or hybrid vehicle
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.WATT_HOUR
- **Version**: 2
- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: EV_REGENERATIVE_BRAKING_STATE

- **ID (Int)**: 289410884
- **Hex Value**: 0x11400F44
- **AIDL Summary**: Regenerative braking or one-pedal drive setting of the car
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Data Enum**: EvRegenerativeBrakingState
- **Version**: 1
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: EV_STOPPING_MODE

- **ID (Int)**: 289408013
- **Hex Value**: 0x1140040D
- **AIDL Summary**: Represents property for the current stopping mode of the vehicle.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_POWERTRAIN
- **Data Enum**: EvStoppingMode
- **Version**: 2
- **Default Value**: {int32Values: [Constants::EV_STOPPING_MODE_CREEP]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [Constants::EV_STOPPING_MODE_CREEP, Constants::EV_STOPPING_MODE_ROLL, Constants::EV_STOPPING_MODE_HOLD]
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_ENABLED

- **ID (Int)**: 287313922
- **Hex Value**: 0x11201002
- **AIDL Summary**: Enable or disable Forward Collision Warning (FCW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FORWARD_COLLISION_WARNING_STATE

- **ID (Int)**: 289411075
- **Hex Value**: 0x11401003
- **AIDL Summary**: Forward Collision Warning (FCW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: ForwardCollisionWarningState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [ForwardCollisionWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, ForwardCollisionWarningState::NO_WARNING, ForwardCollisionWarningState::WARNING]
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_STATE

- **ID (Int)**: 289410875
- **Hex Value**: 0x11400F3B
- **AIDL Summary**: Front fog lights state
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: FRONT_FOG_LIGHTS_SWITCH

- **ID (Int)**: 289410876
- **Hex Value**: 0x11400F3C
- **AIDL Summary**: Front fog lights switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: FUEL_CONSUMPTION_UNITS_DISTANCE_OVER_VOLUME

- **ID (Int)**: 287311364
- **Hex Value**: 0x11200604
- **AIDL Summary**: Fuel consumption units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: FUEL_DOOR_OPEN

- **ID (Int)**: 287310600
- **Hex Value**: 0x11200308
- **AIDL Summary**: Fuel door open
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ENERGY_PORTS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_LEVEL

- **ID (Int)**: 291504903
- **Hex Value**: 0x11600307
- **AIDL Summary**: Fuel level in milliliters
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Unit**: VehicleUnit.MILLILITER
- **Version**: 1
- **Default Value**: {floatValues: [15000.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 100.0
- **Data Type**: float

### Property Name: FUEL_LEVEL_LOW

- **ID (Int)**: 287310853
- **Hex Value**: 0x11200405
- **AIDL Summary**: Warning for fuel low level.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ENERGY
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: FUEL_VOLUME_DISPLAY_UNITS

- **ID (Int)**: 289408513
- **Hex Value**: 0x11400601
- **AIDL Summary**: Fuel volume units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Data Enum**: VehicleUnit
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::US_GALLON]}
- **Config Array**: [VehicleUnit::LITER, VehicleUnit::US_GALLON]
- **Data Type**: int32

### Property Name: GEAR_SELECTION

- **ID (Int)**: 289408000
- **Hex Value**: 0x11400400
- **AIDL Summary**: Currently selected gear
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Data Enum**: VehicleGear
- **Version**: 1
- **Default Value**: {int32Values: [VehicleGear::GEAR_PARK]}
- **Config Array**: [VehicleGear::GEAR_PARK, VehicleGear::GEAR_NEUTRAL, VehicleGear::GEAR_REVERSE, VehicleGear::GEAR_DRIVE, VehicleGear::GEAR_1, VehicleGear::GEAR_2, VehicleGear::GEAR_3, VehicleGear::GEAR_4, VehicleGear::GEAR_5]
- **Data Type**: int32

### Property Name: GENERAL_SAFETY_REGULATION_COMPLIANCE_REQUIREMENT

- **ID (Int)**: 289410887
- **Hex Value**: 0x11400F47
- **AIDL Summary**: EU's General security regulation compliance requirement.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Data Enum**: GsrComplianceRequirementType
- **Version**: 2
- **Default Value**: {int32Values: [GsrComplianceRequirementType::GSR_COMPLIANCE_REQUIRED_V1]}
- **Data Type**: int32

### Property Name: GLOVE_BOX_DOOR_POS

- **ID (Int)**: 356518896
- **Hex Value**: 0x15400BF0
- **AIDL Summary**: Property that represents the current position of the glove box door.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_GLOVE_BOX
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: GLOVE_BOX_LOCKED

- **ID (Int)**: 354421745
- **Hex Value**: 0x15200BF1
- **AIDL Summary**: Lock or unlock the glove box.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_GLOVE_BOX
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_DRIVER_STATE

- **ID (Int)**: 289411095
- **Hex Value**: 0x11401017
- **AIDL Summary**: Hands On Detection (HOD) driver state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: HandsOnDetectionDriverState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [HandsOnDetectionDriverState::HANDS_ON]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, HandsOnDetectionDriverState::HANDS_ON, HandsOnDetectionDriverState::HANDS_OFF]
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_ENABLED

- **ID (Int)**: 287313942
- **Hex Value**: 0x11201016
- **AIDL Summary**: Enable or disable Hands On Detection (HOD).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: HANDS_ON_DETECTION_WARNING

- **ID (Int)**: 289411096
- **Hex Value**: 0x11401018
- **AIDL Summary**: Hands On Detection (HOD) warning.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_DRIVER_MONITORING_STATES
- **Data Enum**: HandsOnDetectionWarning, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [HandsOnDetectionWarning::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_DISABLED, HandsOnDetectionWarning::NO_WARNING, HandsOnDetectionWarning::WARNING]
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_STATE

- **ID (Int)**: 289410563
- **Hex Value**: 0x11400E03
- **AIDL Summary**: Hazard light status
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HAZARD_LIGHTS_SWITCH

- **ID (Int)**: 289410579
- **Hex Value**: 0x11400E13
- **AIDL Summary**: Hazard light switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_STATE

- **ID (Int)**: 289410560
- **Hex Value**: 0x11400E00
- **AIDL Summary**: Headlights State
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HEADLIGHTS_SWITCH

- **ID (Int)**: 289410576
- **Hex Value**: 0x11400E10
- **AIDL Summary**: Headlight switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HEAD_UP_DISPLAY_ENABLED

- **ID (Int)**: 354421254
- **Hex Value**: 0x15200A06
- **AIDL Summary**: Head up display (HUD) enabled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_HEAD_UP_DISPLAY
- **Version**: 3
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_STATE

- **ID (Int)**: 289410561
- **Hex Value**: 0x11400E01
- **AIDL Summary**: High beam lights state
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: HIGH_BEAM_LIGHTS_SWITCH

- **ID (Int)**: 289410577
- **Hex Value**: 0x11400E11
- **AIDL Summary**: High beam light switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: HVAC_ACTUAL_FAN_SPEED_RPM

- **ID (Int)**: 356517135
- **Hex Value**: 0x1540050F
- **AIDL Summary**: Actual fan speed
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [50]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_AC_ON

- **ID (Int)**: 354419973
- **Hex Value**: 0x15200505
- **AIDL Summary**: On/off AC for designated areaId
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Config Flags**: Supported areaIds
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_AUTO_ON

- **ID (Int)**: 354419978
- **Hex Value**: 0x1520050A
- **AIDL Summary**: On/off automatic climate control.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_AUTO_RECIRC_ON

- **ID (Int)**: 354419986
- **Hex Value**: 0x15200512
- **AIDL Summary**: Automatic recirculation on/off
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_DEFROSTER

- **ID (Int)**: 320865540
- **Hex Value**: 0x13200504
- **AIDL Summary**: Fan-based defrost for designated window.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area Type**: WINDOW
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Area Type**: WINDOW
- **Comment**: 0 means using for all areas
- **Data Type**: int32

### Property Name: HVAC_DUAL_ON

- **ID (Int)**: 354419977
- **Hex Value**: 0x15200509
- **AIDL Summary**: Enable temperature coupling between areas.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::HVAC_ALL
  - **Area Type**: HVAC
- **Data Type**: int32

### Property Name: HVAC_ELECTRIC_DEFROSTER_ON

- **ID (Int)**: 320865556
- **Hex Value**: 0x13200514
- **AIDL Summary**: Electric defrosters' status
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area Type**: WINDOW
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Area Type**: WINDOW
- **Comment**: 0 means using for all areas
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION

- **ID (Int)**: 356517121
- **Hex Value**: 0x15400501
- **AIDL Summary**: Fan direction setting
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Data Enum**: VehicleHvacFanDirection
- **Version**: 1
- **Default Value**: {int32Values: [VehicleHvacFanDirection::FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_FAN_DIRECTION_AVAILABLE

- **ID (Int)**: 356582673
- **Hex Value**: 0x15410511
- **AIDL Summary**: Fan Positions Available
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Data Enum**: VehicleHvacFanDirection
- **Version**: 1
- **Default Value**: {int32Values: [Constants::FAN_DIRECTION_UNKNOWN, Constants::FAN_DIRECTION_FACE, Constants::FAN_DIRECTION_FLOOR, Constants::FAN_DIRECTION_FACE_FLOOR, Constants::FAN_DIRECTION_DEFROST, Constants::FAN_DIRECTION_FACE_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST, Constants::FAN_DIRECTION_FLOOR_DEFROST_FACE]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32[]

### Property Name: HVAC_FAN_SPEED

- **ID (Int)**: 356517120
- **Hex Value**: 0x15400500
- **AIDL Summary**: Fan speed setting
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [3]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 1, maxInt32Value: 7
- **Data Type**: int32

### Property Name: HVAC_MAX_AC_ON

- **ID (Int)**: 354419974
- **Hex Value**: 0x15200506
- **AIDL Summary**: On/off max AC
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_MAX_DEFROST_ON

- **ID (Int)**: 354419975
- **Hex Value**: 0x15200507
- **AIDL Summary**: On/off max defrost
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::HVAC_FRONT_ROW
  - **Area Type**: HVAC
  - **Area ID**: Constants::HVAC_REAR_ROW
  - **Area Type**: HVAC
- **Data Type**: int32

### Property Name: HVAC_POWER_ON

- **ID (Int)**: 354419984
- **Hex Value**: 0x15200510
- **AIDL Summary**: Represents global power state for HVAC. Setting this property to false
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Config Array**: [VehicleProperty::HVAC_ACTUAL_FAN_SPEED_RPM, VehicleProperty::HVAC_AC_ON, VehicleProperty::HVAC_AUTO_ON, VehicleProperty::HVAC_AUTO_RECIRC_ON, VehicleProperty::HVAC_FAN_DIRECTION, VehicleProperty::HVAC_FAN_SPEED, VehicleProperty::HVAC_MAX_AC_ON, VehicleProperty::HVAC_RECIRC_ON, VehicleProperty::HVAC_TEMPERATURE_CURRENT, VehicleProperty::HVAC_TEMPERATURE_SET]
- **Data Type**: int32

### Property Name: HVAC_RECIRC_ON

- **ID (Int)**: 354419976
- **Hex Value**: 0x15200508
- **AIDL Summary**: Recirculation on/off
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: HVAC_SEAT_TEMPERATURE

- **ID (Int)**: 356517131
- **Hex Value**: 0x1540050B
- **AIDL Summary**: Seat heating/cooling
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Comment**: +ve values for heating and -ve for cooling
- **Data Type**: int32

### Property Name: HVAC_SEAT_VENTILATION

- **ID (Int)**: 356517139
- **Hex Value**: 0x15400513
- **AIDL Summary**: Seat ventilation
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Comment**: 0 is off and +ve values indicate ventilation level.
- **Data Type**: int32

### Property Name: HVAC_SIDE_MIRROR_HEAT

- **ID (Int)**: 339739916
- **Hex Value**: 0x1440050C
- **AIDL Summary**: Side Mirror Heat
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 2
- **Data Type**: int32

### Property Name: HVAC_STEERING_WHEEL_HEAT

- **ID (Int)**: 289408269
- **Hex Value**: 0x1140050D
- **AIDL Summary**: Steering Wheel Heating/Cooling
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Comment**: +ve values for heating and -ve for cooling
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_CURRENT

- **ID (Int)**: 358614274
- **Hex Value**: 0x15600502
- **AIDL Summary**: HVAC current temperature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 1
- **Default Value**: {floatValues: [19.1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: float

### Property Name: HVAC_TEMPERATURE_DISPLAY_UNITS

- **ID (Int)**: 289408270
- **Hex Value**: 0x1140050E
- **AIDL Summary**: Temperature units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Data Enum**: VehicleUnit
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::FAHRENHEIT]}
- **Config Array**: [VehicleUnit::FAHRENHEIT, VehicleUnit::CELSIUS]
- **Data Type**: int32

### Property Name: HVAC_TEMPERATURE_SET

- **ID (Int)**: 358614275
- **Hex Value**: 0x15600503
- **AIDL Summary**: HVAC target temperature set in Celsius.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Unit**: VehicleUnit.CELSIUS
- **Version**: 1
- **Default Value**: {floatValues: [17.0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minFloatValue: 16.0, maxFloatValue: 28.0
- **Config Array**: [160, 280, 5, 608, 824, 9]
- **Comment**: minFloatValue and maxFloatValue in area config should match corresponding values in configArray
- **Data Type**: float

### Property Name: HVAC_TEMPERATURE_VALUE_SUGGESTION

- **ID (Int)**: 291570965
- **Hex Value**: 0x11610515
- **AIDL Summary**: Suggested values for setting HVAC temperature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_CLIMATE
- **Version**: 1
- **Default Value**: {floatValues: [66.19999694824219, VehicleUnit::FAHRENHEIT, 19.0, 66.2]}
- **Data Type**: float[]

### Property Name: HW_CUSTOM_INPUT

- **ID (Int)**: 289475120
- **Hex Value**: 0x11410A30
- **AIDL Summary**: Defines a custom OEM partner input event.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Data Enum**: CustomInputType
- **Version**: 1
- **Default Value**: {int32Values: [0, 0, 0]}
- **Config Array**: [0, 0, 0, 3, 0, 0, 0, 0, 0]
- **Data Type**: int32[]

### Property Name: HW_KEY_INPUT

- **ID (Int)**: 289475088
- **Hex Value**: 0x11410A10
- **AIDL Summary**: Property to feed H/W input events to android
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Config Flags**: Supported areaIds
- **Version**: 1
- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32[]

### Property Name: HW_KEY_INPUT_V2

- **ID (Int)**: 367004177
- **Hex Value**: 0x15E00A11
- **AIDL Summary**: Property to feed H/W input events to android
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Config Flags**: Supported areaIds
- **Version**: 2
- **Default Value**: {int32Values: [0, 0, 0, 0], int64Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int64

### Property Name: HW_MOTION_INPUT

- **ID (Int)**: 367004178
- **Hex Value**: 0x15E00A12
- **AIDL Summary**: Property to feed H/W input events to android
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Config Flags**: Supported areaIds
- **Version**: 2
- **Default Value**: {int32Values: [0, 0, 0, 0, 1, 0, 0], floatValues: [0, 0, 0, 0], int64Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: float[]

### Property Name: HW_ROTARY_INPUT

- **ID (Int)**: 289475104
- **Hex Value**: 0x11410A20
- **AIDL Summary**: Property to feed H/W rotary events to android
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Data Enum**: RotaryInputType
- **Version**: 1
- **Default Value**: {int32Values: [0, 0, 0]}
- **Data Type**: int32[]

### Property Name: IGNITION_STATE

- **ID (Int)**: 289408009
- **Hex Value**: 0x11400409
- **AIDL Summary**: Represents ignition state
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Data Enum**: VehicleIgnitionState
- **Version**: 1
- **Default Value**: {int32Values: [VehicleIgnitionState::ON]}
- **Data Type**: int32

### Property Name: IMPACT_DETECTED

- **ID (Int)**: 289407792
- **Hex Value**: 0x11400330
- **AIDL Summary**: Impact detected.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_IMPACT_SENSORS
- **Data Enum**: ImpactSensorLocation
- **Version**: 3
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ImpactSensorLocation::FRONT, ImpactSensorLocation::FRONT_LEFT_DOOR_SIDE, ImpactSensorLocation::FRONT_RIGHT_DOOR_SIDE, ImpactSensorLocation::REAR_LEFT_DOOR_SIDE, ImpactSensorLocation::REAR_RIGHT_DOOR_SIDE, ImpactSensorLocation::REAR]
- **Data Type**: int32

### Property Name: INFO_DRIVER_SEAT

- **ID (Int)**: 356516106
- **Hex Value**: 0x1540010A
- **AIDL Summary**: Driver's seat location
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: VehicleAreaSeat
- **Version**: 1
- **Default Value**: {int32Values: [Constants::SEAT_1_LEFT]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
- **Data Type**: int32

### Property Name: INFO_EV_BATTERY_CAPACITY

- **ID (Int)**: 291504390
- **Hex Value**: 0x11600106
- **AIDL Summary**: Nominal usable battery capacity for EV or hybrid vehicle
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Unit**: VehicleUnit.WATT_HOUR
- **Version**: 1
- **Default Value**: {floatValues: [150000.0]}
- **Data Type**: float

### Property Name: INFO_EV_CONNECTOR_TYPE

- **ID (Int)**: 289472775
- **Hex Value**: 0x11410107
- **AIDL Summary**: List of connectors this EV may use
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: EvConnectorType
- **Version**: 1
- **Default Value**: {int32Values: [EvConnectorType::IEC_TYPE_1_AC]}
- **Data Type**: int32

### Property Name: INFO_EV_PORT_LOCATION

- **ID (Int)**: 289407241
- **Hex Value**: 0x11400109
- **AIDL Summary**: EV port location
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: PortLocationType
- **Version**: 1
- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT]}
- **Data Type**: int32

### Property Name: INFO_EXTERIOR_DIMENSIONS

- **ID (Int)**: 289472779
- **Hex Value**: 0x1141010B
- **AIDL Summary**: Exterior dimensions of vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Unit**: VehicleUnit.MILLIMETER
- **Version**: 1
- **Default Value**: {int32Values: [1776, 4950, 2008, 2140, 2984, 1665, 1667, 11800]}
- **Data Type**: int32[]

### Property Name: INFO_FUEL_CAPACITY

- **ID (Int)**: 291504388
- **Hex Value**: 0x11600104
- **AIDL Summary**: Fuel capacity of the vehicle in milliliters
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Unit**: VehicleUnit.MILLILITER
- **Version**: 1
- **Default Value**: {floatValues: [15000.0]}
- **Data Type**: float

### Property Name: INFO_FUEL_DOOR_LOCATION

- **ID (Int)**: 289407240
- **Hex Value**: 0x11400108
- **AIDL Summary**: Fuel door location
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: PortLocationType
- **Version**: 1
- **Default Value**: {int32Values: [Constants::FUEL_DOOR_REAR_LEFT]}
- **Data Type**: int32

### Property Name: INFO_FUEL_TYPE

- **ID (Int)**: 289472773
- **Hex Value**: 0x11410105
- **AIDL Summary**: List of fuels the vehicle may use.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: FuelType
- **Version**: 1
- **Default Value**: {int32Values: [FuelType::FUEL_TYPE_UNLEADED]}
- **Data Type**: int32

### Property Name: INFO_MAKE

- **ID (Int)**: 286261505
- **Hex Value**: 0x11100101
- **AIDL Summary**: Manufacturer of vehicle
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Version**: 1
- **Default Value**: {stringValue: Toy Vehicle}
- **Data Type**: String

### Property Name: INFO_MODEL

- **ID (Int)**: 286261506
- **Hex Value**: 0x11100102
- **AIDL Summary**: Model of vehicle
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Version**: 1
- **Default Value**: {stringValue: Speedy Model}
- **Data Type**: String

### Property Name: INFO_MODEL_YEAR

- **ID (Int)**: 289407235
- **Hex Value**: 0x11400103
- **AIDL Summary**: Model year of vehicle in YYYY format based on the Gregorian calendar.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Unit**: VehicleUnit.YEAR
- **Version**: 1
- **Default Value**: {int32Values: [2023]}
- **Data Type**: int32

### Property Name: INFO_MULTI_EV_PORT_LOCATIONS

- **ID (Int)**: 289472780
- **Hex Value**: 0x1141010C
- **AIDL Summary**: Multiple EV port locations
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_INFO
- **Data Enum**: PortLocationType
- **Version**: 1
- **Default Value**: {int32Values: [Constants::CHARGE_PORT_FRONT_LEFT, Constants::CHARGE_PORT_REAR_LEFT]}
- **Data Type**: int32[]

### Property Name: INFO_VIN

- **ID (Int)**: 286261504
- **Hex Value**: 0x11100100
- **AIDL Summary**: VIN of vehicle
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_IDENTIFICATION
- **Version**: 1
- **Default Value**: {stringValue: 1GCARVIN123456789}
- **Data Type**: String

### Property Name: INITIAL_USER_INFO

- **ID (Int)**: 299896583
- **Hex Value**: 0x11E00F07
- **AIDL Summary**: Defines the initial Android user to be used during initialization.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: LANE_CENTERING_ASSIST_COMMAND

- **ID (Int)**: 289411083
- **Hex Value**: 0x1140100B
- **AIDL Summary**: Lane Centering Assist (LCA) commands.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_STATES
- **Data Enum**: LaneCenteringAssistCommand
- **Version**: 2
- **Data Type**: int32

### Property Name: LANE_CENTERING_ASSIST_ENABLED

- **ID (Int)**: 287313930
- **Hex Value**: 0x1120100A
- **AIDL Summary**: Enable or disable Lane Centering Assist (LCA).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_CENTERING_ASSIST_STATE

- **ID (Int)**: 289411084
- **Hex Value**: 0x1140100C
- **AIDL Summary**: Lane Centering Assist (LCA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: LaneCenteringAssistState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [LaneCenteringAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, LaneCenteringAssistState::ENABLED, LaneCenteringAssistState::ACTIVATION_REQUESTED, LaneCenteringAssistState::ACTIVATED, LaneCenteringAssistState::USER_OVERRIDE, LaneCenteringAssistState::FORCED_DEACTIVATION_WARNING]
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_ENABLED

- **ID (Int)**: 287313926
- **Hex Value**: 0x11201006
- **AIDL Summary**: Enable or disable Lane Departure Warning (LDW).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_DEPARTURE_WARNING_STATE

- **ID (Int)**: 289411079
- **Hex Value**: 0x11401007
- **AIDL Summary**: Lane Departure Warning (LDW) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: LaneDepartureWarningState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [LaneDepartureWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, LaneDepartureWarningState::NO_WARNING, LaneDepartureWarningState::WARNING_LEFT, LaneDepartureWarningState::WARNING_RIGHT]
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_ENABLED

- **ID (Int)**: 287313928
- **Hex Value**: 0x11201008
- **AIDL Summary**: Enable or disable Lane Keep Assist (LKA).
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LANE_KEEP_ASSIST_STATE

- **ID (Int)**: 289411081
- **Hex Value**: 0x11401009
- **AIDL Summary**: Lane Keep Assist (LKA) state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: LaneKeepAssistState, ErrorState
- **Version**: 2
- **Default Value**: {int32Values: [LaneKeepAssistState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_SPEED_LOW, ErrorState::NOT_AVAILABLE_DISABLED, LaneKeepAssistState::ENABLED, LaneKeepAssistState::ACTIVATED_STEER_LEFT, LaneKeepAssistState::ACTIVATED_STEER_RIGHT, LaneKeepAssistState::USER_OVERRIDE]
- **Data Type**: int32

### Property Name: LOCATION_CHARACTERIZATION

- **ID (Int)**: 289410064
- **Hex Value**: 0x11400C10
- **AIDL Summary**: Characterization of inputs used for computing location.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Version**: 2
- **Default Value**: {int32Values: [LocationCharacterization::RAW_GNSS_ONLY]}
- **Data Type**: int32

### Property Name: LOW_SPEED_AUTOMATIC_EMERGENCY_BRAKING_ENABLED

- **ID (Int)**: 287313957
- **Hex Value**: 0x11201025
- **AIDL Summary**: Enable or disable Low Speed Automatic Emergency Braking.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LOW_SPEED_AUTOMATIC_EMERGENCY_BRAKING_STATE

- **ID (Int)**: 289411110
- **Hex Value**: 0x11401026
- **AIDL Summary**: Low Speed Automatic Emergency Braking state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: LowSpeedAutomaticEmergencyBrakingState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [LowSpeedAutomaticEmergencyBrakingState::ENABLED]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_DISABLED, LowSpeedAutomaticEmergencyBrakingState::ENABLED, LowSpeedAutomaticEmergencyBrakingState::ACTIVATED, LowSpeedAutomaticEmergencyBrakingState::USER_OVERRIDE]
- **Data Type**: int32

### Property Name: LOW_SPEED_COLLISION_WARNING_ENABLED

- **ID (Int)**: 287313953
- **Hex Value**: 0x11201021
- **AIDL Summary**: Enable or disable Low Speed Collision Warning.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_ADAS_SETTINGS
- **Version**: 3
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: LOW_SPEED_COLLISION_WARNING_STATE

- **ID (Int)**: 289411106
- **Hex Value**: 0x11401022
- **AIDL Summary**: Low Speed Collision Warning state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ADAS_STATES
- **Data Enum**: LowSpeedCollisionWarningState, ErrorState
- **Version**: 3
- **Default Value**: {int32Values: [LowSpeedCollisionWarningState::NO_WARNING]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [ErrorState::NOT_AVAILABLE_SAFETY, ErrorState::NOT_AVAILABLE_POOR_VISIBILITY, ErrorState::NOT_AVAILABLE_SPEED_HIGH, ErrorState::NOT_AVAILABLE_DISABLED, LowSpeedCollisionWarningState::NO_WARNING, LowSpeedCollisionWarningState::WARNING]
- **Data Type**: int32

### Property Name: MIRROR_AUTO_FOLD_ENABLED

- **ID (Int)**: 337644358
- **Hex Value**: 0x14200B46
- **AIDL Summary**: Represents property for Mirror Auto Fold feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_AUTO_TILT_ENABLED

- **ID (Int)**: 337644359
- **Hex Value**: 0x14200B47
- **AIDL Summary**: Represents property for Mirror Auto Tilt feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::MIRROR_DRIVER_LEFT_RIGHT
- **Data Type**: int32

### Property Name: MIRROR_FOLD

- **ID (Int)**: 287312709
- **Hex Value**: 0x11200B45
- **AIDL Summary**: Mirror Fold
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_LOCK

- **ID (Int)**: 287312708
- **Hex Value**: 0x11200B44
- **AIDL Summary**: Mirror Lock
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: MIRROR_Y_MOVE

- **ID (Int)**: 339741507
- **Hex Value**: 0x14400B43
- **AIDL Summary**: Mirror Y Move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: MIRROR_Y_POS

- **ID (Int)**: 339741506
- **Hex Value**: 0x14400B42
- **AIDL Summary**: Mirror Y Position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
- **Data Type**: int32

### Property Name: MIRROR_Z_MOVE

- **ID (Int)**: 339741505
- **Hex Value**: 0x14400B41
- **AIDL Summary**: Mirror Z Move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: MIRROR_Z_POS

- **ID (Int)**: 339741504
- **Hex Value**: 0x14400B40
- **AIDL Summary**: Mirror Z Position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_MIRRORS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaMirror::DRIVER_LEFT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_RIGHT
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
  - **Area ID**: VehicleAreaMirror::DRIVER_CENTER
  - **Area Type**: MIRROR
  - **Constraints**: minInt32Value: -3, maxInt32Value: 3
- **Data Type**: int32

### Property Name: NIGHT_MODE

- **ID (Int)**: 287310855
- **Hex Value**: 0x11200407
- **AIDL Summary**: Night mode
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_ENVIRONMENT
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: OBD2_FREEZE_FRAME

- **ID (Int)**: 299896065
- **Hex Value**: 0x11E00D01
- **AIDL Summary**: OBD2 Freeze Frame Sensor Data
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Version**: 1
- **Config Array**: [0, 0]
- **Data Type**: mixed

### Property Name: OBD2_FREEZE_FRAME_CLEAR

- **ID (Int)**: 299896067
- **Hex Value**: 0x11E00D03
- **AIDL Summary**: OBD2 Freeze Frame Clear
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_CLEAR
- **Version**: 1
- **Config Array**: [1]
- **Data Type**: mixed

### Property Name: OBD2_FREEZE_FRAME_INFO

- **ID (Int)**: 299896066
- **Hex Value**: 0x11E00D02
- **AIDL Summary**: OBD2 Freeze Frame Information
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Version**: 1
- **Data Type**: mixed

### Property Name: OBD2_LIVE_FRAME

- **ID (Int)**: 299896064
- **Hex Value**: 0x11E00D00
- **AIDL Summary**: OBD2 Live Sensor Data
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DIAGNOSTIC_READ_ALL
- **Version**: 1
- **Config Array**: [0, 0]
- **Data Type**: mixed

### Property Name: PARKING_BRAKE_AUTO_APPLY

- **ID (Int)**: 287310851
- **Hex Value**: 0x11200403
- **AIDL Summary**: Auto-apply parking brake.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PARKING_BRAKE_ON

- **ID (Int)**: 287310850
- **Hex Value**: 0x11200402
- **AIDL Summary**: Parking brake state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_POWERTRAIN
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: PERF_ODOMETER

- **ID (Int)**: 291504644
- **Hex Value**: 0x11600204
- **AIDL Summary**: Current odometer value of the vehicle
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_MILEAGE
- **Unit**: VehicleUnit.KILOMETER
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: PERF_REAR_STEERING_ANGLE

- **ID (Int)**: 291504656
- **Hex Value**: 0x11600210
- **AIDL Summary**: Rear bicycle model steering angle for vehicle
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_STEERING_STATE
- **Unit**: VehicleUnit.DEGREES
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: PERF_STEERING_ANGLE

- **ID (Int)**: 291504649
- **Hex Value**: 0x11600209
- **AIDL Summary**: Front bicycle model steering angle for vehicle
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_STEERING_STATE
- **Unit**: VehicleUnit.DEGREES
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED

- **ID (Int)**: 291504647
- **Hex Value**: 0x11600207
- **AIDL Summary**: Speed of the vehicle
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Unit**: VehicleUnit.METER_PER_SEC
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: PERF_VEHICLE_SPEED_DISPLAY

- **ID (Int)**: 291504648
- **Hex Value**: 0x11600208
- **AIDL Summary**: Speed of the vehicle for displays
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Unit**: VehicleUnit.METER_PER_SEC
- **Version**: 1
- **Default Value**: {floatValues: [0.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: float

### Property Name: PER_DISPLAY_BRIGHTNESS

- **ID (Int)**: 289475076
- **Hex Value**: 0x11410A04
- **AIDL Summary**: Property to represent brightness of the displays which are controlled separately.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_CAR_POWER
- **Version**: 2
- **Data Type**: int32[]

### Property Name: PER_DISPLAY_MAX_BRIGHTNESS

- **ID (Int)**: 289476430
- **Hex Value**: 0x11410F4E
- **AIDL Summary**: Property to represent max brightness of the displays which are controlled separately.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Version**: 3
- **Default Value**: {int32Values: [0, 100, 1, 100, 2, 100, 3, 100]}
- **Data Type**: int32[]

### Property Name: POWER_POLICY_GROUP_REQ

- **ID (Int)**: 286265122
- **Hex Value**: 0x11100F22
- **AIDL Summary**: Defines a request to set the power polic group used to decide a default power policy per
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Data Type**: String

### Property Name: POWER_POLICY_REQ

- **ID (Int)**: 286265121
- **Hex Value**: 0x11100F21
- **AIDL Summary**: Defines a request to apply power policy.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Data Type**: String

### Property Name: RANGE_REMAINING

- **ID (Int)**: 291504904
- **Hex Value**: 0x11600308
- **AIDL Summary**: Range remaining
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_ADJUST_RANGE_REMAINING
- **Unit**: VehicleUnit.METER
- **Version**: 1
- **Default Value**: {floatValues: [50000.0]}
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 2.0
- **Comment**: units in meter
- **Data Type**: float

### Property Name: READING_LIGHTS_STATE

- **ID (Int)**: 356519683
- **Hex Value**: 0x15400F03
- **AIDL Summary**: Reading lights
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: READING_LIGHTS_SWITCH

- **ID (Int)**: 356519684
- **Hex Value**: 0x15400F04
- **AIDL Summary**: Reading lights switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_STATE

- **ID (Int)**: 289410877
- **Hex Value**: 0x11400F3D
- **AIDL Summary**: Rear fog lights state
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Data Type**: int32

### Property Name: REAR_FOG_LIGHTS_SWITCH

- **ID (Int)**: 289410878
- **Hex Value**: 0x11400F3E
- **AIDL Summary**: Rear fog lights switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_EXTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 1
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Data Type**: int32

### Property Name: REMOVE_USER

- **ID (Int)**: 299896586
- **Hex Value**: 0x11E00F0A
- **AIDL Summary**: Called by the Android System after an Android user was removed.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: SEAT_AIRBAGS_DEPLOYED

- **ID (Int)**: 356518821
- **Hex Value**: 0x15400BA5
- **AIDL Summary**: Seat airbags deployed
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_CAR_AIRBAGS
- **Data Enum**: VehicleAirbagLocation
- **Version**: 3
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [VehicleAirbagLocation::FRONT, VehicleAirbagLocation::KNEE, VehicleAirbagLocation::LEFT_SIDE, VehicleAirbagLocation::RIGHT_SIDE, VehicleAirbagLocation::CURTAIN]
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [VehicleAirbagLocation::FRONT, VehicleAirbagLocation::KNEE, VehicleAirbagLocation::LEFT_SIDE, VehicleAirbagLocation::RIGHT_SIDE, VehicleAirbagLocation::CURTAIN]
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [VehicleAirbagLocation::FRONT, VehicleAirbagLocation::CURTAIN]
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [VehicleAirbagLocation::FRONT, VehicleAirbagLocation::CURTAIN]
- **Data Type**: int32

### Property Name: SEAT_AIRBAG_ENABLED

- **ID (Int)**: 354421662
- **Hex Value**: 0x15200B9E
- **AIDL Summary**: Represents feature to enable/disable a seat's ability to deploy airbag(s) when triggered
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_AIRBAGS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_1_MOVE

- **ID (Int)**: 356518792
- **Hex Value**: 0x15400B88
- **AIDL Summary**: Seat backrest angle 1 move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_1_POS

- **ID (Int)**: 356518791
- **Hex Value**: 0x15400B87
- **AIDL Summary**: Seat backrest angle 1 position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_2_MOVE

- **ID (Int)**: 356518794
- **Hex Value**: 0x15400B8A
- **AIDL Summary**: Seat backrest angle 2 move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BACKREST_ANGLE_2_POS

- **ID (Int)**: 356518793
- **Hex Value**: 0x15400B89
- **AIDL Summary**: Seat backrest angle 2 position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_BELT_BUCKLED

- **ID (Int)**: 354421634
- **Hex Value**: 0x15200B82
- **AIDL Summary**: Seatbelt buckled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: SEAT_BELT_HEIGHT_MOVE

- **ID (Int)**: 356518788
- **Hex Value**: 0x15400B84
- **AIDL Summary**: Seatbelt height move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_BELT_HEIGHT_POS

- **ID (Int)**: 356518787
- **Hex Value**: 0x15400B83
- **AIDL Summary**: Seatbelt height position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [10]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_BELT_PRETENSIONER_DEPLOYED

- **ID (Int)**: 354421670
- **Hex Value**: 0x15200BA6
- **AIDL Summary**: Seat belt pretensioner deployed.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_CAR_SEAT_BELTS
- **Version**: 3
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: SEAT_CUSHION_SIDE_SUPPORT_MOVE

- **ID (Int)**: 356518816
- **Hex Value**: 0x15400BA0
- **AIDL Summary**: Represents property for movement direction and speed of seat cushion side support.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_CUSHION_SIDE_SUPPORT_POS

- **ID (Int)**: 356518815
- **Hex Value**: 0x15400B9F
- **AIDL Summary**: Represents property for seat’s hipside (bottom cushion’s side) support position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_DEPTH_MOVE

- **ID (Int)**: 356518798
- **Hex Value**: 0x15400B8E
- **AIDL Summary**: Seat depth move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_DEPTH_POS

- **ID (Int)**: 356518797
- **Hex Value**: 0x15400B8D
- **AIDL Summary**: Seat depth position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_EASY_ACCESS_ENABLED

- **ID (Int)**: 354421661
- **Hex Value**: 0x15200B9D
- **AIDL Summary**: Represents property for Seat easy access feature.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_STATE

- **ID (Int)**: 356518811
- **Hex Value**: 0x15400B9B
- **AIDL Summary**: Represents property for the seat footwell lights state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 2
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_STATE_OFF, Constants::LIGHT_STATE_ON]
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_STATE_OFF, Constants::LIGHT_STATE_ON]
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_STATE_OFF, Constants::LIGHT_STATE_ON]
- **Data Type**: int32

### Property Name: SEAT_FOOTWELL_LIGHTS_SWITCH

- **ID (Int)**: 356518812
- **Hex Value**: 0x15400B9C
- **AIDL Summary**: Represents property for the seat footwell lights switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 2
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_OFF]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_SWITCH_OFF, Constants::LIGHT_SWITCH_ON, Constants::LIGHT_SWITCH_AUTO]
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_SWITCH_OFF, Constants::LIGHT_SWITCH_ON, Constants::LIGHT_SWITCH_AUTO]
  - **Area ID**: Constants::SEAT_2_LEFT_2_RIGHT_2_CENTER
  - **Area Type**: SEAT
  - **Supported Enum Values**: [Constants::LIGHT_SWITCH_OFF, Constants::LIGHT_SWITCH_ON, Constants::LIGHT_SWITCH_AUTO]
- **Data Type**: int32

### Property Name: SEAT_FORE_AFT_MOVE

- **ID (Int)**: 356518790
- **Hex Value**: 0x15400B86
- **AIDL Summary**: Seat fore/aft move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_FORE_AFT_POS

- **ID (Int)**: 356518789
- **Hex Value**: 0x15400B85
- **AIDL Summary**: Seat fore/aft position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_ANGLE_MOVE

- **ID (Int)**: 356518808
- **Hex Value**: 0x15400B98
- **AIDL Summary**: Headrest angle move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_ANGLE_POS

- **ID (Int)**: 356518807
- **Hex Value**: 0x15400B97
- **AIDL Summary**: Headrest angle position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_FORE_AFT_MOVE

- **ID (Int)**: 356518810
- **Hex Value**: 0x15400B9A
- **AIDL Summary**: Headrest fore/aft move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_FORE_AFT_POS

- **ID (Int)**: 356518809
- **Hex Value**: 0x15400B99
- **AIDL Summary**: Headrest fore/aft position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEADREST_HEIGHT_MOVE

- **ID (Int)**: 356518806
- **Hex Value**: 0x15400B96
- **AIDL Summary**: Headrest height move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEADREST_HEIGHT_POS_V2

- **ID (Int)**: 356518820
- **Hex Value**: 0x15400BA4
- **AIDL Summary**: Headrest height position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_HEIGHT_MOVE

- **ID (Int)**: 356518796
- **Hex Value**: 0x15400B8C
- **AIDL Summary**: Seat height move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_HEIGHT_POS

- **ID (Int)**: 356518795
- **Hex Value**: 0x15400B8B
- **AIDL Summary**: Seat height position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_FORE_AFT_MOVE

- **ID (Int)**: 356518802
- **Hex Value**: 0x15400B92
- **AIDL Summary**: Lumbar fore/aft move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_FORE_AFT_POS

- **ID (Int)**: 356518801
- **Hex Value**: 0x15400B91
- **AIDL Summary**: Lumber fore/aft position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_SIDE_SUPPORT_MOVE

- **ID (Int)**: 356518804
- **Hex Value**: 0x15400B94
- **AIDL Summary**: Lumbar side support move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_SIDE_SUPPORT_POS

- **ID (Int)**: 356518803
- **Hex Value**: 0x15400B93
- **AIDL Summary**: Lumbar side support position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_VERTICAL_MOVE

- **ID (Int)**: 356518818
- **Hex Value**: 0x15400BA2
- **AIDL Summary**: Represents property for vertical movement direction and speed of seat lumbar support.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_LUMBAR_VERTICAL_POS

- **ID (Int)**: 356518817
- **Hex Value**: 0x15400BA1
- **AIDL Summary**: Represents property for seat’s lumbar support vertical position.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_MEMORY_SELECT

- **ID (Int)**: 356518784
- **Hex Value**: 0x15400B80
- **AIDL Summary**: Seat memory select
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: SEAT_MEMORY_SET

- **ID (Int)**: 356518785
- **Hex Value**: 0x15400B81
- **AIDL Summary**: Seat memory set
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [1]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3
- **Data Type**: int32

### Property Name: SEAT_OCCUPANCY

- **ID (Int)**: 356518832
- **Hex Value**: 0x15400BB0
- **AIDL Summary**: Seat Occupancy
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Data Enum**: VehicleSeatOccupancyState
- **Version**: 1
- **Default Value**: {int32Values: [VehicleSeatOccupancyState::VACANT]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
- **Data Type**: int32

### Property Name: SEAT_TILT_MOVE

- **ID (Int)**: 356518800
- **Hex Value**: 0x15400B90
- **AIDL Summary**: Seat tilt move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -1, maxInt32Value: 1
- **Data Type**: int32

### Property Name: SEAT_TILT_POS

- **ID (Int)**: 356518799
- **Hex Value**: 0x15400B8F
- **AIDL Summary**: Seat tilt position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
  - **Area ID**: Constants::SEAT_2_CENTER
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: -10, maxInt32Value: 10
- **Data Type**: int32

### Property Name: SEAT_WALK_IN_POS

- **ID (Int)**: 356518819
- **Hex Value**: 0x15400BA3
- **AIDL Summary**: Represents property that indicates the current walk-in position of the seat.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_SEATS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: Constants::SEAT_1_LEFT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
  - **Area ID**: Constants::SEAT_1_RIGHT
  - **Area Type**: SEAT
  - **Constraints**: minInt32Value: 0, maxInt32Value: 5
- **Data Type**: int32

### Property Name: SHUTDOWN_REQUEST

- **ID (Int)**: 289410889
- **Hex Value**: 0x11400F49
- **AIDL Summary**: Request the head unit to be shutdown.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Data Enum**: VehicleApPowerStateShutdownParam
- **Version**: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_MOVE

- **ID (Int)**: 289410017
- **Hex Value**: 0x11400BE1
- **AIDL Summary**: Steering wheel depth movement
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_DEPTH_POS

- **ID (Int)**: 289410016
- **Hex Value**: 0x11400BE0
- **AIDL Summary**: Steering wheel depth position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_EASY_ACCESS_ENABLED

- **ID (Int)**: 287312870
- **Hex Value**: 0x11200BE6
- **AIDL Summary**: Steering wheel easy access feature enabled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_MOVE

- **ID (Int)**: 289410019
- **Hex Value**: 0x11400BE3
- **AIDL Summary**: Steering wheel height movement
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: -2, maxInt32Value: 2
- **Data Type**: int32

### Property Name: STEERING_WHEEL_HEIGHT_POS

- **ID (Int)**: 289410018
- **Hex Value**: 0x11400BE2
- **AIDL Summary**: Steering wheel height position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Constraints**: minInt32Value: 0, maxInt32Value: 10
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_STATE

- **ID (Int)**: 289410828
- **Hex Value**: 0x11400F0C
- **AIDL Summary**: Steering wheel lights state
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightState
- **Version**: 2
- **Default Value**: {int32Values: [Constants::LIGHT_STATE_ON]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [Constants::LIGHT_STATE_OFF, Constants::LIGHT_STATE_ON]
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LIGHTS_SWITCH

- **ID (Int)**: 289410829
- **Hex Value**: 0x11400F0D
- **AIDL Summary**: Steering wheel lights switch
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_INTERIOR_LIGHTS
- **Data Enum**: VehicleLightSwitch
- **Version**: 2
- **Default Value**: {int32Values: [Constants::LIGHT_SWITCH_AUTO]}
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Supported Enum Values**: [Constants::LIGHT_SWITCH_OFF, Constants::LIGHT_SWITCH_ON, Constants::LIGHT_SWITCH_AUTO]
- **Data Type**: int32

### Property Name: STEERING_WHEEL_LOCKED

- **ID (Int)**: 287312869
- **Hex Value**: 0x11200BE5
- **AIDL Summary**: Steering wheel locked
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: STEERING_WHEEL_THEFT_LOCK_ENABLED

- **ID (Int)**: 287312868
- **Hex Value**: 0x11200BE4
- **AIDL Summary**: Steering wheel theft lock feature enabled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_STEERING_WHEEL
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: SWITCH_USER

- **ID (Int)**: 299896584
- **Hex Value**: 0x11E00F08
- **AIDL Summary**: Defines a request to switch the foreground Android user.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: TIRE_PRESSURE

- **ID (Int)**: 392168201
- **Hex Value**: 0x17600309
- **AIDL Summary**: Tire pressure
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_TIRES
- **Unit**: VehicleUnit.KILOPASCAL
- **Version**: 1
- **Default Value**: {floatValues: [200.0]}
- **Areas**:
  - **Area ID**: Constants::WHEEL_FRONT_LEFT
  - **Area Type**: WHEEL
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_FRONT_RIGHT
  - **Area Type**: WHEEL
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_REAR_LEFT
  - **Area Type**: WHEEL
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
  - **Area ID**: Constants::WHEEL_REAR_RIGHT
  - **Area Type**: WHEEL
  - **Constraints**: minFloatValue: 193.0, maxFloatValue: 300.0
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 2.0
- **Comment**: Units in kpa
- **Data Type**: float

### Property Name: TIRE_PRESSURE_DISPLAY_UNITS

- **ID (Int)**: 289408514
- **Hex Value**: 0x11400602
- **AIDL Summary**: Tire pressure units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Data Enum**: VehicleUnit
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::PSI]}
- **Config Array**: [VehicleUnit::KILOPASCAL, VehicleUnit::PSI, VehicleUnit::BAR]
- **Data Type**: int32

### Property Name: TRACTION_CONTROL_ACTIVE

- **ID (Int)**: 287310859
- **Hex Value**: 0x1120040B
- **AIDL Summary**: Traction Control is active
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DYNAMICS_STATE
- **Version**: 1
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: TRAILER_PRESENT

- **ID (Int)**: 289410885
- **Hex Value**: 0x11400F45
- **AIDL Summary**: Indicates if there is a trailer present or not.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_PRIVILEGED_CAR_INFO
- **Data Enum**: TrailerState
- **Version**: 1
- **Default Value**: {int32Values: [2]}
- **Data Type**: int32

### Property Name: TURN_SIGNAL_STATE

- **ID (Int)**: 289408008
- **Hex Value**: 0x11400408
- **AIDL Summary**: State of the vehicles turn signals
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_EXTERIOR_LIGHTS
- **Data Enum**: VehicleTurnSignal
- **Version**: 1
- **Default Value**: {int32Values: [VehicleTurnSignal::NONE]}
- **Data Type**: int32

### Property Name: ULTRASONICS_SENSOR_DETECTION_RANGE

- **ID (Int)**: 406916131
- **Hex Value**: 0x18410C23
- **AIDL Summary**: Static data for the detection range of each ultrasonic sensor in millimeters.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough values representing front left most sensor.
  - **Area ID**: 2
  - **Area Comment**: Rough values representing front 2nd to the left sensor.
  - **Area ID**: 4
  - **Area Comment**: Rough values representing front 3rd to the left sensor.
  - **Area ID**: 8
  - **Area Comment**: Rough values representing front 3rd to the right sensor.
  - **Area ID**: 16
  - **Area Comment**: Rough values representing front 2nd to the right sensor.
  - **Area ID**: 32
  - **Area Comment**: Rough values representing front right most sensor.
  - **Area ID**: 64
  - **Area Comment**: Rough values representing back left most sensor.
  - **Area ID**: 128
  - **Area Comment**: Rough values representing back 2nd to the left sensor.
  - **Area ID**: 256
  - **Area Comment**: Rough values representing back 3rd to the left sensor.
  - **Area ID**: 512
  - **Area Comment**: Rough values representing back 3rd to the right sensor.
  - **Area ID**: 1024
  - **Area Comment**: Rough values representing back 2nd to the right sensor.
  - **Area ID**: 2048
  - **Area Comment**: Rough values representing back right most sensor.
- **Data Type**: int32[]

### Property Name: ULTRASONICS_SENSOR_FIELD_OF_VIEW

- **ID (Int)**: 406916130
- **Hex Value**: 0x18410C22
- **AIDL Summary**: Static data for the field of view of each ultrasonic sensor in degrees.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough values representing front left most sensor.
  - **Area ID**: 2
  - **Area Comment**: Rough values representing front 2nd to the left sensor.
  - **Area ID**: 4
  - **Area Comment**: Rough values representing front 3rd to the left sensor.
  - **Area ID**: 8
  - **Area Comment**: Rough values representing front 3rd to the right sensor.
  - **Area ID**: 16
  - **Area Comment**: Rough values representing front 2nd to the right sensor.
  - **Area ID**: 32
  - **Area Comment**: Rough values representing front right most sensor.
  - **Area ID**: 64
  - **Area Comment**: Rough values representing back left most sensor.
  - **Area ID**: 128
  - **Area Comment**: Rough values representing back 2nd to the left sensor.
  - **Area ID**: 256
  - **Area Comment**: Rough values representing back 3rd to the left sensor.
  - **Area ID**: 512
  - **Area Comment**: Rough values representing back 3rd to the right sensor.
  - **Area ID**: 1024
  - **Area Comment**: Rough values representing back 2nd to the right sensor.
  - **Area ID**: 2048
  - **Area Comment**: Rough values representing back right most sensor.
- **Comment**: Default values for 12 individual ultrasonic sensors installed on the vehicle. Six sensors on the front bumper. Six sensors on the back bumper.
- **Data Type**: int32[]

### Property Name: ULTRASONICS_SENSOR_MEASURED_DISTANCE

- **ID (Int)**: 406916133
- **Hex Value**: 0x18410C25
- **AIDL Summary**: The distance reading of the nearest detected object per sensor in millimeters.
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough values representing front left most sensor.
  - **Area ID**: 2
  - **Area Comment**: Rough values representing front 2nd to the left sensor. Nothing detected.
  - **Area ID**: 4
  - **Area Comment**: Rough values representing front 3rd to the left sensor. Nothing detected.
  - **Area ID**: 8
  - **Area Comment**: Rough values representing front 3rd to the right sensor. Nothing detected.
  - **Area ID**: 16
  - **Area Comment**: Rough values representing front 2nd to the right sensor. Nothing detected.
  - **Area ID**: 32
  - **Area Comment**: Rough values representing front right most sensor. Nothing detected.
  - **Area ID**: 64
  - **Area Comment**: Rough values representing back left most sensor. Nothing detected.
  - **Area ID**: 128
  - **Area Comment**: Rough values representing back 2nd to the left sensor. Nothing detected.
  - **Area ID**: 256
  - **Area Comment**: Rough values representing back 3rd to the left sensor. Nothing detected.
  - **Area ID**: 512
  - **Area Comment**: Rough values representing back 3rd to the right sensor. Nothing detected.
  - **Area ID**: 1024
  - **Area Comment**: Rough values representing back 2nd to the right sensor.
  - **Area ID**: 2048
  - **Area Comment**: Rough values representing back right most sensor. No distance error.
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: int32[]

### Property Name: ULTRASONICS_SENSOR_ORIENTATION

- **ID (Int)**: 409013281
- **Hex Value**: 0x18610C21
- **AIDL Summary**: Static data for the orientation of each ultrasonic sensor installed on the vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front left most sensor rotated 45 degrees counter-clockwise.
  - **Area ID**: 2
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front 2nd to the left sensor rotated 0 degrees
  - **Area ID**: 4
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front 3rd to the left sensor rotated 0 degrees
  - **Area ID**: 8
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front 3rd to the right sensor rotated 0 degrees
  - **Area ID**: 16
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front 2nd to the right sensor rotated 0 degrees
  - **Area ID**: 32
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing front right most sensor rotated 45 degrees clockwise.
  - **Area ID**: 64
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back left most sensor rotated 45 degrees counter-clockwise.
  - **Area ID**: 128
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back 2nd to the left sensor rotated 0 degrees
  - **Area ID**: 256
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back 3rd to the right sensor rotated 0 degrees
  - **Area ID**: 512
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back 3rd to the right sensor rotated 0 degrees
  - **Area ID**: 1024
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back 2nd to the right sensor rotated 0 degrees
  - **Area ID**: 2048
  - **Area Comment**: Rough quaternion values [w, x, y, z] representing back right most sensor rotated 45 degrees clockwise.
- **Comment**: Default values for 12 individual ultrasonic sensors installed on the vehicle. Six sensors on the front bumper. Six sensors on the back bumper.
- **Data Type**: float[]

### Property Name: ULTRASONICS_SENSOR_POSITION

- **ID (Int)**: 406916128
- **Hex Value**: 0x18410C20
- **AIDL Summary**: Static data for the position of each ultrasonic sensor installed on the vehicle.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough numbers representing front left most sensor.
  - **Area ID**: 2
  - **Area Comment**: Rough numbers representing front 2nd to the left sensor.
  - **Area ID**: 4
  - **Area Comment**: Rough numbers representing front 3rd to the left sensor.
  - **Area ID**: 8
  - **Area Comment**: Rough numbers representing front 3rd to the right sensor.
  - **Area ID**: 16
  - **Area Comment**: Rough numbers representing front 2nd to the right sensor.
  - **Area ID**: 32
  - **Area Comment**: Rough numbers representing front right most sensor.
  - **Area ID**: 64
  - **Area Comment**: Rough numbers representing back left most sensor.
  - **Area ID**: 128
  - **Area Comment**: Rough numbers representing back 2nd to the left sensor.
  - **Area ID**: 256
  - **Area Comment**: Rough numbers representing back 3rd to the left sensor.
  - **Area ID**: 512
  - **Area Comment**: Rough numbers representing back 3rd to the right sensor.
  - **Area ID**: 1024
  - **Area Comment**: Rough numbers representing back 2nd to the right sensor.
  - **Area ID**: 2048
  - **Area Comment**: Rough numbers representing back right most sensor.
- **Comment**: Default values for 12 individual ultrasonic sensors installed on the vehicle. Six sensors on the front bumper. Six sensors on the back bumper.
- **Data Type**: int32[]

### Property Name: ULTRASONICS_SENSOR_SUPPORTED_RANGES

- **ID (Int)**: 406916132
- **Hex Value**: 0x18410C24
- **AIDL Summary**: Static data for the supported ranges of each ultrasonic sensor in millimeters.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_ULTRASONICS_SENSOR_DATA
- **Version**: 3
- **Areas**:
  - **Area ID**: 1
  - **Area Comment**: Rough values representing front left most sensor.
  - **Area ID**: 2
  - **Area Comment**: Rough values representing front 2nd to the left sensor.
  - **Area ID**: 4
  - **Area Comment**: Rough values representing front 3rd to the left sensor.
  - **Area ID**: 8
  - **Area Comment**: Rough values representing front 3rd to the right sensor.
  - **Area ID**: 16
  - **Area Comment**: Rough values representing front 2nd to the right sensor.
  - **Area ID**: 32
  - **Area Comment**: Rough values representing front right most sensor.
  - **Area ID**: 64
  - **Area Comment**: Rough values representing back left most sensor.
  - **Area ID**: 128
  - **Area Comment**: Rough values representing back 2nd to the left sensor.
  - **Area ID**: 256
  - **Area Comment**: Rough values representing back 3rd to the left sensor.
  - **Area ID**: 512
  - **Area Comment**: Rough values representing back 3rd to the right sensor.
  - **Area ID**: 1024
  - **Area Comment**: Rough values representing back 2nd to the right sensor.
  - **Area ID**: 2048
  - **Area Comment**: Rough values representing back right most sensor.
- **Data Type**: int32[]

### Property Name: USER_IDENTIFICATION_ASSOCIATION

- **ID (Int)**: 299896587
- **Hex Value**: 0x11E00F0B
- **AIDL Summary**: Property used to associate (or query the association) the current user with vehicle-specific
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: VALET_MODE_ENABLED

- **ID (Int)**: 287312389
- **Hex Value**: 0x11200A05
- **AIDL Summary**: Valet mode enabled
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_VALET_MODE
- **Version**: 3
- **Default Value**: {int32Values: [0]}
- **Data Type**: int32

### Property Name: VEHICLE_CURB_WEIGHT

- **ID (Int)**: 289410886
- **Hex Value**: 0x11400F46
- **AIDL Summary**: Vehicle’s curb weight in kilograms.
- **Change Mode**: VehiclePropertyChangeMode.STATIC
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_PRIVILEGED_CAR_INFO
- **Version**: 1
- **Default Value**: {int32Values: [2211]}
- **Config Array**: [2948]
- **Comment**: unit is kg
- **Data Type**: int32

### Property Name: VEHICLE_DRIVING_AUTOMATION_CURRENT_LEVEL

- **ID (Int)**: 289410892
- **Hex Value**: 0x11400F4C
- **AIDL Summary**: Current state of vehicle autonomy.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CAR_DRIVING_STATE
- **Data Enum**: VehicleAutonomousState
- **Version**: 3
- **Default Value**: {int32Values: [VehicleAutonomousState::LEVEL_0]}
- **Data Type**: int32

### Property Name: VEHICLE_IN_USE

- **ID (Int)**: 287313738
- **Hex Value**: 0x11200F4A
- **AIDL Summary**: Whether the vehicle is currently in use.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 2
- **Default Value**: {int32Values: [1]}
- **Data Type**: int32

### Property Name: VEHICLE_MAP_SERVICE

- **ID (Int)**: 299895808
- **Hex Value**: 0x11E00C00
- **AIDL Summary**: Vehicle Maps Service (VMS) message
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE
- **Requires Permission**: Car.PERMISSION_VMS_PUBLISHER, Car.PERMISSION_VMS_SUBSCRIBER
- **Version**: 1
- **Data Type**: mixed

### Property Name: VEHICLE_SPEED_DISPLAY_UNITS

- **ID (Int)**: 289408517
- **Hex Value**: 0x11400605
- **AIDL Summary**: Speed units for display
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Version**: 1
- **Default Value**: {int32Values: [VehicleUnit::MILES_PER_HOUR]}
- **Config Array**: [VehicleUnit::METER_PER_SEC, VehicleUnit::MILES_PER_HOUR, VehicleUnit::KILOMETERS_PER_HOUR]
- **Data Type**: int32

### Property Name: VHAL_HEARTBEAT

- **ID (Int)**: 290459443
- **Hex Value**: 0x11500F33
- **AIDL Summary**: Defines an event that VHAL signals to car watchdog as a heartbeat.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Version**: 1
- **Areas**:
  - **Area ID**: 0
  - **Area Type**: GLOBAL
  - **Support Variable Update Rate**: false
- **Data Type**: int64

### Property Name: WATCHDOG_ALIVE

- **ID (Int)**: 290459441
- **Hex Value**: 0x11500F31
- **AIDL Summary**: Defines an event that car watchdog updates to tell it's alive.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Data Type**: int64

### Property Name: WATCHDOG_TERMINATED_PROCESS

- **ID (Int)**: 299896626
- **Hex Value**: 0x11E00F32
- **AIDL Summary**: Defines a process terminated by car watchdog and the reason of termination.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.WRITE
- **Version**: 1
- **Data Type**: mixed

### Property Name: WHEEL_TICK

- **ID (Int)**: 290521862
- **Hex Value**: 0x11510306
- **AIDL Summary**: Reports wheel ticks
- **Change Mode**: VehiclePropertyChangeMode.CONTINUOUS
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_SPEED
- **Version**: 1
- **Default Value**: {int64Values: [0, 100000, 200000, 300000, 400000]}
- **Config Array**: [15, 50000, 50000, 50000, 50000]
- **Sample Rate**: minSampleRate: 1.0, maxSampleRate: 10.0
- **Data Type**: int64[]

### Property Name: WINDOW_LOCK

- **ID (Int)**: 320867268
- **Hex Value**: 0x13200BC4
- **AIDL Summary**: Window Child Lock
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Version**: 1
- **Areas**:
  - **Area ID**: Constants::WINDOW_1_RIGHT_2_LEFT_2_RIGHT
- **Data Type**: boolean

### Property Name: WINDOW_MOVE

- **ID (Int)**: 322964417
- **Hex Value**: 0x13400BC1
- **AIDL Summary**: Window Move
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Version**: 1
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
- **AIDL Summary**: Window Position
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_CAR_WINDOWS
- **Version**: 1
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
- **Requires Permission**: Car.PERMISSION_READ_WINDSHIELD_WIPERS
- **Unit**: VehicleUnit.MILLI_SECS
- **Version**: 2
- **Default Value**: {int32Values: [0]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area Type**: WINDOW
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Area Type**: WINDOW
  - **Constraints**: minInt32Value: 0, maxInt32Value: 3000
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_STATE

- **ID (Int)**: 322964422
- **Hex Value**: 0x13400BC6
- **AIDL Summary**: Windshield wipers state.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_READ_WINDSHIELD_WIPERS
- **Data Enum**: WindshieldWipersState
- **Version**: 2
- **Default Value**: {int32Values: [WindshieldWipersState::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area Type**: WINDOW
  - **Supported Enum Values**: [WindshieldWipersState::OFF, WindshieldWipersState::ON, WindshieldWipersState::SERVICE]
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Area Type**: WINDOW
  - **Supported Enum Values**: [WindshieldWipersState::OFF, WindshieldWipersState::ON]
- **Data Type**: int32

### Property Name: WINDSHIELD_WIPERS_SWITCH

- **ID (Int)**: 322964423
- **Hex Value**: 0x13400BC7
- **AIDL Summary**: Windshield wipers switch.
- **Change Mode**: VehiclePropertyChangeMode.ON_CHANGE
- **Access**: VehiclePropertyAccess.READ_WRITE, VehiclePropertyAccess.READ
- **Requires Permission**: Car.PERMISSION_CONTROL_WINDSHIELD_WIPERS
- **Data Enum**: WindshieldWipersSwitch
- **Version**: 2
- **Default Value**: {int32Values: [WindshieldWipersSwitch::OFF]}
- **Areas**:
  - **Area ID**: VehicleAreaWindow::FRONT_WINDSHIELD
  - **Area Type**: WINDOW
  - **Supported Enum Values**: [WindshieldWipersSwitch::OFF, WindshieldWipersSwitch::MIST, WindshieldWipersSwitch::INTERMITTENT_LEVEL_1, WindshieldWipersSwitch::INTERMITTENT_LEVEL_2, WindshieldWipersSwitch::INTERMITTENT_LEVEL_3, WindshieldWipersSwitch::INTERMITTENT_LEVEL_4, WindshieldWipersSwitch::INTERMITTENT_LEVEL_5, WindshieldWipersSwitch::CONTINUOUS_LEVEL_1, WindshieldWipersSwitch::CONTINUOUS_LEVEL_2, WindshieldWipersSwitch::CONTINUOUS_LEVEL_3, WindshieldWipersSwitch::CONTINUOUS_LEVEL_4, WindshieldWipersSwitch::CONTINUOUS_LEVEL_5, WindshieldWipersSwitch::AUTO, WindshieldWipersSwitch::SERVICE]
  - **Area ID**: VehicleAreaWindow::REAR_WINDSHIELD
  - **Area Type**: WINDOW
  - **Supported Enum Values**: [WindshieldWipersSwitch::OFF, WindshieldWipersSwitch::INTERMITTENT_LEVEL_1, WindshieldWipersSwitch::INTERMITTENT_LEVEL_2, WindshieldWipersSwitch::CONTINUOUS_LEVEL_1, WindshieldWipersSwitch::CONTINUOUS_LEVEL_2, WindshieldWipersSwitch::AUTO, WindshieldWipersSwitch::SERVICE]
- **Data Type**: int32

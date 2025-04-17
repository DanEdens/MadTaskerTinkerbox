# InvocationTome Integration Guide

This document explains how to integrate the enhanced MQTT functionality from InvocationTome into other Tasker projects in the Madness Interactive ecosystem.

## Prerequisites

- Tasker app installed on your Android device
- Imported InvocationTome.prj.xml into Tasker
- Basic understanding of Tasker tasks and profiles

## Setting Up MQTT Integration

### 1. Configuration

Before using the MQTT functionality, ensure the MQTT configuration is properly set up:

1. Run the `MqttConfig` task once to initialize all required variables
2. The following variables will be set automatically:
   - `%MqttServer` - MQTT broker address (default: 3.134.3.199)
   - `%MqttPort` - MQTT broker port (default: 9900)
   - `%MqttDeviceId` - Device identifier (default: %DEVID Tasker variable)
   - `%MqttDefaultTopic` - Default topic for publishing (default: status/%MqttDeviceId/log)
   - `%MqttStatus` - Connection status (connected/disconnected/checking)

### 2. Available MQTT Tasks

The following tasks are available for use in your projects:

#### MqttPub
Basic MQTT publish task for text messages.

**Parameters:**
- `%par1` - MQTT topic (uses %MqttDefaultTopic if not specified)
- `%par2` - Message payload
- `%par3` - Optional parameter; set to "timestamp" to include timestamp in the message

**Example Usage:**
```
Perform Task: MqttPub
    Par1: status/device/alert
    Par2: Motion detected in living room
    Par3: timestamp
```

#### MqttPub_Json
MQTT publish task for JSON-formatted messages.

**Parameters:**
- `%par1` - MQTT topic (uses %MqttDefaultTopic if not specified)
- `%par2` - JSON data (should be valid JSON format)
- `%par3` - Optional parameter; set to "timestamp" to include timestamp in the JSON

**Example Usage:**
```
Perform Task: MqttPub_Json
    Par1: data/sensors
    Par2: {"temperature": "25.3", "humidity": "65"}
    Par3: timestamp
```

#### MqttPub_Status
Publishes device status information to MQTT.

**Parameters:**
- `%par1` - Status message or identifier

**Example Usage:**
```
Perform Task: MqttPub_Status
    Par1: active
```

### 3. Integrating with Specific Projects

#### EntropyVector
Replace existing `Mqttpub` and `Mqttpub-r` tasks with InvocationTome's `MqttPub`:

1. Open the EntropyVector project in Tasker
2. Find all references to `Mqttpub` and `Mqttpub-r` in the tasks
3. Replace the task call with `MqttPub` from InvocationTome
4. Keep the same parameters for topic and message

#### PhilosophersAmpoule
Integrate MQTT status updates for medication tracking:

1. Modify medicine logging to use `MqttPub_Json` for structured data
2. Example for medication taken event:
   ```
   Perform Task: MqttPub_Json
       Par1: health/medication
       Par2: {"med_name": "%med_name", "dose": "%dose", "taken": true}
       Par3: timestamp
   ```

#### ContextOfficium
Update location-based MQTT notifications:

1. Replace existing `Mqttpub` calls with `MqttPub` or `MqttPub_Json`
2. Add device status updates using `MqttPub_Status` when location changes

### 4. Troubleshooting

- If MQTT connections fail, check that the `MqttConfig` task has been run
- Verify the MQTT broker is accessible from your device network
- Check the mqttlog.txt file for error messages
- Run the `MQTT Connection Monitor` task to verify connectivity

### 5. Custom MQTT Integration

To add custom MQTT functionality to your Tasker projects:

1. Import the needed tasks from InvocationTome
2. Create a profile that triggers on specific events
3. Call the appropriate MQTT task with parameters
4. For special requirements, create a wrapper task that processes data before calling the MQTT tasks

## Example Integration Code

### EntropyVector - State Monitoring
```
Profile: Battery State Change
    Event: Battery Level [ From:* To:* ]
Enter Task:
    A1: Perform Task [
        Name: MqttPub_Json
        Par1: status/%MqttDeviceId/battery
        Par2: {"level": "%BATT", "charging": "%BATT_STATUS"}
        Par3: timestamp ]
```

### PhilosophersAmpoule - Medicine Tracking
```
Profile: Medicine Reminder
    Time: [Specific time]
Enter Task:
    A1: Perform Task [
        Name: MqttPub
        Par1: health/reminders
        Par2: Time to take medication: %med_name
        Par3: timestamp ]
```

### ContextOfficium - Location Change
```
Profile: Location Changed
    Event: Location [Source:*]
Enter Task:
    A1: Perform Task [
        Name: MqttPub_Status
        Par1: location_changed ]
    A2: Perform Task [
        Name: MqttPub_Json
        Par1: location/update
        Par2: {"lat": "%LOC_LAT", "long": "%LOC_LONG", "accuracy": "%LOC_ACC", "source": "%LOC_SRC"}
        Par3: timestamp ]
``` 

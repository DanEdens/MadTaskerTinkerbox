# InvocationTome MQTT Migration Guide

This guide provides comprehensive instructions for migrating existing Tasker projects to use the enhanced MQTT functionality provided by InvocationTome.

## Migration Overview

The migration process has two approaches:
1. **Wrapper Method** - Uses wrapper tasks to redirect existing MQTT calls to InvocationTome (quick but not as clean)
2. **Direct Replacement** - Directly replaces all MQTT functionality in existing tasks (cleaner but more time-consuming)

## Prerequisites

- Tasker app installed on your Android device
- Imported InvocationTome.prj.xml into Tasker
- Backup of your existing Tasker projects

## Migration Steps

### 1. Install InvocationTome

1. Import `InvocationTome.prj.xml` into Tasker
2. Run the `MqttConfig` task to initialize MQTT variables
3. Verify connection by running the `MQTT Connection Monitor` task

### 2. Import Project-Specific Migration Files

Import the appropriate migration file for each project:

- `EntropyVector/MqttMigration.prj.xml`
- `PhilosophersAmpoule/MqttMigration.prj.xml`
- `ContextOfficium/MqttMigration.prj.xml`

Each migration file contains:
- Wrapper tasks for legacy MQTT functions
- New task implementations for common MQTT scenarios
- Profiles for automated MQTT updates

### 3. Wrapper Method (Quick Migration)

This method uses wrapper tasks that redirect existing MQTT calls to InvocationTome:

1. Keep your existing MQTT-related tasks as they are
2. Replace direct calls to the original `Mqttpub` and `Mqttpub-r` with their wrapper versions
3. The wrappers will automatically handle parameter passing and call the appropriate InvocationTome tasks

#### Example:

Original task:
```
A1: Perform Task [
    Name: Mqttpub
    Par1: status/device
    Par2: Device booted
]
```

Migrated task:
```
A1: Perform Task [
    Name: Mqttpub (Wrapper)
    Par1: status/device
    Par2: Device booted
]
```

### 4. Direct Replacement Method (Clean Migration)

This method directly replaces all MQTT functionality with InvocationTome tasks:

1. Identify all tasks that use MQTT functionality
2. Replace them with direct calls to the appropriate InvocationTome tasks
3. Update any profiles that trigger MQTT-related tasks

#### MQTT Task Mapping

| Original Task | InvocationTome Replacement | Notes |
|---------------|----------------------------|-------|
| `Mqttpub` | `MqttPub` | Standard MQTT publishing |
| `Mqttpub-r` | `MqttPub` with timestamp parameter | Timestamp-enabled publishing |
| Custom MQTT JSON tasks | `MqttPub_Json` | JSON-formatted messages |
| Location/status updates | `MqttPub_Status` | Device status reporting |

### 5. Project-Specific Migration Instructions

#### EntropyVector

1. Replace all `Mqttpub` and `Mqttpub-r` calls in `states.prj.xml` and `subsys.prj.xml`
2. Import the `Battery Status MQTT` task for enhanced battery reporting
3. Import the `Device Status MQTT` task for boot and state reporting

#### PhilosophersAmpoule

1. Replace MQTT calls in `Medicine.prj.xml`
2. Import the `Medicine Taken MQTT` task for structured medication logging
3. Import the `Medicine Reminder MQTT` task for reminder notifications

#### ContextOfficium

1. Replace MQTT calls in `Home.prj.xml` and `Jobname.prj.xml`
2. Import the `Location Update MQTT` task for enhanced location reporting
3. Import the `Home Arrived MQTT` and `Work Arrived MQTT` tasks for location events

### 6. Verify Migration

After migration, verify that:

1. All MQTT communications are working correctly
2. No duplicate messages are being sent
3. The MQTT broker is receiving properly formatted messages
4. The `mqttlog.txt` file contains the expected entries

## Troubleshooting

- **Connection Issues**: Run the `MqttConfig` task to reinitialize connections
- **Missing Messages**: Check that the wrapper tasks are being called with correct parameters
- **Duplicate Messages**: Ensure you're not calling both original and wrapper tasks
- **Format Problems**: Verify JSON formatting in any custom message construction

## Advanced Customization

After successful migration, consider these enhancements:

1. Create custom JSON schemas for your specific device data
2. Set up additional MQTT topics for more granular reporting
3. Implement cross-device MQTT communication
4. Create device-specific profiles in the MQTT broker

## Fallback Plan

If issues arise during migration:

1. Keep both systems running in parallel initially
2. Test thoroughly before removing legacy implementations
3. Maintain backup XML files of your original projects 

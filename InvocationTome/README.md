# InvocationTome

Enhanced MQTT integration for Tasker projects across the Madness Interactive ecosystem.

## Purpose
The InvocationTome project provides standardized MQTT connectivity between Tasker projects and other systems in the Madness Interactive ecosystem. It serves as the central communication hub for Android devices to interact with other services via MQTT.

## Features
- Standardized MQTT publishing tasks for all Tasker projects
- Dynamic topic handling based on device and context
- Support for various MQTT payload formats (JSON, text, binary)
- Automatic message formatting and timestamping
- Error handling and logging
- Connection status monitoring
- Secure credential management

## Integration with Other Projects
InvocationTome is designed to integrate with:
- **Omnispindle** - For task management and todo lists
- **Simple-mqtt-server-agent** - For message routing
- **MqttLogger** - For comprehensive logging
- Other Tasker projects in the ecosystem

## Usage
1. Import the InvocationTome.prj.xml file into Tasker
2. Configure the MQTT broker settings in the Tasker variables
3. Use the provided tasks in your other Tasker projects by calling them with the appropriate parameters

## Task Naming Convention
- `MqttPub` - Standard MQTT publish task
- `MqttPub_Json` - Publish JSON-formatted messages
- `MqttPub_Status` - Publish device status updates
- `MqttSub` - Subscribe to MQTT topics
- `MqttConfig` - Configure MQTT connection settings 

# VALAWAI SR C0 Furhat Interface

## Description

This C0 component connects the _Furhat_ robot to the Social Robots VALAWAI applications.

Furhat is a social robot developed by Furhat Robotics.
It is a robot head with a projected face, which can be customised with different faces and voices.
It has an array of microphones and speakers which allow it to record and play audio.
Although it features a camera, this is not used in this component, which focuses on the audio capabilities of the robot.
Other capabilities of the robot, such as face recognition, facial expressions, and head movements, are not used either.

With this component it is possible to input dialogue in the Social Robots VALAWAI system, and receive the corresponding responses.
The component can be customised with the identifier for the participant in the dialogue.
The input is performed with voice thanks to the embedded speech-to-text functionalities of the robot, and the output dialogue is reproduced using the text-to-speech module, with the default voice model configured in the robot.

The component runs in a _Docker_ container, and communicates with a _RabbitMQ_ message broker and the Furhat robot through a websocket connection.
The websocket connection is facilitated by a bridge architecture: the furhat and this component connect to a websocket server, which relays the messages between the two.
The Furhat robot runs a simple application that sends the transcription of the speech input to the websocket bridge and receives the text to be spoken from the websocket bridge.
In the future, this architecture will be simplified by allowing the Furhat robot to connect directly to the message broker.

## Data Flow

This component uses a single channel (or _topic_ or _routing key_), `valawai.c0.text-interface`, to communicate with the message broker using the `amq.topic` exchange, which broadcasts the messages to all the listeners bound to it with the same routing key.

This channel is intended for dialogue messages containing text.
Subscribing to this channel allows to receive the dialogue messages, and publishing to this channel allows to send them.

The messages exchanged are _JSON_ objects with the following structure:

```json
{
  "version": "0.0.1", // The version of the format.
  "speaker": "HUMAN", // Identifier of the author of the message.
  "text": "Hello, World!", // The text of the message.
  "timestamp": "2019-08-24T14:15:22Z" // The timestamp of the message.
}
```

## Control Flow

This component does not use any control flow channel.

## Usage

When the Furhat is running the application, it will listen for speech and reproduce the text received in alternation.

The component is configurable with the following environment variables, with the default value indicated in parentheses:

- RMQ_HOST (`host.docker.internal`) The hostname of the message broker.
- TEXT_INTERFACE_KEY (`valawai.c0.text-interface`) The channel to use for dialogue messages.
- EXCHANGE (`amq.topic`) The exchange to use for dialogue messages.
- SPEAKER (_HUMAN_) The identifier for the participant in the dialogue.
- BRIDGE_HOST (`wss://host.docker.internal/ws/c0/furhat`) The host name of the websocket bridge.

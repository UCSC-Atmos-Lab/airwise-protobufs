# Meshtastic Protobuf Definitions

[![CI](https://img.shields.io/github/actions/workflow/status/meshtastic/protobufs/pull_request.yml?branch=master&label=actions&logo=github&color=yellow)](https://github.com/meshtastic/protobufs/actions/workflows/pull_request.yml)
[![CLA assistant](https://cla-assistant.io/readme/badge/meshtastic/protobufs)](https://cla-assistant.io/meshtastic/protobufs)
[![Fiscal Contributors](https://opencollective.com/meshtastic/tiers/badge.svg?label=Fiscal%20Contributors&color=deeppink)](https://opencollective.com/meshtastic/)
[![Vercel](https://img.shields.io/static/v1?label=Powered%20by&message=Vercel&style=flat&logo=vercel&color=000000)](https://vercel.com?utm_source=meshtastic&utm_campaign=oss)

## Overview

The [Protobuf](https://developers.google.com/protocol-buffers) message definitions for the Meshtastic project (used by apps and the device firmware).

This fork also carries the [Airwise frame](airwise/airwise.proto). The intended
wire format is a serialized `airwise.Frame` in a Meshtastic `PRIVATE_APP`
payload (port 256). Firmware transports it as application data; the Airwise
sensor and sink applications must encode and validate HELLO, DATA, and ACK.
The `airwise.options` file bounds ACK missing record IDs to 16 for nanopb.

`Data` describes V4 readings from INA3221 power channels, BME680, SCD41,
SHT40, and BMV080 particulate sensors. Each sensor group and
reading is optional so an unavailable sensor is distinct from a valid zero.
The V4 application should populate only groups whose readings it validates.
`Data.position` is optional historical sample context; a future native
Meshtastic Position packet can report the node's current location separately.
The V4 aggregate sketch currently disables UV acquisition, so UV fields will
be added once the production sensor and driver are validated.

Consumers must require the frame ID (`0x41525753`), schema version (`1`),
sensor `node_id`, nonzero persistent `stream_id`, and exactly one body. DATA
requires a nonzero `record_id` and at least one present sensor reading or
status, not merely an empty sensor group. Reject nonfinite floats, out-of-range
sensor values, and incomplete or invalid coordinates when position is present.
HELLO requires all three queue fields: `0/0` means an empty queue; otherwise
the bounds must be nonzero and ordered. ACK requires `contig` and
`highest_seen`; require `contig <= highest_seen` and up to 16 distinct `miss`
IDs in `(contig, highest_seen]`. Validate HELLO and ACK before changing queue,
watermark, or retry state. For DATA and HELLO, check that `node_id` agrees with
the Meshtastic sender; for ACK, check the expected sink sender, destination
sensor, channel, and stream ID. A 64-bit stream ID must cross JSON interfaces
as a string to avoid numeric precision loss in JavaScript.

The generated nanopb maximum for the complete frame is 192 bytes; test the
final Meshtastic envelope and security configuration before selecting an RF
packet size. Full-size broadcasts may exceed the space available for a
Meshtastic signature, so authentication requirements also affect packet layout.

**[Documentation/API Reference](https://buf.build/meshtastic/protobufs)**

## Generated client packages

- TypeScript package: `packages/ts`
- Rust package: `packages/rust`
- Kotlin Multiplatform package (Wire): `packages/kmp`

## Field metadata

Fields can carry app/UI-relevant metadata (e.g. `diy_only`) via the `(meshtastic.field_metadata)`
option - see [`meshtastic/field_metadata.proto`](meshtastic/field_metadata.proto). It is exposed to
consumers as reflection-free generated accessors: the KMP package generates them with a Wire
`SchemaHandler` ([`packages/kmp`](packages/kmp/README.md)), and other languages via the
[`tools/protoc-gen-fieldmeta`](tools/protoc-gen-fieldmeta/README.md) plugin (which has integration
recipes for firmware/Apple/Python).

## Stats

![Alt](https://repobeats.axiom.co/api/embed/47e9ee1d81d9c0fdd2b4b5b4c673adb1756f6db5.svg "Repobeats analytics image")

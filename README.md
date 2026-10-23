# Meshtastic Protobuf Definitions

[![CI](https://img.shields.io/github/actions/workflow/status/meshtastic/protobufs/pull_request.yml?branch=master&label=actions&logo=github&color=yellow)](https://github.com/meshtastic/protobufs/actions/workflows/pull_request.yml)
[![CLA assistant](https://cla-assistant.io/readme/badge/meshtastic/protobufs)](https://cla-assistant.io/meshtastic/protobufs)
[![Fiscal Contributors](https://opencollective.com/meshtastic/tiers/badge.svg?label=Fiscal%20Contributors&color=deeppink)](https://opencollective.com/meshtastic/)
[![Vercel](https://img.shields.io/static/v1?label=Powered%20by&message=Vercel&style=flat&logo=vercel&color=000000)](https://vercel.com?utm_source=meshtastic&utm_campaign=oss)

## Overview

The [Protobuf](https://developers.google.com/protocol-buffers) message definitions for the Meshtastic project (used by apps and the device firmware).

This fork also carries the [Airwise v1 frame](airwise/airwise.proto). Airwise
sensor and sink applications exchange serialized `airwise.Frame` payloads on
Meshtastic `PRIVATE_APP` (port 256). The `airwise.options` file bounds ACK missing
record IDs to 16 for nanopb. Firmware routes the payload as application data;
Airwise consumers validate the frame and handle HELLO, DATA, and ACK.

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

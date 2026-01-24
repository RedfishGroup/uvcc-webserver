# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

uvcc-webserver is a Node.js Express server that provides HTTP endpoints for controlling UVC (USB Video Class) webcam settings. It exists primarily to work around browser limitations on Mac where browsers cannot control camera settings directly. Used by AnySurface for camera configuration.

## Development Commands

- `yarn start` or `node index.js` - Start the server (runs on port 3456)
- `yarn install` - Install dependencies (use `npm install` on Mac)

## Platform Installation

- **Mac**: `npm install`
- **Linux**: `yarn install`
- **Windows**: Install Zadig (https://zadig.akeo.ie/) first, then `npm install` (currently not fully working)

## Architecture

Simple two-file structure:
- `index.js` - Express server with REST endpoints, camera caching, and device discovery
- `utils.js` - Helper functions for querying camera controls and ranges

## API Endpoints

All endpoints use GET (settings changes use GET instead of POST for simplicity):

- `/devices` - List all connected UVC cameras
- `/controls/:deviceIdentifier` - Get supported controls for a camera
- `/info/:deviceIdentifier` - Get all control values and ranges
- `/info/:deviceIdentifier/:control` - Get specific control info
- `/get/:deviceIdentifier/:control` - Get current value of a control
- `/set/:deviceIdentifier/:control/:values` - Set a control value

Device identifier can be: camera name, device address, or vendor ID (e.g., `4057` for Elgato).

## Key Implementation Details

- Cameras are cached after first access to avoid slow rediscovery (~250ms per discover call)
- 80ms pause after creating new camera connection before requests work (uvc-control initialization quirk)
- `isAlive()` checks if cached camera is still connected, removes stale entries
- Some cameras are hardcoded as unsupported: `OBSBOT Tiny 4K`, `HD Webcam C615`

## Dependencies

- `express` + `cors` - HTTP server
- `uvc-control` - Low-level USB camera control (forked version from joelpurra)

Requires Node.js >= 18 (ES modules).
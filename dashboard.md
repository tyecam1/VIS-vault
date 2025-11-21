# RC545a VIS — Dashboard

## Overview
Interactive dashboard for all VIS assets, storage locations, and operational status.

---
## Headsets (VR / MR / AR)

```dataview
TABLE 
  device_name AS "Device",
  model AS "Model",
  serial AS "Serial",
  status AS "Status",
  location AS "Location",
  kit AS "Kit"
FROM "13-projects/VIS/assets"
WHERE category = "headset" OR category = "ar_glasses" OR category = "ar_controller"
SORT device_name ASC
```

---
## Tracking Devices
```dataview
TABLE 
  device_name AS "Device",
  model AS "Model",
  serial AS "Serial",
  status AS "Status",
  location AS "Location",
  kit AS "Kit"
FROM "13-projects/VIS/assets"
WHERE category = "tracker" OR category = "sensor"
SORT device_name ASC
```

---
## Interaction Devices
```dataview
TABLE 
  device_name AS "Device",
  model AS "Model",
  status AS "Status",
  location AS "Location",
  kit AS "Kit"
FROM "13-projects/VIS/assets"
WHERE category = "input_device"
SORT device_name ASC
```

---
## Networking / Infrastructure

```dataview
TABLE 
  device_name AS "Device",
  model AS "Model",
  serial AS "Serial",
  status AS "Status",
  location AS "Location"
FROM "13-projects/VIS/assets"
WHERE category = "networking" OR category = "computer" OR category = "display"
SORT device_name ASC
```
---
## Furniture
```dataview
TABLE 
  device_name AS "Furniture",
  subtype AS "Type",
  location AS "Location",
  status AS "Status"
FROM "13-projects/VIS/assets"
WHERE category = "furniture"
SORT device_name ASC
```
---
## Assets Needing Information
```dataview
TABLE 
  device_name AS "Asset",
  model AS "Model",
  serial AS "Serial",
  notes AS "Notes"
FROM "13-projects/VIS/assets"
WHERE status = "needs_info"
SORT device_name ASC
```
---
## Recently Updated
```dataview
TABLE 
  device_name AS "Asset",
  file.mtime AS "Last Modified"
FROM "13-projects/VIS/assets"
SORT file.mtime DESC
LIMIT 10
```
---

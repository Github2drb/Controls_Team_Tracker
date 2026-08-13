# Equipment Documentation — Data Schema

New data file in `Controls_Team_Tracker` repo: **`equipment-docs.json`**
New image folder in the same repo: **`equipment-images/`**

## `equipment-docs.json` shape

```ts
interface StationImage {
  url: string;         // raw.githubusercontent.com URL
  caption?: string;
  uploadedAt: string;  // ISO timestamp
}

interface StationDoc {
  id: string;                // e.g. "station-1"
  name: string;               // e.g. "Station 1 — Loading"
  status: string;              // reuses existing status codes (02_STATUS_CODES.md)
  electricalParts: string;    // free text: PLC I/O modules, drives, sensors, contactors etc.
  process: string;             // what happens at this station
  inputs: string;              // input signals/devices
  outputs: string;             // output signals/devices
  images: StationImage[];
}

interface EquipmentDoc {
  projectName: string;         // matches /api/project-names entry — primary key
  synopsis: string;            // macro overview of the equipment
  plcArchitecture: string;     // PLC make/model, racks, network topology, redundancy
  safetyLayout: string;        // safety PLC/relays, e-stops, light curtains, category/SIL/PL
  hasMultipleStations: boolean;
  stations: StationDoc[];      // empty array if single-station equipment
  updatedAt: string;
  updatedBy?: string;
}
```

`equipment-docs.json` on disk = `EquipmentDoc[]`, one entry per project that has
been documented. Projects with no entry yet simply aren't in the array —
the GET route returns a blank skeleton for those so the UI has something to edit.

Images are NOT stored inline (keeps JSON small and diff-friendly). Each upload
is committed as its own file to `equipment-images/<project-slug>/<station-id>-<timestamp>-<filename>`,
and only the resulting URL is stored in the JSON.

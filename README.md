# yak-power-config

OEM preset bundles + preset packs for **Yak Power** / **AF LED** controllers,
consumed by the mobile app. **Public** so the app fetches packs anonymously
(same model as the firmware-OTA repo); OEM publishing uses a scoped write token
entered in the app's password-gated **OEM mode**.

## Layout
- `packs/` — installable **preset packs** (`*.bin`), each 1–8 presets. Customers
  browse + additively install these (into empty slots) from the app's Preset Library.
- `bundles/` — full **config bundles** (golden-master clones); used for full
  re-provision and for firmware-embedded factory provisioning.
- `index.json` — optional catalog the app lists (name, file, channels, description).

## Binary format (`YPPK`)
```
'YPPK'(4)  ver(1)  count(1)  then count × [ slot(1)  blobLen(2, LE)  blobBytes ]
```
Each blob is one opaque `PresetBlob` exactly as stored on the controller. An 8-ch
pack only installs onto an 8-ch controller (guarded by `blobLen` + the blob's own
`numChannels` field). Firmware ≥ **v7.20** required (FFFB).

## Flows
- **OEM publish** — configure a golden controller → app OEM mode → Export →
  Publish (in-app via token, or Export-to-file + manual upload here).
- **Customer install** — app Preset Library → pick a pack → installs into empty slots.
- **Factory provisioning** — drop a bundle in the firmware repo's `config/`,
  run `tools/embed_config.py`, rebuild → fresh units self-provision on first boot.
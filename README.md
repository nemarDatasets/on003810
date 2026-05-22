[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on003810-blue)](https://doi.org/10.82901/nemar.on003810)

This dataset consists of electroencephalography (EEG) signals adquired with a low-cost consumer-grade device. The 10 participants had no previous BCI experience. The BCI protocol consisted of two conditions, namely the kinesthetic imagination of grasping movement (MI) of the dominant hand and rest/idle condition.Five protocol runs were asked to be performed by the user. The first run, called RUN0, involved real grasping  movement in order to better explain the protocol and to help the subject to focus on the sensation of making the movement.  The rest of the runs (RUN1-RUN4) were equal, consisting of MI vs.Rest conditions. The EMG signals of the dominant hand was adquired for protocol control.  During acquisition, the EEG signals were filtered between 0.5 and 45 Hz with a 3rd order Butterworth bandpass-filter.

## NEMAR curation changes (2026-05-21)

BIDS validator: 1 error + 1103 warnings → 0 errors + 301 warnings. Raw `.edf` binary payloads unchanged.

### `dataset_description.json`
- Added `DatasetType: "raw"`.
- Added `GeneratedBy: [{Name: "nemar-cli", Version: "0.8.8", CodeURL: "https://github.com/nemar-org/nemar-cli"}]`.
- Bumped `BIDSVersion` `1.1.1` → `1.8.0`.

### `task-MIvsRest_channels.tsv`
- Header `Name/Type/Units` → BIDS-canonical lowercase `name/type/units`.
- All 15 rows: `Microv` → `µV` (BIDS-canonical unit string).

### `task-MIvsRest_eeg.json` (new, inheriting root sidecar)
- Created to close 50× recommended-key warnings via BIDS inheritance.
- Populated only from values already present in per-recording sidecars and from explicit statements in this README. Values: `TaskName="MIvsRest"`, `TaskDescription` (paraphrased from this README — no detail added beyond what is stated here), `EEGReference="Left Ear lobe"`, `SamplingFrequency=125`, `PowerLineFrequency=50`, `EEGChannelCount=15`, count-zero fills (EOG/ECG/EMG/MISC/Trigger), `EEGPlacementScheme="10-20"` (inferred from channel names — T3/T4/T5/T6 are 10-20 labels), `HardwareFilters={Bandpass: Butterworth 3rd-order 0.5-45 Hz}` (from README), `InstitutionName="PROMAB Lab. IMAL - CONICET. Santa Fe. Argentina."`, `RecordingType="continuous"`, `SoftwareFilters="n/a"`. Device manufacturer/model fields (`Manufacturer`, `ManufacturersModelName`, `CapManufacturer`, `CapManufacturersModelName`) and address/department/version/ground fields set to `"n/a"` — not stated in the source dataset.

### `sub-*/eeg/sub-*_task-MIvsRest_run-*_eeg.json` (50 per-recording sidecars)
- Slimmed to only contain `RecordingDuration` (per-recording value, computed from EDF). All other fields now inherited from the new root template.
- Removed incorrect `TaskName: "MIvsRestRUN<N>"` overrides (must match `task-X` entity per BIDS; the inheriting `TaskName: "MIvsRest"` is correct).

### `task-MIvsRest_events.json`
- Added `sample` column definition.
- Cleaned existing `value` Levels descriptions and corrected unit strings (`"second"` → `"s"`).
- HED dictionary unchanged.

### `sub-*/eeg/sub-*_task-MIvsRest_run-*_events.tsv` (50 new files)
- Generated mechanically from each EDF's own annotations using `mne.io.read_raw_edf`. Mapping `OVTK_GDF_Right` → `value=7` (MI) and `OVTK_GDF_Tongue` → `value=9` (Rest) follows the existing `task-MIvsRest_events.json` `Levels` dictionary. Per-event `duration` is the seconds from the cue annotation to the next `OVTK_GDF_End_Of_Trial` annotation in the same EDF. Counts vary by run: RUN0 → 10 events (5 MI + 5 Rest); RUN1–RUN4 → 30–40 events each (typically 20 MI + 20 Rest). No event was synthesized; every row corresponds to an annotation in the EDF. Closes the original `SIDECAR_WITHOUT_DATAFILE` error and the 50× `EVENTS_TSV_MISSING` warnings.

### Directory rename
- `Code/` → `code/` (BIDS-canonical lowercase). Closes `NOT_INCLUDED:/Code/` error.
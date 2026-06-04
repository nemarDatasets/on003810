[![DOI](https://img.shields.io/badge/DOI-10.82901%2Fnemar.on003810-blue)](https://doi.org/10.82901/nemar.on003810)

This dataset consists of electroencephalography (EEG) signals adquired with a low-cost consumer-grade device. The 10 participants had no previous BCI experience. The BCI protocol consisted of two conditions, namely the kinesthetic imagination of grasping movement (MI) of the dominant hand and rest/idle condition.Five protocol runs were asked to be performed by the user. The first run, called RUN0, involved real grasping  movement in order to better explain the protocol and to help the subject to focus on the sensation of making the movement.  The rest of the runs (RUN1-RUN4) were equal, consisting of MI vs.Rest conditions. The EMG signals of the dominant hand was adquired for protocol control.  During acquisition, the EEG signals were filtered between 0.5 and 45 Hz with a 3rd order Butterworth bandpass-filter.

## NEMAR curation changes (2026-05-21, revised 2026-05-27)

The BIDS validator went from 1 error + 1103 warnings to 0 errors + 352 warnings. None of the raw `.edf` files were modified, every change is to a text sidecar.

**Dataset description (`dataset_description.json`)**
- Added `DatasetType: "raw"` so the dataset is validated as raw data rather than a derivative.
- Updated `BIDSVersion` from `1.1.1` to `1.11.1` (the version the current validator checks against).
- `GeneratedBy` was left absent, exactly as the source published it, nothing was added there.

**Channel tables (`task-MIvsRest_channels.tsv`, shared by all 50 recordings)**
- The column headers were `Name`, `Type`, `Units`; BIDS expects the lowercase forms `name`, `type`, `units`, so they were renamed (the values were already correct).
- The unit string `Microv` was replaced with the BIDS-canonical `µV` on every electrode row so the validator recognizes the unit.

**Per-recording sidecars (`_eeg.json`, all 50 recordings)**
- Each per-recording sidecar carried a `TaskName` that ended with the run number (for example `MIvsRestRUN0`). BIDS requires `TaskName` to match the `task-` label in the filename exactly, which is `MIvsRest`, so those run-specific overrides were removed. The correct `TaskName: "MIvsRest"` is supplied once at the dataset root and inherited.
- The per-recording sidecars were trimmed down to only the value that genuinely varies by recording, `RecordingDuration` (computed from each EDF). Every other field is now inherited from the shared root sidecar so the same value isn't repeated 50 times.

**Shared recording sidecar added at the dataset root (`task-MIvsRest_eeg.json`)**
- A single root sidecar was added so the fields that are identical across all 50 recordings live in one place and are inherited automatically. This closes a large number of "recommended field missing" warnings without duplicating information.
- The values are pulled only from what is already documented: the task name `MIvsRest`, a task description paraphrased from the abstract above (no new detail invented), the EEG reference `Left Ear lobe` and sampling frequency `125 Hz` taken from the per-recording sidecars, the power-line frequency `50 Hz` (Argentina mains), 15 EEG channels with zero counts for EOG/ECG/EMG/MISC/Trigger, the `10-20` placement scheme (the channel names T3/T4/T5/T6 are 10-20 labels), the hardware filter (3rd-order Butterworth bandpass 0.5-45 Hz, stated in the abstract), the institution name from the source dataset, and `RecordingType: "continuous"`. Manufacturer, model, cap, ground, and version fields that the source dataset does not state are recorded as `"n/a"` rather than guessed.

**Events dictionary (`task-MIvsRest_events.json`)**
- Added a definition for the `sample` column so the events tables are fully described.
- Cleaned up the descriptions of the `value` levels and corrected the duration unit string from `"second"` to the BIDS-canonical `"s"`.
- The HED tag dictionary was left as-is.

**Events tables (`_events.tsv`, all 50 recordings, newly generated)**
- The per-recording events tables were missing entirely, which the validator flagged as an error against `task-MIvsRest_events.json` (a sidecar without a data file) and as a "missing events" warning for every recording.
- Each `_events.tsv` was generated mechanically from the annotations inside that recording's own EDF (read with `mne.io.read_raw_edf`). The annotation labels in the EDF map directly to the value levels already declared in `task-MIvsRest_events.json`: `OVTK_GDF_Right` is `value=7` (MI) and `OVTK_GDF_Tongue` is `value=9` (Rest). Each event's `duration` is the time from the cue annotation to the next `OVTK_GDF_End_Of_Trial` annotation in the same recording.
- Event counts vary by run, matching the protocol: RUN0 has 10 events (5 MI plus 5 Rest), and RUN1 through RUN4 have 30 to 40 events each (roughly 20 MI plus 20 Rest). No events were synthesized, every row corresponds to an annotation already present in the EDF.

**Directory rename (`Code/` to `code/`)**
- BIDS requires lowercase top-level directory names, so the `Code/` folder was renamed to `code/`. This closes the validator error that flagged `Code/` as a not-allowed top-level entry.

**Remaining warnings (352), left on purpose**
- These are all "recommended but missing" fields that need information from the study, lab, or equipment that isn't in the dataset. Per recording the validator asks for `DeviceSerialNumber`, `Instructions` (the verbatim instructions read to the participant), `CogAtlasID` and `CogPOID` (cognitive-atlas task identifiers), `HeadCircumference`, `SubjectArtefactDescription`, and `StimulusPresentation` details. At the dataset level it also asks for `HEDVersion` and `GeneratedBy`. `GeneratedBy` is among the remaining warnings on purpose: the source dataset does not document what tool generated the data, so nothing was added there. The rest were left blank rather than filled with guesses.

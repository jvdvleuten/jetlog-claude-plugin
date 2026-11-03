# Entry fields you can set

Reference for the jetlog-logbook skill: the fields of an `entry` that a `propose_changes` operation may carry in `data`, and their exact shapes. Send only the fields the pilot gave you. Times are UTC, `HH:MM`. Dates are `YYYY-MM-DD`.

## Every entry

- `date`, `type` (`flight` or `fstd`), `remarks` (text).
- `people`: the crew, a list of `{"person_id": "...", "role": "..."}`. Use `"SELF"` for the pilot. Role codes are below.

## Flights (`type: "flight"`)

- `flight_number`, `registration`, `from`, `to` (ICAO codes).
- Actual times: `off_blocks`, `airborne`, `touchdown`, `on_blocks`. These are the times the logbook counts. Block time is off-blocks to on-blocks.
- `update_flight_data` (true or false): whether automatic flight tracking may fill in and update the times. Set it to `false` when the pilot gives you their own times and wants them kept. A time the pilot enters by hand is respected either way.
- `ifr` (true or false).
- `takeoffs_and_landings`, one of two shapes:
  - Counted by Jetlog, split into day and night from the times: `{"type": "auto", "takeoffs": 1, "landings": 1}`.
  - Given by the pilot per day and night: `{"type": "manual", "takeoffs_day": 1, "takeoffs_night": 0, "landings_day": 0, "landings_night": 1}`. All four are required in this shape.
- `approaches`: a list of `{"type": "...", "count": 1, "autolands": 0}`. Copy the approach type the pilot uses on earlier entries (read one with `get_entry`); ask if you have not seen it.
- `go_arounds`, `passengers_on_board`, `fuel_planned`, `fuel_used`, `cargo_on_board` (whole numbers), only when the pilot asks.

## Simulator sessions (`type: "fstd"`)

- `date`, `start_time`, `end_time` (the session's own start and end, `HH:MM` UTC).
- `fstd_id`: the device. Use the id of a device in the logbook (an `fstd` resource). A new device can be created in the same proposal with a `create` operation on `resource: "fstd"`.
- `session_type`: copy the value the pilot's earlier sessions use (for example a recurrent, LPC, OPC or LOE session).
- `fstd_takeoffs`, `fstd_landings` (whole numbers).
- No route, registration or block times on a session, and no flight fields on a session.

## Per-function time overrides (`manual_times`)

`manual_times` is a map of durations in `H:MM` (hours can run past 24 only on a carry-over entry). Keys: `total_time_of_flight`, `total_air_time`, `night`, `ifr`, `cross_country`, `pilot_in_command`, `co_pilot`, `dual`, `spic`, `picus`, `instructor`, `examiner`, `single_pilot_single_engine`, `single_pilot_multi_engine`, `multi_pilot`, `cruise_relief_co_pilot`, `fstd_session`.

These figures count only on:

- A carry-over entry: the totals from a previous logbook, entered as one entry. Any value of 24 hours or more makes the entry a carry-over entry automatically.
- An entry imported from another logbook, where `total_time_of_flight` is the block time from that logbook.

On a normal flight Jetlog calculates every function time from the actual times, the role, the aircraft type and the airports. To correct a normal flight's time, correct its actual times, not `manual_times`. Do not use `manual_times` to force a total; tell the pilot what drives the figure instead.

## Role codes

Use these exact codes in `people`. Prefer the code the pilot's earlier entries use for the same seat.

| Code | Role |
| --- | --- |
| `PIC` | Pilot in command |
| `CP` (also `FO`) | Co-pilot |
| `CRCP` | Cruise relief co-pilot |
| `PICUS` | Pilot in command under supervision |
| `SPIC` | Student pilot in command |
| `FI` | Flight instructor |
| `FE` | Flight examiner |
| `RI`, `RI_CP` | Route instructor (in the left or right seat) |
| `LCA`, `LCAI`, `LCAIFO` | Line check airman, line check initial, line check initial as FO |
| `SI_PIC`, `SI_CP`, `SI_OBS` | Senior instructor as PIC, as co-pilot, as observer |
| `STU` | Student |
| `OBS` | Observer |
| `SAFETY` | Safety pilot |
| `DH` | Deadhead (positioning as a passenger) |
| `CA`, `CS`, `Purser`, `SP` | Cabin crew roles (other crew on the entry) |
| `FSTD_TRN`, `FSTD_INS`, `FSTD_EXA`, `FSTD_OBS`, `FSTD_SI` | Simulator session roles: trainee, instructor, examiner, observer, senior instructor |

On a simulator session use the `FSTD_*` codes, on a flight the others. Any other string is kept as a custom role but credits no pilot-function time, so do not make one up.

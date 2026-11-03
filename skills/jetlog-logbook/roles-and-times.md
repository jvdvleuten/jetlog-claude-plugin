# Roles and times in a Jetlog logbook

Reference for the jetlog-logbook skill. It explains how Jetlog credits time, so you can explain a total to a pilot without recomputing it. For any figure, use `get_totals`.

## Role decides the time

Every flight entry has crew, and each crew member has a role on that entry. The pilot's role decides which pilot-function time the flight credits: PIC, co-pilot, dual, instructor and so on. Total time, night and IFR are credited regardless of role.

Roles are not entry types. PICUS, SPIC and the others are a role on a normal flight entry. Use the role strings the logbook already uses; do not invent one.

- PIC: pilot in command time.
- Co-pilot: co-pilot time. Cruise Relief Co-pilot (CRCP) is a co-pilot role: the credited time is calculated from block time and the number of crew under Part-FCL, it counts as co-pilot time and needs no signature. For the ATPL at most 250 hours of cruise relief count (FCL.510.A); the ATPL progress tracker in the app applies that cap.
- PICUS (pilot in command under supervision): counts as co-pilot time until the entry has a signature. Once signed, it counts as EASA PIC time.
- SPIC (student pilot in command): counts as dual instruction until the entry is signed. Once signed, it counts as PIC.
- Flight Instructor: logged as PIC time and as instructor time. No signature needed.
- Observer and Safety Pilot log no EASA time.
- No role: when the pilot is not in the crew of an entry, Jetlog credits only the role-free times (total, air, night, IFR, cross-country, aircraft class) and no PIC, co-pilot, dual, instructor, SPIC or PICUS time.

If a PICUS or SPIC total looks too low, the usual cause is unsigned entries. Count them and tell the pilot.

## Multi-pilot time

Multi-pilot is not a role. Jetlog sets it from the aircraft's ICAO type: if the type is certified for multi-pilot operations, the entry shows a multi-pilot figure. The pilot can change it on the entry in the app.

## Times on an entry

- Four actual times: off-blocks, airborne, touchdown, on-blocks (`derived.off_blocks`, `derived.airborne`, `derived.touchdown`, `derived.on_blocks`). Scheduled times exist as well, but the actual ones are what the logbook counts.
- Block time runs from off-blocks to on-blocks. Air time is the part between airborne and touchdown. Entries with no calculated block time yet are left out of totals.
- Tracked or manual: with automatic flight tracking on, the actual times come from the airline feed and the entry's date is the tracked (system) date. With tracking off (`update_flight_data` false) the pilot's manual times are used. A manual edit is always respected.
- Derived date: the date used for period filters and for night calculation is the tracked date when tracking is on, otherwise the planned date.
- Late actuals: an arrival time can post minutes to hours late, or on some routes never. In the meantime the entry shows the scheduled or estimated time. If a time looks wrong, the pilot corrects it by hand; do not enter one for them from a guess.

## Night, IFR, cross-country

- Night is calculated per minute of the block from the sun's position along the route. A minute counts as night when the sun is more than 6 degrees below the horizon. It needs the airport positions. An airport that is not in the catalog (and not one of the pilot's own places with coordinates) gets no night time and no distance figure, so a night total can be lower than the truth. Say so if an airport is missing.
- Night and IFR are never more than the flight's logged total time.
- IFR comes from the entry's IFR flag, which the pilot or tracking sets.

## Simulator sessions

Entries of type `fstd` are simulator sessions. They count in total simulator time and in the pilot's overall totals where a regulator expects them, but not as flights, sectors or route-map legs. They have a device and a session type (recurrent, LPC, OPC, LOE, type rating, or a plain training session), and a start and end time. Night and IFR do not apply. Examiner signatures on LPC, OPC and LOE sessions are common.

## Not flights

Roster days (duty, standby, ground, off) are not flights and do not count in any total.

## Places and codes

The pilot's own places (`list_places`) win over the airport catalog for every code they have. Changing one recalculates the night and distance figures of every flight that uses it.

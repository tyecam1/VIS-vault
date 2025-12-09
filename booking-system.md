# RC545a VIS Booking Process (Microsoft 365)

## Booking system
- Booking is handled via Microsoft 365, not inside this vault.
- Inventory lives in this vault, suite availability lives in Outlook.

## How to book VIS assets

1. Open the calendar in Outlook.
2. Create a new event in the desired time slot.
3. In the **Location**, include:
   - `RC545a VIS` (room name) and any remote call link if needed.

## When to update the asset notes

Update the asset note in these cases:
- The device is out of service for more than one day:
	- Set `status: maintenance` and add a fault note.
- The device is on long-term loan
	- Set `status: out_of_lab` and note who holds it in the body of the note.
- The device is retired or replaced:
	- Set `status: retired` and add a short explanation.

Short bookings (single sessions) are managed entirely in Outlook and do not need to be recorded in Obsidian.

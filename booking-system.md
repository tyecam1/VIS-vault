# RC545a VIS Booking Process (Microsoft 365)

## Booking system
- Booking is handled via Microsoft 365, not inside this vault.
- Inventory lives in this vault, suite availability lives in Outlook.

## How to book VIS assets

1. Open the calendar in Outlook.
2. Create a new event in the desired time slot.
3. In the **Title**, include the asset IDs you need, for example:
   - `MR session – VIS-HMD-VFV-01, VIS-HMD-META-KW49-01`
4. In the **Location**, include:
   - `RC545a VIS` (room name) and any remote call link if needed.
5. In the **Body**, specify:
   - Names of users
   - Purpose of session
   - Any special requirements (Unity / Unreal / Teams, etc).

## How this links to the inventory

- The inventory in `vis/assets` lists all assets and their IDs.
- Staff select the IDs from the vault and paste them into the Outlook booking title.
- Long term changes to availability (maintenance, retirement, long loan) are reflected by updating:
  - `status` in the asset YAML
  - Optional notes in this file.

## When to update the asset notes

Update the asset note in these cases:
- The device is **out of service** for more than one day:
  - Set `status: maintenance` and add a fault note.
- The device is **on long-term loan**:
  - Set `status: out_of_lab` and note who holds it in the body of the note.
- The device is **retired or replaced**:
  - Set `status: retired` and add a short explanation.

Short bookings (single sessions) are managed entirely in Outlook and do not need to be recorded in Obsidian.

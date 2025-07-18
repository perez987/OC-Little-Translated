# Enabling Power and Sleep Buttons (`SSDT-PWRB/SLPB`)

## Instructions

In **DSDT**, search for:

- `PNP0C0C` and add ***SSDT-PWRB*** if it is missing. Adds Power Button Device
- `PNP0C0E` and add ***SSDT-SLPB*** if missing. The Sleep Button Device is mandatory for [`PNP0C0E Sleep Method`](/Content/04_Fixing_Sleep_and_Wake_Issues/PNP0C0E_Sleep_Correction_Method) to work.
- In some cases (like HP or Lenovo laptops), the `SLPB` is present in the `DSDT`, but may be disabled:
	
	```asl
    Scope (_SB)
    {
        Device (SLPB)
        {
            Name (_HID, EisaId ("PNP0C0E") / * Sleep Button Device * /) // _HID: Hardware ID
            Name (_STA, Zero) // _STA: Status
        }
    }
    ```
    
    This is resolved with `SSDT-SLPB_STA0B` by changing the status of `SLPB` to `0x0B`:
    
    ```asl
    Scope (\)
    {
        If (_OSI ("Darwin"))
        {
            \_SB.SLPB._STA = 0x0B
        }
    }
    ```
- Once the Power and/or Sleep button device is present, pressing `Ctrl` and holding the `power` button for 2 seconds, the shutdown menu should appear:<br>![powermenu](https://github.com/user-attachments/assets/8c325335-d750-43cd-a32e-0a35094d2349)

> [!CAUTION]
> 
> Ensure that the ACPI path of the LPC Bus (`LPC` or `LPCB`) used in the SSDT is identical with the one used in your system's `DSDT`! 

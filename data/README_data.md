# Data for indicators HARD_001 (MSMDI) and HARD_002 (Komboindeksen)

Source: Vannmiljo, the Norwegian Environment Agency's database,
<https://vannmiljo.miljodirektoratet.no/>. Licensed under Norsk lisens for
offentlige data (NLOD) 2.0, <https://data.norge.no/nlod>.

Downloaded manually from Vannmiljo and filtered by `draft_analyses/04_prepare_data.R` on 16 september 2026.

## Files

| File | Rows | Content |
|---|---|---|
| `vm_macroalgae.csv` | 19904 | Macroalgae registrations (quality element MAA) plus the littoral habitat description parameters (`DF*`, `AF*`, `BF*`) |
| `vm_hardbottom_fauna.csv` | 6039 | Marine hard-bottom fauna (quality element BVD), used for sea-urchin grazing pressure as supporting information |
| `vm_stations.csv` | 137 | One row per water locality, with UTM33 coordinates and the programmes and years it appears in |
| `vm_parameters.csv` | 31 | Lookup from `Parameter_id` to the Vannmiljo parameter name and unit |

## Scope

Monitoring programmes: OEKK, YOFJ.
Years: 2021-2024.
Parameters: 13 of the 33 requested (see `draft_analyses/output/parameter_availability.csv`).

## Encoding

All csv files are UTF-8 without BOM, with `.` as decimal separator. Read them
with the encoding stated explicitly, so the result does not depend on the
reader's locale:

```r
read_csv("data/vm_macroalgae.csv", locale = locale(encoding = "UTF-8"))
```

## Columns

Column names are verbatim from the Vannmiljo export, with these added:
`source_file`, `date`, `year`, and the parsed numeric columns `value`,
`upper_m` and `lower_m` (which replace the text columns `Verdi`, `Ovre_dyp`
and `Nedre_dyp`).

The following export columns were dropped from the registration files.
Nothing is lost - they were empty, constant, or moved to a lookup table.

*Empty for every macroalgae record:*
`Parameter_casnr`, `UnntasKlassifisering`, `Deteksjonsgrense`, `Kvantifiseringsgrense`, `Produktbeskrivelse`

*Constant on every row, with the value that applied:*
`Type` = Kyst, `Medium_id` = VS, `Medium_navn` = Saltvann, `DybdeEnhet` = m, `Filtrert_Prove` = Ufiltrert, `Operator` = =, `Opprinnelse` = AUTO, `Arkiv` = N

*Moved elsewhere:* `Vannlokalitetsnavn`, `UTM33 Ost (X)`, `UTM33 Nord (Y)`
to `vm_stations.csv`; `Parameter_navn`, `Enhet` to `vm_parameters.csv`;
`Aktivitet_navn` is recoverable from `Aktivitet_id`; `LatinskNavn_id` is
redundant with `VitenskapligNavn`. Join on `Vannlokalitet_kode` and
`Parameter_id` respectively.

## Known gaps

See `draft_analyses/output/parameter_availability.csv` for counts per
parameter and year. Notably absent from Vannmiljo for 2021-2024:
`MAKOMBO1`-`MAKOMBO5` (the finished Komboindeks), `MASS1`-`MASS5`
(EQR sjosone) and `DUTA` (depth extent of filamentous algae, the third
Kombo sub-parameter).

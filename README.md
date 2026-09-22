# Verð á rafmagni og heitu vatni fyrir Home Assistant
Þessi yaml template kóði er ætlaður fyrir [Home Assistant](https://www.home-assistant.io/) og notar gögn frá [snjallmæli](https://www.veitur.is/l/snjallmaelar) með upplýsingum um heildarnotkun á rafmagni og vatni. Kóðinn reiknar svo út heildarverð á rafmagni og heitu vatni bæði daglega og mánaðarlega út frá föstum verðum sem eru innslegin frá notanda.

Þú þarft þegar að vera með tengingu við P1 tengið á snjallmælinum sem skilar heildar rafmagnsnotkun (kWh) og heitavatnsnotkun (m³) yfir í Home Assistant.

# Uppsetning
Þú þarft að vera með [Samba share app](https://www.home-assistant.io/common-tasks/os/#installing-and-using-the-samba-app) uppsett í Home Assistant til að nálgast möppur beint.

Afritaðu [electricity_hot_water_price.yaml](https://github.com/kristjanbjarni/ha-electricity-hot-water-price/releases/download/v1.1/electricity_hot_water_price.yaml) yfir í `config/packages` möppu í Home Assistant.
Hugsanlega þarftu að búa til `packages` möppuna ef hún er ekki þegar til.

Bættu við eftirfarandi `packages` línu í `config/configuration.yaml` skránna:
```yaml
homeassistant:
  packages: !include_dir_named packages
```
Sjá [Configuration packages](https://www.home-assistant.io/docs/configuration/packages/) fyrir leiðbeiningar.

Farðu í `Settings` > `Tools` > `YAML` og smelltu á `Check configuration` til að athuga hvort `configuration.yaml` sé í lagi.

Endurræstu Home Assistant með því að smella á `Restart`

# Velja mæla
Velja þarf þá mæla sem gefa heildarnotkun á rafmagni og heitu vatni. Bættu við entities card með eftirfarandi kóða:
```yaml
type: entities
entities:
  - entity: select.electricity_meter
  - entity: select.hot_water_meter
title: Mælar
```
<img width="519" height="224" alt="image" src="https://github.com/user-attachments/assets/94b8f8c4-7ffa-497f-8dfd-d93e4ed5018d" />


Veldu þær einingar sem gefa heildarnotkun á rafmagni og heitu vatni.

Ef þú ert að nota ESPHome [veitur-meter-reader](https://github.com/gardart/veitur-meter-reader) þá eru þetta réttu einingarnar:
  - sensor.veitur_meter_cumulative_active_import
  - sensor.veitur_meter_hot_water_volume

# Innslegin verð
Bættu við entities card með eftirfarandi kóða:
```yaml
type: entities
entities:
  - entity: input_number.electricity_price
  - entity: input_number.electricity_price_day
  - entity: input_number.hot_water_price
  - entity: input_number.hot_water_price_day
  - entity: input_number.billing_fee
title: Föst verð (án VSK)
```
<img width="517" height="460" alt="image" src="https://github.com/user-attachments/assets/98f5b6f1-a2e8-4440-8744-e7d518bf36d6" />

Breyttu svo verðum í réttar upphæðir eins og þær eru skilgreindar á reikningi. Sem dæmi ef þú ert með rafmagn frá Orkubú Vestfjarða og notar Veitur sem flutningsaðila eru eftirfarandi réttar upphæðir:
  - Rafmagnsverð (kWh) = 17.05 kr (Verð frá OV + Verð frá Veitum)
  - Rafmagnsverð (Dag) = 46.52 kr (Veitur)
  - Verð heitt vatn (m³) = 200.99 kr (Veitur)
  - Verð heitt vatn (Dag) = 90 kr (Veitur)
  - Þjónustugjald = 62 kr (Veitur)

# Súlurit
Til að fá daglega notkun geturðu notað eftirfarandi statistics-graph:
```yaml
type: statistics-graph
entities:
  - sensor.electricity_price_daily_cycle
  - sensor.hot_water_price_daily_cycle
  - sensor.energy_price_daily_cycle
title: Dagur
days_to_show: 31
period: day
chart_type: bar
stat_types:
  - state
```
<img width="518" height="368" alt="image" src="https://github.com/user-attachments/assets/b67658cf-2c6b-4281-8a29-297f7fffdfcb" />

Til að fá mánaðarnotkun geturðu notað eftirfarandi statistics-graph:
```yaml
type: statistics-graph
entities:
  - sensor.electricity_price_monthly_cycle
  - sensor.hot_water_price_monthly_cycle
  - sensor.energy_price_monthly_cycle
title: Mánuður
days_to_show: 365
period: month
chart_type: bar
stat_types:
  - state
```
<img width="518" height="368" alt="image" src="https://github.com/user-attachments/assets/dcd981c5-e8e1-40b1-adee-5eaeb2f09ffd" />

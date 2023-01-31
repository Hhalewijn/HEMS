# Grid Aansluiting
Het eerst onderdeel dat van belang is voor HEMS is de gridaansluiting. Hoe is de woning verbonden met het energienet en wat zijn de specificaties van die aansluiting? Tevens definiëren we nu welke sensoren we nodig hebben en wat deze moeten gaan meten en vastleggen.
Voor de grid aansluiting zijn er 3 aspecten belangrijk om te meten:
- 	Het aantal kWh dat wordt afgenomen
- 	Het aantal kWh dat wordt terug geleverd (via zonnepanelen of andere energiebronnen)
- 	De bandbreedte van de gridaansluiting.

In relatie tot het meten van de bandbreedte moeten dan de volgende sensoren beschikbaar zijn per fase die de gridaansluiting heeft.
-   Spanning – V(olt)
-   Stroom – A(mpere)
-   Vermogen (power: product of Volt and Current) – W(att)

Er zijn verschillende Gridmeters mogelijk: De slimme meter (DSMR) kan bijvoorbeeld via de P1 poort informatie vrijgeven maar is standaard beperkt tot alleen de kWh meting. Er zijn interfaces beschikbaar die meer informatie uit de DSMR kunnen halen. Maar er zijn ook losse tussenmeters verkrijgbaar die het grid kunnen meten:
-	SolarEdge Smart Meter dmv Integratie
-	Elke Modbus smartmeter, bijvoorbeeld Carlo Gavazi, ABB en anderen. Soms worden de meetgegevens via een Integratie vrijgegeven, zoals in de situatie van Victron Energy. Modbus meters kunnen vaak ook spanning, stroom en frequentie meten.
-	Ook een pulsmeter met een S0 aansluiting kan gebruikt worden maar is dan uitsluitend geschikt voor kWh metingen. Pulsen moeten dan omgerekend worden naar Hz en vervolgens naar kWh. In Home Assistant is hiervoor de Riedemann integratie beschikbaar.
-	Om gebruik te maken van dag- en nachttarieven kan het noodzakelijk zijn om ook een grid meter te gebruiken die dit onderscheid kan maken, bijvoorbeeld een Youless met P1 koppeling aan de DSMR. 
 
# Inputvariabelen
Onderstaande Input variabelen worden gebruikt om de maximale aansluitcapaciteit te bewaken.
Sensor | Type | Value | Remark
------ | ---- | ----- | ------
Input_select_grid_connection_capacity | Helper | 16,25,35,40,50,63,80 | Input used to calculate total allowed Grid Power  
Input_select_grid_number_of_phases | Helper	| 1,3 | Input used to calculate total allowed Grid Power
Input_number.grid_threshold	| Helper | 0-100% | Input used to determine the nominal Grid Power we want to use

# Meetsensoren
## Victron Energy / Carlo Gavazzi Grid Sensor
Als Grid sensor maak ik gebruik van de Carlo Gavazzi die onderdeel is van het Victron Energy ESS. Deze meter is met een RS485 datakabel verbonden met de Cerbo, het centrale hart van het Victron ESS. Via de Cerbo kunnen de devices met Modbus-TCP worden uitgelezen. Voor de naamgeving heb ik daarom elke sensor de letters VE meegegeven: Victron Energy.
Sensor | Class | Meas. | Scale |Precision | Integration | Slave | Address | Type
------ | ----- | ----- | ----- | -------- | ----------- | ----- | ------- | ----
sensor.ve_grid_power_l1 | Power | W | 1 | 0 | Victron Energy through Modbus | 30 | 2600 | Int16
sensor.ve_grid_power_l2	| Power | W	| 1	| 0	| Victron Energy through Modbus	| 30 | 2601	| Int16
sensor.ve_grid_power_l3	| Power | W | 1 | 0	| Victron Energy through Modbus	| 30 | 2602	| Int16
sensor.ve_grid_voltage_l1 | Voltage | V |0.1 | 1 | Victron Energy through Modbus | 30 | 2616 | Uint16
sensor.ve_grid_voltage_l2 | Voltage | V | 0.1 |	1 |	Victron Energy through Modbus |	30 | 2618 | Uint16
sensor.ve_grid_voltage_l3 | Voltage | V	| 0.1 | 1 |	Victron Energy through Modbus |	30 | 2620 | Uint16
sensor.ve_grid_current_l1 | Current | A	| 0.1 | 1 |	Victron Energy through Modbus |	30 | 2617 |	Int16
sensor.ve_grid_current_l2 | Current | A	| 0.1 | 1 | Victron Energy through Modbus |	30 | 2619 |	Int16
sensor.ve_grid_current_l3 | Current | A	| 0.1 | 1 | Victron Energy through Modbus |	30 | 2621 |	Int16
sensor.ve_grid_total_energy_from_grid |	Energy | kWh | 0.01 | 1 | Victron Energy through Modbus	| 30 |2634 | Uint32
sensor.ve_grid_total_energy_to_grid	| Energy | kWh | 0.01 |	1 |	Victron Energy through Modbus |	30 | 2636 |	Uint32

## Youless P1 Grid sensor
Als er geen Victron ESS wordt toegepast, kan ook de Youless P1 als Grid Sensor worden gebruikt. De Youless LS120 ondersteunt de 4 telwerken van de slimmemeter. Deze heeft vanuit de Integratie dan de volgende sensoren :
Sensor | Class | Meas. | Integration 
------ | ----- | ----- | -----------
sensor.yl_grid_power | Power | W | Youless LS120			
sensor.yl_energy_to_grid_low |	Energy | kWh | Youless LS120			
sensor.yl_energy_to_grid_high |	Energy | kWh | Youless LS120			
sensor.yl_energy_from_grid_low | Energy | kWh | Youless LS120			
sensor.yl_energy_from_grid_high | Energy | kWh | Youless LS120

## SolarEdge Smart Meter
Ook de SolarEdge Smartmeter kan gebruikt worden. Hiervoor wordt de SE integratie gebruikt die daarna verschillende sensoren beschikbaar geeft. Zie ook onder het hoofdstuk Solarpower.
Sensor | Class | Meas. | Integration 
------ | ----- | ----- | -----------
sensor.se_grid_power |	Power | W | SolarEdge Integration			
sensor.se_energy_to_grid |Energy | Wh| SolarEdge Integration			
sensor.se_energy_from_grid |Energy | Wh	| SolarEdge Integration			

# Calculated Sensors through configuration.yaml
Met enkele extra gedefinieerde sensoren die zijn vastgelegd in configuration.yaml maak ik berekeningen met behulp van de input sensoren en de meetsensoren. Deze berekende sensoren worden gebruikt voor de bewaking van kritische grenzen van de grid-aansluiting met behulp van de uitgebreide set van de Victron Energy Sensoren.
Sensor | Class | Meas. | Calculation
------ | ----- | ----- | -----------
sensor.ve_grid_power | Power | W | `{{ states("sensor.ve_grid_power_l1") \| float + states("sensor.ve_grid_power_l2") \| float + states("sensor.ve_grid_power_l3") \|float }}`
sensor.grid_maximum_power |	Power | W |	`{{ 230 * int ((states ('input_select.grid_connection_capacity' ))) * int ((states ('input_select.grid_number_of_phases' ))) }}`
sensor.grid_nominal_power | Power |	W | `{{ 230 * int ((states ('input_select.grid_connection_capacity' ))) * int ((states ('input_select.grid_number_of_phases' ))) * (int (states ('input_number.grid_threshold'))/100)}}`
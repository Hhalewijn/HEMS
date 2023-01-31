# Installatie, configuratie en grenzen
Hoewel niet gebruikelijk in een technisch ontwerp, wordt er voor dit ontwerp toch al een keuze gemaakt voor een platform: Home Assistant (HA). HA bestaat al enige tijd en is een product van 1000-en ontwikkelaars die allemaal op basis van non-profit dit systeem ontwikkelen, verbeteren en uitbreiden. Ook heeft het al goed werkende eigenschappen die verband houden met de doelen die gesteld zijn aan HEMS: Energie Management en kosten reductie.

Zo lang als dit document nog in bewerking is, is er geen standaard proces beschrijving beschikbaar om ‘met 1 druk op de knop’ een volledige HA installatie te verkrijgen. Het is niet alleen afhankelijk van de hardware waarop HA gaat draaien zoals een Raspberry PI, een Synology of QNPA NAS in Docker of een Windows computer, het is ook afhankelijk van de integraties die nodig zijn en dus afhankelijk van de hardware die de toekomstige gebruiker straks gaat kiezen.

Met dit document probeer ik een verzamellijst aan te leggen van integraties en van andere instellingen die samen het HEMS vorm moeten geven. En ik hoop dat anderen de handschoen kunnen oppakken om hier een ‘out of the box’ implementatie van te kunnen maken.

# Integraties
HA werkt met zogenaamde Integraties. Integraties zijn eigenlijk apps die toegevoegd kunnen worden aan Home Assistant en vervolgens met elkaar verbonden kunnen worden. Er zijn duizenden HA-Integraties beschikbaar en er komen er nog steeds velen bij. Als in dit document over ‘een integratie’ of ‘Integratie’ met een hoofdletter wordt gesproken, wordt vrijwel altijd verwezen naar zo’n app die passende is bij een bepaald stuk hardware of een specifieke toepassing die nodig is voor HEMS.

HEMS moet eenvoudig te implementeren zijn. Integraties die al bestaan moeten gebruikt kunnen worden. Voor Integraties die in het kader van HEMS noodzakelijk zijn, zullen korte instructies beschikbaar moeten worden gesteld. Maar om enige intelligentie toe te kunnen voegen voor de werking van HEMS zullen veel variabelen gedefinieerd worden, niet alleen bij de eerste versie van HEMS maar ook in het vervolg als nieuwe functies gevraagd worden en nieuwe integraties zich aandienen. Dit vraagt om een naamgevingsbeleid (naming convention) dat strikt gevolgd zal moeten worden omdat integratie van Integraties anders een onmogelijk klus wordt. Ook voor de implementatie van deze variabelen zullen scripts gebouwd moeten worden die, liefst als Integratie, toegevoegd kunnen worden.

Ten slotte is er dan de logica, in HA Automatiseringen genoemd. Ook deze zullen op een eenvoudige wijze toegevoegd moeten kunnen worden zodat de gebruiker alleen nog de verbindingen tussen de sensoren en actoren hoeft aan te leggen. In HA kunnen als voorbeeld de Blueprints hiervoor gebruikt worden. Hoe omgegaan moet worden met een hogere versie van intelligentie, laten we zeggen AI, is op dit moment nog moeilijk te voorspellen. En misschien is dat ook wel goed: laten we een grens trekken bij een product dat de eerste noodzakelijke eigenschappen heeft maar tegelijkertijd wel kan meegroeien. Samen doen we veel ervaring op die uiteindelijk tot een soort van AI kan groeien.

# Sensoren
Home Assistant is gebouwd op sensoren. Onderdelen (software maar vooral hardware) die meetgegevens verzamelen. Als techneut ben je snel verleid om alles maar te willen verzamelen. Dat gaan we dus niet doen. Behalve dat dit een enorme aanslag op de hardware van HEMS en alle randapparatuur en sensoren kan doen, heeft dat ook geen waarde. Stel jezelf altijd de vraag: 
“Als ik dít ga meten, wat ga ik er dan mee doen? Waartoe leidt het?” Als je daarop geen antwoord kunt geven is er waarschijnlijk (nog) geen noodzaak om het te gaan meten. In dit document tref je daarom vooral een verzameling aan van wat noodzakelijk is. Het gaat immers om de doelstellingen die we in het Functioneel Ontwerp hebben samengevat.

# Data logging
Home Assistant is niet het beste platform voor uitgebreide datalogging. Om dit mogelijk te maken moet het HA platform uitgebreid worden met een relationele database (InfluxDB) en een analyse tool (Grafana). Ook deze beide functies zullen als Integratie (Add-ons) geïnstalleerd en geactiveerd moeten worden. Voor Grafana zullen de verschillende dashboards gedefinieerd en gedeeld moeten worden.

In dit document en de vervolgdelen die zullen volgen, zullen zoveel als mogelijk de query’s worden opgenomen die gebruikt zijn voor de dashboards. 

# Financieel
Sturing met HEMS op financiën is één van de belangrijkste eigenschappen die we in het Functioneel Ontwerp hebben vastgesteld. Maar dan dient zich de vraag aan “hoever wil je gaan?”. Rapporteren op kosten is een vereiste. Maar voorspellen? Er zijn (te)veel variabelen waarop geen invloed uitgeoefend kan worden (beleid, weer, economie, e.d.) terwijl die wel van invloed kunnen zijn op de kosten. Ook hier zullen we dus grenzen moeten stellen aan wat vereist, gewenst en mogelijk is. Maar veel van dat soort wensen kunnen ook opgelost worden met het exporteren van data zodat in Excel een nadere analyse mogelijk is.

# Sturing
Hoe ver ga je met AI? Door veel te parameteriseren kunnen we later deze parameters eenvoudig met logica laten veranderen. Dus we moeten ons aanleren om zo min mogelijk met vaste getallen in software code te gaan werken maar zo veel als mogelijk met variabelen te gaan werken. Dat betekent ook dat we de variabelen een plek moeten geven om ingevuld te worden. Met een duidelijke omschrijving waarvoor de variabele gebruikt wordt.

Ook de sturing van de hardware en software zelf (de laadpaal, de televisie, de zonnepanelen omvormer, samen ook actoren genoemd) vraagt om het maken van keuzes.

Er zijn een aantal mogelijkheden:
-	Het HEMS kan geïntegreerd samenwerken met diverse apparaten. In Home Assistant is dat door middel van zogenaamde Integraties. De besturing verloopt dan via het huisnetwerk, veelal Wifi of Bluetooth en het apparaat in kwestie zal de opdracht dan uitvoeren.
-	Het HEMS kan via een industrie standaard zoals MODBUS-TCP of MQTT, met randapparaten bi-directioneel communiceren. Dit vereist een ethernet en/of Internet communicatie medium.
-	Het HEMS kan via een of meerdere relais of schakelaars apparatuur besturen. Ook via zogenaamde digitale of analoge inputs kunnen status signalen tussen randapparatuur en het HEMS uitgewisseld worden. 

Voorkeur verdient als eerste een Integratie omdat dit de verdere configuratie vereenvoudigt.

Maar ook Modbus-tcp kan een belangrijke interface zijn omdat deze industrie-standaard is en door zeer veel fabrikanten wordt ondersteund.

# Algoritme
Er zijn meerdere sturingsalgoritmen te bedenken. Voor de eerste versie van HEMS zullen de volgende minimaal worden uitgewerkt:
-	Benutten dynamische tarieven
-	Optimaliseren eigen opwek
-	Optimaliseren gridaansluiting

# Naamgeving sensoren
Zoals hiervoor al werd geschreven is het maken van afspraken over naamgeving handig om Integraties op elkaar te laten aansluiten. Gelukkig geeft Home Assistant hiervoor al enkele richtlijnen. En met een eenduidige naamgeving wordt het ook voor onszelf gemakkelijker om te begrijpen waarvoor een sensor wordt gebruikt. Tegelijkertijd moeten we oppassen dat we niet te veel metadata in de naam willen vastleggen omdat Home Assistant hiervoor ook Attributen kent. Maar natuurlijk kun je ook de Friendly Name gebruiken om het goed leesbaar te houden.

Ook voor het groeperen van sensoren kunnen we de bestaande Groepen functie van Home Assistant gebruiken. Ik beperk mij daarom tot de volgende naamgeving standaard die ik heb ontwikkeld:

Type | Brand | Application
-----| ----- | -----------
`Type`| Sensor | Dit zijn de belangrijkste types die HA heeft vastgelegd.
|| Switch
|| Binary
|| Input_select
|| Input_number
|| Device_tracker
|| Automation
`Brand` |	VE = Victron Energy | Worden door Integraties vastgelegd of deze kun je zelf kiezen in yaml files.
|| SE = SolarEdge
|| YL = YouLess
|| HM = HomeMatic
|| Entso = Entso day ahead price	
`Application` | Varies | Afhankelijk van de Integratie of de sensor.

 
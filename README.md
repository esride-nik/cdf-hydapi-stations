# cdf-hydapi-stations
ArcGIS Server Custom Data Feed, der Daten über hydrologische Messstationen von NVE's hydapi abruft.

#### Voraussetzungen
Um einen Custom Data Feed zu entwickeln, müssen Sie Node.js und ArcGIS Enterprise SDK installieren.

#### Erstellen Sie eine Code-Vorlage
Erstellen Sie ein Repository in GitHub und klonen Sie es auf den Entwicklungsrechner. 

Öffnen Sie das Repository in VS Code und einem Terminalfenster.

Erstellen Sie eine neue Anwendung
```
cdf createapp hydapi_stations_app
```

Wechseln Sie in das Verzeichnis
```
cd hydapi_stations_app
```

Erstellen eines neuen Providers
```
cdf createprovider hydapi_stations_cdf
```
#### Entwickeln eines Providers
Das Erstellen eines Providers beinhaltet das Befüllen der Funktion getData() in src/model.js mit Inhalt. Kurz gesagt, du solltest Code schreiben, der Daten von der gewünschten Quelle abruft und sie als geojson zurückgibt. Denke daran, dass geojson WGS84 (4326) erwartet. Hier kannst du alle verfügbaren Module für node verwenden, um Daten aus verschiedenen Quellen abzurufen. In diesem Beispiel wird request verwendet, um einen https-Aufruf an eine REST-API zu tätigen. Denken Sie daran, das Node-Modul request für den Provider zu installieren (gehen Sie in das Verzeichnis des Providers)

```
cd providers
cd hydapi_stations_cdf
npm install request
```

Bei der Entwicklung eines Providers gibt es einige Dinge zu beachten. Es ist möglich, einen Hostnamen und eine ID des Feature-Dienstes anzugeben, der die Daten empfängt. Wenn Sie dies nicht benötigen, müssen Sie die folgenden Einstellungen in cdconfig.json ändern (setzen Sie hosts auf false und disableIdParam auf true).
```
 „properties": {
    „hosts": false,
    „disableIdParam": true
  }
```

Wenn du eine Geometrie erstellst, muss sie in WGS84 und Dezimalgrad angegeben werden. Denk auch daran, dass die Koordinaten Zahlen und kein Text sein müssen.
```
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [feature['longitude'], feature['latitude']] // longitude først og så latitude
  },
  "properties": feature
}
```
##### Exportieren eines fertigen Provider-Pakets
```
cdf export hydapi_stations_cdf
```

#### Hochladen des Providers auf ArcGIS Server
Ein benutzerdefiniertes Datenprovider-Paket muss auf die von Enterprise verwendete ArcGIS Server-Site hochgeladen und registriert werden. Dies geschieht über die Verwaltungsoberfläche, z.B. https://vmgdts02.azure.geodata.no:6443/arcgis/admin

Gehe zu
home/uploads/upload 
um *.cdpk hochzuladen

Übernimm die ID, die beim Hochladen der Datei zurückgegeben wird

#### Registriere den Datenfeed bei ArcGIS Server
Gehen Sie in der Admin-Oberfläche zu:
home/services/types/customdataproviders/register
um den Provider mit der im vorherigen Schritt kopierten ID zu registrieren.

#### Erstellen eines Feature-Service, der Daten aus dem Feed empfängt
Gehe in der Verwaltungsoberfläche zu:
home/services/createService

Verwende die folgende Vorlage, um einen neuen Feature-Service zu erstellen. Stelle sicher, dass der Typ „FeatureServer“ und die Fähigkeiten „Query“ (nur lesen) sind. Der Name des Datenanbieters muss ebenfalls genau so lauten 


```
{
  "serviceName": "hydapi_stations",
  "type": "FeatureServer",
  "description": "Hydrological stations from api.nve.no",
  "capabilities": "Query",
  "provider": "CUSTOMDATA",
  "clusterName": "default",
  "minInstancesPerNode": 0,
  "maxInstancesPerNode": 0,
  "instancesPerContainer": 1,
  "maxWaitTime": 60,
  "maxStartupTime": 300,
  "maxIdleTime": 1800,
  "maxUsageTime": 600,
  "loadBalancing": "ROUND_ROBIN",
  "isolationLevel": "HIGH",
  "configuredState": "STARTED",
  "recycleInterval": 24,
  "recycleStartTime": "00:00",
  "keepAliveInterval": 1800,
  "private": false,
  "isDefault": false,
  "maxUploadFileSize": 0,
  "allowedUploadFileTypes": "",
  "properties": {"disableCaching": "true"},
  "portalProperties": {"isHosted": false},
  "jsonProperties": {
    "customDataProviderInfo": {
      "dataProviderName": "hydapi_stations_cdf",
      "dataProviderHost": "",
      "dataProviderId": ""
    }
  },
  "extensions": [],
  "frameworkProperties": [],
  "datasets": []
}
```
Hier können Sie sehen, dass dataProviderHost und dataProviderid auf „“ gesetzt sind, da diese nicht verwendet werden. Hier ist erkennbar, dass dies die Einstellungen in cdconfig.json widerspiegelt (siehe vorherige Beschreibungen).



---
---
---



# cdf-hydapi-stations
ArcGIS Server Custom Data Feed som henter data om hydrologiske målestasjoner fra NVEs hydapi.

#### Forutsetninger
For å utvikle en custom data feed trenger du å installere Node.js og ArcGIS Enterprise SDK.

#### Opprette kodemal
Lag et repository i GitHub og clone til utviklingsmaskinen. 

Åpne repositoriet i VS Code og et terminalvindu.

Lage en ny app
```
cdf createapp hydapi_stations_app
```

Bytte til katalogen
```
cd hydapi_stations_app
```

Opprette en ny provider
```
cdf createprovider hydapi_stations_cdf
```
#### Utvikle en provider
Å lage en provider innebærer å fylle funksjonen getData() i src/model.js med innhold. Kort fortalt skal du skrive kode som henter data fra ønsket kilde og returnere disse som geojson. Husk at geojson forventer WGS84 (4326). Her kan du bruke alle tilgjengelige moduler til node for å hente data fra ulike kilder. I dette eksempelet benyttes request for å gjøre et https kall til et REST API. Husk å installere node-modulen request for provideren (gå til katalogen til provideren)

```
cd providers
cd hydapi_stations_cdf
npm install request
```

Ved utvikling av provider er det et par ting å tenke på. Det er mulig å sende med et hostnavn og en id fra featuretjenesten som mottar dataene. Om du ikke har bruk for dette, så må du endre til følgende innstillinger i cdconfig.json (sette hosts til false og disableIdParam til true).
```
 "properties": {
    "hosts": false,
    "disableIdParam": true
  }
```

Når du lager geometri må denne være i WGS84 og desimalgrader. Husk også at koordinatene må være tall og ikke tekst.
```
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [feature['longitude'], feature['latitude']] // longitude først og så latitude
  },
  "properties": feature
}
```


##### Eksportere en ferdig provider-pakke
```
cdf export hydapi_stations_cdf
```

#### Laste opp provideren til ArcGIS Server
En custom data provider pakke må lastes opp og registreres på ArcGIS Server siten som benyttes av Enterprise. Dette gjøres via admin-grensesnittet f.eks. https://vmgdts02.azure.geodata.no:6443/arcgis/admin

Gå til
home/uploads/upload 
for å laste opp *.cdpk

Kopiere id som returneres når filen er lastet opp

#### Registrere data feeden med ArcGIS Server
I admin-grensesnittet, gå til:
home/services/types/customdataproviders/register
for å registrere provideren med id-kopiert i forrige steg.

#### Opprette en feature service som mottar data fra feeden
I admin-grensesnittet gå til:
home/services/createService

Bruk følgende mal for å opprette en ny feature service. Pass på at type er "FeatureServer" og capabilities er "Query" (read only). Navnet på dataprovideren må også være helt likt 

```
{
  "serviceName": "hydapi_stations",
  "type": "FeatureServer",
  "description": "Hydrological stations from api.nve.no",
  "capabilities": "Query",
  "provider": "CUSTOMDATA",
  "clusterName": "default",
  "minInstancesPerNode": 0,
  "maxInstancesPerNode": 0,
  "instancesPerContainer": 1,
  "maxWaitTime": 60,
  "maxStartupTime": 300,
  "maxIdleTime": 1800,
  "maxUsageTime": 600,
  "loadBalancing": "ROUND_ROBIN",
  "isolationLevel": "HIGH",
  "configuredState": "STARTED",
  "recycleInterval": 24,
  "recycleStartTime": "00:00",
  "keepAliveInterval": 1800,
  "private": false,
  "isDefault": false,
  "maxUploadFileSize": 0,
  "allowedUploadFileTypes": "",
  "properties": {"disableCaching": "true"},
  "portalProperties": {"isHosted": false},
  "jsonProperties": {
    "customDataProviderInfo": {
      "dataProviderName": "hydapi_stations_cdf",
      "dataProviderHost": "",
      "dataProviderId": ""
    }
  },
  "extensions": [],
  "frameworkProperties": [],
  "datasets": []
}
```
Her ser du at dataProviderHost og dataProviderid er satt til "" da disse ikke brukes. Dette speiler innstillingene i cdconfig.json (se tidligere beskrivelser).



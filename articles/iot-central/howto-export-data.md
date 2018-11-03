---
title: Exportujte data v Azure IoT Central | Dokumentace Microsoftu
description: Jak exportovat data z aplikace Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3231a956648b80d88059b7b0fc8f790e0e58be99
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50962788"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportujte data v Azure IoT Central

*Toto téma se vztahuje na správce.*

Tento článek popisuje, jak používat souvislá datová funkce exportu v Azure IoT Central pravidelně export dat do účtu úložiště objektů Blob v Azure. Můžete to taky **měření**, **zařízení**, a **šablon** do souborů pomocí [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátu. Exportovaná data je možné pro studené cesty analytics jako trénování modelů ve službě Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Když zapnete nepřetržitý export dat, získáte jenom data dále v tomto okamžiku. V současné době nelze načíst data po dobu, kdy byla nepřetržitý export dat vypnout. Pokud chcete zachovat dalších historických dat, zapněte nepřetržitý export dat již v rané fázi.

## <a name="prerequisites"></a>Požadavky

- Aplikace s průběžnými platbami.
- Správce ve vaší aplikaci IoT Central, který obsahuje:
    - účet Azure v rámci předplatného Azure IoT Central aplikace je v
    - oprávnění k vytvoření úložiště účet nebo získat přístup k existující účet úložiště v rámci tohoto předplatného Azure

## <a name="types-of-data-to-export"></a>Datové typy k exportu

### <a name="measurements"></a>Měření

Měření, která zařízení odesílají, jsou exportovány do vašeho účtu úložiště jednou za minutu. Data obsahují všechny nové zprávy přijaté službou IoT Central ze všech zařízení během této doby. Exportované soubory AVRO používají stejný formát jako soubory zpráv exportované sadou [směrování zpráv služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do úložiště objektů Blob.

> [!NOTE]
> Zařízení, které odesílají měření jsou reprezentovány v ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportujte snímky zařízení. Porovnat všechny záznamy zpráv pomocí **connectionDeviceId** , který odpovídá **deviceId** záznamu zařízení.

Následující příklad ukazuje záznam v dekódovaný soubor AVRO:

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "<connectionDeviceId>",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "<generationId>",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Zařízení

Pokud je první nepřetržitý export dat zapnuté, se exportují jednoho snímku se všemi zařízeními. Každé zařízení zahrnuje:
- `id` zařízení v IoT Central
- `name` zařízení
- `deviceId` z [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností
- Nastavení hodnot

Nový snímek se zapíše jednou za minutu. Snímek zahrnuje:

- Od poslední snímek přidána nová zařízení.
- Zařízení s změněných vlastností a nastavení hodnoty od poslední snímek.

> [!NOTE]
> Zařízení odstranit, protože poslední snímek se nebudou exportovat. V současné době snímky nemají ukazatele pro odstraněné zařízení.
>
> Šablonu zařízení, každé zařízení patří, je reprezentována ID zařízení šablony. Název šablony zařízení získáte exportujte šablonu snímky zařízení.

Záznam v dekódovaný soubor AVRO může vypadat:

```json
{
    "id": "<id>",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
        "id": "<template id>",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Šablony zařízení

Pokud nepřetržitý export dat zapnutý, je exportován jeden snímek pomocí všech zařízení šablon. Každá šablona zařízení obsahuje:
- `id` šablony zařízení
- `name` šablony zařízení
- `version` šablony zařízení
- Měření datových typů a minimální/maximální hodnoty.
- Vlastnost datové typy a výchozí hodnoty.
- Nastavení datových typů a výchozí hodnoty.

Nový snímek se zapíše jednou za minutu. Snímek zahrnuje:

- Nové šablony zařízení přidat, protože poslední snímek.
- Zařízení šablony s změněné měření, vlastností a nastavení definice od poslední snímek.

> [!NOTE]
> Šablony zařízení odstranit, protože poslední snímek se neexportují. V současné době snímky nemají ukazatele pro šablony odstraněné zařízení.

Záznam v dekódovaný soubor AVRO může vypadat například takto:

```json
{
    "id": "<id>",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="set-up-continuous-data-export"></a>Nastavit nepřetržitý export dat

1. Pokud nemáte účet úložiště Azure [vytvořit nový účet úložiště](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) na webu Azure Portal. Vytvoření účtu úložiště **v předplatném Azure, který má aplikace IoT Central**.
    - Pro typ účtu, vyberte **Obecné** nebo **úložiště objektů Blob**.
    - Vyberte předplatné, které má vaše aplikace IoT Central. Pokud nevidíte předplatné, můžete potřebovat k přihlášení různých Azure účtu nebo abychom si vyžádali přístup k předplatnému.
    - Zvolte existující skupinu prostředků nebo vytvořte novou. Další informace o [tom, jak vytvořit nový účet úložiště](https://aka.ms/blobdocscreatestorageaccount).

2. Vytvoření kontejneru v účtu úložiště pro export dat IoT Central. Přejděte do účtu úložiště. V části **služby Blob Service**vyberte **procházet objekty BLOB**. Vyberte **kontejneru** vytvořit nový kontejner.

   ![Vytvoření kontejneru](media/howto-export-data/createcontainer.png)

3. Přihlaste se do vaší aplikace IoT Central pomocí stejného účtu Azure.

4. V části **správu**vyberte **Export dat**.

5. V **účtu úložiště** rozevíracího seznamu vyberte svůj účet úložiště. V **kontejneru** rozevíracího seznamu vyberte kontejner. V části **Data pro export**, určete každý typ hledaných dat exportovat na základě nastavení typu na **na**.

6. Nepřetržitý export dat zapnout, nastavte **export dat** k **na**. Vyberte **Uložit**.

  ![Nepřetržitý export dat konfigurace](media/howto-export-data/continuousdataexport.PNG)

7. Po několika minutách se zobrazí vaše data ve vašem účtu úložiště. Přejděte do účtu úložiště. Vyberte **procházet objekty BLOB** > vašeho kontejneru. Uvidíte tři složky pro data exportu. Výchozí cesty k souborů AVRO s export dat jsou:
    - Zprávy: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Zařízení: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
    - Zařízení šablony: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

## <a name="read-exported-avro-files"></a>Čtení exportovaných souborů AVRO

AVRO je binární formát, takže nelze číst soubory v jejich nezpracovaná stavu. Soubory můžete dekódovat do formátu JSON. Následující příklady ukazují, jak analyzovat měření, zařízení a zařízení šablony souborů AVRO. V příkladech odpovídají příklady je popsáno v předchozí části.

### <a name="read-avro-files-by-using-python"></a>Čtení souborů AVRO s použitím jazyka Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Nainstalujte balíček pandavro a pandas

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor AVRO měření

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)

```

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor AVRO šablon zařízení

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the AVRO file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Čtení AVRO soubory pomocí jazyka C#

#### <a name="install-the-microsofthadoopavro-package"></a>Nainstalovat balíček Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor AVRO měření

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor AVRO šablon zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one AVRO container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the AVRO records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>Čtení souborů AVRO pomocí jazyka Javascript

#### <a name="install-the-avsc-package"></a>Nainstalovat balíček avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor AVRO měření

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor AVRO šablon zařízení

```javascript
const avro = require('avsc');

// Read the AVRO file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Další postup

Teď, když víte, jak exportovat data, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Tom, jak vizualizace dat v Power BI](howto-connect-powerbi.md)

---
title: Exportujte data v Azure IoT Central | Dokumentace Microsoftu
description: Jak exportovat data z aplikace Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008808"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportujte data v Azure IoT Central

Průběžný Export dat pomocí pravidelně export dat do účtu služby Azure Blob Storage. Tlačítko pro export **měření**, **zařízení**, a **šablon** v souborech [Apache AVRO](https://avro.apache.org/docs/current/index.html) formátu. Exportovaná data použijte pro analýzy studené cesty, jako jsou trénování modelů ve službě Azure Machine Learning nebo dlouhodobé analýzy trendů v Power BI.

> [!Note]
> Když zapnete průběžný Export dat, získáte jenom data, která se dodává tomto okamžiku a vyšší. Aktuálně neexistuje žádný způsob, jak načíst data z při průběžný Export dat je vypnuté. Zapněte průběžný Export dat již v rané fázi zachovat dalších historických dat!

## <a name="prerequisites"></a>Požadavky

- Rozšířené aplikace 30denní zkušební nebo placené aplikace
- Účet Azure s předplatným Azure
- Stejný účet Azure je správcem ve vaší aplikaci IoT Central
- Stejný účet Azure má oprávnění vytvořit účet úložiště nebo získat přístup k existující účet úložiště ve stejném předplatném Azure

## <a name="types-of-data-to-export"></a>Datové typy k exportu

### <a name="measurements"></a>Měření

Měření, která zařízení odesílají exportována do účtu úložiště. Měření data se exportují přibližně jednou za minutu, který obsahuje všechny nové zprávy přijaté službou IoT Central ze všech zařízení v rámci dané časové okno. Exportované soubory AVRO jsou ve stejném formátu jako zprávy soubory exportované sadou [směrování zpráv služby IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do úložiště objektů blob.

> [!NOTE]
> Zařízení, které odesílají měření jsou reprezentovány v ID zařízení (viz níže). Chcete-li získat názvy zařízení, je nutné exportovat příliš snímky zařízení. Můžete porovnat jednotlivých záznamů zprávy pomocí connectionDeviceId, která odpovídá ID zařízení.

Toto je příklad záznamu v dekódovaný soubor AVRO.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Zařízení

Při prvním zapnutí průběžného exportu dat, je exportován jeden snímek obsahující všechna zařízení. To zahrnuje:
- ID zařízení
- Názvy zařízení
- ID zařízení šablon
- Hodnoty vlastnosti
- Hodnoty nastavení

Přibližně jednou za minutu, nový snímek je zapsán obsahující:

- Nová zařízení, které byly přidány od poslední snímek
- Zařízení, která obsahovala vlastnosti a nastavení hodnot, které se změnily od poslední snímek

> [!NOTE]
> Zařízení, které byly odstraněny od poslední snímek se neexportují. V snímků pro zařízení, které byly odstraněny v tuto chvíli není k dispozici žádné ukazatele.

> [!NOTE]
> Šablonu zařízení, každé zařízení patří, je reprezentována ID zařízení šablony. Pokud chcete získat název šablony zařízení, je nutné exportovat zařízení šablony snímky příliš.

Každý záznam v dekódovaný soubor AVRO vypadá takto:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
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

Při prvním zapnutí průběžného exportu dat, je exportován jeden snímek obsahující všechny šablony zařízení. To zahrnuje: 
- ID zařízení šablon
- Měření datových typů a minimální/maximální hodnoty
- Vlastnosti datové typy a výchozí hodnoty
- Nastavení datových typů a výchozí hodnoty

Přibližně jednou za minutu, nový snímek je zapsán obsahující:

- Nové šablony zařízení, které byly přidány od poslední snímek
- Šablon, do kterých se měření, vlastnosti a nastavení definice, které se změnily od poslední snímek

> [!NOTE]
> Zařízení šablony, které byly odstraněny od poslední snímek se neexportují. Snímky pro šablony zařízení, které byly odstraněny v tuto chvíli není ukazatel.

Každý záznam v dekódovaný soubor AVRO vypadá takto:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
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

## <a name="how-to-set-up-data-export"></a>Jak nastavit export dat

1. Pokud již nemáte, vytvořte účet služby Azure Storage **v rámci předplatného Azure, které vaše aplikace je v**. [Kliknutím sem](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) můžete přejít na web Azure Portal k vytvoření nového účtu úložiště Azure.

    - Zvolte *Obecné* nebo *úložiště objektů Blob* účtu typy
    - Vyberte předplatné, které je vaše aplikace IoT Central. Pokud nevidíte předplatné, budete muset přihlásit s jiným účtem Azure nebo požádat o přístup k předplatnému.
    - Můžete vybrat existující skupinu prostředků nebo vytvořte novou. Další informace o [tom, jak vytvořit nový účet úložiště.](https://aka.ms/blobdocscreatestorageaccount)

2. Vytvoření kontejneru v účtu úložiště pro export dat IoT Central. Přejděte do účtu Storage -> Procházet objekty BLOB a vytvořit nový kontejner. ![Vytvoření image kontejneru](media/howto-export-data/createcontainer.png)

3. Přihlaste se pomocí stejného účtu Azure IoT Central aplikace.
1. Přejděte na správu -> nepřetržitý Export dat.
![IoT Central CDE](media/howto-export-data/continuousdataexport.PNG)
1. Pomocí rozevíracích seznamech vyberte účet úložiště a kontejner. Pak pomocí přepínačů zapnout nebo vypnout různé typy dat k exportu.
1. Nakonec zapnout pomocí přepínač průběžný Export dat a spuštění "Save".
1. Počkejte několik minut. proto byste měli vidět vaše data zobrazí ve vašem účtu úložiště. Můžete přejít k vašemu účtu úložiště, vyberte procházet objekty BLOB, vyberte kontejner, a uvidíte tři složky. Výchozí cesty k souborů AVRO, který obsahuje různé typy dat jsou:
- Zprávy: **{container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Zařízení: **{container}/devices/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Zařízení šablony: **{container}/deviceTemplates/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Čtení exportovaných souborů AVRO

AVRO je binární formát, takže nelze číst soubory v jejich nezpracovaná stavu. Můžete se dekódovat do formátu JSON. Následující příklady ukazují, jak analyzovat měření, zařízení a zařízení šablony pomocí výše uvedených příkladech souborů AVRO.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Nainstalujte balíčky Pandas a PandaAvro balíčku:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Analyzovat soubor AVRO měření

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Parsovat soubor AVRO šablony zařízení

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Nainstalujte Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Analyzovat soubor AVRO měření

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
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
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

### <a name="parse-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
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
### <a name="parse-device-templates-avro-file"></a>Parsovat soubor AVRO šablony zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
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

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>Nainstalujte avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Analyzovat soubor AVRO měření

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Analyzovat soubor AVRO zařízení

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Parsovat soubor AVRO šablony zařízení

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak [spravujte svá zařízení](howto-manage-devices.md) v Průzkumníku zařízení. 

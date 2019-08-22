---
title: Exportujte data do Azure Blob Storage | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/08/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 7366072dbf6b000981899a56ca1c8cfe6af6f04a
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876050"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportujte data do Azure Blob Storage

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central pravidelně exportovat data do svého **účtu služby Azure Blob Storage**. **Měření**, **zařízení**a **šablony zařízení** můžete exportovat do souborů ve formátu Apache Avro. Exportovaná data je možné použít pro analýzu studených cest, jako jsou školicí modely v Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Po opětovném zapnutí průběžného exportu dat získáte od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.


## <a name="prerequisites"></a>Požadavky

- Musíte být správcem aplikace IoT Central.


## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující úložiště pro export do, postupujte takto:

## <a name="create-storage-account"></a>Vytvořit účet úložiště

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Další informace najdete v [dokumentaci Azure Storage](https://aka.ms/blobdocscreatestorageaccount).
2. Jako typ účtu vyberte **obecné účely** nebo **úložiště objektů BLOB**.
3. Vyberte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, která se neshodují s touto aplikací pro IoT Central s průběžnými platbami. V tomto případě se připojíte pomocí připojovacího řetězce.

4. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service**vyberte **Procházet objekty blob**. Vybrat **+ kontejner** v horní části a vytvořit nový kontejner


## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte cíl úložiště pro export dat, postupujte podle těchto kroků a nastavte průběžný export dat. 

1. Přihlaste se k aplikaci IoT Central.

2. V nabídce vlevo vyberte průběžný **Export dat**.

    > [!Note]
    > Pokud v levé nabídce nevidíte průběžný export dat, nejste správcem vaší aplikace. Pokud chcete nastavit export dat, obraťte se na správce.

    ![Vytvořit nové centrum událostí CDE](media/howto-export-data/export_menu1.png)

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte **Azure Blob Storage** . 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový export průběžných dat](media/howto-export-data/export_new1.png)

4. V rozevíracím seznamu vyberte svůj **obor názvů účtu úložiště**. Můžete také vybrat poslední možnost v seznamu a **zadat připojovací řetězec**. 

    > [!NOTE] 
    > V rámci **stejného předplatného jako aplikace IoT Central**se zobrazí jenom obory názvů účtů úložiště. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.

    > [!NOTE] 
    > U 7 dní zkušebních aplikací je jediným způsobem konfigurace průběžného exportu dat prostřednictvím připojovacího řetězce. Důvodem je to, že 7 dní zkušebních aplikací nemá přidružené předplatné Azure.

    ![Vytvořit nové centrum událostí CDE](media/howto-export-data/export-create-blob.png)

5. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Získání připojovacího řetězce pro:
    - Účet úložiště, v Azure Portal přejít na účet úložiště.
        - V části **Nastavení**vyberte **přístupové klíče** .
        - Zkopírujte buď připojovací řetězec klíč1, nebo připojovací řetězec key2.
 
6. V rozevíracím seznamu vyberte kontejner.

7. V části **data, která chcete exportovat**, určete každý typ dat k exportu nastavením typ na **zapnuto**.

6. Pokud chcete zapnout funkci průběžného exportu dat, ujistěte se,že je **Export dat** zapnutý. Vyberte **Uložit**.

   ![Konfigurace průběžného exportu dat](media/howto-export-data/export-list-blob.png)

7. Po několika minutách se vaše data zobrazí ve zvoleném cíli.


## <a name="export-to-azure-blob-storage"></a>Exportovat do Azure Blob Storage

Data o měřeních, zařízeních a šablonách zařízení se exportují na účet úložiště jednou za minutu a každý soubor, který obsahuje dávku změn od posledního exportovaného souboru. Exportovaná data jsou ve formátu [Apache Avro](https://avro.apache.org/docs/current/index.html) a budou exportována do tří složek. Výchozí cesty v účtu úložiště jsou:
- Zprávy: {Container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Zařízení: {Container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Šablony zařízení: {Container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Měření

Data exportovaných měření mají všechny nové zprávy přijaté IoT Central ze všech zařízení během této doby. Exportované soubory používají stejný formát jako soubory zpráv exportované [IoT Hub směrováním zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do úložiště objektů BLOB.

> [!NOTE]
> Zařízení, která odesílají měření, jsou představována ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportujte snímky zařízení. Proveďte korelaci každého záznamu zprávy pomocí **connectionDeviceId** , který odpovídá poli **deviceId** záznamu zařízení.

Následující příklad ukazuje záznam v Dekódovatelné Avro souboru:

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

Pokud je nejprve zapnutý průběžný export dat, je exportován jeden snímek se všemi zařízeními. Každé zařízení zahrnuje:
- `id`zařízení v IoT Central
- `name`zařízení
- `deviceId`ze [služby Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností
- Nastavení hodnot

Nový snímek se zapisuje jednou za minutu. Snímek obsahuje:

- Nová zařízení přidaná od posledního snímku.
- Zařízení se změněnou vlastností a nastavením hodnot od posledního snímku.

> [!NOTE]
> Zařízení Odstraněná od posledního snímku se neexportují. V současné době snímky nemají indikátory pro Odstraněná zařízení.
>
> Šablona zařízení, do které patří každé zařízení, je reprezentovaná ID šablony zařízení. Pokud chcete získat název šablony zařízení, exportujte snímky šablony zařízení.

Záznam v Dekódovatelné Avro souboru může vypadat takto:

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

Pokud je nejprve zapnutý průběžný export dat, je exportován jeden snímek se všemi šablonami zařízení. Každá šablona zařízení zahrnuje:
- `id`šablony zařízení
- `name`šablony zařízení
- `version`šablony zařízení
- Měření datových typů a minimální/maximální hodnoty.
- Datové typy a výchozí hodnoty vlastností.
- Nastavení datových typů a výchozích hodnot.

Nový snímek se zapisuje jednou za minutu. Snímek obsahuje:

- Od posledního snímku se přidaly nové šablony zařízení.
- Šablony zařízení se změněnými měřeními, vlastnostmi a definicemi nastavení od posledního snímku.

> [!NOTE]
> Šablony zařízení odstraněné od posledního snímku se neexportují. V současné době snímky nemají indikátory pro odstraněné šablony zařízení.

Záznam v Dekódovatelné Avro souboru může vypadat takto:

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

## <a name="read-exported-avro-files"></a>Čtení exportovaných souborů Avro

Avro je binární formát, takže soubory nelze číst v nezpracovaném stavu. Soubory lze dekódovat ve formátu JSON. Následující příklady znázorňují, jak analyzovat měření, zařízení a šablony zařízení Avro soubory. Příklady odpovídají příkladům popsaným v předchozí části.

### <a name="read-avro-files-by-using-python"></a>Čtení souborů Avro pomocí Pythonu

#### <a name="install-pandas-and-the-pandavro-package"></a>Instalace PANDAS a balíčku pandavro

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analýza Avro souboru měření

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor Avro zařízení

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Analyzovat soubor Avro šablon zařízení

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
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
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Čtení souborů Avro pomocíC#

#### <a name="install-the-microsofthadoopavro-package"></a>Instalace balíčku Microsoft. Hadoop. Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analýza Avro souboru měření

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
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor Avro zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

#### <a name="parse-a-device-templates-avro-file"></a>Analyzovat soubor Avro šablon zařízení

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
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

### <a name="read-avro-files-by-using-javascript"></a>Čtení souborů Avro pomocí JavaScriptu

#### <a name="install-the-avsc-package"></a>Instalace balíčku AVSC

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analýza Avro souboru měření

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
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

#### <a name="parse-a-devices-avro-file"></a>Analyzovat soubor Avro zařízení

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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

#### <a name="parse-a-device-templates-avro-file"></a>Analyzovat soubor Avro šablon zařízení

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
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

Když teď víte, jak exportovat data, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak vizualizovat data v Power BI](howto-connect-powerbi.md)

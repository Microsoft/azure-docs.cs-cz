---
title: Exportovat data do úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: Jak exportovat data z Azure IoT Central aplikace do Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: f81ca34931e2ee4bce35fa06195fb64c47ef9a7b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682038"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Exportovat data do úložiště objektů Blob v Azure

*Toto téma se vztahuje na správce.*

Tento článek popisuje způsob použití souvislá datová funkce exportu v Azure IoT Central pravidelně exportovat data do vaší **účtu úložiště objektů Blob v Azure**. Můžete to taky **měření**, **zařízení**, a **šablon** na soubory ve formátu Apache Avro. Exportovaná data je možné pro studené cesty analytics jako trénování modelů ve službě Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Znovu až zapnete nepřetržitý export dat, zobrazí pouze data dále v tomto okamžiku. V současné době nelze načíst data po dobu, kdy byla nepřetržitý export dat vypnout. Pokud chcete zachovat dalších historických dat, zapněte nepřetržitý export dat již v rané fázi.


## <a name="prerequisites"></a>Požadavky

- Musíte být správcem ve vaší aplikaci IoT Central


## <a name="set-up-export-destination"></a>Nastavit cíl exportu

Pokud nemáte existující úložiště pro export do, postupujte podle těchto kroků:

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

1. Vytvoření [nový účet úložiště na webu Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Další informace v [dokumentace služby Azure Storage](https://aka.ms/blobdocscreatestorageaccount).
2. Pro typ účtu, vyberte **Obecné** nebo **úložiště objektů Blob**.
3. Zvolte předplatné. 

    > [!Note] 
    > Teď můžete exportovat data do jiných předplatných, které jsou **není stejný** jako pro aplikace s průběžnými platbami IoT Central. Připojíte se v tomto případě pomocí připojovacího řetězce.

4. Vytvoření kontejneru v účtu úložiště. Přejděte do účtu úložiště. V části **služby Blob Service**vyberte **procházet objekty BLOB**. Vyberte **+ kontejner** v horní části stránky vytvořte nový kontejner


## <a name="set-up-continuous-data-export"></a>Nastavit nepřetržitý export dat

Teď, když máte exportovat data do cílového úložiště, nastavit nepřetržitý export dat pomocí těchto kroků. 

1. Přihlaste se do vaší aplikace IoT Central.

2. V nabídce vlevo vyberte **průběžný Export dat**.

    > [!Note]
    > Pokud nevidíte průběžný Export dat v nabídce vlevo, nejste správcem ve vaší aplikaci. Obraťte se na správce nastavit export dat.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export_menu.PNG)

3. Vyberte **+ nová** tlačítko v pravém horním rohu. Zvolte **Azure Blob Storage** jako cíl pro export. 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový nepřetržitý export dat](media/howto-export-data/export_new.PNG)

4. V rozevíracím seznamu vyberte vaše **obor názvů účtu úložiště**. V seznamu, který je můžete také vybrat jako poslední možnost **zadejte připojovací řetězec**. 

    > [!NOTE] 
    > Zobrazí se pouze účty úložiště obory názvů v **stejném předplatném jako aplikace IoT Central**. Pokud chcete exportovat do umístění mimo toto předplatné, zvolte **zadejte připojovací řetězec** a přejděte ke kroku 5.

    > [!NOTE] 
    > 7 dnů, zkušební verze aplikace, jediný způsob, jak nakonfigurovat průběžné data exportovat je do připojovacího řetězce. Je to proto 7denní zkušební verze aplikace nemusí k přidruženému předplatnému Azure.

    ![Vytvořit nový cde centra událostí](media/howto-export-data/export-create-blob.png)

5. (Volitelné) Pokud jste zvolili **zadejte připojovací řetězec**, můžete vložit připojovací řetězec se zobrazí nové pole. Chcete-li získat připojovací řetězec pro váš:
    - Účet úložiště, přejděte na účet úložiště na webu Azure Portal.
        - V části **nastavení**vyberte **přístupové klíče**
        - Zkopírujte připojovací řetězec key1 a key2 připojovací řetězec
 
6. V rozevíracím seznamu vyberte kontejner.

7. V části **Data pro export**, určete každý typ hledaných dat exportovat na základě nastavení typu na **na**.

6. Nepřetržitý export dat zapnout, ujistěte se, že **export dat** je **na**. Vyberte **Uložit**.

  ![Nepřetržitý export dat konfigurace](media/howto-export-data/export-list-blob.png)

7. Po několika minutách by se vaše data zobrazí v zvolený cíl.


## <a name="export-to-azure-blob-storage"></a>Exportovat do úložiště objektů Blob v Azure

Měření, zařízení a zařízení šablony data se vyexportují do vašeho účtu úložiště jednou za minutu, se každý soubor, který obsahuje batch změny od poslední exportovaný soubor. Exportovaná data se v [Apache Avro](https://avro.apache.org/docs/current/index.html) formátování a se exportují do tři složky v. Výchozí cesty ve vašem účtu úložiště jsou:
- Zprávy: {container}/measurements/{hubname}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Zařízení: {container}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro
- Zařízení šablony: {container}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}.avro

### <a name="measurements"></a>Měření

Exportované měření data mají všechny nové zprávy přijaté službou IoT Central ze všech zařízení během této doby. Exportované soubory používají stejný formát jako soubory zpráv exportované sadou [směrování zpráv služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do úložiště objektů Blob.

> [!NOTE]
> Zařízení, které odesílají měření jsou reprezentovány v ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportujte snímky zařízení. Porovnat všechny záznamy zpráv pomocí **connectionDeviceId** , který odpovídá **deviceId** záznamu zařízení.

Následující příklad ukazuje záznam v dekódovaný soubor Avro:

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

Záznam v dekódovaný soubor Avro může vypadat:

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

Záznam v dekódovaný soubor Avro může vypadat například takto:

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

Avro je binární formát, takže nelze číst soubory v jejich nezpracovaná stavu. Soubory můžete dekódovat do formátu JSON. Následující příklady ukazují, jak analyzovat měření, zařízení a zařízení šablony souborů Avro. V příkladech odpovídají příklady je popsáno v předchozí části.

### <a name="read-avro-files-by-using-python"></a>Čtení souborů Avro s použitím jazyka Python

#### <a name="install-pandas-and-the-pandavro-package"></a>Nainstalujte balíček pandavro a pandas

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor Avro měření

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
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

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
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)

```

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor Avro šablon zařízení

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
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Avro čtení souborů pomocíC#

#### <a name="install-the-microsofthadoopavro-package"></a>Nainstalovat balíček Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor Avro měření

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

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor Avro šablon zařízení

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

### <a name="read-avro-files-by-using-javascript"></a>Čtení souborů Avro pomocí jazyka Javascript

#### <a name="install-the-avsc-package"></a>Nainstalovat balíček avsc

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Analyzovat soubor Avro měření

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

#### <a name="parse-a-device-templates-avro-file"></a>Parsovat soubor Avro šablon zařízení

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

Teď, když víte, jak exportovat data, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Tom, jak vizualizace dat v Power BI](howto-connect-powerbi.md)

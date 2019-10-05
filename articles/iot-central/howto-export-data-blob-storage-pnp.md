---
title: Exportujte data do Azure Blob Storage | Microsoft Docs
description: Jak exportovat data z aplikace Azure IoT Central do Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973227"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Export dat do služby Azure Blob Storage (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Toto téma se týká správců.*

Tento článek popisuje, jak pomocí funkce pro export nepřetržitých dat v Azure IoT Central pravidelně exportovat data do **účtu služby Azure Blob Storage** nebo do **účtu úložiště Azure Data Lake Storage Gen2**. Můžete exportovat **telemetrie**, **zařízení**a **šablony zařízení** do souborů ve formátu JSON. Exportovaná data je možné použít pro analýzu studených cest, jako jsou školicí modely v Azure Machine Learning nebo dlouhodobé analýzy trendů v Microsoft Power BI.

> [!Note]
> Když zapnete export průběžných dat, dostanete od tohoto okamžiku pouze data. V současné době nelze data po vypnutí průběžného exportu dat načíst. Pokud chcete zachovat více historických dat, zapněte průběžný export dat.


## <a name="prerequisites"></a>Předpoklady

- Musíte být správcem aplikace IoT Central.

## <a name="create-storage-account"></a>Vytvoření účtu úložiště
Pokud nemáte existující úložiště pro export do, postupujte takto:

1. Vytvořte [nový účet úložiště v Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Můžete si přečíst další informace o vytváření nových [účtů Azure Blob Storage](https://aka.ms/blobdocscreatestorageaccount) nebo [účtů úložiště Azure Data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Pokud se rozhodnete exportovat data do účtu úložiště ADLS v2, musíte zvolit **druh účtu** jako **BlobStorage**. 

    > [!Note] 
    > Data můžete exportovat do účtů úložiště v předplatných, která jsou odlišná než ta pro vaši průběžné platby IoT Central. V tomto případě se připojíte pomocí připojovacího řetězce.

2. Vytvořte kontejner v účtu úložiště. Přejít na účet úložiště. V části **BLOB Service**vyberte **Procházet objekty blob**. V horní části vyberte **+ kontejner** a vytvořte nový kontejner.


## <a name="set-up-continuous-data-export"></a>Nastavení exportu průběžných dat

Teď, když máte cíl úložiště pro export dat, postupujte podle těchto kroků a nastavte průběžný export dat. 

1. Přihlaste se k aplikaci IoT Central.

2. V nabídce vlevo vyberte **exportovat data**.

    > [!Note]
    > Pokud v nabídce vlevo nevidíte exportovat data, nejste správcem vaší aplikace. Pokud chcete nastavit export dat, obraťte se na správce.

3. V pravém horním rohu vyberte tlačítko **+ Nový** . Jako cíl exportu vyberte **Azure Blob Storage** . 

    > [!NOTE] 
    > Maximální počet exportů na aplikaci je pět. 

    ![Vytvořit nový export průběžných dat](media/howto-export-data-pnp/export-new2.png)

4. V rozevíracím seznamu vyberte svůj **obor názvů účtu úložiště**. Můžete také vybrat poslední možnost v seznamu a **zadat připojovací řetězec**. 

    > [!NOTE] 
    > V rámci **stejného předplatného jako aplikace IoT Central**se zobrazí jenom obory názvů účtů úložiště. Pokud chcete exportovat do cílového umístění mimo toto předplatné, vyberte **zadat připojovací řetězec** a viz krok 5.

    > [!NOTE] 
    > U 7 dní zkušebních aplikací je jediným způsobem konfigurace průběžného exportu dat prostřednictvím připojovacího řetězce. Důvodem je to, že 7 dní zkušebních aplikací nemá přidružené předplatné Azure.

    ![Vytvořit nový export do objektu BLOB](media/howto-export-data-pnp/export-create-blob2.png)

5. Volitelné Pokud jste zvolili **zadat připojovací řetězec**, zobrazí se nové okno pro vložení připojovacího řetězce. Připojovací řetězec pro účet úložiště získáte tak, že přejdete do účtu úložiště v Azure Portal:
    - V části **Nastavení**vyberte **přístupové klíče** .
    - Zkopírujte buď připojovací řetězec klíč1, nebo připojovací řetězec key2.
 
6. V rozevíracím seznamu vyberte kontejner. Pokud nemáte kontejner, v Azure Portal přejít na svůj účet úložiště:
    - V části **BLOB Service**vyberte **objekty blob**. Klikněte na **+ kontejner** a zadejte název svého kontejneru. Vyberte úroveň veřejného přístupu pro vaše data (všechny budou fungovat s průběžným exportem dat). Další informace najdete v [dokumentaci Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  V části **data, která chcete exportovat**, určete každý typ dat k exportu nastavením typ na **zapnuto**.
   
    > [!NOTE] 
    > Data jsou exportována ve formátu JSON.

8. Pokud chcete zapnout funkci průběžného exportu dat, ujistěte se, že je zapnutý přepínač pro **Export dat** . Vyberte **Save** (Uložit).

   ![Konfigurace průběžného exportu dat](media/howto-export-data-pnp/export-list-blob2.png)

9. Po několika minutách se vaše data zobrazí ve vašem účtu úložiště.


## <a name="path-structure"></a>Struktura cesty

Data telemetrie, zařízení a šablon zařízení se exportují do účtu úložiště jednou za minutu a každý soubor, který obsahuje dávku změn od posledního exportovaného souboru. Exportovaná data jsou umístěna ve formátu JSON ve třech složkách. Výchozí cesty v účtu úložiště jsou:
- Telemetrie: {Container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Zařízení: {Container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- Šablony zařízení: {Container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

Exportované soubory můžete procházet v Azure Portal tak, že přejdete do souboru a kliknete na kartu **Upravit objekt BLOB** .

## <a name="data-format"></a>Formát dat
### <a name="telemetry"></a>Telemetrie

Data exportovaných telemetrie mají všechny nové zprávy přijaté IoT Central ze všech zařízení během této doby. Exportované soubory používají stejný formát jako soubory zpráv exportované [IoT Hub směrováním zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) do úložiště objektů BLOB.

> [!NOTE]
> Ujistěte se, že vaše zařízení odesílají zprávy, které mají `contentType: application/JSON` a `contentEncoding:utf-8` (nebo `utf-16`, `utf-32`). Příklad najdete v [dokumentaci k IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) .

> [!NOTE]
> Zařízení, která odesílají telemetrii, jsou představována ID zařízení (viz následující části). Pokud chcete získat názvy zařízení, exportujte snímky zařízení. Proveďte korelaci každého záznamu zprávy pomocí **connectionDeviceId** , který odpovídá poli **deviceId** záznamu zařízení.

Následující příklad ukazuje záznam ve formátu JSON.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Zařízení

Pokud je nejprve zapnutý průběžný export dat, je exportován jeden snímek se všemi zařízeními. Každé zařízení zahrnuje:
- `@id` zařízení v IoT Central
- `name` zařízení
- @no__t – 0 ze [služby Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Informace o šabloně zařízení
- Hodnoty vlastností

Nový snímek se zapisuje jednou za minutu. Snímek obsahuje:

- Nová zařízení přidaná od posledního snímku.
- Zařízení se změněnými hodnotami vlastností od posledního snímku.

> [!NOTE]
> Zařízení Odstraněná od posledního snímku se neexportují. V současné době snímky nemají indikátory pro Odstraněná zařízení.
>
> Šablona zařízení, do které patří každé zařízení, je reprezentovaná polem `instanceOf`. Pokud chcete získat název šablony zařízení, exportujte snímky šablony zařízení.

Exportované soubory obsahují jeden řádek na záznam. Následující příklad ukazuje záznam ve formátu JSON.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Šablony zařízení

Pokud je nejprve zapnutý průběžný export dat, je exportován jeden snímek se všemi šablonami zařízení. Každá šablona zařízení zahrnuje:
- `@id` šablony zařízení
- `name` šablony zařízení
- `version` šablony zařízení
- Zařízení `capabilityModel` včetně `interfaces`, a definice telemetrie, vlastností a příkazů.
- definice `cloudProperties`
- Přepisuje a počáteční hodnoty, které jsou vložené s `capabilityModel`.

Nový snímek se zapisuje jednou za minutu. Snímek obsahuje:

- Od posledního snímku se přidaly nové šablony zařízení. To zahrnuje nové verze šablon zařízení.
- Šablony zařízení s změněnými přepsáními, počátečními hodnotami a definicemi vlastností cloudu od posledního snímku.

> [!NOTE]
> Šablony zařízení odstraněné od posledního snímku se neexportují. V současné době snímky nemají indikátory pro odstraněné šablony zařízení.

Exportované soubory obsahují jeden řádek na záznam. Následující příklad ukazuje záznam ve formátu JSON.

```json
{ 
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{ 
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[ 
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{ 
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{ 
                  "@value":"50"
                }
              },
              "visualizationDetail":{ 
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{ 
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[ 
      { 
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{ 
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{ 
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

Když teď víte, jak exportovat data, pokračujte k dalšímu kroku:

> [!div class="nextstepaction"]
> [Jak používat most IoT Central zařízení k propojení dalších cloudů IoT](howto-build-iotc-device-bridge.md)

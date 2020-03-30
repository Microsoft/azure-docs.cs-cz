---
title: Interakce se zařízením IoT Plug and Play Preview z řešení Azure IoT | Dokumenty společnosti Microsoft
description: Jako vývojář řešení se dozvíte, jak používat službu SDK k interakci se zařízeními IoT Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159213"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Připojení k zařízení IoT Plug and Play Preview a interakce s ním

Tento návod vám ukáže, jak používat ukázky sady SDK služby uzlu, které ukazují, jak vaše řešení IoT může pracovat se zařízeními IoT Plug and Play Preview.

Pokud jste nedokončili [připojení zařízení IoT Plug and Play k](quickstart-connect-pnp-device-solution-node.md) rychlému startu řešení, měli byste tak učinit nyní. Rychlý start ukazuje, jak stáhnout a nainstalovat sadu SDK a spustit některé ukázky.

Před spuštěním ukázek služby otevřete nový terminál, přejděte do kořenové složky klonovaného úložiště, přejděte do složky **digitaltwins/quickstarts/service** a potom spusťte následující příkaz k instalaci závislostí:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Spuštění ukázek služby

Následující ukázky slouží k prozkoumání možností sady SDK služby Node.js. Ujistěte se, že proměnná `IOTHUB_CONNECTION_STRING` prostředí je nastavena v prostředí, které používáte:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Načtení digitálního dvojčete a seznam rozhraní

**get_digital_twin.js** získá digitální dvojče přidružené k vašemu zařízení a vytiskne jeho součást v příkazovém řádku. K úspěšnému spuštění ukázky zařízení nevyžaduje ukázku spuštěného zařízení.

**get_digital_twin_interface_instance.js** získá jednu instanci rozhraní digitálního dvojčete přidruženého k vašemu zařízení a vytiskne jej v příkazovém řádku. Nevyžaduje spuštění ukázky zařízení.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Získání a nastavení vlastností pomocí sady SDK služby Uzlu

**soubor update_digital_twin.js** aktualizuje zapisovatelnou vlastnost na digitální dvojče zařízení pomocí úplné opravy. Pokud chcete, můžete aktualizovat více vlastností na více rozhraních. Aby byl úspěšný, musí být ukázka zařízení spuštěna současně. Úspěch vypadá, že ukázka zařízení tiskne něco o aktualizaci vlastnosti ukázka služby tisk aktualizované digitální dvojče v terminálu.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Odeslání příkazu a načtení odpovědi pomocí sady SDK služby Uzel

**soubor u invoke_command.js** vyvolá na digitálnídvojčedu zařízení synchronní příkaz. Aby byl úspěšný, musí být ukázka zařízení spuštěna současně. Úspěch vypadá, že ukázka zařízení tiskne něco o potvrzení příkazu a klient služby tisk výsledek příkazu v terminálu.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Připojení k veřejnému úložišti a načtení definice modelu pomocí sady SDK služby Uzlu

Pomocí stejných pokynů jako u vzorků služby a zařízení je třeba nastavit následující proměnnou prostředí:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Tento připojovací řetězec najdete na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na kartě **Připojovací řetězce** pro **úložiště společnosti**.

Připojovací řetězec vypadá takto:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Po nastavení těchto čtyř proměnných prostředí spusťte ukázku stejným způsobem, jakým jste spustili ostatní vzorky:

```cmd/sh
node model_repo.js
```

Tato ukázka stáhne rozhraní **ModelDiscovery** a vytiskne tento model v terminálu.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Spouštění dotazů v centru IoT Hub na základě modelů schopností a rozhraní

Dotazovací jazyk centra IoT Hub podporuje `HAS_INTERFACE` a `HAS_CAPABILITYMODEL` jak je znázorněno v následujících příkladech:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Vytváření digitálních dvojitých tras

Vaše řešení může přijímat oznámení o událostech změny digitálních dvojčat. Chcete-li se přihlásit k odběru těchto oznámení, použijte [funkci směrování služby IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) k odeslání oznámení do koncového bodu, jako je úložiště objektů blob, centra událostí nebo fronta service bus.

Vytvoření digitální dvojité trasy:

1. Na webu Azure Portal přejděte na prostředek služby IoT Hub.
1. Vyberte **možnost Směrování zpráv**.
1. Na kartě **Trasy** vyberte **Přidat**.
1. Zadejte hodnotu do pole **Název** a zvolte **koncový bod**. Pokud jste koncový bod nenakonfigurovali, vyberte **Přidat koncový bod**.
1. V rozevíracím seznamu **Zdroj dat** vyberte události **změny digitálních dvojčat**.
1. Vyberte **Uložit**.

Následující json ukazuje příklad události změny digitálního dvojčete:

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o servisních řešeních, která interagují s vašimi zařízeními IoT Plug and Play, je dalším doporučeným krokem informace o [zjišťování modelu](concepts-model-discovery.md).

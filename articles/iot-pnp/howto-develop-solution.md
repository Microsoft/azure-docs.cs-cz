---
title: Interakce se zařízením IoT technologie Plug and Play Preview z řešení Azure IoT | Microsoft Docs
description: Jako vývojář řešení se dozvíte, jak používat sadu SDK služby k interakci se zařízeními IoT technologie Plug and Play.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e349aadfd629202b1c8cdb5c53a88e0a6c2e06de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159213"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>Připojení a interakce se zařízením IoT technologie Plug and Play Preview

V této příručce se dozvíte, jak používat ukázky v sadě SDK pro Node Service, které ukazují, jak může vaše řešení IoT spolupracovat s IoT technologie Plug and Play ve verzi Preview.

Pokud jste nedokončili [připojení zařízení IoT technologie Plug and Play k](quickstart-connect-pnp-device-solution-node.md) rychlému startu vašeho řešení, měli byste to udělat hned teď. V tomto rychlém startu se dozvíte, jak stáhnout a nainstalovat sadu SDK a jak spustit některé ukázky.

Než začnete s ukázkami služby, otevřete nový terminál, přejděte do kořenové složky naklonovaného úložiště, přejděte do složky **digitaltwins/rychlé zprovoznění/služba** a spuštěním následujícího příkazu nainstalujte závislosti:

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>Spuštění ukázek služby

Pomocí následujících ukázek můžete prozkoumat možnosti sady SDK služby Node. js. Ujistěte se, že `IOTHUB_CONNECTION_STRING` je proměnná prostředí nastavená v prostředí, které používáte:

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>Načtení digitálního vlákna a seznam rozhraní

**get_digital_twin. js** získá digitální vlákna přidružená k vašemu zařízení a vytiskne jeho komponentu na příkazovém řádku. Není nutné, aby byla spuštěná ukázka zařízení úspěšná.

**get_digital_twin_interface_instance. js** získá jednu instanci rozhraní digitálního vlákna přidruženou k vašemu zařízení a vytiskne ji na příkazovém řádku. Nevyžaduje spuštění vzorku zařízení.

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>Získání a nastavení vlastností pomocí sady SDK služby Node

**update_digital_twin. js** aktualizuje vlastnost s možností zápisu do digitálního vlákna v zařízení pomocí úplné opravy. Pokud chcete, můžete aktualizovat více vlastností u více rozhraní. Aby bylo úspěšné, musí být ukázka zařízení spuštěná ve stejnou dobu. Úspěch vypadá to, že ukázka zařízení tiskne něco o aktualizaci vlastnosti. Ukázka služby tisk aktualizovaného digitálního vlákna v terminálu.

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>Odeslání příkazu a načtení odpovědi pomocí sady SDK služby Node

**invoke_command. js** vyvolá synchronní příkaz na svém digitálním vlákna na vašem zařízení. Aby bylo úspěšné, musí být ukázka zařízení spuštěná ve stejnou dobu. Úspěch vypadá to, že ukázka zařízení tiskne něco o potvrzení příkazu a klient služby vytiskl výsledek příkazu v terminálu.

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>Připojte se k veřejnému úložišti a načtěte definici modelu pomocí sady SDK služby Node.

Pomocí stejných instrukcí jako u ukázek služeb a zařízení musíte nastavit následující proměnnou prostředí:

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

Tento připojovací řetězec najdete na [portálu Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) na kartě **připojovací řetězce** pro **úložiště vaší společnosti**.

Připojovací řetězec vypadá jako v následujícím příkladu:

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

Po nastavení těchto čtyř proměnných prostředí spusťte ukázku stejným způsobem jako ostatní ukázky:

```cmd/sh
node model_repo.js
```

Tato ukázka stáhne rozhraní **ModelDiscovery** a vytiskne tento model v terminálu.

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>Spouštění dotazů v IoT Hub na základě modelů a rozhraní schopností

Jazyk dotazů IoT Hub podporuje `HAS_INTERFACE` , `HAS_CAPABILITYMODEL` jak je znázorněno v následujících příkladech:

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>Vytváření digitálních dvojitých tras

Vaše řešení může dostávat oznámení o digitálních událostech změny. Chcete-li se přihlásit k odběru těchto oznámení, použijte [funkci směrování IoT Hub](../iot-hub/iot-hub-devguide-endpoints.md) k odeslání oznámení do koncového bodu, jako je například úložiště objektů blob, Event Hubs nebo fronta Service Bus.

Vytvoření digitální zdvojené trasy:

1. V Azure Portal přejít na prostředek IoT Hub.
1. Vyberte **směrování zpráv**.
1. Na kartě **trasy** vyberte **Přidat**.
1. Zadejte hodnotu do pole **název** a vyberte **koncový bod**. Pokud jste koncový bod nenakonfigurovali, vyberte **přidat koncový bod**.
1. V rozevíracím seznamu **zdroj dat** vyberte možnost **digitální události změny**.
1. Vyberte **Uložit**.

Následující JSON ukazuje příklad události digitálního vlákna změny:

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

Teď, když jste se dozvěděli o řešeních služeb, která komunikují se zařízeními IoT technologie Plug and Play, je navržený další krok informace o [zjišťování modelů](concepts-model-discovery.md).

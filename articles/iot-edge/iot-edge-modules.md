---
title: Pochopení moduly Azure IoT Edge | Microsoft Docs
description: Další informace o Azure IoT Edge moduly a jak jsou nakonfigurované
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 60c2c17d7a5cca66a6323f43e1ab2662afff54ee
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630832"
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Pochopení moduly Azure IoT Edge – náhled

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiky hranu ve formě *moduly*. Azure IoT Edge moduly jsou nejmenší jednotka výpočtu spravuje IoT okraj a může obsahovat služby Azure (například Azure Stream Analytics) nebo vlastního kódu pro konkrétní řešení. Chcete-li pochopit, jak jsou moduly vyvinuté, nasadit a udržovat, je dobré se zamyslet nad čtyři koncepční částí, které tvoří modul:

* A **modulu image** je balíček obsahující software, který definuje modul.
* A **instancí modulu** je konkrétní jednotka výpočtu bitovou kopii modulu systémem IoT hraniční zařízení. Instance modulu je spuštěn modulem runtime IoT okraj.
* A **modulu identity** je část informace (včetně zabezpečovací přihlašovací údaje) uložené ve IoT Hub, který je přidružen k jednotlivým instancím modulu.
* A **modulu twin** je dokument JSON uložené v IoT Hub, který obsahuje informace o stavu pro instanci modulu, včetně metadata, konfigurace a podmínky. 

## <a name="module-images-and-instances"></a>Modul bitové kopie a instance

Okraj IoT modulu Image obsahují aplikace, které využít výhod správy, zabezpečení a komunikaci funkce IoT Edge modulu runtime. Můžete vyvíjet vlastní Image modulu nebo exportování jedné z podporovaných služba Azure, jako je například Azure Stream Analytics.
Bitové kopie existovat v cloudu a jejich lze aktualizovat, změnit a nasadit v různých řešeních. Například modul, který využívá strojové učení k předvídání výstup produkční řádku existuje jako samostatná bitová kopie než modul, který se používá k řízení drone vize počítače. 

Pokaždé, když bitovou kopii modulu je nasazená na zařízení a spuštění modulem runtime IoT okraj, je vytvořena nová instance tohoto modulu. Dvě zařízení v různých částech světa použít stejnou bitovou kopii modulu; ale každý by měla mít vlastní instanci modulu při spuštění modulu v zařízení. 

![Modul obrázků v cloudu – modul instancí na zařízení][1]

V implementaci moduly Image existují jako obrázky kontejner v úložišti a modul instance jsou kontejnery na zařízení. Jelikož případů použití pro Azure IoT Edge, vytvoří se nové typy obrázků modulu a instancí. Například prostředků omezené zařízení nelze spustit kontejnerů, může vyžadovat modulu bitové kopie, které existují jako dynamické knihovny a instancí, které jsou spustitelné soubory. 

## <a name="module-identities"></a>Modul identit

Když modulem runtime IoT okraj je vytvořena nová instance modulu, je přidružen odpovídající identitu modulu instance. Modul identity je uložený v IoT Hub a pracuje jako adresování a zabezpečení obor pro všechny místní a cloudové komunikaci pro tuto instanci konkrétního modulu.
Identita spojená s instancí modulu závisí na identitu zařízení, na kterém je spuštěn a název, zadejte do tohoto modulu ve vašem řešení. Například když zavoláte `insight` modul, který používá Azure Stream Analytics a můžete ho nasadit na zařízení s názvem `Hannover01`, modul runtime IoT Edge vytvoří odpovídající identitu modul volá `/devices/Hannover01/modules/insight`.

Je zřejmé ve scénářích když potřebujete nasadit jeden modul image vícekrát na stejném zařízení, můžete nasadit stejnou bitovou kopii vícekrát s různými názvy.

![Modul identity jsou jedinečné][2]

## <a name="module-twins"></a>Modul dvojčata

Každá instance modul má také odpovídající twin modul, který vám pomůže nakonfigurovat instanci modulu. Instance a twin a jsou přidruženy mezi sebou prostřednictvím modulu identity. 

Modul twin je dokument JSON, který ukládá vlastnosti informace a konfigurace modulu. Tento koncept je paralelní [dvojče zařízení] [ lnk-device-twin] koncept ze služby IoT Hub. Struktura twin modulu je přesně stejný jako dvojče zařízení. Rozhraní API používají k interakci se oba typy dvojčata jsou také stejné. Jediným rozdílem mezi těmito dvěma je identita používaný k vytváření instancí klienta SDK. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>Další postup
 - [Pochopení modulu runtime Azure IoT okraj a jeho architektura][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md
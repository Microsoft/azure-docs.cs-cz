---
title: Zjistěte, jak moduly běží logiku na vašich zařízeních - Azure IoT Edge | Dokumenty společnosti Microsoft
description: Moduly Azure IoT Edge jsou kontejnerizované logické jednotky, které lze nasadit a spravovat vzdáleně, takže můžete spouštět obchodní logiku na zařízeních IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548709"
---
# <a name="understand-azure-iot-edge-modules"></a>Vysvětlení modulů Azure IoT Edge

Azure IoT Edge umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních ve formě *modulů*. Moduly Azure IoT Edge jsou nejmenší výpočetní jednotkou spravovanou ioT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Chcete-li pochopit, jak jsou moduly vyvíjeny, nasazovány a udržovány, zvažte čtyři koncepční prvky modulu:

* **Bitová kopie modulu** je balíček obsahující software, který definuje modul.
* **Instance modulu** je specifická jednotka výpočtu, která spouštějí bitovou kopii modulu na zařízení IoT Edge. Instance modulu je spuštěna modulem ioT Edge runtime.
* **Identita modulu** je informace (včetně pověření zabezpečení) uložená v centru IoT Hub, která je přidružena ke každé instanci modulu.
* Dvojče **modulu** je dokument JSON uložený v centru IoT Hub, který obsahuje informace o stavu instance modulu, včetně metadat, konfigurací a podmínek.

## <a name="module-images-and-instances"></a>Obrázky modulů a instance

Bitové kopie modulu IoT Edge obsahují aplikace, které využívají funkce správy, zabezpečení a komunikace modulu Runtime IoT Edge. Můžete vyvinout vlastní ifotky modulů nebo exportovat z podporované služby Azure, jako je Azure Stream Analytics.
Image existují v cloudu a mohou být aktualizovány, změněny a nasazeny v různých řešeních. Například modul, který používá strojové učení k předvídání výstupu výrobní linky, existuje jako samostatný obraz než modul, který používá počítačové vidění k ovládání drone.

Pokaždé, když image modulu je nasazen do zařízení a spuštěn a spustí modul runtime IoT Edge, je vytvořena nová instance tohoto modulu. Dvě zařízení v různých částech světa by mohla používat stejný obraz modulu. Každé zařízení by však mělo vlastní instanci modulu při spuštění modulu v zařízení.

![Diagram – obrázky modulů v cloudu, instance modulů na zařízeních](./media/iot-edge-modules/image_instance.png)

V implementaci existují image modulů jako image kontejnerů v úložišti a instance modulů jsou kontejnery na zařízeních.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identity modulů

Když je nová instance modulu vytvořena modulem IoT Edge, získá odpovídající identitu modulu. Identita modulu je uložena v centru IoT Hub a používá se jako obor adresování a zabezpečení pro veškerou místní a cloudovou komunikaci pro tuto instanci modulu.

Identita přidružená k instanci modulu závisí na identitě zařízení, na kterém je instance spuštěna, a na názvu, který tomuto modulu poskytnete ve vašem řešení. Například pokud zavoláte `insight` modul, který používá Azure Stream Analytics a `Hannover01`nasadíte jej na zařízení s názvem `/devices/Hannover01/modules/insight`, modul runtime IoT Edge vytvoří odpovídající identitu modulu s názvem .

Je zřejmé, že ve scénářích, kdy potřebujete nasadit jednu bitovou kopii modulu vícekrát na stejném zařízení, můžete nasadit stejnou bitovou kopii vícekrát s různými názvy.

![Diagram – identity modulů jsou jedinečné v rámci zařízení a napříč zařízeními](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Dvojčata modulů

Každá instance modulu má také odpovídající dvojče modulu, které můžete použít ke konfiguraci instance modulu. Instance a dvojče jsou vzájemně spojeny prostřednictvím identity modulu.

Dvojče modulu je dokument JSON, který ukládá informace o modulu a vlastnosti konfigurace. Tento koncept je paralelou konceptu [dvojčete zařízení](../iot-hub/iot-hub-devguide-device-twins.md) z IoT Hubu. Struktura dvojčete modulu je stejná jako dvojče zařízení. Api používané k interakci s oběma typy dvojčat jsou také stejné. Jediný rozdíl mezi těmito dvěma je identita slouží k vytvoření instance klienta SDK.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>Offline možnosti

Moduly Azure IoT Edge můžou fungovat offline po synchronizaci s IoT Hubem alespoň jednou. Zařízení IoT Edge můžou tuto offline funkci rozšířit i na další zařízení IoT. Další informace najdete [v tématu Principy rozšířených offline funkcí pro zařízení, moduly a podřízená zařízení IoT Edge](offline-capabilities.md).

## <a name="next-steps"></a>Další kroky

* [Seznamte se s požadavky a nástroji pro vývoj modulů IoT Edge](module-development.md)
* [Principy runtime Azure IoT Edge a jeho architektury](iot-edge-runtime.md)

---
title: Zjistěte, jak moduly spouštět logiku na vaše zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Moduly Azure IoT Edge jsou kontejnerizovaných jednotky logiku, která je možné nasadit a spravovat vzdáleně, takže můžete spustit obchodní logiku na hraničních zařízeních IoT zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1c625e628f53d156ad56a1c69df1c23aec9120ac
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548709"
---
# <a name="understand-azure-iot-edge-modules"></a>Vysvětlení modulů Azure IoT Edge

Azure IoT Edge umožňuje nasazovat a spravovat obchodní logiku na hraničních zařízeních ve formě *moduly*. Moduly Azure IoT Edge je nejmenší jednotka výpočtu spravuje IoT Edge a může obsahovat služeb Azure (jako je Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Chcete-li pochopit, jak jsou moduly vyvíjeny, nasazeny a udržovány, vezměte v úvahu čtyři koncepční prvky modulu:

* A **image s modulem** je balíček, který obsahuje software, který definuje modulu.
* A **instancí modulu** je konkrétní jednotka výpočtu spuštění bitové kopie modulu na zařízení IoT Edge. Instance modulu je spuštěn modul runtime IoT Edge.
* A **modul identity** je část informací (včetně zabezpečovací přihlašovací údaje) uložených ve službě IoT Hub, který je přidružený ke každé instanci modulu.
* A **dvojče modulu** je dokument JSON uložený ve službě IoT Hub, který obsahuje informace o stavu pro instanci modulu, včetně metadata, konfigurace a podmínky.

## <a name="module-images-and-instances"></a>Bitové kopie modulu a instance

Bitové kopie modulu IoT Edge obsahují aplikace, které budou využívat správu, zabezpečení a komunikační funkce modulu runtime IoT Edge. Můžete vyvíjet vlastní bitové kopie modulu nebo exportovat z podporované služby Azure, jako je Azure Stream Analytics.
Obrázky existovat v cloudu a může být aktualizován, změnit a nasazené v různých řešeních pro. Například modul, který využívá strojové učení k předpovědi výrobní linky výstup existuje jako samostatná bitová kopie než modul, který se používá k řízení dron pro počítačové zpracování obrazu.

Pokaždé, když modul image je nasazená na zařízení a tím, že modul runtime IoT Edge je vytvořena nová instance tohoto modulu. Stejná image modulu můžou používat dvě zařízení v různých částech světa. Každé zařízení by ale mělo vlastní instanci modulu, když je modul na zařízení spuštěný.

![Diagram - bitové kopie modulu v cloudu, instancí modulu na zařízeních](./media/iot-edge-modules/image_instance.png)

V implementaci moduly imagí existovat jako imagí kontejnerů v úložišti a instance modulu jsou kontejnery na zařízeních.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modul identity

Když IoT Edge modul runtime vytvoří novou instanci modulu, získá odpovídající identitu modulu. Identita modulu je uložena v IoT Hub a slouží jako obor adresování a zabezpečení pro veškerou místní a cloudovou komunikaci pro danou instanci modulu.

Identita spojenou s instancí modulu závisí na identitě zařízení, na kterém běží instance a název zadáte do tohoto modulu ve vašem řešení. Například při volání `insight` modul, který používá Azure Stream Analytics a můžete ji nasadit na zařízení s názvem `Hannover01`, modul runtime IoT Edge vytvoří odpovídající identitu modulu s názvem `/devices/Hannover01/modules/insight`.

Je zřejmé ve scénářích když potřebujete nasadit jednu image modul více než jednou na jednom zařízení můžete nasadit stejnou bitovou kopii několikrát s různými názvy.

![Diagram – modul identity musí být jedinečné v rámci zařízení a napříč zařízeními](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Dvojčaty modulů

Každá instance modulu má také odpovídající dvojčete modulu, který vám pomůže nakonfigurovat instanci modulu. Instance a dvojčeti jsou spojeny s navzájem prostřednictvím modulu identity.

Dvojče modulu je dokument JSON, který ukládá vlastnosti informace a konfigurace modulu. Tento koncept parallels [dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md) koncept ze služby IoT Hub. Struktura vlákna modulu je stejná jako nevlákenná zařízení. Rozhraní API používaná pro interakci s oběma typy dvojčat jsou také stejné. Jediným rozdílem mezi těmito dvěma je identity použité k vytvoření instance klientskou sadou SDK.

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

Azure IoT Edge moduly můžou pracovat offline po neomezenou dobu po synchronizaci s IoT Hub aspoň jednou. Zařízení IoT Edge můžou také tuto funkci offline rozšiřuje na jiná zařízení IoT. Další informace najdete v tématu [porozumění rozšířené offline možnosti pro IoT Edge, zařízení, moduly a podřízená zařízení](offline-capabilities.md).

## <a name="next-steps"></a>Další kroky

* [Pochopení požadavků a nástroje pro vývoj modulů IoT Edge](module-development.md)
* [Pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md)

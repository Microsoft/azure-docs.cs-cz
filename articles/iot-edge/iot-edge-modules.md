---
title: Vysvětlení modulů Azure IoT Edge | Dokumentace Microsoftu
description: Další informace o Azure IoT Edge modulů a jejich konfiguraci
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 80679d6efd44598fbe403707ad2e757010eb8d91
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741670"
---
# <a name="understand-azure-iot-edge-modules"></a>Vysvětlení modulů Azure IoT Edge

Azure IoT Edge umožňuje nasazovat a spravovat obchodní logiku na hraničních zařízeních ve formě *moduly*. Moduly Azure IoT Edge je nejmenší jednotka výpočtu spravuje IoT Edge a může obsahovat služeb Azure (jako je Azure Stream Analytics) nebo vlastní kód specifický pro řešení. Abyste pochopili, jak jsou moduly vyvíjel, nasadit a udržovat, pomáhá Zamyslete se nad čtyři úryvky koncepční, které společně tvoří modul:

* A **image s modulem** je balíček, který obsahuje software, který definuje modulu.
* A **instancí modulu** je konkrétní jednotka výpočtu spuštění bitové kopie modulu na zařízení IoT Edge. Instance modulu je spuštěn modul runtime IoT Edge.
* A **modul identity** je část informací (včetně zabezpečovací přihlašovací údaje) uložených ve službě IoT Hub, který je přidružený ke každé instanci modulu.
* A **dvojče modulu** je dokument JSON uložený ve službě IoT Hub, který obsahuje informace o stavu pro instanci modulu, včetně metadata, konfigurace a podmínky. 

## <a name="module-images-and-instances"></a>Bitové kopie modulu a instance

Bitové kopie modulu IoT Edge obsahují aplikace, které budou využívat správu, zabezpečení a komunikační funkce modulu runtime IoT Edge. Můžete vyvíjet vlastní bitové kopie modulu nebo exportovat z podporované služby Azure, jako je Azure Stream Analytics.
Obrázky existovat v cloudu a může být aktualizován, změnit a nasazené v různých řešeních pro. Například modul, který využívá strojové učení k předpovědi výrobní linky výstup existuje jako samostatná bitová kopie než modul, který se používá k řízení dron pro počítačové zpracování obrazu. 

Pokaždé, když modul image je nasazená na zařízení a tím, že modul runtime IoT Edge je vytvořena nová instance tohoto modulu. Dvě zařízení v různých částech světa použít stejnou image modulem; ale každý má své vlastní instance modulu při spuštění modulu na zařízení. 

![Bitové kopie modulu v cloudu – instance modulu na zařízeních](./media/iot-edge-modules/image_instance.png)

V implementaci moduly imagí existovat jako imagí kontejnerů v úložišti a instance modulu jsou kontejnery na zařízeních. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Modul identity

Když je vytvořena nová instance modulu modulem runtime IoT Edge, instance je spojené s odpovídající identitu modulu. Modul identity je uložená ve službě IoT Hub a pracuje jako obor adresování a zabezpečení pro všechny místní a cloudové komunikaci pro danou instanci modulu pro konkrétní.
Identita spojenou s instancí modulu závisí na identitě zařízení, na kterém běží instance a název zadáte do tohoto modulu ve vašem řešení. Například při volání `insight` modul, který používá Azure Stream Analytics a můžete ji nasadit na zařízení s názvem `Hannover01`, modul runtime IoT Edge vytvoří odpovídající identitu modulu s názvem `/devices/Hannover01/modules/insight`.

Je zřejmé ve scénářích když potřebujete nasadit jednu image modul více než jednou na jednom zařízení můžete nasadit stejnou bitovou kopii několikrát s různými názvy.

![Modul identity musí být jedinečné](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>Dvojčaty modulů

Každá instance modulu má také odpovídající dvojčete modulu, který vám pomůže nakonfigurovat instanci modulu. Instance a dvojčeti jsou spojeny s navzájem prostřednictvím modulu identity. 

Dvojče modulu je dokument JSON, který ukládá vlastnosti informace a konfigurace modulu. Tento koncept parallels [dvojče zařízení](../iot-hub/iot-hub-devguide-device-twins.md) koncept ze služby IoT Hub. Struktura dvojčete modulu je přesně dvojčete zařízení. Rozhraní API používaná pro interakci s oběma typy dvojčat jsou také stejné. Jediným rozdílem mezi těmito dvěma je identity použité k vytvoření instance klientskou sadou SDK. 

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

Azure IoT Edge podporuje offline operace s vašimi zařízeními IoT Edge. Tyto možnosti jsou teď omezená. 

Moduly IoT Edge může být offline delší dobu, za předpokladu splnění následujících požadavků: 

* **Zpráva time to live (TTL) nevypršela platnost**. Výchozí hodnota TTL zprávy je dvě hodiny, ale může být změněné vyšší nebo nižší v Store a přeposlat konfiguraci ve službě IoT Edge hub nastavení. 
* **Moduly není nutné donutit s centrem IoT Edge v režimu offline**. Moduly lze pouze ověření s centry Edge, které mají aktivní připojení k službě IoT hub. Moduly se muset znovu ověřovat, pokud se restartují z jakéhokoli důvodu. Moduly může i dál posílat zprávy do centra Edge po vypršení platnosti tokenu SAS. Po obnovení připojení Centrum Edge vyžádá nový token z modulu a ověřuje prostřednictvím služby IoT hub. V případě úspěchu se že Centrum Edge předává zprávy modulu, které jsou uloženy, dokonce i zprávy, které byly odeslány, zatímco byl vypršela platnost tokenu modulu. 
* **Modul, který odeslané zprávy při offline je stále funkční po obnovení připojení**. Při opětovném připojení ke službě IoT Hub, je potřeba ověřit nový token modulu (Pokud předchozí platnost) předtím, než nebude moct přesměrovávat zprávy modulu Centrum Edge. Pokud modul není k dispozici nový token, Centrum Edge nemůže reagovat na zprávy uložené modulu. 
* **Centrum Edge se místo na disku pro uložení zpráv**. Ve výchozím nastavení zprávy jsou uloženy v systému souborů kontejneru Edge hub. Neexistuje parametr konfigurace pro specifikování připojený svazek pro uložení zpráv místo. V obou případech musí být místa pro ukládání zpráv pro odložené doručování do služby IoT Hub.  

Další možnosti offline jsou dostupné ve verzi public preview. Další informace najdete v tématu [porozumění rozšířené offline možnosti pro IoT Edge, zařízení, moduly a podřízená zařízení](offline-capabilities.md).

## <a name="next-steps"></a>Další postup
 - [Pochopení požadavků a nástroje pro vývoj modulů IoT Edge](module-development.md)
 - [Pochopení runtime Azure IoT Edge a jeho architektura](iot-edge-runtime.md)

<!-- Images -->
[2]: ./media/iot-edge-modules/identity.png

---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175126"
---
## <a name="automatic-device-management"></a>Automatická správa zařízení
Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy velkých vozových parků zařízení po celou dobu jejich životního cyklu. Pomocí automatické správy zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a nechat zařízení služby IoT Hub aktualizovat vždy, když se dostanou do oboru.  Skládá se z [automatických konfigurací zařízení](../articles/iot-hub/iot-hub-auto-device-config.md) a [automatických nasazení IoT Edge](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge umožňuje nasazení služeb Azure a kódu specifického pro řešení na základě cloudu do místních zařízení. Zařízení IoT Edge můžou agregovat data z jiných zařízení a provádět tak výpočetní a analytické práce před odesláním dat do cloudu. Další informace najdete v [tématu Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Část modulu runtime IoT Edge, která je zodpovědná za nasazení a monitorování modulů.

## <a name="iot-edge-device"></a>Zařízení IoT Edge
Zařízení IoT Edge mají nainstalovanou runtime IoT Edge a v podrobnostech o zařízení jsou označena jako **zařízení IoT Edge.** Zjistěte, jak [nasadit Azure IoT Edge na simulované zařízení v Linuxu – preview](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatické nasazení IoT Edge
Automatické nasazení IoT Edge konfiguruje cílovou sadu zařízení IoT Edge pro spuštění sady modulů IoT Edge. Každé nasazení průběžně zajišťuje, že všechna zařízení, která odpovídají jeho cílové podmínce, používají zadanou sadu modulů, a to i v případě, že jsou vytvořena nová zařízení nebo upravena tak, aby odpovídala cílové podmínce. Každé zařízení IoT Edge obdrží pouze nasazení s nejvyšší prioritou, jehož cílovou podmínkou splňuje. Další informace o [automatickém nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest nasazení IoT Edge
Dokument Json obsahující informace, které mají být zkopírovány v jednom nebo více dvojčat zařízení IoT Edge pro nasazení sady modulů, tras a přidružených vlastností modulu.

## <a name="iot-edge-gateway-device"></a>Zařízení brány IoT Edge
Zařízení IoT Edge s následným zařízením. Navazující zařízení může být buď IoT Edge nebo ne zařízení IoT Edge.

## <a name="iot-edge-hub"></a>Centrum IoT Edge
Část runtime IoT Edge zodpovědná za komunikaci modulu s modulem, upstream (směrem k IoT Hub) a následnou komunikaci (mimo IoT Hub). 

## <a name="iot-edge-leaf-device"></a>Zařízení s listy IoT Edge
Zařízení IoT Edge bez zařízení pro příjem dat. 

## <a name="iot-edge-module"></a>Modul IoT Edge
Modul IoT Edge je kontejner Dockeru, který můžete nasadit do zařízení IoT Edge. Provádí konkrétní úkol, jako je například ingestování zprávy ze zařízení, transformace zprávy nebo odeslání zprávy do služby IoT hub. Komunikuje s ostatními moduly a odesílá data do modulu runtime IoT Edge. [Seznamte se s požadavky a nástroji pro vývoj modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identita modulu IoT Edge
Záznam v registru identit modulu IoT Hub s podrobnostmi o existenci a pověření zabezpečení, které mají modul použít k ověření pomocí hraničního centra nebo služby IoT Hub.

## <a name="iot-edge-module-image"></a>Obrázek modulu IoT Edge
Image dockeru, který se používá modulu runtime IoT Edge k vytvoření instance modulu.

## <a name="iot-edge-module-twin"></a>Dvojče modulu IoT Edge
Dokument Json trval v centru IoT Hub, který ukládá informace o stavu pro instanci modulu.

## <a name="iot-edge-priority"></a>Priorita IoT Edge
Když dvě nasazení IoT Edge cílí na stejné zařízení, použije se nasazení s vyšší prioritou. Pokud dvě nasazení mají stejnou prioritu, nasazení s pozdější datum vytvoření získá použít. Další informace o [prioritě](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge
Běhový běh IoT Edge zahrnuje vše, co Microsoft distribuuje k instalaci na zařízení IoT Edge. Zahrnuje agentedge, edge hub a demon zabezpečení IoT Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge nastavit moduly na jedno zařízení
Operace, která kopíruje obsah manifestu IoT Edge na dvojčete modulu jednoho zařízení. Základní rozhraní API je obecný "použít konfiguraci", která jednoduše bere manifest IoT Edge jako vstup.

## <a name="iot-edge-target-condition"></a>Cílová podmínka ioT edge
V nasazení IoT Edge je podmínka Target libovolná logická podmínka na značkách dvojčat zařízení, která vybere cílová zařízení nasazení, například **tag.environment = prod**. Cílová podmínka je průběžně vyhodnocována tak, aby zahrnovala všechna nová zařízení, která splňují požadavky, nebo odeberte zařízení, která již nesplňují. Další informace o [cílové podmínce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)
---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: 0b478775aa572ce89f3dcdc6f28ee0418e818d99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91377361"
---
## <a name="automatic-device-management-in-azure-iot-hub"></a>Automatická správa zařízení v Azure IoT Hub
Automatická správa zařízení v Azure IoT Hub automatizuje mnoho opakujících se a složitých úloh správy rozsáhlých loďstva zařízení v celém životním cyklu. Díky automatické správě zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definovat požadovanou konfiguraci a nechat IoT Hub aktualizovat zařízení pokaždé, když vstoupí do rozsahu.  Skládá se z [automatických konfigurací zařízení](../articles/iot-hub/iot-hub-auto-device-config.md) a [IoT Edgech automatických nasazení](../articles/iot-edge/how-to-deploy-at-scale.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge umožňuje cloudové nasazení služeb Azure a kódu specifického pro řešení do místních zařízení. Zařízení IoT Edge můžou agregovat data z jiných zařízení a provádět výpočty a analýzy před odesláním dat do cloudu. Další informace najdete v tématu [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agent IoT Edge
Součást modulu runtime IoT Edge zodpovědná za nasazení a monitorování modulů.

## <a name="iot-edge-device"></a>Zařízení IoT Edge
U zařízení IoT Edge je modul runtime IoT Edge nainstalovaný a v podrobnostech o zařízení se označil jako **IoT Edge zařízení** . Naučte se, jak [nasadit Azure IoT Edge na simulovaném zařízení v systému Linux – Preview](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>IoT Edge automatické nasazení
IoT Edge automatické nasazení konfiguruje cílovou sadu IoT Edgech zařízení, aby spouštěla sadu IoT Edge modulů. Každé nasazení průběžně zajišťuje, aby všechna zařízení, která odpovídají jeho cílovým podmínkám, používala zadanou sadu modulů, i když se vytvoří nová zařízení nebo se upraví tak, aby odpovídaly cílové podmínce. Každé zařízení IoT Edge přijímá jenom nasazení s nejvyšší prioritou, jehož cílová podmínka splňuje. Přečtěte si další informace o [IoT Edge automatickém nasazení](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest nasazení IoT Edge
Dokument JSON obsahující informace, které mají být kopírovány v jednom nebo více podmnožinách modulu IoT Edge zařízení, aby se nasadila sada modulů, tras a přidružených modulů, které jsou požadované.

## <a name="iot-edge-gateway-device"></a>Zařízení IoT Edge brány
Zařízení IoT Edge se zařízením pro příjem dat. Zařízení pro příjem dat může být buď IoT Edge, nebo není IoT Edge zařízení.

## <a name="iot-edge-hub"></a>Centrum IoT Edge
Součást modulu runtime IoT Edge zodpovědná za modul komunikace, nadřazeného streamování (směrem k IoT Hub) a podřízeného (od IoT Hub) komunikace. 

## <a name="iot-edge-leaf-device"></a>Zařízení IoT Edge list
Zařízení IoT Edge bez zařízení pro příjem dat. 

## <a name="iot-edge-module"></a>Modul IoT Edge
Modul IoT Edge je kontejner Docker, který můžete nasadit do IoT Edge zařízení. Provede konkrétní úlohu, například ingestování zprávy ze zařízení, transformuje zprávu nebo posílá zprávu do služby IoT Hub. Komunikuje s ostatními moduly a odesílá data do modulu runtime IoT Edge. Seznamte [se s požadavky a nástroji pro vývoj IoT Edgech modulů](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identita modulu IoT Edge
Záznam v registru identit modulu IoT Hub, který podrobně popisuje existenci a přihlašovací údaje zabezpečení používané modulem k ověřování pomocí hraničního centra nebo IoT Hub.

## <a name="iot-edge-module-image"></a>Obrázek modulu IoT Edge
Image Docker, kterou používá modul runtime IoT Edge k vytvoření instance instancí modulu.

## <a name="iot-edge-module-twin"></a>Nevlákenný modul IoT Edge
Dokument JSON trvalý v IoT Hub, ve kterém jsou uložené informace o stavu instance modulu.

## <a name="iot-edge-priority"></a>Priorita IoT Edge
Pokud se ke stejnému zařízení cílí dvě IoT Edge nasazení, použije se nasazení s vyšší prioritou. Pokud mají dvě nasazení stejnou prioritu, bude použito nasazení s pozdějším datem vytvoření. Přečtěte si další informace o [prioritách](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge
IoT Edge runtime zahrnuje všechno, co Microsoft distribuuje, aby se nainstaloval na IoT Edge zařízení. Zahrnuje hraničního agenta, hraničního centra a IoT Edge démona zabezpečení.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge nastavit moduly na jedno zařízení
Operace, která kopíruje obsah IoT Edge manifestu v modulu jednoho zařízení je dvojitá. Základní rozhraní API je obecná možnost použít konfiguraci, která jednoduše přebírá IoT Edge manifest jako vstup.

## <a name="iot-edge-target-condition"></a>Cílová podmínka IoT Edge
V nasazení IoT Edge je cílová podmínka jakákoli logická podmínka pro značky nepodmíněných zařízení, aby se vybrala cílová zařízení nasazení, například **tag. Environment = prod**. Cílová podmínka se průběžně vyhodnocuje tak, aby zahrnovala nová zařízení, která splňují požadavky, nebo odebrat zařízení, která už nedělají. Další informace o [cílové podmínce](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)
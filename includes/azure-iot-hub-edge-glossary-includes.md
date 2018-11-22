---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279540"
---
## <a name="automatic-device-management"></a>Automatická správa zařízení
Automatická správa zařízení ve službě Azure IoT Hub umožňuje automatizovat mnoho opakovaných a složité úlohy správy flotily nebo velké zařízení přes celého jejich životního cyklu. Automatická správa zařízení můžete cílit na sadu zařízení na základě jejich vlastností, definují požadovanou konfiguraci a nechat pokaždé, když se do rozsahu sem přišli aktualizaci zařízení služby IoT Hub.  Se skládá z [automatické konfigurace](../articles/iot-hub/iot-hub-auto-device-config.md) a [automatické nasazení IoT Edge](../articles/iot-edge/how-to-deploy-monitor.md).

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge umožňuje řízené cloudové nasazení služby Azure a kód specifický pro řešení do různých zařízení. Zařízení IoT Edge můžou agregovat data z jiných zařízení provádět výpočty a analýzy před odesláním dat do cloudu. Další informace najdete v tématu [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).

## <a name="iot-edge-agent"></a>Agenta IoT Edge
Součást zodpovědná za nasazení a monitorování modulů runtime IoT Edge.

## <a name="iot-edge-device"></a>Zařízení IoT Edge
Zařízení IoT Edge mají modul runtime IoT Edge, které jsou nainstalované a jsou označeny jako **zařízení IoT Edge** v podrobností o zařízení. Zjistěte, jak [nasazení Azure IoT Edge na simulovaném zařízení v systému Linux – ve verzi preview](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux).

## <a name="iot-edge-automatic-deployment"></a>Automatické nasazení IoT Edge
Automatické nasazení IoT Edge Konfiguruje sadu cílových zařízení IoT Edge na spuštění sady IoT Edge moduly. Každé nasazení průběžně zajišťuje, že všechna zařízení, která odpovídají jeho cílové podmínce, běží Zadaná sada modulů, i když nové zařízení jsou vytvořeny nebo jsou upraveny tak, aby odpovídaly cílovou podmínku. Každé zařízení IoT Edge přijímá pouze nejvyšší prioritu nasazení, jehož cílovou podmínku splňuje. Další informace o [automatického nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

## <a name="iot-edge-deployment-manifest"></a>Manifest nasazení IoT Edge
Dokument Json obsahující informace, které se mají zkopírovat do twin(s) jeden nebo více zařízení IoT Edge modul pro nasazení sady modulů, trasy a přidružený modul požadované vlastnosti.

## <a name="iot-edge-gateway-device"></a>Zařízení brány IoT Edge
Zařízení IoT Edge se podřízené zařízením. Příjem dat zařízení může být buď IoT Edge, nebo ne zařízení IoT Edge.

## <a name="iot-edge-hub"></a>Centrum IoT Edge
Součást zodpovědná za modulu do modulu komunikace, (směrem k službě IoT Hub) směrem nahoru a dolů (od služby IoT Hub) modul runtime IoT Edge komunikace. 

## <a name="iot-edge-leaf-device"></a>Zařízení IoT Edge typu list
Zařízení IoT Edge se žádné podřízené zařízení. 

## <a name="iot-edge-module"></a>Modul pro IoT Edge
Modul IoT Edge je kontejner Dockeru, který nasadíte do zařízení IoT Edge. Provede určitou úlohu, jako je příjem zpráv ze zařízení, transformují zprávy nebo odeslání zprávy do služby IoT hub. Komunikuje s ostatními moduly a odesílá data do modulu runtime IoT Edge. [Pochopení požadavků a nástroje pro vývoj modulů IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-development).

## <a name="iot-edge-module-identity"></a>Identita modul IoT Edge
Záznam v registru identit služby IoT Hub modulu s podrobnostmi o existenci a zabezpečení pověření, která mají být modul používá k ověřování pomocí služby IoT Hub nebo centrum edge.

## <a name="iot-edge-module-image"></a>Image s modulem IoT Edge
Image dockeru, který používá modul runtime IoT Edge k vytvoření instancí modulu.

## <a name="iot-edge-module-twin"></a>Dvojče zařízení IoT Edge
Dokument Json jako trvalý, ve službě IoT Hub, která ukládá informace o stavu pro instance modulu.

## <a name="iot-edge-priority"></a>Priorita IoT Edge
Když dvě nasazení IoT Edge cílí na jedno zařízení, použije se nasazení s vyšší prioritou. Pokud mají dvě nasazení stejnou prioritu, použije se nasazení s novější datum vytvoření. Další informace o [priority](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority).

## <a name="iot-edge-runtime"></a>Modul runtime IoT Edge
Modul runtime IoT Edge zahrnuje vše, co Microsoft distribuuje být nainstalovaný na zařízení IoT Edge. Zahrnuje démona zabezpečení IoT Edge, Edge agent a Centrum Edge.

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge na jediné zařízení nastavit moduly
Operace, která zkopíruje obsah na jednom zařízení IoT Edge manifestu ' dvojčete modulu. Základní rozhraní API je obecný použít konfigurace, který jednoduše by manifest IoT Edge jako vstup.

## <a name="iot-edge-target-condition"></a>Cílová podmínka IoT Edge
V nasazení IoT Edge, cílová podmínka se jakoukoli logickou podmínku na značky dvojčat zařízení vybrat cílová zařízení daného nasazení, například **tag.environment = prod**. Cílová podmínka se průběžně vyhodnocuje tak zahrnula nová zařízení, které splňují požadavky nebo odebrat zařízení, která již plní. Další informace o [cílová podmínka](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)
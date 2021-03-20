---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560008"
---
<!-- operation names for the diag logs for IoT Hub -->

|Název operace|Level|Description|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Informace|Zprávu nelze vyhodnotit pomocí podmínky předání. Například pokud ve zprávě chybí vlastnost v podmínce dotaz trasy. Přečtěte si další informace o [syntaxi dotazů směrování](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md).|
|RouteEvaluationError|Chyba|Při vyhodnocování zprávy došlo k chybě z důvodu problému s formátem zprávy. Tato chyba se zaznamená například v případě, že kódování obsahu není zadáno nebo typ obsahu není ve zprávě platný. Ty musí být nastavené ve [vlastnostech systému](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties).|
|DroppedMessage|Chyba|Zpráva byla vyřazena a nebyla směrována. To může být způsobeno tím, že se zpráva neshodovala s žádným dotazem na směrování nebo koncový bod nebyl doručen a zpráva nemohla být doručena po několika opakovaných pokusech. K získání dalších podrobností o koncovém bodu doporučujeme použít REST API [získat stav koncového bodu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointUnhealthy|Chyba|Koncový bod nepřijal zprávy od IoT Hub a IoT Hub se pokouší znovu odeslat zprávy. Doporučujeme, abyste zobrazili poslední známou chybu prostřednictvím REST API [získat stav koncového bodu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointDead|Chyba|Koncový bod nepřijímal zprávy od IoT Hub po dobu delší než hodinu. Doporučujeme, abyste zobrazili poslední známou chybu prostřednictvím REST API [získat stav koncového bodu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth).|
|EndpointHealthy|Informace|Koncový bod je v pořádku a přijímá zprávy z IoT Hub. Tato zpráva se neprotokoluje průběžně, ale přihlásila se jenom v případě, že se koncový bod znovu obnoví. Tato zpráva znamená, že IoT Hub nebylo možné odeslat zprávy do koncového bodu, ale koncový bod je nyní v pořádku.|
|OrphanedMessage|Informace|Zpráva neodpovídá žádné trase.|
|InvalidMessage|Chyba|Zpráva je neplatná kvůli nekompatibilitě s koncovým bodem. Doporučujeme, abyste zkontrolovali konfigurace koncového bodu.|


Operace *UndefinedRouteEvaluation*, *RouteEvaluationError* a *OrphanedMessage* jsou omezené a protokolují se nejenom jednou za minutu na IoT Hub.
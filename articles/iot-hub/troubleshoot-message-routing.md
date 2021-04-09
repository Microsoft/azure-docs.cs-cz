---
title: Řešení potíží se směrováním zpráv Azure IoT
description: Jak řešit potíže s směrováním zpráv Azure IoT
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 3abff5645775d724042acba3ee2461c7cad771a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149660"
---
# <a name="troubleshooting-message-routing"></a>Řešení potíží s směrováním zpráv

Tento článek poskytuje pokyny pro monitorování a řešení potíží pro běžné problémy a řešení pro IoT Hub [směrování zpráv](iot-hub-devguide-messages-d2c.md).

## <a name="monitoring-message-routing"></a>Sledování směrování zpráv

Doporučujeme vám monitorovat [IoT Hub metriky související se směrováním zpráv a koncovými body](monitor-iot-hub-reference.md#routing-metrics) , které vám poskytnou přehled o odeslaných zprávách. Můžete také vytvořit nastavení diagnostiky k odesílání operací pro [ **trasy** v IoT Hubch protokolech prostředků](monitor-iot-hub-reference.md#routes) Azure monitor protokoly, Event Hubs nebo Azure Storage pro vlastní zpracování. Další informace o používání metrik, protokolů prostředků a nastavení diagnostiky najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md). Kurz najdete v tématu [nastavení a používání metrik a protokolů prostředků ve službě IoT Hub](tutorial-use-metrics-and-diags.md).

Pokud chcete udržovat zprávy, které se neshodují s dotazem na žádných trasách, doporučujeme taky povolit [záložní cestu](iot-hub-devguide-messages-d2c.md#fallback-route) . Ty je možné uchovat v [předdefinovaném koncovém bodu](iot-hub-devguide-messages-read-builtin.md) pro dobu nakonfigurovaného uchovávání dní.

## <a name="top-issues"></a>Hlavní problémy

Níže jsou uvedené nejběžnější problémy, které jsou pozorovány při směrování zpráv. Pokud chcete začít řešit potíže, klikněte na problém pro podrobné kroky.

* [Zprávy ze zařízení se nesměrují podle očekávání](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Náhle jsem přestal získávat zprávy na předdefinovaný Event Hubs koncový bod.](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Zprávy ze zařízení se nesměrují podle očekávání

Pokud chcete tento problém vyřešit, analyzujte následující.

#### <a name="the-routing-metrics-for-this-endpoint"></a>Metriky směrování pro tento koncový bod

Všechny [metriky IoT Hub související se směrováním](monitor-iot-hub-reference.md#routing-metrics) jsou s předponou *Směrování*. Můžete zkombinovat informace z několika metrik a identifikovat tak hlavní příčinu problémů. Například použijte **doručení směrování** metriky k určení počtu zpráv, které byly doručeny do koncového bodu nebo vyřazeny, když neodpovídaly dotazům na některé z tras a záložní trase byly zakázány. Zkontrolujte metriku **latence směrování** a sledujte, jestli je latence pro doručování zpráv stabilní nebo roste. Rostoucí latence může indikovat problém s konkrétním koncovým bodem a doporučujeme zkontrolovat [stav koncového bodu](#the-health-of-the-endpoint). Tyto metriky směrování mají také [dimenze](monitor-iot-hub-reference.md#metric-dimensions) , které poskytují podrobnosti o metrikě, jako je typ koncového bodu, konkrétní název koncového bodu a důvod, proč nebyla zpráva doručena.

#### <a name="the-resource-logs-for-any-operational-issues"></a>Protokoly prostředků pro jakékoli provozní problémy

Pokud chcete získat další informace o [operacích](#operation-names) směrování a koncových bodů nebo určit chyby a relevantní [kód chyby](#common-error-codes) , abyste mohli problém lépe pochopit, Prohlédněte si [protokoly prostředků **Směrování**](monitor-iot-hub-reference.md#routes) . Například název operace **RouteEvaluationError** v protokolu indikuje, že trasu nebylo možné vyhodnotit kvůli problému s formátem zprávy. Pomocí tipů pro konkrétní [názvy operací](#operation-names) můžete problém zmírnit. V případě, že událost je protokolována jako chyba, protokol také poskytne další informace o tom, proč selhalo vyhodnocení. Například pokud je název operace **EndpointUnhealthy**, [kód chyby](#common-error-codes) 403004 znamená, že koncový bod nemá dostatek místa.

#### <a name="the-health-of-the-endpoint"></a>Stav koncového bodu

Pro získání [stavu](iot-hub-devguide-endpoints.md#custom-endpoints) koncových bodů použijte REST API [získat stav koncového bodu](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) . Rozhraní API pro *zjištění stavu koncového bodu* poskytuje také informace o poslední úspěšné odeslání zprávy do koncového bodu, [poslední známou chybu](#last-known-errors-for-iot-hub-routing-endpoints), poslední známou chybu a čas posledního pokusu o odeslání pro tento koncový bod. Využijte možné zmírnění poskytované pro konkrétní [poslední známou chybu](#last-known-errors-for-iot-hub-routing-endpoints).

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Náhle jsem přestal získávat zprávy na předdefinovaném koncovém bodu

Pokud chcete tento problém vyřešit, analyzujte následující.

#### <a name="was-a-new-route-created"></a>Byl vytvořen nový postup?

Po vytvoření trasy se data přestanou předávat do integrovaného koncového bodu, pokud se do tohoto koncového bodu nevytvoří trasa. Chcete-li zajistit, aby zprávy pokračovaly v toku s integrovaným koncovým bodem, pokud je přidána nová trasa, nakonfigurujte trasu na koncový bod *události* . 

#### <a name="was-the-fallback-route-disabled"></a>Je záložní trasa zakázaná?

Záložní trasa pošle všechny zprávy, které nesplňují podmínky pro dotazování na všechny existující trasy, na [integrované Event Hubs](iot-hub-devguide-messages-read-builtin.md) (zprávy/události), které jsou kompatibilní s [Event Hubs](../event-hubs/index.yml). Pokud je zapnuté směrování zpráv, můžete povolit funkci záložních tras. Pokud neexistují žádné trasy k integrovanému koncovému bodu a je povolena záložní trasa, budou do integrovaného koncového bodu odesílány pouze zprávy, které neodpovídají podmínkám dotazu na trasách. I když se odstraní všechny existující trasy, musí být povolená záložní trasa pro příjem všech dat na integrovaném koncovém bodu.

Záložní cestu můžete povolit nebo zakázat v okně Azure Portal->směrování zpráv. Můžete také použít Azure Resource Manager pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) k použití vlastního koncového bodu pro záložní směrování.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Poslední známé chyby pro koncové body směrování IoT Hub

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>Směrování protokolů prostředků

Níže jsou uvedené názvy operací a kódy chyb zaznamenané v [protokolech prostředků tras](monitor-iot-hub-reference.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Názvy operací

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Běžné kódy chyb

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Další kroky

Pokud potřebujete další informace, můžete se obrátit na odborníky na Azure na [fórech Microsoft Q&a a Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.

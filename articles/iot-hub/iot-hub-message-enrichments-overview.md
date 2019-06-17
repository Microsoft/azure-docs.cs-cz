---
title: Přehled služby Azure IoT Hub zpráva obohacení
description: Přehled zprávy obohacení zpráv služby Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 13e35ab93fc37541548785c6355489eaf3a3efc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754561"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>Obohacení zprávu pro zprávy typu zařízení cloud služby IoT Hub (preview)

*Zpráva obohacení* je schopnost služby IoT Hub do *razítko* zprávy s doplňujícími informacemi před odesláním zprávy do určeným koncového bodu. Jedním z důvodů použití obohacení zprávy se zahrnou data, která slouží k zjednodušení zpracování příjmu dat. Rozšíření telemetrických zpráv zařízení s značku dvojčete zařízení můžete například snížit zatížení zákazníkům dvojče zařízení pro tyto informace volání rozhraní API.

![Obohacení toku zpráv](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Zpráva rozšíření má tři klíčové prvky:

* Název rozšíření nebo klíč

* Hodnota

* Jeden nebo více [koncové body](iot-hub-devguide-endpoints.md) pro který obohacení bude použito.

Klíč může být libovolný řetězec.

Hodnota může být některý z následujících příkladech:

* Žádné statický řetězec. Dynamické hodnoty, jako jsou podmínky, logika, operací a funkce nejsou povoleny. Například pokud vyvíjíte aplikaci SaaS, která používá několik zákazníků, můžete přiřadit identifikátor každého zákazníka a zpřístupnit tento identifikátor v aplikaci. Při spuštění aplikace, služby IoT Hub se razítko zařízení telemetrické zprávy s identifikátorem zákazníka, což umožňuje zpracovávat zprávy, které jsou jinak pro každého zákazníka.

* Informace z dvojčete zařízení, jako je například cesta. Příklady by *$twin.tags.field* a *$twin.tags.latitude*.

* Název služby IoT hub odesílá zprávy. Tato hodnota je *$iothubname*.

## <a name="applying-enrichments"></a>Použití obohacení

Zprávy mohou pocházet z jakéhokoli zdroje dat podporované [směrování zpráv služby IoT Hub](iot-hub-devguide-messages-d2c.md), včetně následujících příkladech:

* telemetrie zařízení, jako je například teploty a tlaku
* oznámení o změnách dvojče zařízení – změny ve dvojčeti zařízení
* události životního cyklu zařízení, jako je například při vytvoření nebo odstranění zařízení

Obohacení přidáte do zprávy, které se chystáte integrovaný koncový bod služby IoT Hub nebo zprávy, které jsou směrovány na vlastní koncové body, jako je například úložiště objektů Blob v Azure, do fronty služby Service Bus nebo téma služby Service Bus.

Obohacení můžete také přidat do zprávy, které se publikují do služby Event Grid tak, že vyberete koncový bod jako služby Event Grid. Další informace najdete v tématu [služby Iot Hub a Event Grid](iot-hub-event-grid.md).

Obohacení se vztahují na koncový bod. Pokud chcete zadat pět obohacení, chcete-li označením pro určitý koncový bod, jsou stejné pět obohacení označený všechny zprávy, že přejdete do tohoto koncového bodu.

Jak si vyzkoušet naši zprávu obohacení najdete v tématu [kurzu obohacení zprávy](tutorial-message-enrichments.md)

## <a name="limitations"></a>Omezení

* Můžete přidat až 10 obohacení za služby IoT Hub pro služby hubs na úrovni standard nebo basic. Pro IoT hub na úrovni free můžete přidat až 2 obohacení.

* V některých případech Pokud jste se má použít rozšíření s hodnotou nastavenou značku nebo vlastnosti v dvojčeti zařízení hodnota bude být označený jako hodnotu řetězce. Například pokud je nastavena hodnota obohacení $twin.tags.field, zprávy se být označený řetězec "$twin.tags.field" místo hodnoty tohoto pole od dvojčeti. To se stane v následujících případech:

   * Služby IoT Hub je na úrovni basic. Úroveň Basic služby IoT hubs nepodporují dvojčata zařízení.

   * Služby IoT Hub je na úrovni standard, ale zařízení posílání zprávy nemá žádné dvojče zařízení.

   * Služby IoT Hub je na úrovni standard, ale cesty dvojčete zařízení použité pro hodnotu vlastnosti rozšíření neexistuje. Například hodnota rozšíření je nastavený na $twin.tags.location a dvojče zařízení nemá vlastnost umístění v části značky, zpráva má s řetězcem "$twin.tags.location". 

* Aktualizace dvojčete zařízení může trvat až pět minut, než se projeví v odpovídající hodnota, která rozšíření.

* Celková velikost zpráv, včetně obohacení, nesmí překročit 256 KB. Pokud překročí velikost zprávy 256 KB, bude služby IoT Hub vyřadit zprávu. Můžete použít [metriky služby IoT Hub](iot-hub-metrics.md) k identifikaci a ladit chyby, když se zprávy zahodí. Můžete například monitorovat d2c.telemetry.egress.invalid.

## <a name="pricing"></a>Ceny

Obohacení zprávy jsou k dispozici žádné další poplatky. V současné době se účtují při odeslání zprávy do služby IoT Hub. Vám účtovat jenom jednou zprávě, i v případě, že zpráva přejde na několik koncových bodů.

## <a name="availability"></a>Dostupnost

Tato funkce je dostupná ve verzi preview a je k dispozici ve všech oblastech kromě USA – východ, USA – Západ, západní Evropa, [Azure Government](/azure/azure-government/documentation-government-welcome), [Azure China 21Vianet](/azure/china), a [Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>Další postup

Projděte si tyto články pro další informace o směrování zpráv do služby IoT Hub:

* [Použití směrování zpráv služby IoT Hub pro odesílání zpráv typu zařízení cloud do různých koncových bodů](iot-hub-devguide-messages-d2c.md)

* [Kurz: Směrování služby IoT Hub](tutorial-routing.md)
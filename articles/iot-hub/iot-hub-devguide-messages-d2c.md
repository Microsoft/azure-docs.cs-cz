---
title: Vysvětlení směrování zpráv služby Azure IoT Hub | Dokumentace Microsoftu
description: Průvodce pro vývojáře – jak používat směrování zpráv k odesílání zpráv typu zařízení cloud. Obsahuje informace o odesílání telemetrických dat a jiných telemetrická data.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: cabfe4381f7f941f2a5e049eed73be546902f6ae
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55485343"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Odesílání zpráv typu zařízení cloud do různých koncových bodů pomocí směrování zpráv

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv umožňuje odesílání zpráv z vašich zařízení ke cloudovým službám automatizované, škálovatelných a spolehlivých způsobem. Směrování zpráv je možné pro: 

* **Odesílání telemetrie zprávy typu zařízení, stejně jako události** konkrétně události životního cyklu zařízení a do koncových bodů integrované-endpoint a vlastní události změn dvojčete zařízení. Další informace o [směrování koncových bodů](#routing-endpoints).

* **Filtrování dat před směrování do různých koncových bodů** použitím bohaté dotazy. Směrování zpráv umožňuje dotazování na vlastnosti zprávy a tělo zprávy stejně jako značky dvojčat zařízení a vlastnosti dvojčete zařízení. Další informace o používání [dotazy v směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

IoT Hub potřebuje oprávnění k zápisu do těchto koncových bodů služby pro směrování zpráv do práce. Při konfiguraci vašich koncových bodů na webu Azure portal, potřebná oprávnění jsou přidány automaticky. Ujistěte se, že konfigurace služeb pro podporu očekávané propustnosti. Při první konfiguraci řešení IoT, budete muset monitorovat další koncové body a proveďte všechny nezbytné úpravy podle skutečné zátěže.

Definuje služby IoT Hub [běžný formát](iot-hub-devguide-messages-construct.md) pro všechna zařízení cloud zasílání zpráv pro interoperabilitu mezi protokoly. Pokud zpráva odpovídá několik tras, které odkazují na stejný koncový bod, IoT Hub doručí zprávu do tohoto koncového bodu pouze jednou. Proto není nutné konfigurace odstranění duplicitních dat na frontu služby Service Bus nebo téma. V dělené fronty oddílu spřažení záruky pořadí zpráv. Pomocí tohoto kurzu se dozvíte, jak [konfigurace směrování zpráv](tutorial-routing.md).

## <a name="routing-endpoints"></a>Směrování koncových bodů

Služby IoT hub má integrované-v – koncový bod (**zpráv/události**), která je kompatibilní s Event Hubs. Můžete vytvořit [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) pro směrování zpráv propojením dalších služeb v rámci vašeho předplatného do služby IoT Hub. Tyto služby IoT Hub aktuálně podporuje jako vlastní koncové body:

### <a name="built-in-endpoint"></a>Integrovaný koncový bod

Můžete použít standardní [integraci služby Event Hubs a sady SDK](iot-hub-devguide-messages-read-builtin.md) pro příjem zpráv typu zařízení cloud z integrovaného koncového bodu (**zpráv/události**). Všimněte si, že po vytvoření trasy data přestanou přicházet do integrované-v-koncového bodu Pokud není vytvořené trasy do tohoto koncového bodu.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub podporuje pouze zápis dat do Azure Blob Storage v [Apache Avro](http://avro.apache.org/) formátu. IoT Hub dávek zpráv a zapisuje data do objektu blob pokaždé, když se dosáhne určité velikosti dávky nebo uplynutí určité doby.

Výchozí nastavení služby IoT Hub následujícími zásadami vytváření názvů souborů:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolné zásady vytváření názvů souborů, ale musí využívat všechny uvedené tokeny. IoT Hub bude zapisovat do prázdný objekt blob, pokud neexistuje žádná data k zápisu.

### <a name="service-bus-queues-and-service-bus-topics"></a>Fronty služby Service Bus a témat Service Bus

Fronty služby Service Bus a témat použít jako nesmí obsahovat koncové body IoT Hubu **relace** nebo **duplicit** povolena. Pokud některý z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na webu Azure Portal.

### <a name="event-hubs"></a>Event Hubs

Kromě integrované Event Hubs kompatibilní koncový bod můžete také směrovat data do vlastní koncové body typu Event Hubs. 

Při použití směrování a vlastní koncové body zprávy pouze doručovaly do integrovaného koncového bodu pokud neodpovídají žádná pravidla. Doručení zprávy na integrovaný koncový bod a vlastní koncové body, přidejte trasu, která odesílá zprávy do koncového bodu events.

## <a name="reading-data-that-has-been-routed"></a>Čtení dat, který prochází

Můžete nakonfigurovat trasu pomocí této [kurzu](tutorial-routing.md).

Použijte v následujících kurzech se naučíte číst zprávy z koncového bodu.

* Čtení z [integrované-endpoint](quickstart-send-telemetry-node.md)

* Čtení z [úložiště objektů Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Čtení z [služby Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Čtení z [fronty služby Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Čtení z [témata služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Náhradní trasa

Náhradní trasa odešle všechny zprávy, které není splňují podmínky dotazu na žádné existující trasy pro integrované Event Hubs (**zpráv/události**), která je kompatibilní s [Event Hubs](/azure/event-hubs/). Pokud je zapnutá směrování zpráv, můžete povolit funkci náhradní trasa. Všimněte si, že po vytvoření trasy data přestanou přicházet do integrované-v-koncového bodu, pokud není vytvořené trasy do tohoto koncového bodu. Pokud nejsou žádné trasy na integrované-v – koncový bod a je povolen záložní trasu, pošle jenom zprávy, které neodpovídají žádné podmínky dotazu na trasách integrované-v – koncový bod. Také se odstraní všechny existující trasy, musí být záložní trasy povoleno přijímat všechna data na integrované-v – koncový bod. 

Můžete povolit nebo zakázat záložní trasy v Azure Portal -> směrování zpráv okna. Můžete použít také Azure Resource Manageru pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) použít vlastní koncový bod pro použití náhradní lokality trasu.

## <a name="non-telemetry-events"></a>Bez telemetrie událostí

Kromě telemetrie zařízení směrování zpráv umožňuje odesílání události změn dvojčete zařízení a události životního cyklu zařízení. Například, pokud se vytvoří trasu se zdrojem dat nastaveno **události změn dvojčete zařízení**, IoT Hub odesílá zprávy do koncového bodu, který obsahuje změny ve dvojčeti zařízení. Podobně pokud se vytvoří trasu se zdrojem dat nastaveno **události životního cyklu zařízení**, Centrum IoT pošle zpráva označující, zda byla odstraněna nebo vytvoření zařízení. 

[IoT Hub se také integruje s Azure Event Grid](iot-hub-event-grid.md) publikovat události zařízení na podporu integrace v reálném čase a automatizace pracovních postupů na základě těchto událostí. Klíč [rozdíly mezi směrování zpráv a služby Event Grid](iot-hub-event-grid-routing-comparison.md) informace, které pro váš scénář nejvhodnější.

## <a name="testing-routes"></a>Testování trasy

Když vytvoříte novou trasu nebo upravíte existující trasy, měli byste otestovat trasu dotaz s ukázkovou zprávu. Můžete testovat jednotlivé trasy nebo otestujte všechny trasy najednou a žádné zprávy jsou směrovány na koncové body během testu. Azure Portal, Azure Resource Manageru, Azure Powershellu a rozhraní příkazového řádku Azure můžete použít pro testování. Výsledky pomoct zjistit, jestli ukázková zpráva odpovídá dotazu, zpráva se neshodoval dotazu nebo test nejde spustit, protože ukázková zpráva nebo dotazu syntaxe jsou nesprávné. Další informace najdete v tématu [trasy testu](/rest/api/iothub/iothubresource/testroute) a [otestovat všechny trasy](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latence

Při směrování zpráv telemetrická data typu zařízení cloud pomocí integrovaných koncových bodech, je po vytvoření prvního postupu mírné zvýšení-celkovou latenci.

Ve většině případů je průměrná zvýšení latence kratší než 500ms. Můžete monitorovat pomocí latence **směrování: zpráva latence pro zprávy/události** nebo **d2c.endpoints.latency.builtIn.events** metriky služby IoT Hub. Vytvoření nebo odstranění žádné trasy po první z nich nemá žádný vliv na celkovou latenci.

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

Poskytuje službě IoT Hub, několik směrování a koncový bod související metriky, které vám poskytnou přehled o stavu centra a odeslaných zpráv. Můžete kombinovat informace z několika metrik a identifikovat hlavní příčinu problémů. Například použijte metriku **směrování: počet ztracených zpráv telemetrie** nebo **d2c.telemetry.egress.dropped** identifikovat počet zpráv, které byly při neodpovídají dotazy na žádném z trasy a náhradní trasa byla zakázána. [Metriky služby IoT Hub](iot-hub-metrics.md) uvádí všechny metriky, které jsou ve výchozím nastavení povolená pro službu IoT Hub.

Použití **trasy** diagnostické protokoly ve službě Azure Monitor [nastavení diagnostiky](../iot-hub/iot-hub-monitor-resource-health.md), můžete sledují chyby, ke kterým dochází při vyhodnocování směrování dotazů a koncový bod stavu vnímanou ve službě IoT Hub, například Pokud koncový bod je neaktivní. Tyto diagnostické protokoly je odeslat do Log Analytics a Event Hubs, Azure Storage pro vlastní zpracování.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit trasy zpráv, najdete v článku [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](tutorial-routing.md).

* [Postup odesílání zpráv typu zařízení cloud](quickstart-send-telemetry-node.md)

* Informace o sadách SDK můžete použít k odesílání zpráv typu zařízení cloud, najdete v části [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

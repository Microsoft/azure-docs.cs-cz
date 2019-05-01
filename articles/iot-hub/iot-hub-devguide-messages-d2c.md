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
ms.openlocfilehash: dc5bfe6b431659b7b99140eb29a0e64922a42275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364497"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Použití směrování zpráv služby IoT Hub pro odesílání zpráv typu zařízení cloud do různých koncových bodů

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv umožňuje odesílání zpráv z vašich zařízení ke cloudovým službám automatizované, škálovatelných a spolehlivých způsobem. Směrování zpráv je možné pro: 

* **Odesílání telemetrie zprávy typu zařízení, stejně jako události** konkrétně události životního cyklu zařízení a do koncových bodů integrované-endpoint a vlastní události změn dvojčete zařízení. Další informace o [směrování koncových bodů](#routing-endpoints).

* **Filtrování dat před směrování do různých koncových bodů** použitím bohaté dotazy. Směrování zpráv umožňuje dotazování na vlastnosti zprávy a tělo zprávy stejně jako značky dvojčat zařízení a vlastnosti dvojčete zařízení. Další informace o používání [dotazy v směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

IoT Hub potřebuje oprávnění k zápisu do těchto koncových bodů služby pro směrování zpráv do práce. Při konfiguraci vašich koncových bodů na webu Azure portal, potřebná oprávnění jsou přidány automaticky. Ujistěte se, že konfigurace služeb pro podporu očekávané propustnosti. Při první konfiguraci řešení IoT, budete muset monitorovat další koncové body a proveďte všechny nezbytné úpravy podle skutečné zátěže.

Definuje služby IoT Hub [běžný formát](iot-hub-devguide-messages-construct.md) pro všechna zařízení cloud zasílání zpráv pro interoperabilitu mezi protokoly. Pokud zpráva odpovídá několik tras, které odkazují na stejný koncový bod, IoT Hub doručí zprávu do tohoto koncového bodu pouze jednou. Proto není nutné konfigurace odstranění duplicitních dat na frontu služby Service Bus nebo téma. V dělené fronty oddílu spřažení záruky pořadí zpráv. Pomocí tohoto kurzu se dozvíte, jak [konfigurace směrování zpráv](tutorial-routing.md).

## <a name="routing-endpoints"></a>Směrování koncových bodů

Služby IoT hub má integrované-v – koncový bod (**zpráv/události**), která je kompatibilní s Event Hubs. Můžete vytvořit [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) pro směrování zpráv propojením dalších služeb v rámci vašeho předplatného do služby IoT Hub. Tyto služby IoT Hub aktuálně podporuje jako vlastní koncové body:

### <a name="built-in-endpoint"></a>Integrovaný koncový bod

Můžete použít standardní [integraci služby Event Hubs a sady SDK](iot-hub-devguide-messages-read-builtin.md) pro příjem zpráv typu zařízení cloud z integrovaného koncového bodu (**zpráv/události**). Po vytvoření trasy data přestanou přicházet do integrované-v-koncového bodu není-li do tohoto koncového bodu se vytvoří trasy.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub podporuje zápis dat do Azure Blob Storage v [Apache Avro](https://avro.apache.org/) stejně jako formátu JSON. Možnost kódování formátu JSON je ve verzi preview ve všech oblastech, které služby IoT Hub je k dispozici, s výjimkou východní USA, západní USA a západní Evropa. Výchozí hodnota je AVRO. Formát kódování lze nastavit pouze, když je nakonfigurovaný koncový bod služby blob storage. Formát nelze upravovat pro existující koncový bod. Při použití kódování JSON, musíte nastavit typ obsahu do formátu JSON a contentEncoding na UTF-8 ve zprávě [vlastnosti systému](iot-hub-devguide-routing-query-syntax.md#system-properties). Můžete vybrat formát kódování pomocí IoT Hub Create nebo aktualizace REST API, konkrétně [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), na webu Azure Portal [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest) nebo [Azure Prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Následující diagram znázorňuje postup výběru formátu kódování na webu Azure Portal.

![Kódování koncový bod úložiště objektů BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub dávek zpráv a zapisuje data do objektu blob pokaždé, když se dosáhne určité velikosti dávky nebo uplynutí určité doby. Výchozí nastavení služby IoT Hub následujícími zásadami vytváření názvů souborů:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolné zásady vytváření názvů souborů, ale musí využívat všechny uvedené tokeny. IoT Hub bude zapisovat do prázdný objekt blob, pokud neexistuje žádná data k zápisu.

Při směrování do úložiště objektů blob, doporučujeme uvedení objektů BLOB a pak iterace je zajistit, že všechny kontejnery, které jsou pro čtení bez vytváření žádných předpokladů vyhodnocený oddílu. Rozsah oddílů může potenciálně změnit během [iniciované Microsoft převzetí služeb při selhání](iot-hub-ha-dr.md#microsoft-initiated-failover) nebo službu IoT Hub [ruční převzetí služeb při selhání](iot-hub-ha-dr.md#manual-failover-preview). Můžete použít [seznam objektů BLOB rozhraní API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) výčet seznamu objektů BLOB. Jako pokyny najdete v následující ukázce.

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Fronty služby Service Bus a témat Service Bus

Fronty služby Service Bus a témat použít jako nesmí obsahovat koncové body IoT Hubu **relace** nebo **duplicit** povolena. Pokud některý z těchto možností jsou povolené, koncový bod se zobrazí jako **Unreachable** na webu Azure Portal.

### <a name="event-hubs"></a>Event Hubs

Kromě integrované Event Hubs kompatibilní koncový bod můžete také směrovat data do vlastní koncové body typu Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Čtení dat, který prochází

Můžete nakonfigurovat trasu pomocí této [kurzu](tutorial-routing.md).

Použijte v následujících kurzech se naučíte číst zprávy z koncového bodu.

* Čtení z [integrované-endpoint](quickstart-send-telemetry-node.md)

* Čtení z [úložiště objektů Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Čtení z [služby Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Čtení z [fronty služby Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Čtení z [témata služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Náhradní trasa

Náhradní trasa odešle všechny zprávy, které není splňují podmínky dotazu na žádné existující trasy pro integrované Event Hubs (**zpráv/události**), která je kompatibilní s [Event Hubs](/azure/event-hubs/). Pokud je zapnutá směrování zpráv, můžete povolit funkci náhradní trasa. Po vytvoření trasy data přestanou přicházet do integrované-v-koncového bodu, pokud není vytvořené trasy do tohoto koncového bodu. Pokud nejsou žádné trasy na integrované-v – koncový bod a je povolen záložní trasu, pošle jenom zprávy, které neodpovídají žádné podmínky dotazu na trasách integrované-v – koncový bod. Také se odstraní všechny existující trasy, musí být záložní trasy povoleno přijímat všechna data na integrované-v – koncový bod. 

Můžete povolit nebo zakázat záložní trasy v Azure Portal -> směrování zpráv okna. Můžete použít také Azure Resource Manageru pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) použít vlastní koncový bod pro použití náhradní lokality trasu.

## <a name="non-telemetry-events"></a>Bez telemetrie událostí

Kromě telemetrie zařízení směrování zpráv umožňuje odesílání události změn dvojčete zařízení a události životního cyklu zařízení. Například, pokud se vytvoří trasu se zdrojem dat nastaveno **události změn dvojčete zařízení**, IoT Hub odesílá zprávy do koncového bodu, který obsahuje změny ve dvojčeti zařízení. Podobně pokud se vytvoří trasu se zdrojem dat nastaveno **události životního cyklu zařízení**, Centrum IoT pošle zpráva označující, zda byla odstraněna nebo vytvoření zařízení. 

[IoT Hub se také integruje s Azure Event Grid](iot-hub-event-grid.md) publikovat události zařízení na podporu integrace v reálném čase a automatizace pracovních postupů na základě těchto událostí. Klíč [rozdíly mezi směrování zpráv a služby Event Grid](iot-hub-event-grid-routing-comparison.md) informace, které pro váš scénář nejvhodnější.

## <a name="testing-routes"></a>Testování trasy

Když vytvoříte novou trasu nebo upravíte existující trasy, měli byste otestovat trasu dotaz s ukázkovou zprávu. Můžete testovat jednotlivé trasy nebo otestujte všechny trasy najednou a žádné zprávy jsou směrovány na koncové body během testu. Azure Portal, Azure Resource Manageru, Azure Powershellu a rozhraní příkazového řádku Azure můžete použít pro testování. Výsledky pomoct zjistit, jestli ukázková zpráva odpovídá dotazu, zprávy neodpovídá dotaz nebo test nejde spustit, protože ukázková zpráva nebo dotazu syntaxe, nejsou správné. Další informace najdete v tématu [trasy testu](/rest/api/iothub/iothubresource/testroute) a [otestovat všechny trasy](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latence

Při směrování zpráv telemetrická data typu zařízení cloud pomocí integrovaných koncových bodech, je po vytvoření prvního postupu mírné zvýšení-celkovou latenci.

Ve většině případů průměrném zvyšování latence je menší než 500 ms. Můžete monitorovat pomocí latence **směrování: zpráva latence pro zprávy/události** nebo **d2c.endpoints.latency.builtIn.events** metriky služby IoT Hub. Vytvoření nebo odstranění žádné trasy po první z nich nemá žádný vliv na celkovou latenci.

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

Poskytuje službě IoT Hub, několik směrování a koncový bod související metriky, které vám poskytnou přehled o stavu centra a odeslaných zpráv. Můžete kombinovat informace z několika metrik a identifikovat hlavní příčinu problémů. Například použijte metriku **směrování: počet ztracených zpráv telemetrie** nebo **d2c.telemetry.egress.dropped** identifikovat počet zpráv, které byly při neodpovídají dotazy na žádné trasy a náhradní trasa byla zakázána. [Metriky služby IoT Hub](iot-hub-metrics.md) uvádí všechny metriky, které jsou ve výchozím nastavení povolená pro službu IoT Hub.

Můžete použít rozhraní REST API [získat stav koncového bodu](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) zobrazíte [stav](iot-hub-devguide-endpoints.md#custom-endpoints) koncových bodů. Doporučujeme použít [metriky služby IoT Hub](iot-hub-metrics.md) související s latencí směrování zprávy k identifikaci a ladit chyby, pokud stav koncového bodu je neaktivní nebo není v pořádku. Například pro typ koncového bodu služby Event Hubs, můžete monitorovat **d2c.endpoints.latency.eventHubs**. Stav není v pořádku koncového bodu bude aktualizován v pořádku, zřízeno konzistentní stav stavu služby IoT Hub.

Použití **trasy** diagnostické protokoly ve službě Azure Monitor [nastavení diagnostiky](../iot-hub/iot-hub-monitor-resource-health.md), můžete sledují chyby, ke kterým dochází při vyhodnocování směrování dotazů a koncový bod stavu vnímanou ve službě IoT Hub, například Pokud koncový bod je neaktivní. Tyto diagnostické protokoly je odeslat protokoly Azure monitoru, Event Hubs nebo Azure Storage pro vlastní zpracování.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak vytvořit trasy zpráv, najdete v článku [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras](tutorial-routing.md).

* [Postup odesílání zpráv typu zařízení cloud](quickstart-send-telemetry-node.md)

* Informace o sadách SDK můžete použít k odesílání zpráv typu zařízení cloud, najdete v části [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

---
title: Principy směrování zpráv v Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – jak používat směrování zpráv k posílání zpráv ze zařízení do cloudu. Obsahuje informace o posílání telemetrie i dat mimo telemetrie.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d2d4d39cc7b330794094745851856365ef54b42f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828195"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv vám umožní posílat zprávy ze svých zařízení do cloudových služeb automatizovaným, škálovatelným a spolehlivým způsobem. Směrování zpráv lze použít pro: 

* **Posílání zpráv telemetrie zařízení a také** událostí, událostí životního cyklu zařízení a událostech s dvojitou změnou v zařízení na integrovaný koncový bod a vlastní koncové body. Přečtěte si informace o [koncových bodech směrování](#routing-endpoints).

* **Filtrování dat před jejich směrováním do různých koncových bodů** použitím bohatých dotazů. Směrování zpráv vám umožní dotazovat se na vlastnosti zprávy a text zprávy a také na vlastní značky zařízení a vlastnosti, které jsou v zařízení. Přečtěte si další informace o používání [dotazů v směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

Aby směrování zpráv fungovalo, IoT Hub potřebuje přístup pro zápis k těmto koncovým bodům služby. Pokud vaše koncové body nakonfigurujete prostřednictvím Azure Portal, přidají se potřebná oprávnění. Ujistěte se, že jste nakonfigurovali služby pro podporu očekávané propustnosti. Při první konfiguraci řešení IoT budete možná muset monitorovat další koncové body a provádět veškeré nezbytné úpravy pro skutečné zatížení.

IoT Hub definuje [společný formát](iot-hub-devguide-messages-construct.md) pro všechny zprávy typu zařízení-Cloud pro interoperabilitu napříč protokoly. Pokud zpráva odpovídá více trasám, které ukazují na stejný koncový bod, IoT Hub doručuje zprávu do tohoto koncového bodu pouze jednou. Proto nemusíte konfigurovat odstraňování duplicitních dat v Service Bus fronty nebo tématu. V dělených frontách garantuje spřažení oddílů řazení zpráv. Pomocí tohoto kurzu se naučíte [konfigurovat směrování zpráv](tutorial-routing.md).

## <a name="routing-endpoints"></a>Koncové body směrování

Služba IoT Hub má výchozí integrovaný integrovaný koncový bod (**zprávy nebo události**), který je kompatibilní s Event Hubs. Můžete vytvořit [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) , na které budou směrovat zprávy propojením dalších služeb ve vašem předplatném s IoT Hub. IoT Hub aktuálně podporuje následující služby jako vlastní koncové body:

### <a name="built-in-endpoint"></a>Vestavěný koncový bod

Pomocí standardní [Event Hubs integrace a sad SDK](iot-hub-devguide-messages-read-builtin.md) můžete přijímat zprávy ze zařízení do cloudu z integrovaného koncového bodu (**zprávy/události**). Po vytvoření trasy se data přestanou předávat do integrovaného koncového bodu, pokud se do tohoto koncového bodu nevytvoří trasa.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub podporuje zápis dat do Azure Blob Storage ve formátu [Apache Avro](https://avro.apache.org/) a také ve formátu JSON. Možnost kódovat formát JSON je obecně dostupná ve všech oblastech, ve kterých je IoT Hub k dispozici. Výchozí hodnota je AVRO. Formát kódování lze nastavit pouze v případě, že je nakonfigurován koncový bod úložiště objektů BLOB. Formát nelze upravit pro existující koncový bod. Při použití kódování JSON musíte nastavit contentType na JSON a contentEncoding na UTF-8 ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv. Pokud tato akce není nastavena, IoT Hub zapíše zprávy ve formátu kódování Base 64. Formát kódování můžete vybrat pomocí IoT Hub vytvořit nebo aktualizovat REST API, konkrétně [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)nebo [Azure PowerShellu](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Následující diagram ukazuje, jak vybrat formát kódování v Azure Portal.

![Kódování koncového bodu služby Blob Storage](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub také podporuje směrování zpráv ADLS Gen2 účty, což jsou hierarchické účty úložiště s povoleným [oborem názvů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)postavené na úložišti objektů BLOB. Tato funkce je ve verzi Public Preview a dostupná pro nové účty ADLS Gen2 v Západní USA 2 a Středozápadní USA. Tuto možnost brzy zavedeme do všech oblastí cloudu.

IoT Hub vytvoří dávky zprávy a zapisuje data do objektu blob, kdykoli dávka dosáhne určité velikosti nebo po uplynutí určité doby. IoT Hub výchozí nastavení pro následující konvence pojmenovávání souborů:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolnou konvenci pojmenovávání souborů, ale musíte použít všechny uvedené tokeny. Pokud nejsou k dispozici žádná data pro zápis, IoT Hub bude zapisovat do prázdného objektu BLOB.

Při směrování do úložiště objektů BLOB doporučujeme zařadit objekty BLOB a potom je v nich vyřadit, aby se zajistilo, že všechny kontejnery budou čteny bez jakýchkoli předpokladů oddílu. Rozsah oddílu se může během [převzetí služeb při selhání iniciované společností Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) nebo [ruční převzetí služeb při](iot-hub-ha-dr.md#manual-failover-preview)selhání IoT Hub změnit. K vytvoření výčtu seznamů objektů blob můžete použít [rozhraní list API objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/list-blobs) . Podívejte se prosím na následující ukázku jako na doprovodné materiály.

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

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus fronty a Service Bus témata

Service Bus front a témat používaných jako IoT Hub koncových bodů nesmí mít povoleny **relace** nebo je povolena **Detekce duplicitních** dat. Pokud je některá z těchto možností povolená, koncový bod se v Azure Portal jeví jako nedosažitelný.

### <a name="event-hubs"></a>Event Hubs

Kromě předdefinovaného koncového bodu, který je kompatibilní s Event Hubs, můžete také směrovat data na vlastní koncové body typu Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Čtení dat, která byla směrována

Pomocí tohoto [kurzu](tutorial-routing.md)můžete nakonfigurovat trasu.

Pomocí následujících kurzů se naučíte číst zprávy z koncového bodu.

* Čtení z [integrovaného koncového bodu](quickstart-send-telemetry-node.md)

* Čtení ze služby [BLOB Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Čtení z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Čtení z [front Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Čtení z [Service Bus témata](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Záložní trasa

Záložní trasa pošle všechny zprávy, které nesplňují podmínky pro dotazování na všechny existující trasy, na integrované Event Hubs (**zprávy/události**), které jsou kompatibilní s [Event Hubs](/azure/event-hubs/). Pokud je zapnuté směrování zpráv, můžete povolit funkci záložních tras. Po vytvoření trasy se data přestanou předávat do integrovaného koncového bodu, pokud se do tohoto koncového bodu nevytvoří trasa. Pokud neexistují žádné trasy k integrovanému koncovému bodu a je povolena záložní trasa, budou do integrovaného koncového bodu odesílány pouze zprávy, které neodpovídají podmínkám dotazu na trasách. I když se odstraní všechny existující trasy, musí být povolená záložní trasa pro příjem všech dat na integrovaném koncovém bodu.

Záložní cestu můžete povolit nebo zakázat v okně Azure Portal-> směrování zpráv. Můžete také použít Azure Resource Manager pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) k použití vlastního koncového bodu pro záložní směrování.

## <a name="non-telemetry-events"></a>Události bez telemetrie

Kromě telemetrie zařízení umožňuje směrování zpráv taky posílat události události změny zařízení a životního cyklu zařízení. Pokud je například vytvořená trasa se zdrojem dat nastaveným na **události změny zařízení s dvojitou změnou**, IoT Hub odesílá zprávy na koncový bod, který obsahuje změnu v zařízení. Podobně platí, že pokud se vytvoří trasa se zdrojem dat nastaveným na **události životního cyklu zařízení**, IoT Hub odešle zprávu s oznámením, jestli se zařízení odstranilo nebo vytvořilo. 

[IoT Hub se taky integruje s Azure Event Grid](iot-hub-event-grid.md) k publikování událostí zařízení, aby podporovaly integrace v reálném čase a automatizaci pracovních postupů založených na těchto událostech. Podívejte se na klíčové [rozdíly mezi směrováním a Event Gridm zpráv a](iot-hub-event-grid-routing-comparison.md) Naučte se, co nejlépe vyhovuje vašemu scénáři.

## <a name="testing-routes"></a>Testování tras

Když vytvoříte novou trasu nebo upravíte existující trasu, měli byste otestovat dotaz trasy pomocí ukázkové zprávy. Můžete testovat jednotlivé trasy nebo testovat všechny trasy najednou a v průběhu testu nejsou směrovány žádné zprávy do koncových bodů. Pro testování je možné použít Azure Portal, Azure Resource Manager, Azure PowerShell a rozhraní příkazového řádku Azure CLI. Výsledky vám pomůžou zjistit, jestli se ukázková zpráva shodovala s dotazem, zpráva se neshodovala s dotazem, nebo nešlo spustit test, protože ukázková zpráva nebo syntaxe dotazu nejsou správné. Další informace najdete v tématu [testovací trasa](/rest/api/iothub/iothubresource/testroute) a [testování všech tras](/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latence

Když směrujete zprávy o telemetrie typu zařízení-Cloud pomocí integrovaných koncových bodů, dojde po vytvoření první trasy k mírnému nárůstu koncové latence.

Ve většině případů je průměrné zvýšení latence menší než 500 ms. Latenci můžete monitorovat pomocí **směrování: latence zpráv pro zprávy/události** nebo **D2C. Endpoints. latence. builtIn. events** IoT Hub metrika. Vytvoření nebo odstranění jakékoli trasy po prvním z nich nemá vliv na koncovou latenci.

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

IoT Hub poskytuje několik metrik vztahujících se ke směrování a koncovým bodům, které vám poskytnou přehled o stavu vašeho centra a zpráv odesílaných. Můžete zkombinovat informace z několika metrik a identifikovat tak hlavní příčinu problémů. Například použijte směrování metriky **: zprávy telemetrie vyřazené** nebo **D2C.** Prometric. disabled k určení počtu zpráv, které byly zahozeny, když neodpovídaly dotazům na některé z tras a záložní trasy byly zakázány. [IoT Hub metriky](iot-hub-metrics.md) uvádí všechny metriky, které jsou ve výchozím nastavení povolené pro vaši IoT Hub.

Ke zjištění [stavu](iot-hub-devguide-endpoints.md#custom-endpoints) koncových bodů můžete použít REST API [získat](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) stav koncových bodů. Doporučujeme použít [IoT Hub metriky](iot-hub-metrics.md) týkající se latence zprávy směrování k identifikaci a ladění chyb v případě, že stav koncového bodu je neaktivní nebo není v pořádku. Například pro typ koncového bodu Event Hubs můžete monitorovat **D2C. Endpoints. latence. eventHubs**. Stav koncového bodu, který není v pořádku, bude aktualizován na stav v pořádku, když IoT Hub navázala trvalý stav stavu.

Pomocí diagnostického diagnostického protokolu v Azure Monitor [nastavení diagnostiky](../iot-hub/iot-hub-monitor-resource-health.md)můžete sledovat chyby, ke kterým dojde během hodnocení směrovacího dotazu a stavu koncového bodu, jak je znázorněno IoT Hub, například když je koncový bod neaktivní. Tyto diagnostické protokoly lze odeslat do Azure Monitor protokolů, Event Hubs nebo Azure Storage pro vlastní zpracování.

## <a name="next-steps"></a>Další postup

* Informace o tom, jak vytvářet směrování zpráv, najdete v tématu [proces IoT Hub zprávy ze zařízení do cloudu pomocí tras](tutorial-routing.md).

* [Jak odesílat zprávy ze zařízení do cloudu](quickstart-send-telemetry-node.md)

* Informace o sadách SDK, které můžete použít k posílání zpráv ze zařízení do cloudu, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

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
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: d2fe8f32ec854e1e6db644a039e6a249cfbddcaa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012883"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Použití směrování zpráv IoT Hub k posílání zpráv ze zařízení do cloudu do různých koncových bodů

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv vám umožní posílat zprávy ze svých zařízení do cloudových služeb automatizovaným, škálovatelným a spolehlivým způsobem. Směrování zpráv lze použít pro: 

* **Posílání zpráv telemetrie zařízení a také událostí** , události životního cyklu zařízení, události změny zařízení s dvojitou změnou a digitální události změny pro integrovaný koncový bod a vlastní koncové body. Přečtěte si informace o [koncových bodech směrování](#routing-endpoints). Další informace o událostech odeslaných ze zařízení IoT technologie Plug and Play najdete v tématu [Principy IoT technologie Plug and Play digitálních vláken](../iot-pnp/concepts-digital-twin.md).

* **Filtrování dat před jejich směrováním do různých koncových bodů** použitím bohatých dotazů. Směrování zpráv vám umožní dotazovat se na vlastnosti zprávy a text zprávy a také na vlastní značky zařízení a vlastnosti, které jsou v zařízení. Přečtěte si další informace o používání [dotazů v směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

Aby směrování zpráv fungovalo, IoT Hub potřebuje přístup pro zápis k těmto koncovým bodům služby. Pokud vaše koncové body nakonfigurujete prostřednictvím Azure Portal, přidají se potřebná oprávnění. Ujistěte se, že jste nakonfigurovali služby pro podporu očekávané propustnosti. Pokud například používáte Event Hubs jako vlastní koncový bod, musíte nakonfigurovat **jednotky propustnosti** pro toto centrum událostí, aby bylo možné zpracovávat příchozí a příchozí události, které plánujete odeslat prostřednictvím IoT Hub směrování zpráv. Podobně platí, že při použití Service Bus fronty jako koncového bodu je nutné nakonfigurovat **maximální velikost** , aby fronta mohla uchovávat všechna data, která se mají použít, dokud je nekoncoví uživatelé nevrátí. Při první konfiguraci řešení IoT budete možná muset monitorovat další koncové body a provádět veškeré nezbytné úpravy pro skutečné zatížení.

IoT Hub definuje [společný formát](iot-hub-devguide-messages-construct.md) pro všechny zprávy typu zařízení-Cloud pro interoperabilitu napříč protokoly. Pokud zpráva odpovídá více trasám, které ukazují na stejný koncový bod, IoT Hub doručuje zprávu do tohoto koncového bodu pouze jednou. Proto nemusíte konfigurovat odstraňování duplicitních dat v Service Bus fronty nebo tématu. V dělených frontách garantuje spřažení oddílů řazení zpráv. Pomocí tohoto kurzu se naučíte [konfigurovat směrování zpráv](tutorial-routing.md).

## <a name="routing-endpoints"></a>Koncové body směrování

Služba IoT Hub má výchozí integrovaný integrovaný koncový bod (**zprávy nebo události**), který je kompatibilní s Event Hubs. Můžete vytvořit [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) , na které budou směrovat zprávy propojením dalších služeb ve vašem předplatném s IoT Hub. 

Každá zpráva je směrována do všech koncových bodů, jejichž směrovací dotazy odpovídají. Jinými slovy, zpráva může být směrována do více koncových bodů.

Pokud má váš vlastní koncový bod konfigurace brány firewall, zvažte použití výjimky důvěryhodné první strany společnosti Microsoft, aby váš IoT Hub měl přístup ke konkrétním koncovým [Azure Storageám](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing)a [službám Azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) a [Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Tato možnost je k dispozici ve vybraných oblastech pro centra IoT s [identitou spravované služby](./virtual-network-support.md).

IoT Hub aktuálně podporuje následující koncové body:

 - Vestavěný koncový bod
 - Azure Storage
 - Service Bus fronty a Service Bus témata
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Vestavěný koncový bod jako koncový bod směrování

Pomocí standardní [Event Hubs integrace a sad SDK](iot-hub-devguide-messages-read-builtin.md) můžete přijímat zprávy ze zařízení do cloudu z integrovaného koncového bodu (**zprávy/události**). Po vytvoření trasy se data přestanou předávat do integrovaného koncového bodu, pokud se do tohoto koncového bodu nevytvoří trasa.

## <a name="azure-storage-as-a-routing-endpoint"></a>Azure Storage jako koncový bod směrování

K dispozici jsou dvě služby úložiště, IoT Hub můžou směrovat zprávy na účty služeb [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (adls Gen2). Účty Azure Data Lake Storage jsou hierarchické účty úložiště s povoleným [oborem názvů](../storage/blobs/data-lake-storage-namespace.md)postavené nad úložištěm objektů BLOB. Oba používají objekty blob pro své úložiště.

IoT Hub podporuje zápis dat do Azure Storage ve formátu [Apache Avro](https://avro.apache.org/) a také ve formátu JSON. Výchozí hodnota je AVRO. Při použití kódování JSON musíte nastavit contentType na **Application/JSON** a ContentEncoding na **UTF-8** ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv. U obou z těchto hodnot se nerozlišují malá a velká písmena. Pokud není kódování obsahu nastaveno, IoT Hub zapíše zprávy ve formátu kódování Base 64.

Formát kódování lze nastavit pouze v případě, že je nakonfigurován koncový bod úložiště objektů BLOB; nedá se upravovat pro existující koncový bod. Chcete-li přepnout formáty kódování pro existující koncový bod, bude nutné odstranit a znovu vytvořit vlastní koncový bod s požadovaným formátem. Jednou z užitečných strategií může být vytvoření nového vlastního koncového bodu s požadovaným formátem kódování a přidání paralelní trasy do tohoto koncového bodu. Tímto způsobem můžete ověřit data před odstraněním existujícího koncového bodu.

Formát kódování můžete vybrat pomocí IoT Hub vytvořit nebo aktualizovat REST API, konkrétně [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](/cli/azure/iot/hub/routing-endpoint)nebo [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). Následující obrázek ukazuje, jak vybrat formát kódování v Azure Portal.

![Kódování koncového bodu služby Blob Storage](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub vytvoří dávky zprávy a zapisuje data do úložiště vždy, když dávka dosáhne určité velikosti nebo po uplynutí určité doby. IoT Hub výchozí nastavení pro následující konvence pojmenovávání souborů:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolnou konvenci pojmenovávání souborů, ale musíte použít všechny uvedené tokeny. Pokud nejsou k dispozici žádná data pro zápis, IoT Hub bude zapisovat do prázdného objektu BLOB.

Doporučujeme vypsat objekty blob nebo soubory a potom je v nich vymezit, aby se zajistilo, že všechny objekty blob nebo soubory budou čteny bez nutnosti provádět žádné předpoklady oddílu. Rozsah oddílu se může během [převzetí služeb při selhání iniciované společností Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover) nebo [ruční převzetí služeb při](iot-hub-ha-dr.md#manual-failover)selhání IoT Hub změnit. K vytvoření výčtu seznamů objektů BLOB nebo [seznamu adls Gen2 rozhraní API](/rest/api/storageservices/datalakestoragegen2/path/list) pro seznam souborů můžete použít [rozhraní list API blobů](/rest/api/storageservices/list-blobs) . Podívejte se prosím na následující ukázku jako na doprovodné materiály.

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

Pokud chcete vytvořit účet úložiště kompatibilního s Azure Data Lake Gen2, vytvořte nový účet úložiště v2 a v poli *hierarchický obor názvů* na kartě **Upřesnit** vyberte *povoleno* , jak je znázorněno na následujícím obrázku:

![Vybrat Azure datum Lake Gen2 Storage](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Service Bus fronty a Service Bus témata jako koncový bod směrování

Service Bus front a témat používaných jako IoT Hub koncových bodů nesmí mít povoleny **relace** nebo je povolena **Detekce duplicitních** dat. Pokud je některá z těchto možností povolená, koncový bod se v Azure Portal jeví jako **nedosažitelný** .

## <a name="event-hubs-as-a-routing-endpoint"></a>Event Hubs jako koncový bod směrování

Kromě předdefinovaného koncového bodu, který je kompatibilní s Event Hubs, můžete také směrovat data na vlastní koncové body typu Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Čtení dat, která byla směrována

Pomocí tohoto [kurzu](tutorial-routing.md)můžete nakonfigurovat trasu.

Pomocí následujících kurzů se naučíte číst zprávy z koncového bodu.

* Čtení z [integrovaného koncového bodu](quickstart-send-telemetry-node.md)

* Čtení ze služby [BLOB Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Čtení z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Čtení z [front Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Čtení z [Service Bus témata](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Záložní trasa

Záložní trasa pošle všechny zprávy, které nesplňují podmínky pro dotazování na všechny existující trasy, na integrované Event Hubs (**zprávy/události**), které jsou kompatibilní s [Event Hubs](../event-hubs/index.yml). Pokud je zapnuté směrování zpráv, můžete povolit funkci záložních tras. Po vytvoření trasy se data přestanou předávat do integrovaného koncového bodu, pokud se do tohoto koncového bodu nevytvoří trasa. Pokud neexistují žádné trasy k integrovanému koncovému bodu a je povolena záložní trasa, budou do integrovaného koncového bodu odesílány pouze zprávy, které neodpovídají podmínkám dotazu na trasách. I když se odstraní všechny existující trasy, musí být povolená záložní trasa pro příjem všech dat na integrovaném koncovém bodu.

Záložní cestu můžete povolit nebo zakázat v okně Azure Portal->směrování zpráv. Můžete také použít Azure Resource Manager pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) k použití vlastního koncového bodu pro záložní směrování.

## <a name="non-telemetry-events"></a>Události bez telemetrie

Kromě telemetrie zařízení umožňuje směrování zpráv také odesílat události s náhodnými změnami zařízení, události životního cyklu zařízení a digitální události změny. Pokud je například vytvořená trasa se zdrojem dat nastaveným na **události změny zařízení s dvojitou změnou**, IoT Hub odesílá zprávy na koncový bod, který obsahuje změnu v zařízení. Podobně platí, že pokud se vytvoří trasa se zdrojem dat nastaveným na **události životního cyklu zařízení**, IoT Hub odešle zprávu s oznámením, jestli se zařízení odstranilo nebo vytvořilo. V rámci [technologie Plug and Play Azure IoT](../iot-pnp/overview-iot-plug-and-play.md)může vývojář vytvořit trasy se zdrojem dat nastavenými na **digitální události změny** , a IoT Hub posílá zprávy vždy, když se nastaví nebo změní vlastnost s digitálním nastavením, digitální zdvojení se nahradí nebo když se událost změny stane pro podkladové vlákna zařízení.

[IoT Hub se taky integruje s Azure Event Grid](iot-hub-event-grid.md) k publikování událostí zařízení, aby podporovaly integrace v reálném čase a automatizaci pracovních postupů založených na těchto událostech. Podívejte se na klíčové [rozdíly mezi směrováním a Event Gridm zpráv a](iot-hub-event-grid-routing-comparison.md) Naučte se, co nejlépe vyhovuje vašemu scénáři.

## <a name="testing-routes"></a>Testování tras

Když vytvoříte novou trasu nebo upravíte existující trasu, měli byste otestovat dotaz trasy pomocí ukázkové zprávy. Můžete testovat jednotlivé trasy nebo testovat všechny trasy najednou a v průběhu testu nejsou směrovány žádné zprávy do koncových bodů. Pro testování je možné použít Azure Portal, Azure Resource Manager, Azure PowerShell a rozhraní příkazového řádku Azure CLI. Výsledky vám pomůžou zjistit, jestli se ukázková zpráva shodovala s dotazem, zpráva se neshodovala s dotazem, nebo nešlo spustit test, protože ukázková zpráva nebo syntaxe dotazu nejsou správné. Další informace najdete v tématu [testovací trasa](/rest/api/iothub/iothubresource/testroute) a [testování všech tras](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Řazení záruk alespoň po doručení

Služba směrování zpráv IoT Hub garantuje pořadí a alespoň jedno doručení zpráv do koncových bodů. To znamená, že může dojít k duplicitním zprávám a je možné znovu přenést řadu zpráv, které budou respektovat původní řazení zpráv. Pokud je například původní pořadí zpráv [1, 2, 3, 4], může se zobrazit sekvence zpráv, například [1, 2, 1, 2, 3, 1, 2, 3, 4]. Záruka objednávání znamená, že pokud se vám někdy zobrazí zpráva [1], vždy za ní následuje [2, 3, 4].

Pro zpracování duplicit zpráv doporučujeme, abyste ve vlastnostech aplikace ve zprávě v bodě původu vyražení jedinečný identifikátor, což je obvykle zařízení nebo modul. Služba, která zprávy spotřebovává, může zpracovávat duplicitní zprávy pomocí tohoto identifikátoru.

## <a name="latency"></a>Latence

Když směrujete zprávy o telemetrie typu zařízení-Cloud pomocí integrovaných koncových bodů, dojde po vytvoření první trasy k mírnému nárůstu koncové latence.

Ve většině případů je průměrné zvýšení latence menší než 500 ms. Latenci můžete monitorovat pomocí **směrování: latence zpráv pro zprávy/události** nebo **D2C. Endpoints. latence. builtIn. events** IoT Hub metrika. Vytvoření nebo odstranění jakékoli trasy po prvním z nich nemá vliv na koncovou latenci.

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení potíží

IoT Hub poskytuje několik metrik vztahujících se ke směrování a koncovým bodům, které vám poskytnou přehled o stavu vašeho centra a zpráv odesílaných. Seznam všech metrik IoT Hub rozdělených podle funkční kategorie najdete v tématu [metriky v referenčních informacích k datům monitorování](monitor-iot-hub-reference.md#metrics). Můžete sledovat chyby, ke kterým dochází během hodnocení směrovacího dotazu a stavu koncového bodu, jak je znázorněno IoT Hub s [kategorií **Směrování** v protokolech prostředků IoT Hub](monitor-iot-hub-reference.md#routes). Další informace o používání metrik a protokolů prostředků s IoT Hub najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md).

Ke zjištění [stavu](iot-hub-devguide-endpoints.md#custom-endpoints) koncových bodů můžete použít REST API [získat stav koncových](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) bodů.

Další podrobnosti a podporu pro směrování potíží najdete v [Průvodci odstraňováním potíží pro směrování](troubleshoot-message-routing.md) .

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvářet směrování zpráv, najdete v tématu [proces IoT Hub zprávy ze zařízení do cloudu pomocí tras](tutorial-routing.md).

* [Jak odesílat zprávy ze zařízení do cloudu](quickstart-send-telemetry-node.md)

* Informace o sadách SDK, které můžete použít k posílání zpráv ze zařízení do cloudu, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).
---
title: Principy směrování zpráv služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – jak pomocí směrování zpráv odesílat zprávy mezi zařízeními a cloudy. Obsahuje informace o odesílání telemetrických i netelemetrických dat.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370453"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Směrování zpráv služby IoT Hub slouží k odesílání zpráv mezi zařízeními a cloudy do různých koncových bodů

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Směrování zpráv umožňuje odesílat zprávy ze zařízení do cloudových služeb automatizovaným, škálovatelným a spolehlivým způsobem. Směrování zpráv lze použít pro: 

* **Odesílání telemetrických zpráv zařízení, stejně jako události** konkrétně události životního cyklu zařízení a události dvojité ho diodzařízení změnit na vestavěný koncový bod a vlastní koncové body. Informace o [koncových bodech směrování](#routing-endpoints).

* **Filtrování dat před směrováním do různých koncových bodů** použitím rozšířených dotazů. Směrování zpráv umožňuje dotaz na vlastnosti zprávy a text zprávy, stejně jako značky dvojčete zařízení a vlastnosti dvojčete zařízení. Další informace o používání [dotazů ve směrování zpráv](iot-hub-devguide-routing-query-syntax.md).

Služba IoT Hub potřebuje přístup pro zápis do těchto koncových bodů služby, aby směrování zpráv fungovalo. Pokud nakonfigurujete koncové body prostřednictvím portálu Azure, budou za vás přidána potřebná oprávnění. Ujistěte se, že nakonfigurujete služby tak, aby podporovaly očekávanou propustnost. Například pokud používáte Event Hubs jako vlastní koncový bod, musíte nakonfigurovat **jednotky propustnosti** pro toto centrum událostí tak, aby mohl zpracovávat příchozí přenos událostí, které chcete odeslat prostřednictvím směrování zpráv služby IoT Hub. Podobně při použití fronty služby Service Bus jako koncového bodu je nutné nakonfigurovat **maximální velikost,** aby fronta mohla obsahovat všechna příchozí data, dokud ji spotřebitelé neodchozí. Při první konfiguraci řešení IoT, budete muset sledovat další koncové body a provést všechny nezbytné úpravy pro skutečné zatížení.

Služba IoT Hub definuje [společný formát](iot-hub-devguide-messages-construct.md) pro všechny zasílání zpráv mezi zařízeními a cloudy pro interoperabilitu napříč protokoly. Pokud zpráva odpovídá více tras, které odkazují na stejný koncový bod, služba IoT Hub doručuje zprávu do tohoto koncového bodu pouze jednou. Proto není nutné konfigurovat odstranění duplicit ve frontě service bus nebo tématu. V rozdělených frontách zaručuje spřažení oddílů řazení zpráv. V tomto kurzu se dozvíte, jak [konfigurovat směrování zpráv](tutorial-routing.md).

## <a name="routing-endpoints"></a>Koncové body směrování

Centrum IoT má výchozí vestavěný koncový bod **(zprávy nebo události),** který je kompatibilní s event huby. Můžete vytvořit [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) pro směrování zpráv pomocí propojení jiných služeb ve vašem předplatném služby IoT Hub. 

Každá zpráva je směrována do všech koncových bodů, jejichž směrovací dotazy odpovídá. Jinými slovy, zpráva může být směrována do více koncových bodů.

Služba IoT Hub aktuálně podporuje následující služby jako vlastní koncové body:

### <a name="built-in-endpoint"></a>Vestavěný koncový bod

Standardní integrace [centra událostí a sady SDK](iot-hub-devguide-messages-read-builtin.md) můžete použít k přijímání zpráv mezi zařízeními a cloudy z integrovaného koncového bodu **(zprávy/události).** Po vytvoření Route data zastaví toku do integrovaného koncového bodu, pokud route je vytvořen do tohoto koncového bodu.

### <a name="azure-storage"></a>Azure Storage

Existují dvě služby úložiště IoT Hub můžete směrovat zprávy -- [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) účty. Účty Azure Data Lake Storage jsou účty úložiště s [povolenou hierarchickým oborem,](../storage/blobs/data-lake-storage-namespace.md)které jsou postavené na úložišti objektů blob. Oba tyto použití objektů BLOB pro jejich úložiště.

IoT Hub podporuje zápis dat do Azure Storage ve formátu [Apache Avro](https://avro.apache.org/) i ve formátu JSON. Výchozí hodnota je AVRO. Formát kódování lze nastavit pouze v případě, že je nakonfigurován koncový bod úložiště objektů blob. Formát nelze upravit pro existující koncový bod. Při použití kódování JSON, musíte nastavit contentType na **application/json** a contentEncoding na **UTF-8** ve [vlastnostech systému](iot-hub-devguide-routing-query-syntax.md#system-properties)zpráv . Obě tyto hodnoty jsou malá a velká písmena. Pokud kódování obsahu není nastaveno, ioT Hub zapíše zprávy v základním formátu 64 kódované. Formát kódování můžete vybrat pomocí rozhraní API pro vytvoření nebo aktualizaci rest služby IoT Hub, konkrétně [rozhraní RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), portálu Azure, [rozhraní API Azure](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)nebo Azure [PowerShellu](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). Následující diagram ukazuje, jak vybrat formát kódování na webu Azure Portal.

![Kódování koncového bodu úložiště objektů blob](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

Služba IoT Hub dávky zprávy a zapisuje data do úložiště vždy, když dávka dosáhne určité velikosti nebo určité množství času uplynulo. Centrum IoT výchozí nastavení na následující konvence pojmenování souborů: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Můžete použít libovolnou konvenci pojmenování souborů, ale musíte použít všechny uvedené tokeny. IoT Hub zapíše do prázdného objektu blob, pokud nejsou k dispozici žádná data k zápisu.

Doporučujeme vyjmenovávat objekty BLOB nebo soubory a pak je iterace, aby bylo zajištěno, že všechny objekty BLOB nebo soubory jsou čteny bez jakýchkoli předpokladů oddílu. Rozsah oddílů může potenciálně změnit během microsoft [iniciované převzetí služeb při selhání](iot-hub-ha-dr.md#microsoft-initiated-failover) nebo ioT Hub [ruční převzetí služeb při selhání](iot-hub-ha-dr.md#manual-failover). Pomocí rozhraní API objektů [BLOB seznamu](https://docs.microsoft.com/rest/api/storageservices/list-blobs) můžete vytvořit výčet seznamu objektů BLOB nebo [seznamu rozhraní API ADLS Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) pro seznam souborů. Viz následující ukázka jako vodítko.

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

> [!NOTE]
> Pokud váš účet úložiště má konfigurace brány firewall, které omezují připojení služby IoT Hub, zvažte použití [výjimky microsoftu důvěryhodné první strany](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (k dispozici ve vybraných oblastech pro centra IoT hubs identitou spravované služby).

Chcete-li vytvořit účet úložiště kompatibilní s Azure Data Lake Gen2, vytvořte nový účet úložiště V2 a vyberte *povoleno* v poli *Hierarchický obor názvů* na kartě **Upřesnit,** jak je znázorněno na následujícím obrázku:

![Vyberte úložiště Azure Date Lake Gen2](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Fronty sběrnice a témata služby Service Bus

Fronty a témata služby Service Bus používaná jako koncové body služby IoT Hub nesmí mít povolené **relace** nebo **vyhledávání duplicit.** Pokud jsou povoleny některé z těchto možností, koncový bod se zobrazí jako **nedostupný** na webu Azure Portal.

> [!NOTE]
> Pokud má váš prostředek služby Service Bus konfigurace brány firewall, které omezují připojení služby IoT Hub, zvažte použití [výjimky microsoftu důvěryhodné první strany](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) (k dispozici ve vybraných oblastech pro centra IoT s identitou spravované služby).


### <a name="event-hubs"></a>Event Hubs

Kromě koncového bodu kompatibilního s integrovanými centry událostí můžete také směrovat data do vlastních koncových bodů typu Event Hubs. 

> [!NOTE]
> Pokud váš prostředek centra událostí má konfigurace brány firewall, které omezují připojení služby IoT Hub, zvažte použití [výjimky microsoftu důvěryhodné první strany](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) (k dispozici ve vybraných oblastech pro centra IoT hubs identitou spravované služby).


## <a name="reading-data-that-has-been-routed"></a>Čtení směrovaných dat

Trasu můžete nakonfigurovat podle tohoto [kurzu](tutorial-routing.md).

Pomocí následujících kurzů se dozvíte, jak číst zprávy z koncového bodu.

* Čtení z [integrovaného koncového bodu](quickstart-send-telemetry-node.md)

* Čtení z [úložiště objektů Blob](../storage/blobs/storage-blob-event-quickstart.md)

* Čtení z [centra událostí](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Čtení z [front sběrnice service bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Čtení z [témat služby Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Záložní trasa

Záložní trasa odešle všechny zprávy, které nesplňují podmínky dotazu na některé z existujících tras do vestavěná centra událostí **(zprávy nebo události**), který je kompatibilní s [Event Hubs](/azure/event-hubs/). Pokud je směrování zpráv zapnuto, můžete povolit možnost záložní trasy. Po vytvoření trasy data zastaví toku do integrovaného koncového bodu, pokud trasa je vytvořen do tohoto koncového bodu. Pokud neexistují žádné trasy k předdefinovanému koncovému bodu a je povolena záložní trasa, budou do předdefinovaného koncového bodu odeslány pouze zprávy, které neodpovídají žádným podmínkám dotazu na trasách. Také pokud jsou odstraněny všechny existující trasy, záložní trasa musí být povolena pro příjem všech dat v integrovaném koncovém bodu.

Záložní trasu můžete povolit nebo zakázat v okně Směrování zpráv >portálu Azure. Správce prostředků Azure pro [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) můžete také použít vlastní koncový bod pro záložní trasu.

## <a name="non-telemetry-events"></a>Netelemetrické události

Kromě telemetrie zařízení směrování zpráv také umožňuje odesílání událostí změny dvojčete zařízení, události životního cyklu zařízení a události změny digitálních dvojčat (ve verzi Public Preview). Například pokud je vytvořena trasa se zdrojem dat nastavena na **události změny dvojčete zařízení**, IoT Hub odesílá zprávy do koncového bodu, které obsahují změnu v dvojčete zařízení. Podobně pokud je trasa vytvořena se zdrojem dat nastaveným na **události životního cyklu zařízení**, služba IoT Hub odešle zprávu s oznámením, zda bylo zařízení odstraněno nebo vytvořeno. Nakonec jako součást [veřejné verze IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)může vývojář vytvářet trasy se zdrojem dat nastaveným na **události změny digitálních dvojčat** a IoT Hub odesílá zprávy při každém nastavení nebo změně [vlastnosti](../iot-pnp/iot-plug-and-play-glossary.md) digitálního dvojčete, nahrazení [digitálního dvojčete](../iot-pnp/iot-plug-and-play-glossary.md) nebo když dojde k události změny pro základní dvojče zařízení.

[IoT Hub se také integruje s Azure Event Grid](iot-hub-event-grid.md) k publikování událostí zařízení pro podporu integrace v reálném čase a automatizace pracovních postupů na základě těchto událostí. Podívejte se na klíčové [rozdíly mezi směrováním zpráv a událostí mřížky](iot-hub-event-grid-routing-comparison.md) se dozvíte, který funguje nejlépe pro váš scénář.

## <a name="testing-routes"></a>Testovací trasy

Když vytvoříte novou trasu nebo upravíte existující trasu, měli byste otestovat dotaz trasy pomocí ukázkové zprávy. Můžete otestovat jednotlivé trasy nebo otestovat všechny trasy najednou a žádné zprávy jsou směrovány do koncových bodů během testu. Portál Azure, Azure Resource Manager, Azure PowerShell a Azure CLI se můžou použít k testování. Výsledky pomáhají určit, zda ukázková zpráva odpovídá dotazu, zpráva neodpovídá dotazu nebo test nelze spustit, protože ukázková zpráva nebo syntaxe dotazu jsou nesprávné. Další informace najdete v [tématu Test Route](/rest/api/iothub/iothubresource/testroute) and [Test all route](/rest/api/iothub/iothubresource/testallroutes).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Garanty objednávání s alespoň jednou dodáním

Směrování zpráv služby IoT Hub zaručuje objednané a alespoň jednou doručení zpráv koncovým bodům. To znamená, že mohou existovat duplicitní zprávy a řada zpráv může být znovu odeslána, která respektuje původní pořadí zpráv. Pokud je například původní pořadí zpráv [1,2,3,4], můžete obdržet posloupnost zpráv jako [1,2,1,2,3,1,2,3,4]. Záruka objednávky spoáčita na to, že pokud někdy obdržíte zprávu [1], bude vždy následována [2,3,4].

Pro zpracování duplicity zprávy doporučujeme razítko jedinečný identifikátor ve vlastnostech aplikace zprávy v místě původu, což je obvykle zařízení nebo modul. Služba, která zprávy spotřebovává, může zpracovávat duplicitní zprávy pomocí tohoto identifikátoru.

## <a name="latency"></a>Latence

Při směrování telemetrických zpráv zařízení cloud pomocí předdefinovaných koncových bodů, je mírné zvýšení latence end-to-end po vytvoření první trasy.

Ve většině případů je průměrné zvýšení latence menší než 500 ms. Latenci můžete sledovat pomocí **směrování: latence zpráv pro zprávy nebo události** nebo **metriku d2c.endpoints.latency.builtIn.events** IoT Hub. Vytvoření nebo odstranění libovolné trasy po první z nich nemá vliv na latenci od konce do konce.

## <a name="monitoring-and-troubleshooting"></a>Monitorování a řešení problémů

IoT Hub poskytuje několik metrik souvisejících s směrováním a koncovými body, které vám poskytnou přehled o stavu vašeho centra a odeslaných zpráv. Můžete kombinovat informace z více metrik k identifikaci hlavní příčinu problémů. Například použít metriku **Směrování: telemetrické zprávy vynechány** nebo **d2c.telemetry.egress.dropped** k identifikaci počtu zpráv, které byly vynechány, když neodpovídaly dotazy na žádné z tras a záložní trasa byla zakázána. [Metriky služby IoT Hub](iot-hub-metrics.md) obsahují seznam všech metrik, které jsou ve výchozím nastavení povolené pro vaše služby IoT Hub.

K získání [stavu](iot-hub-devguide-endpoints.md#custom-endpoints) koncových bodů můžete použít stav rozhraní REST API [Get Endpoint Health.](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) Doporučujeme používat [metriky služby IoT Hub](iot-hub-metrics.md) související s latencí směrování zpráv k identifikaci a ladění chyb, když je stav koncového bodu mrtvý nebo není v pořádku. Například pro centra událostí typu koncového bodu můžete sledovat **d2c.endpoints.latency.eventHubs**. Stav koncového bodu není v pořádku bude aktualizován na v pořádku, když služba IoT Hub vytvořila nakonec konzistentní stav stavu.

Pomocí **protokolů diagnostiky tras** v [nastavení diagnostiky](../iot-hub/iot-hub-monitor-resource-health.md)Azure Monitor můžete sledovat chyby, ke kterým dochází při vyhodnocování směrovacího dotazu a stavu koncového bodu, jak je vnímáno službou IoT Hub, například když je koncový bod mrtvý. Tyto diagnostické protokoly lze odeslat do protokolů Azure Monitor, Centra událostí nebo Azure Storage pro vlastní zpracování.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak vytvořit trasy zpráv, najdete [v tématu Zpracování zpráv mezi zařízeními a cloudem služby IoT Hub pomocí tras](tutorial-routing.md).

* [Jak odesílat zprávy mezi zařízeními a cloudy](quickstart-send-telemetry-node.md)

* Informace o sadách SDK, které můžete použít k odesílání zpráv mezi zařízeními, naleznete v [tématu Sady SDK Azure IoT](iot-hub-devguide-sdks.md).

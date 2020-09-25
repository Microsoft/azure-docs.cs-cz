---
title: Výstup front Service Bus z Azure Stream Analytics
description: Tento článek popisuje Service Bus front jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: bad81e8929cd0c5c66c87fd9f6cc11dc746b3e5f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317771"
---
# <a name="service-bus-queues-output-from-azure-stream-analytics"></a>Výstup front Service Bus z Azure Stream Analytics

[Fronty Service Bus](../service-bus-messaging/service-bus-queues-topics-subscriptions.md) nabízejí doručování zpráv typu FIFO jednomu nebo více konkurenčním spotřebitelům. Obvykle jsou zprávy přijímány a zpracovávány příjemci v dočasném pořadí, ve kterém byly přidány do fronty. Každá zpráva je přijata a zpracována pouze jedním příjemcem zprávy.

Na [úrovni kompatibility 1,2](stream-analytics-compatibility-level.md)Azure Stream Analytics používá rozšířený protokol zasílání zpráv [protokolu AMQP (Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) k zápisu do Service Busch front a témat. AMQP umožňuje sestavovat hybridní aplikace pro různé platformy s využitím otevřeného standardního protokolu.

## <a name="output-configuration"></a>Konfigurace výstupu

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu fronty.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do této Service Bus fronty. |
| Obor názvů Service Bus |Kontejner pro sadu entit zasílání zpráv. |
| Název fronty |Název fronty Service Bus. |
| Název zásad fronty |Když vytvoříte frontu, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** fronty. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad fronty |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Encoding |V případě CSV a JSON je v tuto chvíli jediným podporovaným formátem kódování UTF-8. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát |Platí pouze pro typ JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. Pokud vyberete možnost **oddělený řádek**, bude JSON číst jeden objekt současně. Celý obsah samotný by nepředstavoval platný formát JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON. |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce systémových vlastností | Nepovinný parametr. Páry klíč-hodnota vlastností systému a odpovídajících názvů sloupců, které je třeba připojit k odchozí zprávě místo datové části.  |

Počet oddílů je [založený na Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.

## <a name="partitioning"></a>Dělení

Automaticky se zvolí dělení. Počet oddílů je založený na [Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl. Počet výstupních zapisovačí je stejný jako počet oddílů ve výstupní frontě.

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy je 256 KB na jednu zprávu pro úroveň Standard a 1 MB pro úroveň Premium. Další informace najdete v tématu [omezení Service Bus](../service-bus-messaging/service-bus-quotas.md). K optimalizaci použijte jednu událost na zprávu.

## <a name="custom-metadata-properties-for-output"></a>Vlastnosti vlastních metadat pro výstup

Sloupce dotazu můžete k odchozím zprávám připojit jako vlastnosti uživatele. Tyto sloupce neobsahují datovou část. Vlastnosti jsou k dispozici ve formě slovníku ve výstupní zprávě. *Klíč* je název sloupce a *hodnota* je hodnota sloupce ve slovníku Properties (vlastnosti). Všechny datové typy Stream Analytics jsou podporovány kromě záznamu a pole.

V následujícím příkladu `DeviceId` jsou pole a `DeviceStatus` přidána do metadat.

1. Použijte následující dotaz:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Nakonfigurujte `DeviceId,DeviceStatus` jako sloupce vlastností ve výstupu.

   :::image type="content" source="media/service-bus-queues-output/property-columns.png" alt-text="Sloupce vlastností":::

Následující obrázek má očekávané vlastnosti výstupní zprávy, které byly zkontrolovány v centru EventHub pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-queues-output/custom-properties.png" alt-text="Vlastní vlastnosti události":::

## <a name="system-properties"></a>Systémové vlastnosti

Sloupce dotazu můžete připojit jako [systémové vlastnosti](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) do odchozí fronty služby Service Bus nebo do zpráv tématu.

Tyto sloupce neobsahují datovou část, místo toho se naplní odpovídající [vlastnost BrokeredMessage systému](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) hodnotami sloupce dotazu.
Tyto vlastnosti systému jsou podporovány – `MessageId, ContentType, Label, PartitionKey, ReplyTo, SessionId, CorrelationId, To, ForcePersistence, TimeToLive, ScheduledEnqueueTimeUtc` .

Řetězcové hodnoty těchto sloupců se analyzují jako odpovídající typ hodnoty vlastnosti systému a jakékoli selhání při analýze se považují za chyby dat.
Toto pole je k dispozici jako formát objektu JSON. Podrobnosti o tomto formátu jsou následující:

* Obklopené složenými závorkami {} .
* Napsané páry klíč/hodnota.
* Klíče a hodnoty musí být řetězce.
* Klíč je název vlastnosti systému a hodnota je název sloupce dotazu.
* Klíče a hodnoty jsou oddělené dvojtečkou.
* Jednotlivé páry klíč/hodnota jsou oddělené čárkou.

Ukazuje, jak používat tuto vlastnost –

* Zadávání `select *, column1, column2 INTO queueOutput FROM iotHubInput`
* Sloupce systémových vlastností: `{ "MessageId": "column1", "PartitionKey": "column2"}`

Tím se nastaví `MessageId` zprávy fronty služby Service Bus s `column1` hodnotami a PartitionKey se nastaví s `column2` hodnotami.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)

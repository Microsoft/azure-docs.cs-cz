---
title: Service Bus výstup témat z Azure Stream Analytics
description: Tento článek popisuje Service Bus témata jako výstup pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: cc09912bb0c9ab553d180ff5cc06fc52c4c5cc0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261038"
---
# <a name="service-bus-topics-output-from-azure-stream-analytics"></a>Service Bus výstup témat z Azure Stream Analytics

Fronty Service Bus poskytují metodu komunikace 1:1 od odesílatele k přijímači. [Service Bus témata](https://msdn.microsoft.com/library/azure/hh367516.aspx) poskytují vzájemnou formu komunikace 1: n.

V následující tabulce jsou uvedeny názvy vlastností a jejich popisy pro vytvoření výstupu Service Bus tématu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup |Popisný název, který se používá v dotazech k přesměrování výstupu dotazu do tohoto Service Bus tématu. |
| Obor názvů Service Bus |Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste také vytvořili Service Bus obor názvů. |
| Název tématu |Témata jsou entity zasílání zpráv, podobně jako centra událostí a fronty. Jsou navržené ke shromažďování datových proudů událostí ze zařízení a služeb. Při vytvoření tématu se také zobrazí konkrétní název. Zprávy odeslané do tématu nejsou k dispozici, dokud nevytvoříte odběr, a zajistěte, aby bylo v tématu jedno nebo více předplatných. |
| Název zásad tématu |Když vytvoříte Service Bus téma, můžete také vytvořit zásady sdíleného přístupu na kartě **Konfigurace** tématu. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad tématu |Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů Service Bus. |
| Formát serializace události |Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Encoding |Pokud používáte formát CSV nebo JSON, je nutné zadat kódování. Formát UTF-8 v tuto chvíli podporuje pouze kódování UTF-8. |
| Oddělovač |Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |
| Sloupce systémových vlastností | Nepovinný parametr. Páry klíč-hodnota vlastností systému a odpovídajících názvů sloupců, které je třeba připojit k odchozí zprávě místo datové části. |

Počet oddílů je [založený na Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl.

## <a name="partitioning"></a>Dělení

Automaticky se zvolí dělení. Počet oddílů je založený na [Service Bus SKU a velikosti](../service-bus-messaging/service-bus-partitioning.md). Klíč oddílu je jedinečná celočíselná hodnota pro každý oddíl. Počet výstupních zapisovačí je stejný jako počet oddílů v tématu výstup.

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

   :::image type="content" source="media/service-bus-topics-output/property-columns.png" alt-text="Sloupce vlastností":::

Následující obrázek má očekávané vlastnosti výstupní zprávy, které byly zkontrolovány v centru EventHub pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/service-bus-topics-output/custom-properties.png" alt-text="Vlastní vlastnosti události":::

## <a name="system-properties"></a>Systémové vlastnosti

Sloupce dotazu můžete připojit jako [systémové vlastnosti](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) do odchozí fronty služby Service Bus nebo do zpráv tématu. Tyto sloupce neobsahují datovou část, místo toho se naplní odpovídající [vlastnost BrokeredMessage systému](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet#properties&preserve-view=true) hodnotami sloupce dotazu.
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

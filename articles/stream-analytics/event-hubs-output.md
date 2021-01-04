---
title: Event Hubs výstup z Azure Stream Analytics
description: Tento článek popisuje, jak výstupní data z Azure Stream Analytics do Azure Event Hubs.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: e01a4418681e0f78864eacbf70016cfb33fa6f53
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739633"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Event Hubs výstup z Azure Stream Analytics

Služba [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) je vysoce škálovatelný ingestování událostí publikování a odběru. Může shromažďovat miliony událostí za sekundu. Jedním z nich je použití centra událostí jako výstup, když se výstup Stream Analytics úlohy stal vstupem jiné úlohy streamování. Informace o maximální velikosti zpráv a optimalizaci velikosti dávky najdete v části [Velikost výstupních dávek](#output-batch-size) .

## <a name="output-configuration"></a>Konfigurace výstupu

Následující tabulka obsahuje parametry potřebné ke konfiguraci datových proudů z Center událostí jako výstupu.

| Název vlastnosti | Popis |
| --- | --- |
| Alias pro výstup | Popisný název, který se používá v dotazech k nasměrování výstupu dotazu do tohoto centra událostí. |
| Obor názvů centra událostí | Kontejner pro sadu entit zasílání zpráv. Při vytváření nového centra událostí jste taky vytvořili obor názvů centra událostí. |
| Název centra událostí | Název výstupu centra událostí. |
| Název zásad centra událostí | Zásady sdíleného přístupu, které můžete vytvořit na kartě **Konfigurace** centra událostí. Každá zásada sdíleného přístupu má název, oprávnění, která jste nastavili, a přístupové klíče. |
| Klíč zásad centra událostí | Sdílený přístupový klíč, který se používá k ověření přístupu k oboru názvů centra událostí. |
| Sloupec klíče oddílu | Nepovinný parametr. Sloupec obsahující klíč oddílu pro výstup centra událostí |
| Formát serializace události | Formát serializace pro výstupní data. Podporují se JSON, CSV a Avro. |
| Encoding | V případě CSV a JSON je v tuto chvíli jediným podporovaným formátem kódování UTF-8. |
| Oddělovač | Platí pouze pro serializaci CSV. Stream Analytics podporuje řadu běžných oddělovačů pro serializaci dat ve formátu CSV. Podporované hodnoty jsou čárka, středník, mezera, tabulátor a svislá čára. |
| Formát | Platí pouze pro serializaci JSON. **Oddělený řádek** určuje, že výstup je formátován tak, že má každý objekt JSON oddělený novým řádkem. Pokud vyberete možnost **oddělený řádek**, bude JSON číst jeden objekt současně. Celý obsah samotný by nepředstavoval platný formát JSON. **Pole** určuje, že výstup je formátován jako pole objektů JSON.  |
| Sloupce vlastností | Nepovinný parametr. Sloupce oddělené čárkami, které je třeba připojit jako vlastnosti uživatele odchozí zprávy namísto datové části. Další informace o této funkci najdete v části [vlastní vlastnosti metadat pro výstup](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Dělení

Dělení na oddíly se liší v závislosti na zarovnání oddílů. Když je klíč oddílu pro výstup centra událostí rovnoměrně zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů ve výstupu centra událostí. Každý zapisovač používá ke posílání událostí do konkrétního oddílu [třídu EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet&preserve-view=true) . Pokud klíč oddílu pro výstup centra událostí není zarovnaný k nadřazenému kroku (předchozímu) dotazu, počet zapisovačů je stejný jako počet oddílů v předchozím kroku. Každý zapisovač používá v **EventHubClient** [třídu SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet&preserve-view=true) k odesílání událostí do všech výstupních oddílů. 

## <a name="output-batch-size"></a>Velikost výstupní dávky

Maximální velikost zprávy je 256 KB nebo 1 MB na zprávu. Další informace najdete v tématu [omezení Event Hubs](../event-hubs/event-hubs-quotas.md). Když není zarovnaný vstup/výstup dělení, každá událost se zabalí jednotlivě v `EventData` dávce a pošle se do maximální velikosti zprávy. K tomu dojde také v případě, že se používají [vlastní vlastnosti metadat](#custom-metadata-properties-for-output) . Při zarovnávání vstupních/výstupních dělení se více událostí balí do jedné `EventData` instance, až do maximální velikosti zprávy a pošle se.

## <a name="custom-metadata-properties-for-output"></a>Vlastnosti vlastních metadat pro výstup

Sloupce dotazu můžete k odchozím zprávám připojit jako vlastnosti uživatele. Tyto sloupce neobsahují datovou část. Vlastnosti jsou k dispozici ve formě slovníku ve výstupní zprávě. *Klíč* je název sloupce a *hodnota* je hodnota sloupce ve slovníku Properties (vlastnosti). Všechny datové typy Stream Analytics jsou podporovány kromě záznamu a pole.

V následujícím příkladu `DeviceId` jsou pole a `DeviceStatus` přidána do metadat.

1. Použijte následující dotaz:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Nakonfigurujte `DeviceId,DeviceStatus` jako sloupce vlastností ve výstupu.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Sloupce vlastností":::

Následující obrázek má očekávané vlastnosti výstupní zprávy, které byly zkontrolovány v centru EventHub pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Vlastní vlastnosti události":::

## <a name="next-steps"></a>Další kroky

* [Použití spravovaných identit k přístupu k centru událostí z Azure Stream Analytics úlohy (Preview)](event-hubs-managed-identity.md)
* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

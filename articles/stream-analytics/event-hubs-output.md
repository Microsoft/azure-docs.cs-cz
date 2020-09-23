---
title: Event Hubs výstup z Azure Stream Analytics
description: Tento článek popisuje, jak výstupní data z Azure Stream Analytics do Azure Event Hubs.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 50d2d974815e0921d99154bce67f604b7314970d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90892024"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Event Hubs výstup z Azure Stream Analytics

Služba [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) je vysoce škálovatelný ingestování událostí publikování a odběru. Může shromažďovat miliony událostí za sekundu. Jedním z nich je použití centra událostí jako výstup, když se výstup Stream Analytics úlohy stal vstupem jiné úlohy streamování. Informace o maximální velikosti zpráv a optimalizaci velikosti dávky najdete v části [Velikost výstupních dávek](#output-batch-size) .

## <a name="output-configuration"></a>Konfigurace výstupu

Následující tabulka obsahuje parametry potřebné ke konfiguraci datových proudů z Center událostí jako výstupu.

| Název vlastnosti | Description |
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

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI](quick-create-azure-cli.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí šablony ARM](quick-create-azure-resource-manager.md)
* [Rychlý Start: vytvoření úlohy Stream Analytics pomocí Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md)
* [Rychlý Start: vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md)

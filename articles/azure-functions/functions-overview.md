---
title: Přehled Azure Functions
description: Přečtěte si, jak Azure Functions můžou přispět k vytváření robustních aplikací bez serveru.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperf-fy21q2
ms.openlocfilehash: 41b627259d84539c868f95eb3cf33db5dbdab52c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97862402"
---
# <a name="introduction-to-azure-functions"></a>Úvod do Azure Functions

Azure Functions je řešení bez serveru, které umožňuje psát méně kódu, udržovat méně infrastruktury a ušetřit náklady. Nemusíte si dělat starosti s nasazením a správou serverů, cloudová infrastruktura poskytuje všechny aktuální prostředky potřebné k zachování spuštěné aplikace.

Vy se zaměříte na části kódu, které jsou pro vás nejdůležitější, a služba Azure Functions se postará o všechno ostatní.<br /><br />

> [!VIDEO https://www.youtube.com/embed/8-jz5f_JyEQ]

Často vytváříme systémy, které reagují na řadu kritických událostí. Bez ohledu na to, jestli vytváříte webové rozhraní API, reagujete na změny v databázi, zpracováváte datové proudy IoT nebo dokonce spravujete fronty zpráv – každá aplikace potřebuje způsob, jak spustit nějaký kód, když dojde k těmto událostem.

Pro splnění tohoto požadavku Azure Functions poskytuje "výpočetní výkon na vyžádání" dvěma významnými způsoby.

Nejprve Azure Functions umožňuje implementovat logiku systému do snadno dostupných bloků kódu. Tyto bloky kódu se nazývají "Functions". Různé funkce můžou běžet kdykoli, když potřebujete reagovat na kritické události.

S tím, jak se žádosti zvyšují, Azure Functions v případě potřeby splňovat požadavky s libovolným počtem prostředků a instancí funkcí, ale pouze v případě potřeby. V případě požadavků se všechny nadbytečné prostředky a instance aplikace vyřadí automaticky.

Odkud přicházejí všechny výpočetní prostředky? Azure Functions [poskytuje tolik nebo víc výpočetních prostředků, kolik je potřeba](./functions-scale.md) , aby splňovala požadavky vaší aplikace.

Poskytování výpočetních prostředků na vyžádání je podstatou výpočetní technologie bez [serveru](https://azure.microsoft.com/solutions/serverless/) v Azure Functions.

## <a name="scenarios"></a>Scénáře

V mnoha případech se funkce [integruje s polem cloudových služeb](./functions-triggers-bindings.md) , aby poskytovala implementace náročné na funkce.

Níže jsou uvedeny běžné, _ale nemají vyčerpávající význam_, sadu scénářů pro Azure Functions.

| Pokud chcete... | pak... |
| --- | --- |
| **Sestavení webového rozhraní API** | Implementace koncového bodu pro webové aplikace pomocí [triggeru protokolu HTTP](./functions-bindings-http-webhook.md) |
| **Zpracovat nahrávání souborů** | Spustit kód, když se v [úložišti objektů BLOB](./functions-bindings-storage-blob.md) nahraje nebo změní soubor |
| **Sestavení pracovního postupu bez serveru** | Řetězení řady funkcí pomocí [trvalých funkcí](./durable/durable-functions-overview.md) |
| **Reakce na změny v databázi** | Spuštění vlastní logiky při vytvoření nebo aktualizaci dokumentu v [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Spouštění naplánovaných úloh** | Spustit kód v [Nastavení časů](./functions-bindings-timer.md) |
| **Vytváření spolehlivých systémů fronty zpráv** | Zpracování front zpráv pomocí [Queue Storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md)nebo [Event Hubs](./functions-bindings-event-hubs.md) |
| **Analýza datových proudů IoT** | Shromažďování a zpracování [dat ze zařízení IoT](./functions-bindings-event-iot.md) |
| **Zpracování dat v reálném čase** | Použití [funkcí a signálů](./functions-bindings-signalr-service.md) k reakci na data v daném okamžiku |

Při sestavování funkcí máte k dispozici následující možnosti a prostředky:

- **Použijte preferovaný jazyk**: funkce zápisu v [jazycích C#, Java, JavaScript, PowerShell nebo Python](./supported-languages.md)nebo použijte [vlastní obslužnou rutinu](./functions-custom-handlers.md) pro použití prakticky jakéhokoli jiného jazyka.

- **Automatizace nasazení**: z přístupu na základě nástrojů pro použití externích kanálů je k dispozici [nesčetných možností nasazení](./functions-deployment-technologies.md) .

- **Řešení potíží s funkcí**: pomocí [monitorovacích nástrojů](./functions-monitoring.md) a [strategií testování](./functions-test-a-function.md) získáte přehled o vašich aplikacích.

- **Flexibilní cenové možnosti**: s plánem [spotřeby](./pricing.md) platíte jenom za běhu funkcí, zatímco plány [Premium](./pricing.md) a [App Service](./pricing.md) nabízí funkce pro specializované potřeby.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme prostřednictvím lekcí, ukázek a interaktivních kurzů](./functions-get-started.md)

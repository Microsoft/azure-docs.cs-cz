---
title: Přehled Azure Functions
description: Přečtěte si, jak Azure Functions může pomáhat vytvářet škálovatelné aplikace bez serveru.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 514f2e9a82a50f95f9c054c6a54e7b5af3c0af15
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96167773"
---
# <a name="introduction-to-azure-functions"></a>Úvod do Azure Functions

Často vytváříme systémy, které reagují na řadu kritických událostí. Bez ohledu na to, jestli vytváříte webové rozhraní API, reagujete na změny v databázi, zpracováváte datové proudy IoT nebo dokonce spravujete fronty zpráv – každá aplikace potřebuje způsob, jak spustit nějaký kód, když dojde k těmto událostem.

Pro splnění této potřeby Azure Functions poskytuje "výpočetní výkon na vyžádání" a dvěma významnými způsoby.

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
| **Zpracování dat v reálném čase** | Použití [funkcí a signálu R](./functions-bindings-signalr-service.md) k reakci na data v daném okamžiku |

Při sestavování funkcí máte k dispozici následující možnosti a prostředky:

- **Použijte preferovaný jazyk**: funkce zápisu v [jazycích C#, Java, JavaScript, PowerShell nebo Python](./supported-languages.md)nebo použijte [vlastní obslužnou rutinu](./functions-custom-handlers.md) pro použití prakticky jakéhokoli jiného jazyka.

- **Automatizace nasazení**: z přístupu na základě nástrojů pro použití externích kanálů je k dispozici [nesčetných možností nasazení](./functions-deployment-technologies.md) .

- **Řešení potíží s funkcí**: pomocí [monitorovacích nástrojů](./functions-monitoring.md) a [strategií testování](./functions-test-a-function.md) získáte přehled o vašich aplikacích.

- **Flexibilní cenové možnosti**: s plánem [spotřeby](./pricing.md) platíte jenom za běhu funkcí, zatímco plány [Premium](./pricing.md) a [App Service](./pricing.md) nabízí funkce pro specializované potřeby.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme prostřednictvím lekcí, ukázek a interaktivních kurzů](./functions-get-started.md)
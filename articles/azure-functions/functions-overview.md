---
title: Přehled Azure Functions
description: Během několika minut se naučte používat Azure Functions k optimalizaci asynchronních úloh.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77621004"
---
# <a name="an-introduction-to-azure-functions"></a>Seznámení s Azure Functions

Funkce Azure umožňuje spouštět malé části kódu (nazývané "funkce") bez obav o aplikační infrastrukturu. Díky funkcím Azure poskytuje cloudová infrastruktura všechny aktuální servery, které potřebujete k tomu, aby vaše aplikace běžela ve velkém měřítku.

Funkce je "spuštěna" určitým typem události. [Mezi podporované aktivační události](./functions-triggers-bindings.md) patří reakce na změny v datech, odpovídání na zprávy, spuštění podle plánu nebo jako výsledek požadavku HTTP.

Zatímco vždy můžete kód přímo proti nesčetné množství služeb, integrace s jinými službami je efektivnější pomocí vazby. Vazby poskytují [deklarativní přístup k široké škále služeb Azure a třetích stran](./functions-triggers-bindings.md).

## <a name="features"></a>Funkce

Mezi klíčové funkce funkcí Azure patří:

- **Aplikace bez serveru**: Funkce umožňují vyvíjet aplikace [bez serveru v](https://azure.microsoft.com/solutions/serverless/) Microsoft Azure.

- **Volba jazyka**: Pište funkce pomocí [c#, java, javascriptu, pythonu a prostředí PowerShell](supported-languages.md).

- **Cenový model platby za použití**: Zaplaťte jenom za čas strávený spuštěním kódu. Viz možnost plánu hostování Consumption v [části týkající se cen](#pricing).  

- **Přineste si vlastní závislosti**: Funkce podporuje NuGet a NPM, což vám umožní přístup k oblíbeným knihovnám.

- **Integrované zabezpečení:** Chraňte funkce spouštěné http s poskytovateli OAuth, jako jsou Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.

- **Zjednodušená integrace:** Snadno se integraujte se službami Azure a nabídkami softwaru jako služby (SaaS).

- **Flexibilní vývoj:** Nastavte průběžnou integraci a nasaďte kód prostřednictvím [GitHubu](../app-service/scripts/cli-continuous-deployment-github.md), [Služby Azure DevOps services](../app-service/scripts/cli-continuous-deployment-vsts.md)a dalších [podporovaných vývojových nástrojů](../app-service/deploy-local-git.md).

- **Stavová architektura bez serveru:** Orchestrate bezserverových aplikací s [trvalými funkcemi](durable/durable-functions-overview.md).

- **Open source**: Běhový čas Funkce je open source a [je k dispozici na GitHubu](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Co můžu dělat s Functions?

Funkce jsou skvělým řešením pro zpracování hromadných dat, integraci systémů, práci s internetem věcí (IoT) a vytváření jednoduchých api a mikroslužeb.

K dispozici je řada šablon, které vám pomohou začít s klíčovými scénáři, včetně:

- **HTTP**: Spuštění kódu na základě [požadavků HTTP](functions-create-first-azure-function.md)

- **Časovač:** Naplánovat spuštění kódu [v předdefinovaných časech](./functions-create-scheduled-function.md)

- **Azure Cosmos DB:** Zpracování [nových a upravených dokumentů Azure Cosmos DB](./functions-create-cosmos-db-triggered-function.md)

- **Úložiště objektů blob:** Zpracování [nových a upravených objektů BLOB úložiště Azure](./functions-create-storage-blob-triggered-function.md)

- **Úložiště front:** Reakce na [zprávy fronty úložiště Azure](./functions-create-storage-queue-triggered-function.md)

- **Event Grid:** Reakce na [události služby Azure Event Grid prostřednictvím předplatných a filtrů](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Centrum událostí:** Reakce na [velké objemy událostí centra Událostí Azure](./functions-bindings-event-hubs.md)

- **Fronta služby Service Bus:** Připojení k jiným službám Azure nebo místníslužby [odpovídající zprávy fronty služby Service Bus](./functions-bindings-service-bus.md)

- **Téma služby Service Bus:** Připojení dalších služeb Azure nebo místních služeb [reakcí na zprávy tématu služby Service Bus](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Kolik stojí Functions?

Azure Functions má tři druhy cenových plánů. Zvolte plán, který nejlépe vyhovuje vašim potřebám:

- **Plán spotřeby**: Azure poskytuje všechny potřebné výpočetní prostředky. Nemusíte se starat o správu prostředků a platit pouze za čas, který váš kód běží.

- **Prémiový plán**: Zadáte několik předteplých instancí, které jsou vždy online a připravené okamžitě reagovat. Když se spustí vaše funkce, Azure poskytuje další výpočetní prostředky, které jsou potřeba. Platíte za předem zahřáté instance, které běží nepřetržitě, a za všechny další instance, které používáte, když Azure škáluje vaši aplikaci dovnitř a ven.

- **Plán služby App Service:** Spouštějte funkce stejně jako vaše webové aplikace. Pokud používáte službu App Service pro ostatní aplikace, vaše funkce můžete spustit na stejném plánu bez dalších nákladů.

Další informace o plánech hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Úplné podrobnosti o cenách jsou dostupné na [stránce Ceny Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Další kroky

- [Vytvoření první funkce Azure](functions-create-first-function-vs-code.md)  
  Začínáme s [Visual Studio Code](functions-create-first-function-vs-code.md), [příkazový řádek](functions-create-first-azure-function-azure-cli.md)nebo použijte portál [Azure](functions-create-first-azure-function.md).

- [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Poskytuje další odborné informace o modulu runtime Azure Functions a referenční informace pro kódování funkcí a definování triggerů a vazeb.

- [Postup škálování Azure Functions](functions-scale.md)  
  Toto téma popisuje plány služby, které jsou dostupné se službou Azure Functions (včetně plánu hostování Consumption), a výběr správného plánu.

- [Další informace o Azure App Service](../app-service/overview.md)  
  Azure Functions využívá službu Azure App Service pro základní funkce, jako jsou nasazení, proměnné prostředí a diagnostika.

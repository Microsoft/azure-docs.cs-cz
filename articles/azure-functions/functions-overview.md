---
title: Přehled Azure Functions
description: Během několika minut se naučte používat Azure Functions k optimalizaci asynchronních úloh.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "77621004"
---
# <a name="an-introduction-to-azure-functions"></a>Seznámení s Azure Functions

Azure Functions umožňuje spustit malé části kódu (označované jako "funkce"), aniž byste se museli starat o infrastrukturu aplikace. Díky Azure Functions cloudová infrastruktura poskytuje všechny aktuální servery, které potřebujete k zajištění, aby vaše aplikace běžela ve velkém měřítku.

Funkce je "aktivovaná" konkrétním typem události. Mezi [podporované triggery](./functions-triggers-bindings.md) patří reakce na změny v datech, reakce na zprávy, spuštěné podle plánu nebo jako výsledek požadavku HTTP.

I když můžete vždy kód přímo na nesčetných služby, integrace s jinými službami je zjednodušená pomocí vazeb. Vazby poskytují [deklarativní přístup k široké škále služeb Azure a služeb třetích stran](./functions-triggers-bindings.md).

## <a name="features"></a>Funkce

Mezi klíčové funkce Azure Functions patří:

- **Aplikace bez serveru**: funkce umožňují vyvíjet aplikace bez [serveru](https://azure.microsoft.com/solutions/serverless/) na Microsoft Azure.

- **Volba jazyka**: Zapište funkce pomocí jazyka [C#, Java, JavaScriptu, Pythonu a PowerShellu](supported-languages.md).

- **Cenový model pro platby za použití**: Plaťte jenom za čas strávený spouštěním kódu. Viz možnost plánu hostování Consumption v [části týkající se cen](#pricing).  

- **Přineste si vlastní závislosti**: funkce podporují NUGET a NPM, které vám umožní přístup k oblíbeným knihovnám.

- **Integrované zabezpečení**: Chraňte funkce aktivované protokolem HTTP poskytovateli OAuth, jako je Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.

- **Zjednodušená integrace**: Snadná integrace se službami Azure a nabídkami software-as-a-Service (SaaS).

- **Flexibilní vývoj**: nastavte průběžnou integraci a nasaďte kód prostřednictvím [githubu](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)a dalších [podporovaných vývojových nástrojů](../app-service/deploy-local-git.md).

- **Stavová architektura bez serveru**: orchestrujte aplikace bez serveru pomocí [Durable Functions](durable/durable-functions-overview.md).

- **Open Source**: běhový modul Functions je open source a [dostupný na GitHubu](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Co můžu dělat s Functions?

Funkce je skvělé řešení pro zpracování hromadných dat, integraci systémů, práci s internetem věcí (IoT) a vytváření jednoduchých rozhraní API a mikroslužeb.

K dispozici je řada šablon, které vám pomohou začít s klíčovými scénáři, včetně:

- **Http**: spuštění kódu na základě [požadavků HTTP](functions-create-first-azure-function.md)

- **Timer**: naplánovat spuštění kódu [v předdefinovaných časech](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: zpracování [nových a upravených dokumentů Azure Cosmos DB](./functions-create-cosmos-db-triggered-function.md)

- **Úložiště objektů BLOB**: zpracovat [nové a upravené objekty blob Azure Storage](./functions-create-storage-blob-triggered-function.md)

- **Queue Storage**: reakce na [zprávy ve frontě Azure Storage](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: reakce na [Azure Event Grid události prostřednictvím předplatných a filtrů](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Centrum událostí**: reakce na [velké objemy událostí centra událostí v Azure](./functions-bindings-event-hubs.md)

- **Service Bus fronta**: Připojte se k jiným službám Azure nebo místním službám tím, že [odpovíte Service Bus zpráv fronty](./functions-bindings-service-bus.md) .

- **Service Bus téma**: připojení dalších služeb Azure nebo místních služeb pomocí [reakcí na zprávy o Service Bus tématu](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Kolik stojí Functions?

Azure Functions má tři druhy cenových plánů. Zvolte plán, který nejlépe vyhovuje vašim potřebám:

- **Plán spotřeby**: Azure poskytuje všechny nezbytné výpočetní prostředky. Nemusíte se starat o správu prostředků a platit jenom za čas, kdy je váš kód spuštěný.

- **Plán Premium**: zadáte počet předem zavedených instancí, které jsou vždycky online a připravené k okamžité reakci. Když je vaše funkce spuštěná, Azure poskytuje všechny další výpočetní prostředky, které jsou potřeba. Platíte za průběžné průběžné instance a všechny další instance, které používáte jako Azure, škálují svou aplikaci a jsou v provozu.

- **Plán App Service**: spouštějte vaše funkce stejně jako vaše webové aplikace. Pokud používáte App Service pro jiné aplikace, můžou se vaše funkce spouštět ve stejném plánu bez dalších nákladů.

Další informace o plánech hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Úplné podrobnosti o cenách jsou dostupné na [stránce Ceny Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Další kroky

- [Vytvoření první funkce Azure](functions-create-first-function-vs-code.md)  
  Začněte s [Visual Studio Code](functions-create-first-function-vs-code.md), [příkazovým řádkem](functions-create-first-azure-function-azure-cli.md)nebo použijte [Azure Portal](functions-create-first-azure-function.md).

- [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Poskytuje další odborné informace o modulu runtime Azure Functions a referenční informace pro kódování funkcí a definování triggerů a vazeb.

- [Postup škálování Azure Functions](functions-scale.md)  
  Toto téma popisuje plány služby, které jsou dostupné se službou Azure Functions (včetně plánu hostování Consumption), a výběr správného plánu.

- [Další informace o Azure App Service](../app-service/overview.md)  
  Azure Functions využívá službu Azure App Service pro základní funkce, jako jsou nasazení, proměnné prostředí a diagnostika.

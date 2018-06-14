---
title: Přehled Azure Functions | Dokumentace Microsoftu
description: Během několika minut se naučte používat Azure Functions k optimalizaci asynchronních úloh.
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: ''
tags: ''
keywords: funkce azure, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serverů
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2017
ms.locfileid: "24877897"
---
# <a name="an-introduction-to-azure-functions"></a>Seznámení s Azure Functions  
Azure Functions je řešení umožňující snadno spouštět malé kódy (tzv. „funkce“) v cloudu. Můžete napsat přesně takový kód, jaký potřebujete pro aktuální problém, a nestarat se o infrastrukturu k jeho spuštění nebo aplikaci jako celek. Služba Functions může ještě zvýšit produktivitu vývoje, a navíc k vývoji můžete použít jazyk podle vlastní volby, například C#, F#, Node.js, Javu nebo PHP. Plaťte pouze za čas, kdy kód běží, a potřebné škálování svěřte Azure. Služba Azure Functions umožňuje v Microsoft Azure vytvářet aplikace [bez serveru](https://azure.microsoft.com/overview/serverless-computing/).

Toto téma obsahuje obecný přehled Azure Functions. Pokud chcete rovnou začít používat službu Functions, začněte tématem [Vytvoření první funkce Azure](functions-create-first-azure-function.md). Pokud hledáte další odborné informace o Functions, přejděte k části [referenční informace pro vývojáře](functions-reference.md).

## <a name="features"></a>Funkce
Toto jsou některé klíčové funkce služby Functions:

* **Možnost volby jazyka** – Funkce můžete podle vlastní volby psát v jazycích C#, F# nebo JavaScript. Další možnosti najdete v tématu [Podporované jazyky](supported-languages.md).
* **Cenový model platba za použití** – Platíte jen za čas, kdy byl kód spuštěný. Viz možnost plánu hostování Consumption v [části týkající se cen](#pricing).  
* **Přineste si vlastní závislosti** – Functions podporuje NuGet a NPM, takže můžete používat své oblíbené knihovny.  
* **Integrované zabezpečení** – Chraňte funkce aktivované protokolem HTTP pomocí poskytovatelů OAuth, jako jsou Azure Active Directory, Facebook, Google, Twitter a účet Microsoft.  
* **Zjednodušená integrace** – Snadné využívání služeb Azure a nabídek softwaru jako služby (SaaS). Příklady najdete v [části týkající se integrace](#integrations).  
* **Flexibilní vývoj** – Kódujte funkce přímo na portálu nebo nastavte průběžnou integraci a nasaďte kód prostřednictvím nástrojů [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md) a dalších [podporovaných vývojových nástrojů](../app-service/app-service-deploy-local-git.md).  
* **Open Source** – Modul runtime Functions je typu Open Source a [je dostupný na GitHubu](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Co můžu dělat s Functions?
Služba Functions je vynikající řešení pro zpracování dat, integraci systémů, práci s internetem věcí (IoT) a vytváření jednoduchých rozhraní API a mikroslužeb. Zvažte Functions v případě úloh, jako je zpracování obrazu nebo objednávek a údržba souborů, a libovolných úloh, které chcete spustit podle plánu. 

Functions poskytuje šablony, které vám pomůžou začít s klíčovými scénáři, včetně těchto:

* **HTTPTrigger** – Aktivace provádění kódu pomocí žádosti protokolu HTTP. Příklad najdete v tématu [Vytvoření první funkce](functions-create-first-azure-function.md).
* **TimerTrigger** – Provádění úkolů čištění nebo jiných dávkových úkolů podle předdefinovaného plánu. Příklad najdete v tématu [Vytvoření funkce aktivované časovačem](functions-create-scheduled-function.md).
* **Webhook GitHubu** – Reakce na události, které nastaly v úložištích GitHubu. Příklad najdete v tématu [Vytvoření funkce aktivované webhookem GitHubu](functions-create-github-webhook-triggered-function.md).
* **Obecný webhook** – Zpracování žádostí webhooku protokolu HTTP z jakékoli služby, která podporuje webhooky. Příklad najdete v tématu [Vytvoření funkce aktivované obecným webhookem](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** – Zpracování dokumentů Azure Cosmos DB při jejich přidání nebo nahrání do kolekcí v databázi NoSQL. Příklad najdete v tématu [Vytvoření funkce aktivované službou Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** – Zpracování objektů blob Azure Storage po jejich přidání do kontejnerů. Tuto funkci můžete použít k změně velikosti imagí. Další informace najdete v tématu [Vazby služby Blob Storage](functions-bindings-storage-blob.md).
* **QueueTrigger** – Reakce na zprávy přicházející do fronty Azure Storage. Příklad najdete v tématu [Vytvoření funkce aktivované službou Azure Queue Storage](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** – Reakce na události doručené do centra událostí Azure. Toto je obzvlášť užitečné pro scénáře instrumentace aplikací, zpracování činnosti nebo pracovního postupu uživatele a internetu věcí (IoT). Další informace najdete v tématu [Vazby služby Event Hubs](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** – Připojení kódu k jiným službám Azure nebo místním službám prostřednictvím naslouchání frontě zpráv. Další informace najdete v tématu [Vazby služby Service Bus](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** – Připojení kódu k jiným službám Azure nebo místním službám prostřednictvím registrace k odběru témat. Další informace najdete v tématu [Vazby služby Service Bus](functions-bindings-service-bus.md).

Azure Functions podporuje *triggery*, které představují jeden ze způsobů spuštění provádění kódu, a *vazby*, které zjednodušují kódování u vstupních a výstupních dat. Podrobný popis triggerů a vazeb podporovaných Azure Functions najdete v tématu [Referenční informace pro vývojáře triggerů a vazeb Azure Functions](functions-triggers-bindings.md).

## <a name="integrations"></a>Integrace
Azure Functions se integruje s celou řadou služeb Azure a služeb třetích stran. Tyto služby mohou aktivovat funkci a spustit provádění, nebo mohou sloužit jako vstup a výstup kódu. Azure Functions podporuje následující integrace služeb:

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps (tabulky)
* Azure Notification Hubs
* Azure Service Bus (fronty a témata)
* Azure Storage (objekt blob, fronty a tabulky) 
* GitHub (webhooky)
* Místní (pomocí služby Service Bus)
* Twilio (SMS zprávy)

## <a name="pricing"></a>Kolik stojí Functions?
Azure Functions nabízí dva druhy cenových plánů. Zvolte plán, který nejlépe vyhovuje vašim potřebám: 

* **Plán Consumption** – Když je funkce spuštěná, Azure poskytuje všechny nezbytné výpočetní prostředky. Nemusíte se starat o správu prostředků a platíte jenom čas, kdy byl kód spuštěný. 
* **Plán služby App Service** – Spouštějte funkce stejně jako webové a mobilní aplikace nebo aplikace API. Pokud již službu App Service používáte pro jiné aplikace, můžete spouštět funkce v tomtéž plánu bez dalších poplatků. 

Další informace o plánech hostování najdete v [porovnání plánů hostování služby Azure Functions](functions-scale.md). Úplné podrobnosti o cenách jsou dostupné na [stránce Ceny Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Další kroky
* [Vytvoření první funkce Azure](functions-create-first-azure-function.md)  
  Umožňuje rovnou začít a vytvořit první funkci pomocí rychlého startu Azure Functions. 
* [Referenční informace pro vývojáře Azure Functions](functions-reference.md)  
  Poskytuje další odborné informace o modulu runtime Azure Functions a referenční informace pro kódování funkcí a definování triggerů a vazeb.
* [Testování Azure Functions](functions-test-a-function.md)  
  Toto téma popisuje různé nástroje a techniky pro testování funkcí.
* [Postup škálování Azure Functions](functions-scale.md)  
  Toto téma popisuje plány služby, které jsou dostupné se službou Azure Functions (včetně plánu hostování Consumption), a výběr správného plánu. 
* [Další informace o Azure App Service](../app-service/app-service-web-overview.md)  
  Azure Functions využívá službu Azure App Service pro základní funkce, jako jsou nasazení, proměnné prostředí a diagnostika. 


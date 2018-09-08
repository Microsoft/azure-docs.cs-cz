---
title: Osvědčené postupy pro službu Azure Functions | Dokumentace Microsoftu
description: Přečtěte si osvědčené postupy a vzory pro Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Služba Azure functions, vzorky, osvědčeným postupem, funkce, zpracování událostí, webhook, dynamické výpočty, architektura bez serveru
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9fe8b2cc09c7a08ab6d897a0fe1f1204d1903eca
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090859"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimalizace výkonu a spolehlivosti Azure Functions

Tento článek obsahuje pokyny ke zlepšení výkonu a spolehlivosti vašich [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) aplikace function. 

## <a name="general-best-practices"></a>Obecné osvědčené postupy

Tady jsou osvědčené postupy v tom, jak vytvářet a navrhovat řešení bez serveru pomocí služby Azure Functions.

### <a name="avoid-long-running-functions"></a>Vyhněte se dlouho trvající funkce

Velké, dlouhotrvající funkce může způsobit neočekávané vypršení časového limitu. Funkce může být velký kvůli velký počet závislostí Node.js. Import závislosti, může způsobit zvýšenou zátěž pokusů mít za následek neočekávané vypršení časového limitu. Závislosti se načtou i explicitně a implicitně. Jeden modul načítá váš kód může načíst vlastní dalších modulů.  

Pokaždé, když je to možné, refaktorace rozsáhlé funkce do menších funkce nastaví, které pracují společně a rychle vrátit odpovědi. Například webhookem nebo funkci triggeru HTTP může vyžadovat odpověď potvrzení v rámci určité časového limitu; je běžné, že webhooky vyžadují okamžitou reakci. Datová část triggeru HTTP můžete předat do fronty pro zpracování funkce pro aktivaci fronty. Tento přístup umožňuje pozdržet samotnou práci a vrátí okamžitou odezvu.


### <a name="cross-function-communication"></a>Různé komunikační – funkce

[Odolná služba Functions](durable-functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md) jsou za účelem správy přechodů mezi stavy a komunikaci mezi více funkcí.

Pokud nepoužíváte Durable Functions nebo Logic Apps můžete integrovat s více funkcí, je obecně osvědčeným postupem je použití front služby storage pro různé funkce komunikace.  Hlavním důvodem je, že front služby storage jsou levnější a mnohem snadněji zřizovat. 

Jednotlivé zprávy ve frontě úložiště mají omezenou velikost na 64 KB. Pokud je potřeba předat větší zprávy mezi funkcemi, sběrnici Azure Service Bus fronty může použít pro podporu zpráva o velikosti až 256 KB na úrovni Standard a až 1 MB na úrovni Premium.

Témata služby Service Bus jsou užitečné, pokud budete potřebovat filtrování před zpracováním zpráv.

Služba Event hubs jsou užitečné pro podporu velký objem komunikace.


### <a name="write-functions-to-be-stateless"></a>Zápis funkce jako bezstavové 

Funkce by měly být bezstavové a idempotentní, pokud je to možné. Přidružte žádné informace o stavu vyžaduje se svými daty. Například pořadí zpracování bude mít pravděpodobně přidružené `state` člena. Funkce může zpracovat objednávku založen na tento stav zůstává bezstavové samotné funkce. 

Idempotentní funkce se doporučuje, zejména s aktivačními událostmi časovače. Například pokud máte něco, co naprosto nutné spouštět jednou denně, tak zapisovat může probíhat kdykoli během dne se stejné výsledky. Funkce můžete ukončit, pokud neexistuje žádná práce pro určitý den. Také pokud předchozí spuštění se nepovedlo dokončit, dalším spuštění by měl pokračovat tam, kde skončila.


### <a name="write-defensive-functions"></a>Napsat obranné funkce

Předpokládejme, že funkce setkat se výjimka v každém okamžiku. Návrh funkce s možností z předchozího bodu selhání pokračovat během dalšího provedení. Představte si třeba situaci, která vyžaduje následující akce:

1. Dotaz na 10 000 řádků v databázi.
2. Vytvoření zprávy fronty pro každou z těchto řádků, které mají zpracovávat další dolů na řádek.
 
V závislosti na tom, jak složitá je systém, bude pravděpodobně: chová nesprávně zapojené navazujících službách, výpadků sítě nebo kvóty omezuje dosáhlo a tak podobně. Všechny tyto může ovlivnit vaši funkci v každém okamžiku. Je potřeba navrhnout vaše funkce, abyste byli připraveni pro něj.

Jak váš kód react Pokud dojde k selhání po vložení 5 000 položek do fronty pro zpracování? Sledování položek v sadě, který jste dokončili. V opačném případě může vložit je znovu při příštím. To může mít závažný dopad na pracovní postup. 

Pokud položka fronty již byla zpracována, umožňují vaší funkci no-op.

Využijte výhod obranné opatření již komponenty, které používáte na platformě Azure Functions. Viz například **zpracování nezpracovatelných zpráv** v dokumentaci k [fronty Azure Storage triggerů a vazeb](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Osvědčené postupy škálovatelnost

Existuje mnoho faktorů, které ovlivnit, jak škálovat instance vaší aplikace function App. Podrobnosti jsou uvedeny v dokumentaci pro [funkce škálování](functions-scale.md).  Tady jsou některé osvědčené postupy k zajištění optimální škálovatelnost aplikaci function app.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nekombinujte testovací a produkční kód ve stejné aplikaci function app

Funkce v rámci aplikace function app sdílení prostředků. Například je paměť sdílená. Pokud používáte aplikaci function app v produkčním prostředí, nepřidávejte do ní související s funkcí a prostředků. Může to způsobit neočekávané režii během provádění kódu produkčního prostředí.

Buďte opatrní načtete do svých aplikací funkce produkčního prostředí. Paměť je průměrovaný napříč každá funkce v aplikaci.

Pokud máte sdílené sestavení odkazuje víc funkcí .net, vložte ho do běžné sdílené složky. Pokud používáte skripty jazyka C# (.csx), odkazovat na sestavení příkazem podobně jako v následujícím příkladu: 

    #r "..\Shared\MyAssembly.dll". 

V opačném případě je snadno omylem nasadit více testovací verzí stejného binárního souboru, které se chovají odlišně mezi funkcemi.

Nepoužívejte podrobné protokolování v produkčním kódu. Má vliv na výkon negativní.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Použít asynchronní kód, ale Vyhněte se blokování volání

Asynchronní programování je součástí osvědčeného. Nicméně vždy-li zabránit odkazování `Result` vlastností nebo volání `Wait` metodu na `Task` instance. Tento přístup může vést k vyčerpání vlákna.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Příjem zpráv v dávce, kdykoli je to možné

Některé aktivačních událostí, jako je Centrum událostí povolit příjem dávku zpráv v jednom vyvolání.  Dávkování zpráv má mnohem lepší výkon.  Můžete nakonfigurovat velikost maximální dávky v `host.json` podle popisu v souboru [referenční dokumentace k host.json](functions-host-json.md)

Pro funkce C# můžete změnit typ na pole silného typu.  Například namísto z `EventData sensorEvent` podpis metody může být `EventData[] sensorEvent`.  Pro ostatní jazyky je potřeba explicitně nastavit vlastnost Kardinalita objektu ve vaší `function.json` k `many` Chcete-li povolit dávkování [jak je znázorněno zde](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurace hostitele chování lépe poradí s souběžnosti

`host.json` Souboru do aplikace function App umožňuje konfigurovat chování modulu runtime a aktivaci hostitele.  Kromě dávkování chování, můžete spravovat souběžného zpracování několika aktivačních událostí.  Často Úprava hodnoty v těchto možností může pomoct každé instance škálovací odpovídajícím způsobem pro požadavky vyvolané funkcí.

Přes všechny funkce v aplikaci, v rámci použít nastavení v souboru hostitelů *jednu instanci* funkce. Například pokud jste měli aplikaci function app s 2 funkce protokolu HTTP a nastavte souběžných požadavků na 25, požadavek na buď triggeru HTTP bude započítávat sdílené 25 souběžných požadavků.  Pokud tuto aplikaci function app se škálovat na 10 instancí, 2 funkce umožní efektivně 250 souběžných požadavků (10 instancí * 25 souběžných požadavků na instanci).

**Možnosti hostitele HTTP souběžnosti**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Můžete najít další možnosti konfigurace hostitele [v dokumentu konfigurace hostitele](functions-host-json.md).

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Správa připojení v Azure Functions](manage-connections.md)
* [Osvědčené postupy pro Azure App Service](../app-service/app-service-best-practices.md)

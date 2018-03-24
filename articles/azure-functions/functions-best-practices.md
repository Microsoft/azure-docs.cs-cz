---
title: Osvědčené postupy pro řešení Azure Functions | Microsoft Docs
description: Přečtěte si informace osvědčené postupy a vzory pro Azure Functions.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: ''
tags: ''
keywords: řešení Azure functions, vzory, osvědčeným postupem, funkce, událostí zpracování, webhooků, dynamické výpočetní, bez serveru architektura
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 19ca9d70f769a19556d131d1d131f1bc9d107ef0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimalizace výkonu a spolehlivosti Azure functions

Tento článek obsahuje pokyny pro zvýšení výkonu a spolehlivosti vaše [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) funkce aplikace. 

## <a name="general-best-practices"></a>Obecné doporučené postupy

Následují osvědčené postupy v tom, jak vytvořit a architektury vašich bez serveru řešení pomocí Azure Functions.

### <a name="avoid-long-running-functions"></a>Vyhněte se dlouho běžící funkce

Velké, dlouhotrvajících funkce může způsobit neočekávané vypršení časového limitu problémy. Funkce se může stát velké z důvodu velký počet závislostí Node.js. Import závislosti může také způsobit způsobit neočekávané vypršení časových limitů opakování zvýšené zátěže. Závislosti jsou načíst jak explicitně a implicitně. Jeden modul načteny kódu může načíst vlastní dalších modulů.  

Kdykoli je to možné, refactor velké funkce do menší funkce nastaví které pracují společně a rychle vrátit odpovědi. Například webhooku nebo funkce aktivace protokolu HTTP může vyžadovat odpověď potvrzení v určitých časovém limitu; je běžné, že webhooky vyžadují okamžitou reakci. Datová část aktivace protokolu HTTP můžete předat do fronty ke zpracování funkcí frontě aktivační události. Tento přístup umožňuje odložení samotnou práci a vrátí okamžitou reakci.


### <a name="cross-function-communication"></a>Mezi funkce komunikace

[Trvanlivý funkce](durable-functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md) využívají ke správě přechodů mezi stavy a komunikace mezi víc funkcí.

Pokud nepoužíváte trvanlivý funkce nebo Logic Apps pro integraci s více funkcemi, je obecně doporučujeme používat fronty úložiště pro různé funkce komunikace.  Hlavním důvodem je, že jsou fronty úložiště levnější a výrazně usnadňují zřizování. 

Jednotlivé zprávy ve frontě úložiště mají omezenou velikost na 64 KB. Pokud potřebujete předat větší zprávy mezi funkce, Azure Service Bus fronty může podporovat zpráva velikosti až 256 KB na vrstvě Standard a až 1 MB v úrovni Premium.

Témata Service Bus jsou užitečné, pokud budete potřebovat filtrování před zpracováním zpráv.

Centra událostí jsou užitečné pro podporu velkému komunikace.


### <a name="write-functions-to-be-stateless"></a>Zápis funkce bez zadání stavu 

Funkce by měla být bezstavové a idempotent, pokud je to možné. Přidružte žádné informace o stavu požadovaná data. Například pořadí zpracování by měla mít pravděpodobně přiřazený `state` člen. Funkce může zpracovat pořadí na základě tohoto stavu při současném zachování bezstavové funkce sám sebe. 

Funkce Idempotent doporučují zejména s aktivační události časovače. Například pokud máte něco, co nezbytně nutné spouštět jednou denně, zapisovat tak může probíhat kdykoli během dne se stejné výsledky. Funkce můžete ukončit, i když není žádná práce pro určitý den. Také pokud předchozím spuštění se nepovedlo dokončit, příštím spuštění by měl vyzvedávat kde bylo přerušeno.


### <a name="write-defensive-functions"></a>Napsat Obranným funkce

Předpokládejme, že funkce může dojít k výjimce kdykoli. Návrh funkcí s možností pokračovat z předchozího bodu selhání při dalším spuštění. Vezměte v úvahu scénář, který vyžaduje následující akce:

1. Dotaz na 10 000 řádků v databáze.
2. Vytvořit zprávu fronty pro každou z těchto řádků ke zpracování další dolů na řádku.
 
V závislosti na tom, jak složitá je systém, můžete mít: související se situací službami pro příjem dat chovají chybně výpadky sítě nebo kvóty omezuje dosáhlo maximálního atd. Všechny tyto může ovlivnit funkce kdykoli. Je třeba navrhnout funkcí abyste byli připraveni pro ni.

Jak kódu reagovat Pokud dojde k selhání po vložení 5 000 těchto položek do fronty pro zpracování? Sledování položek v sadě, která po dokončení. Jinak může vložte je dalším. To může mít vážný dopad na pracovní postup. 

Pokud již byla zpracována položka fronty, povolte funkce jako no-op.

Výhodou Obranným opatření již součástí, které použijete v platformě Azure Functions. Například v tématu **zpracování poškozených fronty zpráv** v dokumentaci k [fronty Azure Storage triggerů a vazeb](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Škálovatelnost osvědčené postupy

Existuje několik faktorů, což ovlivňuje, jak instancí aplikace funkce škálování. Podrobnosti jsou uvedeny v dokumentaci pro [funkce škálování](functions-scale.md).  Následují některé osvědčené postupy k zajištění optimální škálovatelnost aplikaci funkce.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nekombinujte testovací a produkční kódu ve stejné aplikaci – funkce

Funkce v rámci funkce aplikace sdílet prostředky. Například je sdílené paměti. Pokud používáte aplikaci funkce v produkčním prostředí, není funkcí spojených se testovací a prostředky do něj přidat. Může to způsobit neočekávané režii během provádění kódu produkční.

Dávejte pozor, zatížení ve svých aplikacích funkce produkční. Paměť je průměrem každou funkci v aplikaci.

Pokud máte sdílené sestavení odkazuje více funkcí rozhraní .net, můžete ji umístěte do běžné sdílené složky. Toto sestavení s podobně jako v následujícím příkladu příkaz odkazu, pokud pomocí jazyka C# skriptů (.csx): 

    #r "..\Shared\MyAssembly.dll". 

Jinak je snadno omylem nasadit více testovací verzí binární stejné s odlišným mezi funkce.

Nepoužívejte podrobné protokolování v produkčním kódu. Má negativním dopadem na výkon.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Použít asynchronní kód, ale zabránění blokování volání

Asynchronní programování je součástí osvědčeného postupu. Vždy-li však odkazující na `Result` vlastnost nebo volání `Wait` metodu `Task` instance. Tento přístup může vést k vyčerpání přístup z více vláken.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Příjem zpráv ve službě batch, kdykoli je to možné

Některé aktivačních událostí jako centra událostí povolit příjem dávku zpráv v jednom volání.  Dávkování zpráv má mnohem lepší výkon.  Můžete nakonfigurovat velikost maximální dávky v `host.json` podle popisu v souboru [host.json referenční dokumentaci k nástroji](functions-host-json.md)

Pro funkce C# můžete změnit typ k poli silného typu.  Například místo z `EventData sensorEvent` může být podpis metody `EventData[] sensorEvent`.  Pro jiné jazyky budete potřebovat explicitně nastavit vlastnost Kardinalita ve vaší `function.json` k `many` Chcete-li povolit dávkování [jak je vidět tady](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurace chování hostitele pro lepší zpracování souběžnosti

`host.json` Souboru v aplikaci funkce umožňuje konfigurovat chování hostitele modulu runtime a aktivační události.  Kromě dávkování chování, můžete spravovat souběžnosti pro počet aktivační události.  Často úpravě hodnoty v těchto možností může pomoci každé instance škálování pro požadavky vyvolaná funkcí.

Použít nastavení v souboru hostitelů mezi všechny funkce v aplikaci, v *jediné instance* funkce. Například pokud jste měli aplikaci funkce s 2 funkce protokolu HTTP a souběžných požadavků na 25, požadavek na buď triggeru protokolu HTTP bude započítávat sdílené 25 souběžných požadavků.  Pokud tuto aplikaci funkce škálovat na 10 instancí, 2 funkce by efektivně povolit 250 souběžných požadavků (10 instancí * 25 souběžných požadavků na každou instanci).

**Možnosti hostitele souběžnosti protokolu HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Můžete najít další možnosti konfigurace hostitele [v dokumentu konfigurace hostitele](functions-host-json.md).

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

Protože Azure Functions využívá Azure App Service také měli být vědomi pokyny služby App Service.
* [Patterns and Practices optimalizací výkonu protokolu HTTP](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)

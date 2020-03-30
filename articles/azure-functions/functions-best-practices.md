---
title: Doporučené postupy pro funkce Azure
description: Seznamte se s doporučenými postupy a vzory pro funkce Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277775"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimalizace výkonu a spolehlivosti Azure Functions

Tento článek obsahuje pokyny ke zlepšení výkonu a spolehlivosti aplikací [bez serveru.](https://azure.microsoft.com/solutions/serverless/)  

## <a name="general-best-practices"></a>Obecné osvědčené postupy

Níže jsou uvedeny osvědčené postupy při vytváření a navrhování řešení bez serveru pomocí Azure Functions.

### <a name="avoid-long-running-functions"></a>Vyhněte se dlouho běžícím funkcím

Velké, dlouhotrvající funkce může způsobit neočekávané problémy s časovým časem. Další informace o časových časech pro daný hostingový plán najdete v [tématu doba trvání časového času aplikace funkce](functions-scale.md#timeout). 

Funkce může být velký z důvodu mnoha závislostí Node.js. Import závislostí může také způsobit delší dobu načítání, které vedou k neočekávaným časovým časům. Závislosti jsou načteny explicitně i implicitně. Jeden modul načtený vaším kódem může načíst vlastní další moduly. 

Kdykoli je to možné, refaktorovat velké funkce do menších sad funkcí, které spolupracují a rychle vrátí odpovědi. Například funkce aktivační události webhooku nebo HTTP může vyžadovat odpověď na potvrzení v určitém časovém limitu; je běžné, že webhooky vyžadují okamžitou reakci. Datové části aktivační události HTTP můžete předat do fronty, která má být zpracována funkcí aktivační události fronty. Tento přístup umožňuje odložit skutečnou práci a vrátit okamžitou odpověď.


### <a name="cross-function-communication"></a>Mezifunkční komunikace

[Odolné funkce](durable/durable-functions-overview.md) a [Aplikace logiky Azure](../logic-apps/logic-apps-overview.md) jsou vytvořené pro správu přechodů stavu a komunikace mezi více funkcemi.

Pokud nepoužíváte trvalé funkce nebo logické aplikace pro integraci s více funkcemi, je nejlepší použít fronty úložiště pro komunikaci mezi funkcemi. Hlavním důvodem je, že fronty úložiště jsou levnější a mnohem jednodušší než jiné možnosti úložiště. 

Jednotlivé zprávy ve frontě úložiště jsou omezeny na 64 kB. Pokud potřebujete předat větší zprávy mezi funkcemi, fronta Azure Service Bus se dá použít k podpoře velikosti zpráv až 256 kB na úrovni Standard a až 1 MB na úrovni Premium.

Témata služby Service Bus jsou užitečná, pokud před zpracováním požadujete filtrování zpráv.

Centra událostí jsou užitečná pro podporu komunikace s velkým objemem.


### <a name="write-functions-to-be-stateless"></a>Psát funkce bezstavové 

Funkce by měly být bezstavové a idempotentní, pokud je to možné. Přidružte k datům všechny požadované informace o stavu. Například zpracovávaná objednávka by pravděpodobně `state` měla přidružený člen. Funkce může zpracovat pořadí na základě tohoto stavu, zatímco samotná funkce zůstává bezstavová. 

Idempotentní funkce jsou zvláště doporučovány s časovačem spouští. Například, pokud máte něco, co absolutně musí běžet jednou denně, napište to tak, aby mohl běžet kdykoliběhem dne se stejnými výsledky. Funkce může být ukončena, když není práce pro určitý den. Také v případě, že předchozí spuštění se nepodařilo dokončit, další spuštění by mělo pokračovat tam, kde skončilo.


### <a name="write-defensive-functions"></a>Psaní obranných funkcí

Předpokládejme, že vaše funkce může kdykoli dojít k výjimce. Navrhněte své funkce s možností pokračovat z předchozího bodu selhání během dalšího spuštění. Zvažte scénář, který vyžaduje následující akce:

1. Dotaz na 10 000 řádků v databázi.
2. Vytvořte zprávu fronty pro každý z těchto řádků pro zpracování dále po řádku.
 
V závislosti na tom, jak složitý je váš systém, můžete mít: zapojené navazující služby se chovají špatně, výpadky sítě nebo kvóty dosažené atd. To vše může mít vliv na vaši funkci kdykoliv. Musíte navrhnout své funkce, abyste na to byli připraveni.

Jak váš kód reagovat, pokud dojde k selhání po vložení 5 000 těchto položek do fronty pro zpracování? Sledujte položky v sadě, kterou jste dokončili. V opačném případě je můžete příště vložit znovu. Toto dvojité vložení může mít vážný dopad na váš pracovní tok, takže [vaše funkce idempotentní](functions-idempotent.md). 

Pokud již byla položka fronty zpracována, povolte, aby vaše funkce byla bez operace.

Využijte výhod obranných opatření, která jsou už k dispozici pro součásti, které používáte na platformě Azure Functions. Například najdete **v tématu zpracování zpráv fronty poison** v dokumentaci pro Azure Storage Queue aktivační události a [vazby](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Doporučené postupy škálovatelnosti

Existuje celá řada faktorů, které ovlivňují, jak se mění velikost instancí aplikace pro funkci. Podrobnosti jsou uvedeny v dokumentaci pro [změnu měřítka funkce](functions-scale.md).  Níže jsou uvedeny některé osvědčené postupy k zajištění optimální škálovatelnosti aplikace funkce.

### <a name="share-and-manage-connections"></a>Sdílení a správa připojení

Pokud je to možné, znovu použijte připojení k externím prostředkům. Podívejte [se, jak spravovat připojení v Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Vyhněte se sdílení účtů úložiště

Když vytvoříte aplikaci funkce, musíte ji přidružit k účtu úložiště. Připojení účtu úložiště se udržuje v [nastavení aplikace AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nemíchejte testovací a produkční kód ve stejné aplikaci funkcí

Funkce v rámci funkce aplikace sdílet prostředky. Například paměť je sdílena. Pokud používáte aplikaci funkcí v produkčním prostředí, nepřidávejte do ní funkce a prostředky související s testováním. Může způsobit neočekávané režie během provádění výrobního kódu.

Dávejte pozor, co načtete v aplikacích produkčních funkcí. Paměť je zprůměrována napříč jednotlivými funkcemi v aplikaci.

Pokud máte sdílené sestavení odkazované ve více funkcích rozhraní .NET, vložte jej do společné sdílené složky. V opačném případě by mohlo omylem nasadit více verzí stejného binárního souboru, které se chovají odlišně mezi funkcemi.

Nepoužívejte podrobné protokolování v produkčním kódu, který má negativní vliv na výkon.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Použití asynchronního kódu, ale vyhněte se blokování hovorů

Asynchronní programování je doporučeným osvědčeným postupem, zejména při blokování vstupně-va operací.

V C# vždy vyhněte odkazování na `Result` vlastnost nebo volání `Wait` metody na instanci. `Task` Tento přístup může vést k vyčerpání závitu.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Použití více pracovních procesů

Ve výchozím nastavení používá jakákoli instance hostitele pro funkce jeden pracovní proces. Chcete-li zvýšit výkon, zejména s jednovláknovými runtimes, jako je Python, použijte [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) zvýšit počet pracovních procesů na hostitele (až 10). Azure Functions se pak pokusí rovnoměrně distribuovat souběžné vyvolání funkcí napříč těmito pracovníky. 

FUNCTIONS_WORKER_PROCESS_COUNT platí pro každého hostitele, který funkce vytvoří při škálování z vaší aplikace tak, aby splňovaly požadavky. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Přijímat zprávy v dávce, kdykoli je to možné

Některé aktivační události, jako je Event Hub povolit příjem dávky zpráv na jedno vyvolání.  Dávkování zpráv má mnohem lepší výkon.  Maximální velikost dávky v `host.json` souboru můžete nakonfigurovat, jak je podrobně popsáno v [referenční dokumentaci host.json.](functions-host-json.md)

Pro funkce jazyka C# můžete změnit typ na pole silného typu.  Například místo `EventData sensorEvent` podpisu metody `EventData[] sensorEvent`může být .  Pro ostatní jazyky, budete muset explicitně nastavit mohutnost vlastnost ve vašem `function.json` chcete-li `many` povolit dávkování, jak je [znázorněno zde](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurace chování hostitele pro lepší zpracování souběžnosti

Soubor `host.json` v aplikaci funkce umožňuje konfiguraci běhu hostitele a chování aktivační události.  Kromě dávkování chování, můžete spravovat souběžnost pro počet aktivačních událostí. Často úprava hodnot v těchto možnostech může pomoci každé instanci škálovat odpovídajícím způsobem pro požadavky vyvolaných funkcí.

Nastavení v souboru host.json platí pro všechny funkce v rámci aplikace, v rámci *jedné instance* funkce. Například pokud jste měli aplikaci funkce [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) se dvěma funkcemi PROTOKOLU HTTP a požadavky nastaveny na 25, požadavek na aktivační událost PROTOKOLU HTTP by se započítával do sdílených 25 souběžných požadavků.  Pokud je tato aplikace funkce škálována na 10 instancí, dvě funkce efektivně umožňují 250 souběžných požadavků (10 instancí * 25 souběžných požadavků na instanci). 

Další možnosti konfigurace hostitele naleznete v [článku konfigurace host.json](functions-host-json.md).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Jak spravovat připojení ve funkcích Azure](manage-connections.md)
* [Doporučené postupy azure app služby](../app-service/app-service-best-practices.md)

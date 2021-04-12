---
title: Osvědčené postupy pro Azure Functions
description: Seznamte se s osvědčenými postupy a vzory pro Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378243"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Osvědčené postupy pro výkon a spolehlivost Azure Functions

Tento článek poskytuje pokyny pro zlepšení výkonu a spolehlivosti aplikací pracujících s bez [serveru](https://azure.microsoft.com/solutions/serverless/) .  

Níže jsou uvedené osvědčené postupy při sestavování a architekti řešení bez serveru pomocí Azure Functions.

## <a name="avoid-long-running-functions"></a>Nepoužívejte dlouho běžící funkce

Velké a dlouho běžící funkce můžou způsobit neočekávané problémy s časovým limitem. Další informace o časových limitech pro daný plán hostování najdete v tématu [Doba trvání časového limitu aplikace Function App](functions-scale.md#timeout).

Funkce může být velká z důvodu řady Node.jsch závislostí. Import závislostí může také způsobit delší dobu načítání, která vede k neočekávaným časovým limitům. Závislosti jsou načítány explicitně i implicitně. Jeden modul načtený vaším kódem může načíst vlastní další moduly.

Kdykoli je to možné, refaktorujte velké funkce na menší sady funkcí, které fungují společně, a rychle vrátí odpovědi. Například Webhook nebo funkce triggeru HTTP může vyžadovat odpověď potvrzení v určitém časovém limitu. pro Webhooky je běžné, že vyžadují okamžitou reakci. Datovou část triggeru HTTP můžete předat do fronty, aby ji bylo možné zpracovat funkcí triggeru fronty. Tento přístup umožňuje odložit skutečnou práci a vrátit okamžitou odezvu.

## <a name="cross-function-communication"></a>Komunikace mezi funkcemi

[Durable Functions](durable/durable-functions-overview.md) a [Azure Logic Apps](../logic-apps/logic-apps-overview.md) jsou sestaveny pro správu přechodů stavu a komunikaci mezi více funkcemi.

Pokud při integraci s více funkcemi nepoužíváte Durable Functions nebo Logic Apps, je nejlepší pro komunikaci mezi funkcemi používat fronty úložiště. Hlavním důvodem je, že fronty úložiště mají levnější a mnohem snazší zřizování než jiné možnosti úložiště.

Jednotlivé zprávy ve frontě úložiště mají omezení velikosti až 64 KB. Pokud potřebujete předat větší zprávy mezi funkcemi, Azure Service Bus frontu můžete použít k podpoře velikosti zpráv až do 256 KB na úrovni Standard a až 1 MB na úrovni Premium.

Service Bus témata jsou užitečná v případě, že před zpracováním potřebujete filtrování zpráv.

Centra událostí jsou užitečná pro podporu komunikace s vysokými objemy.

## <a name="write-functions-to-be-stateless"></a>Psaní funkcí, které mají být bezstavové

Funkce by měly být bezstavové a idempotentní, pokud je to možné. Přidružte ke svým datům požadované informace o stavu. Například zpracování objednávky by pravděpodobně mělo přidruženého `state` člena. Funkce může zpracovat objednávku na základě tohoto stavu, zatímco samotná funkce zůstane Bezstavová.

Funkce idempotentní jsou obzvláště Doporučené s triggery časovače. Například pokud máte něco, co naprosto musí běžet jednou denně, zapište ho, aby mohl běžet kdykoli během dne se stejnými výsledky. Funkce může skončit, i když pro určitý den nebude fungovat žádná práce. I v případě, že se nepovedlo dokončit předchozí spuštění, mělo by se další spuštění vystavit tam, kde skončila.

## <a name="write-defensive-functions"></a>Zápis funkcí obrannou linií

Předpokládejme, že vaše funkce může kdykoli narazit na výjimku. Navrhněte své funkce s možností pokračovat z předchozího bodu selhání během příštího spuštění. Vezměte v úvahu scénář, který vyžaduje následující akce:

1. Dotaz na 10 000 řádků v databázi
2. Vytvořte zprávu fronty pro každý z těchto řádků, která bude zpracována dále v řádku.

V závislosti na tom, jak komplexní je váš systém, možná budete mít k dispozici tyto služby, které se budou chovat chybou, výpadky sítě nebo dosažené limity kvót atd. Všechny tyto funkce mohou mít na funkci kdykoli vliv. Je potřeba navrhnout vaše funkce, které se na ni budou připravovat.

Jak váš kód reaguje, když po vložení 5 000 těchto položek do fronty ke zpracování dojde k chybě? Sledujte položky v sadě, kterou jste dokončili. V opačném případě je můžete vložit znovu později. Toto dvojité vložení může mít vážný dopad na pracovní tok, takže [idempotentní funkce](functions-idempotent.md). 

Pokud byla položka fronty již zpracována, povolte funkci no-op.

Využijte výhod obrannou liniích opatření, která už jsou k dispozici pro komponenty, které používáte v Azure Functions platformě. Například viz **zpracování zpráv o nepoškozených frontách** v dokumentaci pro [aktivační události a vazby fronty Azure Storage](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Osvědčené postupy pro organizaci funkcí

V rámci vašeho řešení můžete vyvíjet a publikovat více funkcí. Tyto funkce jsou často zkombinovány do jediné aplikace Function App, ale mohou být také spuštěny v samostatných aplikacích Function App. V plánech hostování na úrovni Premium a vyhrazené (App Service) můžou několik aplikací Function App sdílet stejné prostředky také spuštěním ve stejném plánu. Způsob seskupení funkcí a aplikací funkcí může mít vliv na výkon, škálování, konfiguraci, nasazení a zabezpečení vašeho celkového řešení. Neexistují pravidla, která se vztahují na všechny scénáře, takže při plánování a vývoji vašich funkcí zvažte informace v této části.

### <a name="organize-functions-for-performance-and-scaling"></a>Uspořádání funkcí pro výkon a škálování

Každá vytvořená funkce má paměťové nároky. I když je tato činnost obvykle nízká, může mít příliš mnoho funkcí v rámci aplikace Function App způsobit pomalejší spuštění aplikace na nových instancích. Také to znamená, že celkové využití paměti aplikace Function App může být vyšší. Je těžké říci, kolik funkcí by mělo být v jedné aplikaci, což závisí na konkrétním zatížení. Pokud však vaše funkce ukládá velké množství dat do paměti, zvažte, že v jedné aplikaci máte méně funkcí.

Pokud spouštíte více aplikací funkcí v rámci jednoho plánu Premium nebo vyhrazeného (App Serviceho) plánu, všechny tyto aplikace se škálují společně. Pokud máte jednu aplikaci funkcí, která má mnohem větší požadavky na paměť než ostatní, používá v každé instanci, do které je aplikace nasazená, neúměrné množství paměťových prostředků. Vzhledem k tomu, že to může pro ostatní aplikace na jednotlivých instancích zůstat nedostupné méně paměti, můžete chtít spustit aplikaci Function App s vysokou pamětí, jako je to ve vlastním samostatném plánu hostování.

> [!NOTE]
> Při použití [plánu spotřeby](./functions-scale.md)doporučujeme, abyste každou aplikaci vždycky umístili do svého vlastního plánu, protože aplikace se pořád škálovat nezávisle.

Zvažte, zda chcete seskupit funkce s různými profily zatížení. Například pokud máte funkci, která zpracovává mnoho tisíc zpráv fronty a druhý, který je používán pouze občas, ale má vysoké nároky na paměť, můžete je chtít nasadit v samostatných aplikacích funkcí, aby získaly vlastní sady prostředků a škálovat nezávisle na sobě.

### <a name="organize-functions-for-configuration-and-deployment"></a>Uspořádání funkcí pro konfiguraci a nasazení

Aplikace Function App mají `host.json` soubor, který se používá ke konfiguraci pokročilého chování triggerů funkcí a Azure Functions modulu runtime. Změny souboru se `host.json` vztahují na všechny funkce v rámci aplikace. Pokud máte některé funkce, které vyžadují vlastní konfigurace, zvažte jejich přesun do své vlastní aplikace Function App.

Všechny funkce v místním projektu se nasazují společně jako sada souborů do aplikace Function App v Azure. Jednotlivé funkce možná budete muset nasadit samostatně nebo můžete použít funkce, jako jsou [sloty nasazení](./functions-deployment-slots.md) pro některé funkce, a ne jiné. V takových případech byste tyto funkce měli nasadit (v samostatných kódových projektech) do různých aplikací Function App.

### <a name="organize-functions-by-privilege"></a>Uspořádání funkcí podle oprávnění

Připojovací řetězce a další přihlašovací údaje uložené v nastavení aplikace poskytují všem funkcím aplikace Function App stejnou sadu oprávnění v přidruženém prostředku. Pomocí přesunu funkcí, které tyto přihlašovací údaje nepoužívají, do samostatné aplikace Function App zvažte minimalizaci počtu funkcí s přístupem k určitým přihlašovacím údajům. K předávání dat mezi funkcemi v různých aplikacích funkcí můžete vždy použít techniky, jako je například [řetězení funkcí](/learn/modules/chain-azure-functions-data-using-bindings/) .  

## <a name="scalability-best-practices"></a>Osvědčené postupy škálovatelnosti

Existuje několik faktorů, které mají vliv na to, jak se instance aplikace Functions škálují. Podrobnosti jsou uvedeny v dokumentaci pro [škálování funkce](functions-scale.md).  Níže najdete některé osvědčené postupy pro zajištění optimální škálovatelnosti aplikace Function App.

### <a name="share-and-manage-connections"></a>Sdílení a Správa připojení

Kdykoliv je to možné, znovu použijte připojení k externím prostředkům. Další informace najdete [v tématu Správa připojení v Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Vyhněte se sdílení účtů úložiště

Když vytvoříte aplikaci Function App, musíte ji přidružit k účtu úložiště. Připojení k účtu úložiště se udržuje v [nastavení aplikace AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage).

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nemíchejte testovací a produkční kód ve stejné aplikaci Function App

Funkce v rámci sdílených prostředků aplikace Function App. Například paměť je sdílená. Pokud používáte aplikaci funkcí v produkčním prostředí, nepřidávejte do ní funkce související s testováním a prostředky. Může způsobit neočekávanou režii během provádění produkčního kódu.

Buďte opatrní, co nahráváte do aplikací produkčních funkcí. Průměrná velikost paměti napříč všemi funkcemi v aplikaci.

Pokud máte sdílené sestavení odkazované více funkcemi .NET, vložte ho do společné sdílené složky. V opačném případě byste mohli omylně nasadit více verzí stejného binárního souboru, které se chovají odlišně mezi funkcemi.

Nepoužívejte podrobné protokolování v produkčním kódu, který má negativní dopad na výkon.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Použít asynchronní kód, ale vyhnout se blokování volání

Asynchronní programování je doporučený osvědčený postup, zejména při zablokování vstupně-výstupních operací.

V jazyce C# vždy vyhněte odkazování na `Result` vlastnost nebo volání `Wait` metody v `Task` instanci. Tento přístup může vést k vyčerpání vlákna.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Použití více pracovních procesů

Ve výchozím nastavení používá každá instance hostitele pro funkce jeden pracovní proces. Chcete-li zvýšit výkon, zejména pomocí prostředí runtime s jedním vláknem, jako je Python, použijte [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) ke zvýšení počtu pracovních procesů na hostitele (až 10). Azure Functions se pak pokusí rovnoměrně distribuovat souběžná volání funkcí mezi tyto pracovní procesy.

FUNCTIONS_WORKER_PROCESS_COUNT se vztahuje na každého hostitele, který funkce vytvoří při horizontálním navýšení kapacity aplikace, aby splňovala požadavky.

### <a name="receive-messages-in-batch-whenever-possible"></a>Kdykoli je to možné, přijímat zprávy v dávce

Některé triggery, jako je centrum událostí, umožňují příjem dávky zpráv na jednom volání.  Dávkování zpráv má mnohem lepší výkon.  Maximální velikost dávky v souboru můžete nakonfigurovat `host.json` tak, jak je popsáno v [host.jsv referenční dokumentaci](functions-host-json.md) .

U funkcí jazyka C# lze typ změnit na pole silného typu.  Například namísto `EventData sensorEvent` signatury metody může být `EventData[] sensorEvent` .  Pro jiné jazyky budete muset explicitně nastavit vlastnost mohutnosti v sadě na, aby `function.json` `many` bylo možné dávkování povolit [, jak je znázorněno zde](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurace chování hostitelů pro lepší zpracování souběžnosti

`host.json`Soubor v aplikaci Function App umožňuje konfiguraci chování hostitele a spuštění.  Kromě dávkování chování můžete spravovat souběžnost pro určitý počet triggerů. Často se upravují hodnoty v těchto možnostech, které mohou pokaždé škálovat každou instanci odpovídajícím způsobem pro požadavky vyvolaných funkcí.

Nastavení v souboru host.json se aplikují napříč všemi funkcemi v rámci aplikace v rámci *jedné instance* funkce. Pokud máte například aplikaci funkcí se dvěma funkcemi HTTP a [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) požadavky nastavenými na 25, požadavek na Trigger http by se nacházel do sdíleného 25 souběžných požadavků.  Když se tato aplikace Functions škáluje na 10 instancí, deset funkcí efektivně umožní 250 souběžných požadavků (10 instancí × 25 souběžných požadavků na instanci). 

Další možnosti konfigurace hostitele najdete v [host.jsčlánku o konfiguraci](functions-host-json.md).

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

* [Správa připojení v Azure Functions](manage-connections.md)
* [Azure App Service osvědčené postupy](../app-service/app-service-best-practices.md)

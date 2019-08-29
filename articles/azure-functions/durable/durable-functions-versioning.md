---
title: Správa verzí v Durable Functions – Azure
description: Naučte se implementovat správu verzí v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: ef64a43cbed7f033a938351506b7f78142ff044c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097618"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Správa verzí v Durable Functions (Azure Functions)

Je nevyhnutelné, že funkce budou přidány, odebrány a změněny po dobu života aplikace. [Durable Functions](durable-functions-overview.md) umožňuje zřetězení funkcí společně s možnostmi, které nebyly dřív možné, a toto zřetězení ovlivňuje způsob, jakým můžete zvládnout správu verzí.

## <a name="how-to-handle-breaking-changes"></a>Postup zpracování nejnovějších změn

K dispozici je několik příkladů zásadních změn, o kterých byste měli vědět. Tento článek popisuje nejběžnější. Hlavním motivem v celém z nich je, že nové i existující orchestrace funkcí jsou ovlivněny změnami v kódu funkce.

### <a name="changing-activity-function-signatures"></a>Změna signatur funkcí aktivity

Změna podpisu odkazuje na změnu názvu, vstupu nebo výstupu funkce. Pokud je tento typ změny proveden na funkci aktivity, může dojít k přerušení funkce Orchestrator, která na ní závisí. Pokud aktualizujete funkci Orchestrator tak, aby odpovídala této změně, můžete přerušit existující instance v letadlech.

Předpokládejme například, že máme následující funkci.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato funkce zjednodušený přijímá výsledky **foo** a předává je do **pruhů**. Řekněme, že potřebujeme změnit vrácenou hodnotu **foo** z `bool` na `int` na, aby podporovala širší škálu výsledných hodnot. Výsledek bude vypadat takto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato změna funguje pro všechny nové instance funkce Orchestrator, ale přerušuje jakékoli služby v letadle. Například je třeba vzít v úvahu případ, kde instance orchestrace volá **foo**, vrátí logickou hodnotu a pak kontrolní body. Pokud je změna podpisu nasazena v tomto okamžiku, kontrolní instance selže okamžitě po obnovení a znovu spustí volání `context.CallActivityAsync<int>("Foo")`. Důvodem je, že výsledek v tabulce historie je `bool` ale nový kód se pokusí ho deserializovat do. `int`

Toto je pouze jeden z mnoha různých způsobů, kterými změna podpisu může přerušit stávající instance. Obecně platí, že pokud nástroj Orchestrator potřebuje změnit způsob, jakým volá funkci, pak změna bude pravděpodobně problematická.

### <a name="changing-orchestrator-logic"></a>Změna logiky nástroje Orchestrator

Druhá třída problémů se správou verzí přichází ze změny kódu funkce nástroje Orchestrator způsobem, který zaplní logiku opětovného přehrání pro instance v letadle.

Vezměte v úvahu následující funkci Orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nyní předpokládejme, že chcete provést zdánlivě Innocent změnu pro přidání dalšího volání funkce.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

Tato změna přidá nové volání funkce **SendNotification** mezi **foo** a **bar**. Neexistují žádné změny podpisu. K tomuto problému dochází, když existující instance obnoví volání na **bar**. Při opakovaném přehrání, pokud se vrátí `true`původní volání foo, pak bude znovu přehrání nástroje Orchestrator volat do **SendNotification** , které není v historii spuštění. Výsledkem je, že rozhraní odolného úlohy se nezdařila s a `NonDeterministicOrchestrationException` , protože při očekávaném volání na **panel**se objevilo volání **SendNotification** .

## <a name="mitigation-strategies"></a>Strategie zmírňování

Tady jsou některé strategie pro práci s problémy se správou verzí:

* Nedělat nic
* Zastavit všechny instance v letadle
* Souběžná nasazení

### <a name="do-nothing"></a>Nedělat nic

Nejjednodušší způsob, jak zvládnout zásadní změnu, je nechat instance orchestrace v letadlech neúspěšné. Nové instance úspěšně spustily změněný kód.

Bez ohledu na to, jestli se jedná o problém, závisí na důležitosti vašich leteckých instancí. Pokud pracujete v aktivním vývoji a nezáleží na tom, jestli jde o instance v letadle, může to být dostatečné. V kanálu diagnostiky ale budete muset zabývat s výjimkami a chybami. Pokud se chcete těmto akcím vyhnout, zvažte další možnosti správy verzí.

### <a name="stop-all-in-flight-instances"></a>Zastavit všechny instance v letadle

Další možností je zastavit všechny letecké instance. To se dá udělat vymazáním obsahu fronty interního **řízení** a pracovní položky a **fronty** . Instance se budou trvale zablokovat tam, kde jsou, ale nebudou mít k disřádek žádnou telemetrii se zprávami o chybách. To je ideální v rychlém vývoji prototypů.

> [!WARNING]
> Podrobnosti o těchto frontách se můžou v průběhu času měnit, takže se na tuto techniku nespoléhá pro produkční úlohy.

### <a name="side-by-side-deployments"></a>Souběžná nasazení

Nejpravděpodobnější způsob, jak zajistit, aby se změny v bezpečném nasazení nezdařily, je nasazením souběžně se staršími verzemi. To lze provést pomocí některého z následujících postupů:

* Všechny aktualizace nasaďte jako zcela nové funkce (nové názvy).
* Nasaďte všechny aktualizace jako novou aplikaci Function App s jiným účtem úložiště.
* Nasaďte novou kopii aplikace Function App, ale s aktualizovaným `TaskHub` názvem. Toto je doporučený postup.

### <a name="how-to-change-task-hub-name"></a>Postup změny názvu centra úloh

Centrum úloh lze v souboru *Host. JSON* nakonfigurovat následujícím způsobem:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

Výchozí hodnota je `DurableFunctionsHub`.

Všechny entity Azure Storage jsou pojmenovány na `HubName` základě hodnoty konfigurace. Když zadáte novému centru úkolů nový název, zajistíte, aby se pro novou verzi vaší aplikace vytvořily samostatné fronty a tabulky historie.

Doporučujeme nasadit novou verzi aplikace Function App do nového slotu pro [nasazení](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Sloty nasazení umožňují souběžně spustit více kopií aplikace Function App s jedním z nich, jako je aktivní *produkční* slot. Až budete připraveni vystavit novou logiku orchestrace pro stávající infrastrukturu, může to být jednoduché jako záměna nové verze do produkčního slotu.

> [!NOTE]
> Tato strategie funguje nejlépe při použití aktivačních událostí HTTP a Webhooku pro funkce Orchestrator. U triggerů bez protokolu HTTP, jako jsou fronty nebo Event Hubs, by definice triggeru měla [odvozovat z nastavení aplikace](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , které se v rámci operace swapu aktualizuje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se zvládnout problémy s výkonem a škálováním.](durable-functions-perf-and-scale.md)

---
title: Správa verzí v Durable Functions – Azure
description: Naučte se implementovat správu verzí v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74232761"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Správa verzí v Durable Functions (Azure Functions)

Je nevyhnutelné, že funkce budou přidány, odebrány a změněny po dobu života aplikace. [Durable Functions](durable-functions-overview.md) umožňuje zřetězení funkcí společně s možnostmi, které nebyly dřív možné, a toto zřetězení ovlivňuje způsob, jakým můžete zvládnout správu verzí.

## <a name="how-to-handle-breaking-changes"></a>Postup zpracování nejnovějších změn

K dispozici je několik příkladů zásadních změn, o kterých byste měli vědět. Tento článek popisuje nejběžnější. Hlavním motivem v celém z nich je, že nové i existující orchestrace funkcí jsou ovlivněny změnami v kódu funkce.

### <a name="changing-activity-or-entity-function-signatures"></a>Změna signatur funkcí aktivity nebo entity

Změna podpisu odkazuje na změnu názvu, vstupu nebo výstupu funkce. Pokud je tento typ změny proveden u aktivity nebo funkce entity, může dojít k přerušení jakékoli funkce nástroje Orchestrator, která na ní závisí. Pokud aktualizujete funkci Orchestrator tak, aby odpovídala této změně, můžete přerušit existující instance v letadlech.

Předpokládejme například, že máme následující funkci Orchestrator.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato funkce zjednodušený přijímá výsledky **foo** a předává je do **pruhů**. Řekněme, že potřebujeme změnit vrácenou hodnotu **foo** z na na `bool` `int` , aby podporovala širší škálu výsledných hodnot. Výsledek vypadá takto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Předchozí příklady C# Target Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Tato změna funguje pro všechny nové instance funkce Orchestrator, ale přerušuje jakékoli služby v letadle. Zvažte například případ, kdy instance orchestrace volá funkci s názvem `Foo` , vrátí zpět logickou hodnotu a pak kontrolní body. Pokud je změna podpisu nasazena v tomto okamžiku, kontrolní instance selže okamžitě po obnovení a znovu spustí volání `context.CallActivityAsync<int>("Foo")` . K této chybě dochází, protože výsledek v tabulce historie je `bool` ale nový kód se pokusí ho deserializovat na `int` .

Tento příklad je jedním z mnoha různých způsobů, kterými změna podpisu může přerušit existující instance. Obecně platí, že pokud nástroj Orchestrator potřebuje změnit způsob, jakým volá funkci, pak změna bude pravděpodobně problematická.

### <a name="changing-orchestrator-logic"></a>Změna logiky nástroje Orchestrator

Druhá třída problémů se správou verzí přichází ze změny kódu funkce nástroje Orchestrator způsobem, který zaplní logiku opětovného přehrání pro instance v letadle.

Vezměte v úvahu následující funkci Orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nyní předpokládejme, že chcete provést zdánlivě Innocent změnu pro přidání dalšího volání funkce.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Předchozí příklady C# Target Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

Tato změna přidá nové volání funkce **SendNotification** mezi **foo** a **bar**. Neexistují žádné změny podpisu. K tomuto problému dochází, když existující instance obnoví volání na **bar**. Při opakovaném přehrání, pokud se vrátí původní volání **foo** `true` , pak bude znovu přehrání nástroje Orchestrator volat do **SendNotification**, které není v historii spuštění. Výsledkem je, že rozhraní odolného úlohy se nezdařila s a, `NonDeterministicOrchestrationException` protože při očekávaném volání na **panel**se objevilo volání **SendNotification** . Stejný typ problému může nastat při přidávání jakýchkoli volání do "trvanlivého" rozhraní API, včetně `CreateTimer` , `WaitForExternalEvent` atd.

## <a name="mitigation-strategies"></a>Strategie zmírňování

Tady jsou některé strategie pro práci s problémy se správou verzí:

* Nedělat nic
* Zastavit všechny instance v letadle
* Souběžná nasazení

### <a name="do-nothing"></a>Nedělat nic

Nejjednodušší způsob, jak zvládnout zásadní změnu, je nechat instance orchestrace v letadlech neúspěšné. Nové instance úspěšně spustily změněný kód.

Bez ohledu na to, jestli je tento druh selhání, záleží na důležitosti vašich leteckých instancí. Pokud pracujete v aktivním vývoji a nezáleží na tom, jestli jde o instance v letadle, může to být dostatečné. V kanálu diagnostiky ale budete muset zabývat s výjimkami a chybami. Pokud se chcete těmto akcím vyhnout, zvažte další možnosti správy verzí.

### <a name="stop-all-in-flight-instances"></a>Zastavit všechny instance v letadle

Další možností je zastavit všechny letecké instance. Zastavení všech instancí může probíhat vymazáním obsahu fronty interního **řízení** a fronty pracovní položky a **fronty** . Instance se budou trvale zablokovat tam, kde jsou, ale nebudou mít v protokolu žádné zprávy o chybách. Tento přístup je ideální v rychlém vývoji prototypů.

> [!WARNING]
> Podrobnosti o těchto frontách se můžou v průběhu času měnit, takže se na tuto techniku nespoléhá pro produkční úlohy.

### <a name="side-by-side-deployments"></a>Souběžná nasazení

Nejpravděpodobnější způsob, jak zajistit, aby se změny v bezpečném nasazení nezdařily, je nasazením souběžně se staršími verzemi. To lze provést pomocí některého z následujících postupů:

* Všechny aktualizace nasaďte jako zcela nové funkce a existující funkce tak budou fungovat tak, jak jsou. To může být obtížné, protože volající nových verzí funkcí se musí aktualizovat stejně jako stejné pokyny.
* Nasaďte všechny aktualizace jako novou aplikaci Function App s jiným účtem úložiště.
* Nasaďte novou kopii aplikace Function App se stejným účtem úložiště, ale s aktualizovaným `taskHub` názvem. Pro souběžná nasazení je doporučena technika.

### <a name="how-to-change-task-hub-name"></a>Postup změny názvu centra úloh

Centrum úloh lze nakonfigurovat v *host.js* souboru následujícím způsobem:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funkce 2,0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Výchozí hodnota pro Durable Functions v1. x je `DurableFunctionsHub` . Počínaje Durable Functions v 2.0 je výchozí název centra úloh stejný jako název aplikace Function App v Azure nebo `TestHubName` Pokud je spuštěný mimo Azure.

Všechny entity Azure Storage jsou pojmenovány na základě `hubName` hodnoty konfigurace. Když zadáte novému centru úkolů nový název, zajistíte, aby se pro novou verzi vaší aplikace vytvořily samostatné fronty a tabulky historie. Aplikace Function App ale zastaví zpracování událostí pro orchestraci nebo entity vytvořené v předchozím názvu centra úloh.

Doporučujeme nasadit novou verzi aplikace Function App do nového [slotu pro nasazení](../functions-deployment-slots.md). Sloty nasazení umožňují souběžně spustit více kopií aplikace Function App s jedním z nich, jako je aktivní *produkční* slot. Až budete připraveni vystavit novou logiku orchestrace pro stávající infrastrukturu, může to být jednoduché jako záměna nové verze do produkčního slotu.

> [!NOTE]
> Tato strategie funguje nejlépe při použití aktivačních událostí HTTP a Webhooku pro funkce Orchestrator. U triggerů bez protokolu HTTP, jako jsou fronty nebo Event Hubs, by definice triggeru měla [odvozovat z nastavení aplikace](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) , které se v rámci operace swapu aktualizuje.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se zvládnout problémy s výkonem a škálováním.](durable-functions-perf-and-scale.md)

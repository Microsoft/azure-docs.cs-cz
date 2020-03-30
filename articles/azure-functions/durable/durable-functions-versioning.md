---
title: Správa verzí v trvalých funkcích – Azure
description: Zjistěte, jak implementovat správu verzí v rozšíření Durable Functions pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 87cbb94dbab241630dc7585bdf4314d858d5b4da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232761"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Správa verzí v trvalých funkcích (funkce Azure)

Je nevyhnutelné, že funkce budou přidány, odebrány a změněny po celou dobu životnosti aplikace. [Odolné funkce](durable-functions-overview.md) umožňuje řetězení funkce dohromady způsoby, které nebyly dříve možné a toto řetězení ovlivňuje, jak můžete zvládnout správu verzí.

## <a name="how-to-handle-breaking-changes"></a>Jak zpracovat změny porušení

Existuje několik příkladů narušující změny být vědomi. Tento článek popisuje ty nejběžnější. Hlavním tématem za všechny z nich je, že nové i existující funkce orchestrations jsou ovlivněny změny kódu funkce.

### <a name="changing-activity-or-entity-function-signatures"></a>Změna podpisů funkcí aktivity nebo entity

Změna podpisu odkazuje na změnu názvu, vstupu nebo výstupu funkce. Pokud tento druh změny je provedena aktivity nebo funkce entity, může přerušit všechny funkce orchestrator, který závisí na něm. Pokud aktualizujete funkci orchestrator tak, aby vyhovovala této změně, můžete přerušit existující instance za letu.

Jako příklad předpokládejme, že máme následující funkci orchestrator.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato zjednodušující funkce přebírá výsledky **Foo** a předá ji **Bar**. Předpokládejme, že musíme změnit vrácenou hodnotu `int` **Foo** z `bool` na podporu širší škály hodnot výsledků. Výsledek vypadá takto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

> [!NOTE]
> Předchozí příklady jazyka C# cíldurable funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

Tato změna funguje pro všechny nové instance funkce orchestrator, ale přeruší všechny instance za letu. Zvažte například případ, kdy instance orchestrace `Foo`volá funkci s názvem , získá zpět logickou hodnotu a pak kontrolní body. Pokud je změna podpisu nasazena v tomto okamžiku, instance s kontrolním bodem `context.CallActivityAsync<int>("Foo")`se nezdaří okamžitě při obnovení a přehraje volání . K této chybě dochází, protože `bool` výsledek v tabulce historie je, ale `int`nový kód se pokusí rekonstruovat do .

Tento příklad je pouze jedním z mnoha různých způsobů, jak může změna podpisu přerušit existující instance. Obecně platí, že pokud orchestrator potřebuje změnit způsob, jakým volá funkci, pak změna je pravděpodobně problematické.

### <a name="changing-orchestrator-logic"></a>Změna logiky orchestrátoru

Ostatní třídy problémy s verzí pocházejí ze změny kódu funkce orchestrator způsobem, který zaměňuje logiku přehrání in-flight instance.

Zvažte následující funkci orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nyní předpokládejme, že chcete provést zdánlivě nevinnou změnu, abyste přidali další volání funkce.

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
> Předchozí příklady jazyka C# cíldurable funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

Tato změna přidá volání nové funkce **SendNotification** mezi **Foo** a **Bar**. Nejsou k dispozici žádné změny podpisu. Problém nastává, když existující instance obnoví volání **Bar**. Během přehrávání, pokud původní volání **Foo** vrátil `true`, pak orchestrator replay bude volat **sendnotification**, který není v jeho historii provádění. V důsledku toho trvalé rozhraní úloh `NonDeterministicOrchestrationException` nezdaří, protože došlo k volání **SendNotification,** když očekává, že zobrazení volání **bar**. Stejný typ problému může nastat při přidávání volání "trvalé" API, včetně `CreateTimer`, `WaitForExternalEvent`, atd.

## <a name="mitigation-strategies"></a>Strategie zmírňování dopadů

Zde jsou některé ze strategií pro řešení problémů s verzí:

* Nedělat nic
* Zastavení všech instancí za letu
* Souběžná nasazení

### <a name="do-nothing"></a>Nedělat nic

Nejjednodušší způsob, jak zpracovat narušující změnu, je nechat instance orchestrace za letu selhat. Nové instance úspěšně spustit změněný kód.

Zda tento druh selhání je problém závisí na důležitosti instancí in-flight. Pokud jste v aktivním vývoji a nestaráte se o instanci za letu, může to být dost dobré. Budete se však muset vypořádat s výjimkami a chybami v kanálu diagnostiky. Pokud se chcete těmto věcem vyhnout, zvažte další možnosti správy verzí.

### <a name="stop-all-in-flight-instances"></a>Zastavení všech instancí za letu

Další možností je zastavit všechny instancí za letu. Zastavení všech instancí lze provést vymazáním obsahu front vnitřní **ho řízení** a **fronty front pracovních položek.** Instance budou navždy přilepená, kde jsou, ale nebudou nepořádek protokoly s selhání zprávy. Tento přístup je ideální pro rychlý vývoj prototypů.

> [!WARNING]
> Podrobnosti o těchto frontách se mohou v průběhu času měnit, takže se nespoléhejte na tuto techniku pro produkční úlohy.

### <a name="side-by-side-deployments"></a>Souběžná nasazení

Nejvíce odolný proti selhání způsob, jak zajistit, že nejnovější změny jsou nasazeny bezpečně je nasazením je vedle sebe se staršími verzemi. To lze provést pomocí některéz následujících technik:

* Nasaďte všechny aktualizace jako zcela nové funkce a ponechte existující funkce tak, jak jsou. To může být složité, protože volající nové verze funkcí musí být aktualizovány také podle stejných pokynů.
* Nasaďte všechny aktualizace jako novou aplikaci funkcí s jiným účtem úložiště.
* Nasaďte novou kopii aplikace funkce se stejným účtem úložiště, ale s aktualizovaným `taskHub` názvem. Doporučená technika je souběžná nasazení.

### <a name="how-to-change-task-hub-name"></a>Jak změnit název centra úloh

Centrum úloh lze nakonfigurovat v souboru *host.json* následujícím způsobem:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "hubName": "MyTaskHubV2"
    }
}
```

#### <a name="functions-20"></a>Funkce 2.0

```json
{
    "extensions": {
        "durableTask": {
            "hubName": "MyTaskHubV2"
        }
    }
}
```

Výchozí hodnota pro trvalé funkce v1.x je `DurableFunctionsHub`. Spuštění v trvanlivé funkce v2.0, výchozí název centra úloh `TestHubName` je stejný jako název aplikace funkce v Azure, nebo pokud běží mimo Azure.

Všechny entity úložiště Azure jsou `hubName` pojmenovány na základě hodnoty konfigurace. Tím, že rozbočovač úloh nový název, zajistíte, že samostatné fronty a historie tabulky jsou vytvořeny pro novou verzi aplikace. Aplikace funkce však zastaví zpracování událostí pro orchestrations nebo entity vytvořené pod názvem předchozího centra úloh.

Doporučujeme nasadit novou verzi aplikace funkce do nového [slotu pro nasazení](../functions-deployment-slots.md). Sloty pro nasazení umožňují spouštět více kopií aplikace funkce vedle sebe, přičemž pouze jeden z nich je aktivní *produkční* slot. Až budete připraveni vystavit novou logiku orchestrace do stávající infrastruktury, může to být stejně jednoduché jako výměna nové verze do produkčního slotu.

> [!NOTE]
> Tato strategie funguje nejlépe při použití aktivačních událostí HTTP a webhooku pro funkce orchestratoru. Pro aktivační události bez protokolu HTTP, jako jsou fronty nebo centra událostí, definice aktivační události by měla [být odvozena z nastavení aplikace,](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings) které se aktualizuje jako součást operace odkládacího nastavení.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zjistěte, jak zvládat problémy s výkonem a škálování](durable-functions-perf-and-scale.md)

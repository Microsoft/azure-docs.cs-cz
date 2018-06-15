---
title: Správa verzí trvanlivý funkcí – Azure
description: Zjistěte, jak implementovat správy verzí v rozšíření trvanlivý funkce pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 0a86e4a87f5ec23c284aa4e5cfb2c67622b3ebe9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23838552"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Správa verzí trvanlivý funkcí (Azure Functions)

Je nevyhnutelné, že funkce bude přidat, odebrat a změnit během životního cyklu aplikace. [Trvanlivý funkce](durable-functions-overview.md) umožňuje řetězení společně funkce způsoby, které nebyly dřív, a tento řetězení ovlivňuje, jak zpracovávat správy verzí.

## <a name="how-to-handle-breaking-changes"></a>Postupy: zpracování nejnovější změny

Existuje několik příkladů, nejnovější změny znát. Tento článek popisuje nejobvyklejší z nich. Hlavní motiv za všechny z nich je i nové a stávající funkce orchestrations je postiženo změny kódu funkce.

### <a name="changing-activity-function-signatures"></a>Změna funkce podpisy aktivity

Podpis změnu odkazuje na změnu názvu, vstup nebo výstup funkce. Pokud se tento druh změn do funkce aktivity, může rozdělit funkce orchestrator, který na něm závisí. Pokud aktualizujete funkce orchestrator přizpůsobil této změně, by mohlo způsobit narušení existující instance během letu.

Jako příklad předpokládejme, že máme následující funkce.

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato funkce zneužívající vlastností prohlížeče přebírá výsledky **Foo** a předává jej do **panelu**. Předpokládejme, je potřeba změnit návratová hodnota **Foo** z `bool` k `int` pro podporu širší rozsah hodnoty výsledek. Výsledek vypadá takto:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    int result = await context.CallActivityAsync<int>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Tato změna funguje bez problémů pro všechny nové instance služby orchestrator funkce ale dělí všechny instance během letu. Představte si třeba v případě, kde orchestration instance volá **Foo**, získá zpět logickou hodnotu a pak kontrolní body. Pokud se podpis změnu v tomto okamžiku nasadí, kontrolní bod instance selže okamžitě, když se obnoví a replays volání `context.CallActivityAsync<int>("Foo")`. Důvodem je, že je výsledek v tabulce historie `bool` ale nový kód se pokusí rekonstruovat do `int`.

Toto je pouze jedním z mnoha různými způsoby, že ke změně podpis může dojít k narušení existující instance. Obecně platí Pokud orchestrator je potřeba změnit způsob zavolá funkci a potom změnu je pravděpodobně problematické.

### <a name="changing-orchestrator-logic"></a>Změna logiku orchestrator

Další třídu problémy se správou verzí pocházet z Změna kódu funkce orchestrator způsobem, který confuses opětovného přehrání logiku pro instance během letu.

Vezměte v úvahu následující funkce orchestrator:

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

Nyní předpokládejme chcete zdánlivě nevinnosti změňte přidat další volání funkce.

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

Tato změna přidá nové funkce volání na **SendNotification** mezi **Foo** a **panelu**. Neexistují žádné změny podpis. Vznikne problém, když se obnoví existující instanci z volání **panelu**. Během opětovného přehrání, pokud původní volat na **Foo** vrátil `true`, pak zavolá opětovného přehrání orchestrator **SendNotification** nenacházející se v historii provádění. V důsledku toho rozhraní trvanlivý úloh selže `NonDeterministicOrchestrationException` protože došlo volání **SendNotification** očekávaného zobrazíte volání **panelu**.

## <a name="mitigation-strategies"></a>Zmírnění dopadů strategie

Tady jsou některé strategie pro práci s problémy Správa verzí:

* Nic nestane.
* Zastavte všechny instance během letu
* Nasazení vedle sebe

### <a name="do-nothing"></a>Nic nestane.

Nejjednodušší způsob, jak zpracovat narušující změně je umožnit během letu orchestration instance selhala. Nové instance úspěšně proběhnout kód změněné.

Zda se jedná o problém závisí na významu vaše během letu instance. Pokud jsou v active vývoj a nezáleží během letu instancí, může to být dostatečně funkční. Musíte však řešit výjimky a chyby v kanálu vaší diagnostiky. Pokud chcete, aby se zabránilo tyto věci, zvažte další možnosti správy verzí.

### <a name="stop-all-in-flight-instances"></a>Zastavte všechny instance během letu

Další možností je zastavte všechny instance během letu. To lze provést tak, že smažete obsah interní **řízení fronty** a **pracovní položka fronty** fronty. Instance bude navždy zablokované, kde jsou, ale jejich nebude zbytečných souborů telemetrie s zprávy o selhání. To se hodí při vývoji rychlé prototypu.

> [!WARNING]
> Podrobnosti o tyto fronty může změnit v čase, takže nemusíte spoléhat na tato technika pro úlohy v produkčním prostředí.

### <a name="side-by-side-deployments"></a>Nasazení vedle sebe

Většina selhání ověření způsobem zajistit, aby byly bezpečně nasazené nejnovější změny je nasazením souběžného se staršími verzemi. To lze provést pomocí kteréhokoli z následujících postupů:

* Nasadit všechny aktualizace jako zcela nové funkce (nové názvy).
* Všechny aktualizace nasaďte jako novou aplikaci funkce s jiný účet úložiště.
* Nasadit nové kopie funkce aplikace, ale s aktualizované `TaskHub` název. Toto je doporučený postup.

### <a name="how-to-change-task-hub-name"></a>Jak změnit název centra úloh

Centrum úkolů lze konfigurovat v *host.json* následujícím způsobem:

```json
{
    "durableTask": {
        "HubName": "MyTaskHubV2"
    }
}
```

Výchozí hodnota je `DurableFunctionsHub`.

Všechny entity Azure Storage jsou pojmenované na základě `HubName` hodnota konfigurace. Tím, že nový název rozbočovače úloh, je zajistit, že samostatné fronty a tabulky historie jsou vytvořeny pro novou verzi vaší aplikace.

Doporučujeme nasadit nové verze aplikace funkce na nový [nasazovací Slot](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/). Nasazovací sloty umožňují spuštění více kopií vaší funkce aplikace-souběžného s pouze jedním z nich jako aktivní *produkční* slot. Jakmile budete připraveni ke zveřejnění nové logika orchestration pro stávající infrastruktury, může být stejně jednoduché jako odkládací nové verze na produkční slot.

> [!NOTE]
> Tato strategie funguje nejlíp, když používáte protokol HTTP a webhooku aktivační události pro orchestrator funkce. Pro aktivační události jiným protokolem než HTTP, jako je například fronty nebo Event Hubs definici aktivace by měl být odvozen z nastavení aplikace, který získá aktualizován v rámci operace prohození.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zjistěte, jak zpracovat výkonu a škálování problémy](durable-functions-perf-and-scale.md)

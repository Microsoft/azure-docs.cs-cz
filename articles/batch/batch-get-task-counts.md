---
title: Úloha průběh sledovat prostřednictvím počítání úlohy podle stavu – Azure Batch | Microsoft Docs
description: Monitorování průběhu úlohy voláním operace získat úloh spočítá počet úloh pro úlohu. Můžete získat počet aktivní, spuštěné a dokončené úlohy a úlohy, které mají byla úspěšná nebo neúspěšná.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: danlep
ms.openlocfilehash: bc112ed5b481560362962d6b550d336de6b3d9b4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312639"
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Počet úloh podle stavu můžete sledovat průběh úlohy (Preview)

Azure Batch poskytuje účinný způsob, jak monitorovat průběh úlohy při jeho spuštění její úkoly. Můžete volat [získat počty úloh] [ rest_get_task_counts] na zjistit, kolik úlohy jsou ve stavu aktivní, spuštěné nebo pokud byla dokončena a kolik mají operace byla úspěšná nebo neúspěšná. Určovat počet úloh v každém stavu, můžete snadno zobrazit průběh úlohy na uživatele nebo detekci neočekávané zpoždění nebo chybám, které může mít vliv na úlohu.

> [!IMPORTANT]
> Operace získání počty úloha je aktuálně ve verzi preview a dosud nejsou k dispozici v Azure Government, Čína Azure a Azure v Německu. 
>
>

## <a name="how-tasks-are-counted"></a>Jak se úlohy počítají

Operace získání úkolů počty počty úlohy podle stavu, následujícím způsobem:

- Úloha se počítá jako **active** při zařazených do fronty a moct spustit, ale není aktuálně přiřazen k výpočetním uzlu. Úloha se také počítá jako **active** Pokud závisí na nadřazený úkol, který zatím není dokončený. Další informace o závislosti úkolů najdete v tématu [vytvoření závislosti úkolů ke spouštění úloh, které jsou závislé na dalších úkolech,](batch-task-dependencies.md). 
- Úloha se počítá jako **systémem** když byl přiřazen do výpočetního uzlu, ale zatím není dokončený. Úloha se počítá jako **systémem** při její stav je buď `preparing` nebo `running`, jak je uvedené [získat informace o úkolu] [ rest_get_task] operaci.
- Úloha se počítá jako **Dokončit** Pokud již není vhodné spouštět. Úloha se počítají jako **Dokončit** má obvykle buď bylo dokončeno úspěšně, nebo je neúspěšně dokončené a také již vyčerpán limitu opakování. 

Operace získání počty úloh také sestavy, kolik úlohy mají byla úspěšná nebo neúspěšná. Batch Určuje, jestli má úloha byla úspěšná nebo neúspěšná kontrolou **výsledek** vlastností [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] vlastnost:

    - Úloha se počítá jako **úspěšné** Pokud je výsledek provádění úlohy `success`.
    - Úloha se počítá jako **se nezdařilo** Pokud je výsledek provádění úlohy `failure`.

Další informace o stavů úkolů najdete v tématu [získat informace o úkolu][rest_get_task].

Následující ukázka kódu .NET ukazuje, jak načíst úlohy počty podle stavu: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> Podobně jako vzor REST a jiné podporované jazyky slouží k získání počty úloh pro úlohu. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Pro počtů úloha kontroly konzistence

Služba Batch agreguje počty úloh ve shromažďování dat z více částí asynchronní distribuovaného systému. Chcete, aby počty úloh jsou správné, Batch poskytuje další ověření stavu počty provedením konzistence ověří proti více komponent systému. Batch provádí tyto kontroly konzistence, dokud nejsou méně než 200 000 úkoly v úloze. K nepravděpodobnému, kontrola konzistence zjistí chyby opraví Batch výsledek operace získání počty úlohy na základě výsledků kontroly konzistence. Kontrola konzistence je navíc opatřením zajistit, že zákazníci, kteří využívají operace získání počty úloh získat náležité informace, které potřebují kvůli své řešení.

**ValidationStatus** vlastnost v odpovědi udává, zda dávky byla provedena kontrola konzistence. Pokud Batch nebyla schopna provést kontrolu stavu započítává skutečné stavy uchovávat v systému, pak se **validationStatus** je nastavena na `unvalidated`. Z důvodů výkonu Batch nebude provádět kontrolu konzistence Pokud úloha obsahuje více než 200 000 úlohy, proto **validationStatus** vlastnost může být nastavena na `unvalidated` v tomto případě. Ale počet úloh není nutně nesprávný v takovém případě jako i ke ztrátě dat velmi omezená je vysoce nepravděpodobné. 

Při změně stavu úloha zpracuje kanálu agregace změně v rámci několik sekund. Operace získání úkolů počty odráží počty aktualizované úloh v rámci této doby. Ale pokud kanálu agregace nezdařených přístupů k zjištění změny ve stavu úloh, pak tato změna není registrovaný dokud průchodu další ověření. Během této doby počty úloh mohou být mírně nepřesné kvůli zmeškaných událostí, ale jsou opravena při průchodu další ověřování.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>Osvědčené postupy pro počítání úlohy

Volání operace získání počty úloh je nejúčinnější způsob, jak vrátit základní počet úlohy podle stavu. Pokud používáte verzi služby Batch 2017-06-01.5.1, doporučujeme zápis nebo aktualizaci vašeho kódu pro použití získat počty úloh.

Operace získání úkolů počty starší než 2017-06-01.5.1 není k dispozici ve verzích služby Batch. Pokud používáte starší verzi služby, potom pomocí seznamu dotazu místo počet úkoly v úloze. Další informace najdete v tématu [dotazy vytvořit seznam Batch efektivně prostředky](batch-efficient-list-queries.md).

## <a name="next-steps"></a>Další postup

* Další informace o koncepcích a funkcích služby Batch najdete v článku [Přehled funkcí Batch](batch-api-basics.md). Článek popisuje primární prostředky služby Batch například fondy, výpočetní uzly, úlohy a úkoly a nabízí přehled funkcí služby.
* Seznamte se se základy vývoje aplikací s podporou služby Batch pomocí [klientské knihovny Batch .NET](batch-dotnet-get-started.md) nebo [Pythonu](batch-python-tutorial.md). Tyto články úvodní vás provede funkční aplikaci, která používá službu Batch při spouštění úlohy na několika výpočetních uzlech.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job

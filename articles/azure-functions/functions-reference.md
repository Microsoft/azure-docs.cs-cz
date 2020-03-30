---
title: Pokyny pro vývoj funkcí Azure
description: Seznamte se s koncepty a technikami Azure Functions, které potřebujete k vývoji funkcí v Azure napříč všemi programovacími jazyky a vazbami.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7dd7ef3c4833fb9ffa3781f06faba4f40cd40cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276618"
---
# <a name="azure-functions-developers-guide"></a>Průvodce vývojáři funkcí Azure
Ve funkcích Azure funkce, konkrétní funkce sdílet několik základních technických konceptů a komponent, bez ohledu na jazyk nebo vazbu, kterou používáte. Než přejdete do podrobností o učení, které jsou specifické pro daný jazyk nebo vazbu, přečtěte si tento přehled, který se vztahuje na všechny z nich.

Tento článek předpokládá, že jste si už přečetli [přehled funkcí Azure](functions-overview.md).

## <a name="function-code"></a>Kód funkce
*Funkce* je primární koncept v Azure Functions. Funkce obsahuje dva důležité kusy - váš kód, který může být napsán v různých jazycích, a některé config, function.json souboru. Pro kompilované jazyky je tento konfigurační soubor generován automaticky z anotací ve vašem kódu. Pro skriptovací jazyky je nutné zadat konfigurační soubor sami.

Soubor function.json definuje aktivační událost funkce, vazby a další nastavení konfigurace. Každá funkce má jednu a jenom jednu aktivační událost. Runtime používá tento konfigurační soubor k určení událostí ke sledování a jak předat data do a vrátit data z provádění funkce. Následuje ukázkový soubor function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Další informace najdete v [tématu Azure Functions aktivační události a vazby koncepty](functions-triggers-bindings.md).

Vlastnost `bindings` je místo, kde nakonfigurujete aktivační události a vazby. Každá vazba sdílí několik běžných nastavení a některá nastavení, která jsou specifická pro určitý typ vazby. Každá vazba vyžaduje následující nastavení:

| Vlastnost | Hodnoty/typy | Komentáře |
| --- | --- | --- |
| `type` |řetězec |Typ vazby. Například, `queueTrigger`. |
| `direction` |'in', 'out' |Označuje, zda je vazba pro příjem dat do funkce nebo odesílání dat z funkce. |
| `name` |řetězec |Název, který se používá pro vázaná data ve funkci. Pro C#se jedná o název argumentu; pro JavaScript, je to klíč v seznamu klíč/hodnota. |

## <a name="function-app"></a>Function App
Aplikace funkce poskytuje kontext spuštění v Azure, ve kterém běží vaše funkce. Jako takový je jednotka nasazení a správy pro vaše funkce. Aplikace funkce se skládá z jedné nebo více jednotlivých funkcí, které jsou spravovány, nasazovány a škálovány společně. Všechny funkce v aplikaci funkce sdílejí stejný cenový plán, metodu nasazení a verzi runtime. Představte si aplikaci pro funkci jako způsob, jak organizovat a kolektivně spravovat své funkce. Další informace najdete v [tématu Správa aplikace pro funkci](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Všechny funkce v aplikaci funkce musí být vytvořeny ve stejném jazyce. V [předchozích verzích](functions-versions.md) runtime Funkce Azure to nebylo nutné.

## <a name="folder-structure"></a>Struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Výše uvedené je výchozí (a doporučená) struktura složek pro aplikaci Function. Chcete-li změnit umístění kódu funkce, upravte `scriptFile` část souboru _function.json._ Doporučujeme také použít [nasazení balíčku](deployment-zip-push.md) k nasazení projektu do aplikace funkce v Azure. Můžete také použít existující nástroje, jako je [průběžná integrace a nasazení](functions-continuous-deployment.md) a Azure DevOps.

> [!NOTE]
> Pokud nasazujete balíček ručně, nezapomeňte nasadit soubor _host.json_ a složky funkcí přímo do `wwwroot` složky. Nezahrnujte složku do `wwwroot` nasazení. V opačném případě `wwwroot\wwwroot` skončíte se složkami.

#### <a name="use-local-tools-and-publishing"></a>Použití místních nástrojů a publikování
Aplikace funkcí lze vytvářet a publikovat pomocí různých nástrojů, včetně [Visual Studia](./functions-develop-vs.md), Visual [Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)a Nástroje azure functions [core tools](./functions-develop-local.md). Další informace najdete v [tématu kód a testování funkcí Azure místně](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Jak upravit funkce na webu Azure Portal
Editor functions integrovaný na portálu Azure umožňuje aktualizovat kód a soubor *function.json* přímo vpájecí. To se doporučuje pouze pro malé změny nebo popisy konceptu - osvědčeným postupem je použití nástroje pro místní vývoj, jako je VS Code.

## <a name="parallel-execution"></a>Paralelní provádění
Pokud více aktivační události dojít rychleji než jeden podproces funkce runtime může zpracovat, runtime může vyvolat funkci vícekrát paralelně.  Pokud aplikace funkce používá [plán hostování spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work), aplikace funkce může automaticky škálovat.  Každá instance aplikace funkce, ať už je aplikace spuštěna v plánu hostování spotřeby nebo v plánu hostování běžné [služby App Service](../app-service/overview-hosting-plans.md), může souběžně zpracovávat souběžné vyvolání funkcí pomocí více vláken.  Maximální počet vyvolání souběžných funkcí v každé instanci aplikace funkce se liší v závislosti na typu aktivační události, která se používá, stejně jako prostředky používané jinými funkcemi v rámci aplikace funkce.

## <a name="functions-runtime-versioning"></a>Správa verzí za běhu funkcí

Pomocí nastavení `FUNCTIONS_EXTENSION_VERSION` aplikace můžete nakonfigurovat verzi běhu Funkce. Například hodnota "~3" označuje, že aplikace funkce bude používat 3.x jako hlavní verzi. Aplikace funkcí jsou upgradovány na každou novou dílčí verzi po jejich vydání. Další informace, včetně toho, jak zobrazit přesnou verzi aplikace funkce, najdete v tématu [Jak cílit na runtime verze Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro Funkce Azure je open source a je uložený v úložištích GitHubu:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Hostitel Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portál Azure Functions](https://github.com/azure/azure-functions-ux)
* [Šablony Azure Functions](https://github.com/azure/azure-functions-templates)
* [Sada Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozšíření sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Zde je tabulka všech podporovaných vazeb.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte problémy s chybami přicházejícími z vazeb? Projděte si dokumentaci [k kódům chyb vazby vazby Azure.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Hlášení problémů
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících materiálech:

* [Azure Funkce aktivační události a vazby](functions-triggers-bindings.md)
* [Místní psaní kódu a testování funkcí Azure Functions](./functions-develop-local.md)
* [Doporučené postupy pro funkce Azure](functions-best-practices.md)
* [Odkaz na vývojáře Azure Functions C#](functions-dotnet-class-library.md)
* [Odkaz na vývojáře azure functions node.js](functions-reference-node.md)

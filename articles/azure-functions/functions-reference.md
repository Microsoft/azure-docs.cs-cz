---
title: Pokyny k vývoji Azure Functions
description: Naučte se Azure Functions koncepty a techniky, které potřebujete k vývoji funkcí v Azure, a to napříč všemi programovacími jazyky a vazbami.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 3ccfc28dc332d2ff90ec09256a43a9143e37c03b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974219"
---
# <a name="azure-functions-developers-guide"></a>Příručka pro vývojáře Azure Functions
V Azure Functions konkrétní funkce sdílí několik základních technických konceptů a komponent bez ohledu na jazyk nebo vazbu, které používáte. Než přejdete k podrobnostem učení, které jsou specifické pro daný jazyk nebo vazbu, přečtěte si tento přehled, který platí pro všechny.

V tomto článku se předpokládá, že už jste si přečetli [Azure Functions Overview](functions-overview.md).

## <a name="function-code"></a>Kód funkce
*Funkce* je primární koncept v Azure Functions. Funkce obsahuje dva důležité díly – váš kód, který lze zapsat v nejrůznějších jazycích, a některé konfigurační soubory Function. JSON. V případě kompilovaných jazyků se tento konfigurační soubor automaticky generuje z poznámek ve vašem kódu. V případě skriptovacích jazyků je nutné zadat konfigurační soubor sami.

Soubor Function. JSON definuje Trigger funkce, vazby a další nastavení konfigurace. Každá funkce má jednu a jenom jednu aktivační událost. Modul runtime používá tento konfigurační soubor k určení událostí ke sledování a k předávání dat do a návratového data z provádění funkce. Následuje příklad souboru Function. JSON.

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

Další informace najdete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

Vlastnost `bindings` je tam, kde konfigurujete triggery i vazby. Každá vazba sdílí několik společných nastavení a některá nastavení, která jsou specifická pro konkrétní typ vazby. Každá vazba vyžaduje následující nastavení:

| Vlastnost | Hodnoty a typy | Komentáře |
| --- | --- | --- |
| `type` |string |Typ vazby Například, `queueTrigger`. |
| `direction` |' in ', ' out ' |Označuje, zda je vazba určena pro příjem dat do funkce nebo odesílání dat z funkce. |
| `name` |string |Název, který se používá pro vázaná data ve funkci. Pro C#je toto název argumentu; v případě JavaScriptu je klíč v seznamu klíč/hodnota. |

## <a name="function-app"></a>Function App
Function App poskytuje kontext spuštění v Azure, ve kterém se vaše funkce spouštějí. V takovém případě je to jednotka nasazení a správy pro vaše funkce. Aplikace Function App se skládá z jedné nebo několika individuálních funkcí, které jsou spravované, nasazené a škálovat dohromady. Všechny funkce ve Function App sdílí stejný cenový plán, způsob nasazení a běhovou verzi. Aplikaci Function App si můžete představit jako způsob, jak organizovat a shromažďovat své funkce. Další informace najdete v tématu [Správa aplikace Function App](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Všechny funkce aplikace Function App musí být vytvořené ve stejném jazyce. V [předchozích verzích](functions-versions.md) modulu runtime Azure Functions to nebylo nutné.

## <a name="folder-structure"></a>Struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Výše uvedená je výchozí (a doporučená) struktura složky aplikace Function App. Pokud chcete změnit umístění souboru kódu funkce, upravte část `scriptFile` souboru _Function. JSON_ . K nasazení vašeho projektu do aplikace Function App v Azure doporučujeme také použít [nasazení balíčku](deployment-zip-push.md) . Můžete také použít stávající nástroje, jako je [průběžná integrace a nasazování](functions-continuous-deployment.md) a Azure DevOps.

> [!NOTE]
> Při ručním nasazování balíčku nezapomeňte nasadit soubor _Host. JSON_ a složky funkcí přímo do složky `wwwroot`. Nezahrnujte složku `wwwroot` do nasazení. V opačném případě skončíte s `wwwroot\wwwroot`mi složkami.

#### <a name="use-local-tools-and-publishing"></a>Použití místních nástrojů a publikování
Aplikace Function App lze vytvářet a publikovat pomocí různých nástrojů, včetně sady [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [zatmění](./functions-create-maven-eclipse.md)a [Azure Functions Core Tools](./functions-develop-local.md). Další informace naleznete v tématu [Code and test Azure Functions lokálně](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Postup úpravy funkcí v Azure Portal
Editor funkcí integrovaný do Azure Portal umožňuje aktualizovat kód a soubor *Function. JSON* přímo vložený. Tento postup je doporučený pouze pro malé změny nebo zkoušku konceptu – osvědčeným postupem je použití místního vývojového nástroje, jako je VS Code.

## <a name="parallel-execution"></a>Paralelní provádění
Když dojde k více událostem triggeru rychleji než modul runtime s jedním vláknem, může je zpracovat, modul runtime může funkci vyvolat víckrát.  Pokud aplikace Function App používá [plán hostování spotřeby](functions-scale.md#how-the-consumption-and-premium-plans-work), aplikace Function app by mohla automaticky škálovat horizontální navýšení kapacity.  Každá instance aplikace Function App, bez ohledu na to, jestli aplikace běží na plánu hostování spotřeby nebo běžném [App Service plánu hostování](../app-service/overview-hosting-plans.md), může zpracovávat souběžné volání funkcí paralelně pomocí několika vláken.  Maximální počet souběžných volání funkcí v každé instanci aplikace Function App se liší v závislosti na používaném typu triggeru a na zdrojích používaných jinými funkcemi v aplikaci Function App.

## <a name="functions-runtime-versioning"></a>Správa verzí modulu runtime Functions

Verzi modulu runtime Functions můžete nakonfigurovat pomocí nastavení aplikace `FUNCTIONS_EXTENSION_VERSION`. Například hodnota "~ 3" označuje, že vaše Function App bude jako hlavní verzi používat 3. x. Aplikace Function App jsou při vydání upgradovány na každou novou dílčí verzi. Další informace, včetně toho, jak zobrazit přesnou verzi aplikace Function App, najdete v tématu [jak cílit na verze Azure Functions runtime](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro Azure Functions je open source a uložený v úložištích GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions hostitele](https://github.com/Azure/azure-functions-host/)
* [Portál Azure Functions](https://github.com/azure/azure-functions-ux)
* [Šablony Azure Functions](https://github.com/azure/azure-functions-templates)
* [Sada Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozšíření sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Tady je tabulka všech podporovaných vazeb.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte problémy s chybami pocházejícími z vazeb? Přečtěte si dokumentaci [Azure Functions kódy chyb vazeb](functions-bindings-error-pages.md) .

## <a name="reporting-issues"></a>Vytváření sestav – problémy
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících materiálech:

* [Aktivační události a vazby Azure Functions](functions-triggers-bindings.md)
* [Místní psaní kódu a testování funkcí Azure Functions](./functions-develop-local.md)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Referenční C# informace pro vývojáře Azure Functions](functions-dotnet-class-library.md)
* [Referenční informace pro vývojáře Azure Functions NodeJS](functions-reference-node.md)

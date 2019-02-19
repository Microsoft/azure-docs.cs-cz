---
title: Pokyny k vývoji funkcí Azure Functions | Dokumentace Microsoftu
description: Informace o Azure Functions koncepty a techniky, které potřebujete k vývoji funkcí v Azure, ve všech programovacích jazyků a vazeb.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: funkce průvodce, azure pro vývojáře, funkce zpracování událostí, webhook, dynamické výpočty, architektura bez serveru
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 01fce9813299465767bdaed7f3c2939813cfab87
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339479"
---
# <a name="azure-functions-developers-guide"></a>Příručka pro vývojáře Azure Functions
Ve službě Azure Functions konkrétní funkce sdílet několik klíčových technických konceptech a komponenty, bez ohledu na jazyk nebo vazby, kterou používáte. Před přechodem do učení podrobnosti specifické pro daný jazyk nebo vazby, ujistěte se, že jste si tento přehled, který se vztahuje na všechny z nich.

Tento článek předpokládá, že jste už čtete [přehled Azure Functions](functions-overview.md).

## <a name="function-code"></a>Kód – funkce
A *funkce* je primárním konceptu ve službě Azure Functions. Funkce obsahuje dvě důležité součásti - kódu, který je možné psát v různých jazycích a některé konfigurační soubor function.json. Pro kompilovaných jazycích se automaticky generuje tento konfigurační soubor z poznámky ve vašem kódu. Pro skriptovací jazyky je nutné zadat konfigurační soubor sami.

Soubor function.json definuje aktivační události této funkce, vazby a dalších nastavení konfigurace. Každá funkce má jeden a pouze jeden trigger. Modul runtime používá tento konfigurační soubor k určení události, které chcete monitorovat a jak předat data do a vrátit data z provádění funkce. Následuje příklad souboru function.json.

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

`bindings` Vlastnost je, kde můžete nakonfigurovat aktivační události a vazby. Každá vazba sdílí několik běžných nastavení a některá nastavení, které jsou specifické pro konkrétní typ vazby. Každá vazba vyžaduje následující nastavení:

| Vlastnost | Hodnoty a typy | Komentáře |
| --- | --- | --- |
| `type` |řetězec |Typ vazby. Například, `queueTrigger`. |
| `direction` |"in" "out" |Označuje, zda vazba pro příjem dat do funkce nebo odesílání dat z funkce. |
| `name` |řetězec |Název, který se používá k vázaným datům ve funkci. Pro jazyk C# Toto je název argumentu; pro jazyk JavaScript je klíč v seznamu klíč/hodnota. |

## <a name="function-app"></a>Function App
Aplikace function app poskytuje kontext spuštění v Azure, ve kterém funkce spuštěné. Aplikace function app se skládá z jedné nebo více jednotlivých funkcí, které jsou spravované, nasazovat a škálovat společně. Všechny funkce v aplikaci function app sdílejí stejné cenový plán, průběžného nasazování a verze modulu runtime. Aplikace function app můžete představit jako způsob, jak uspořádat a kolektivně spravovat vaše funkce. 

> [!NOTE]
> Všechny funkce v aplikaci function app musí být vytvořen ve stejném jazyce. V [předchozí verze](functions-versions.md) modulu runtime Azure Functions, to se vyžaduje.

## <a name="folder-structure"></a>struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Výše uvedené je výchozí (a doporučené) strukturu složek pro aplikaci Function app. Pokud chcete změnit umístění souboru kód funkce, upravte `scriptFile` část _function.json_ souboru. Doporučujeme také používat [balíček nasazení](deployment-zip-push.md) nasazení projektu do vaší aplikace funkcí v Azure. Můžete také použít stávající nástroje, jako je [průběžnou integraci a nasazování](functions-continuous-deployment.md) a Azure DevOps.

> [!NOTE]
> Pokud nasazení balíčku ručně, ujistěte se, že k nasazení vaší _host.json_ souborů a složek přímo do funkce `wwwroot` složky. Nejsou zahrnuté `wwwroot` složky v nasazeních. Jinak můžete skončit s `wwwroot\wwwroot` složek.

#### <a name="use-local-tools-and-publishing"></a>Použití nástrojů pro místní a publikování
Aplikace Function App můžete vytvořili a publikovali pomocí různých nástrojů, včetně [sady Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md)a [nástroje Azure Functions Core](./functions-develop-local.md). Další informace najdete v tématu [kódu a testování Azure Functions místně](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Postup úpravy funkce na webu Azure Portal
Funkce v editoru kódu postaveném na web Azure Portal umožňuje aktualizujte svůj kód a vaše *function.json* vložených souborů přímo. Tato možnost se doporučuje jenom pro malé změny nebo testování konceptů, - osvědčeným postupem je použití místního vývojového nástroje, jako je VS Code.

## <a name="parallel-execution"></a>Paralelní provádění
Dojde-li více spouštěcí události rychleji, než dokáže zpracovat s jedním vláknem funkce modulu runtime, modul runtime může volat funkci více než jednou paralelně.  Pokud používáte aplikaci function app [plán hostování Consumption](functions-scale.md#how-the-consumption-plan-works), aplikace function app může automatické horizontální navýšení kapacity.  Každá instance aplikace function app, jestli aplikace běží na spotřebu hostování plán nebo běžný [plán hostování služby App Service](../app-service/overview-hosting-plans.md), může zpracovat volání funkce souběžné používání více vláken současně.  Maximální počet souběžných funkce volání v každé instanci aplikace funkce se liší v závislosti na typu triggeru se používají, jakož i prostředky využívané třídou jiné funkce v rámci aplikace function app.

## <a name="functions-runtime-versioning"></a>Správa verzí modulu runtime functions

Můžete nakonfigurovat pomocí funkce modulu runtime verze `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace. Například hodnota "~ 2" označuje, že vaši aplikaci Function App se používat 2.x jako jeho hlavní verze. Aplikace Function App jsou upgradovány na každý nový dílčí verze při jejich vydání. Další informace, včetně zobrazování přesnou verzi vaší aplikace function App najdete v části [způsobu cílení verze modulu runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro službu Azure Functions je typu open source a uložená v úložištích GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Hostitele služby Azure Functions](https://github.com/Azure/azure-functions-host/)
* [Portál Azure Functions](https://github.com/azure/azure-functions-ux)
* [Šablony Azure Functions](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozšíření Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Tady je tabulka znázorňující všechny podporované vazby.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte problémy s chyby pocházející z vazby? Zkontrolujte [kódy chyb vazeb Azure Functions](functions-bindings-error-pages.md) dokumentaci.

## <a name="reporting-issues"></a>Hlášení problémů s
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Místní psaní kódu a testování funkcí Azure Functions](./functions-develop-local.md)
* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Funkce jazyka C# pro vývojáře referenční informace k Azure](functions-reference-csharp.md)
* [Referenční informace pro vývojáře Azure Functions NodeJS](functions-reference-node.md)

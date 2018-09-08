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
ms.openlocfilehash: d2b05c83f77a58e224760d90d111b270d71a6514
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092423"
---
# <a name="azure-functions-developers-guide"></a>Příručka pro vývojáře Azure Functions
Ve službě Azure Functions konkrétní funkce sdílet několik klíčových technických konceptech a komponenty, bez ohledu na jazyk nebo vazby, kterou používáte. Před přechodem do učení podrobnosti specifické pro daný jazyk nebo vazby, ujistěte se, že jste si tento přehled, který se vztahuje na všechny z nich.

Tento článek předpokládá, že jste už čtete [přehled Azure Functions](functions-overview.md) a jste obeznámeni s [sada WebJobs SDK konceptů, jako jsou triggery, vazby a modulu runtime JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). Služba Azure Functions je založena na sadě WebJobs SDK. 

## <a name="function-code"></a>Kód – funkce
A *funkce* je primárním konceptu ve službě Azure Functions. Napište kód pro funkci v jazyce podle vašeho výběru a uložit kód a konfigurační soubory ve stejné složce. Konfigurace má název `function.json`, která obsahuje konfigurační data JSON. Podporuje různé jazyky a každý z nich má mírně odlišné činnost optimalizován pro spolupráci pro daný jazyk. 

Soubor function.json definuje vazby funkcí a dalších nastavení konfigurace. Modul runtime používá k určení události, které chcete sledovat tento soubor a jak předat data do a vrátit data z provádění funkce. Následuje příklad souboru function.json.

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

Nastavte `disabled` vlastnost `true` zabránit při provádění funkce.

`bindings` Vlastnost je, kde můžete nakonfigurovat aktivační události a vazby. Každá vazba sdílí několik běžných nastavení a některá nastavení, které jsou specifické pro konkrétní typ vazby. Každá vazba vyžaduje následující nastavení:

| Vlastnost | Hodnoty a typy | Komentáře |
| --- | --- | --- |
| `type` |řetězec |Typ vazby. Například, `queueTrigger`. |
| `direction` |"in" "out" |Označuje, zda vazba pro příjem dat do funkce nebo odesílání dat z funkce. |
| `name` |řetězec |Název, který se používá k vázaným datům ve funkci. Pro jazyk C# Toto je název argumentu; pro jazyk JavaScript je klíč v seznamu klíč/hodnota. |

## <a name="function-app"></a>Function App
Aplikace function app se skládá z jednoho nebo více jednotlivých funkcí, které se spravují společně ve službě Azure App Service. Všechny funkce v aplikaci function app sdílejí stejné cenový plán, průběžného nasazování a verze modulu runtime. Všechny funkce, které jsou napsané v různých jazycích můžou sdílet stejné aplikace function app. Aplikace function app můžete představit jako způsob, jak uspořádat a kolektivně spravovat vaše funkce. 

## <a name="runtime-script-host-and-web-host"></a>Modul runtime (hostitel skriptu a webového hostitele)
Modul runtime nebo hostitel skriptu, je základní sada WebJobs SDK hostitele, který naslouchá událostem, shromažďuje a odesílá data a nakonec spustí váš kód. 

Pro usnadnění triggerů HTTP, je zde také webového hostitele, který je navržený tak před hostitel skriptu v produkčních scénářích. S dvěma hostiteli pomáhá izolovat hostitel skriptu z přední ukončit provoz spravuje webového hostitele.

## <a name="folder-structure"></a>Struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Při vytváření projektu pro nasazení funkce do aplikace function app v Azure App Service, můžete tuto strukturu složek považovat váš kód lokality. Můžete použít stávající nástroje, jako jsou průběžná integrace a nasazování nebo vlastního nasazení skripty pro postup nasazení čas instalace balíčku nebo code transpilation.

> [!NOTE]
> Ujistěte se, že k nasazení vaší `host.json` souborů a složek přímo do funkce `wwwroot` složky. Nejsou zahrnuté `wwwroot` složky v nasazeních. Jinak můžete skončit s `wwwroot\wwwroot` složek. 
> 
> 

## <a id="fileupdate"></a> Postup aktualizace souborů aplikace – funkce
Funkce v editoru kódu postaveném na web Azure Portal umožňuje aktualizovat *function.json* soubor a soubor kódu pro funkci. K nahrání nebo aktualizace dalších souborů, jako *package.json* nebo *project.json* nebo závislosti, budete muset použít jiné metody nasazení.

Aplikace Function App jsou postavené na službě App Service, takže všechno [dostupným možnostem nasazení do standardních webových aplikací](../app-service/app-service-deploy-local-git.md) jsou také k dispozici pro aplikace function App. Tady jsou některé metody, které můžete použít k nahrání nebo aktualizace souborů aplikace funkce. 

#### <a name="to-use-app-service-editor"></a>Použití App Service Editor
1. Na portálu Azure Functions, klikněte na tlačítko **funkce platformy**.
2. V **nástroje pro vývoj** klikněte na tlačítko **App Service Editor**.   
   Po načtení App Service Editor, zobrazí se vám *host.json* složky souboru a funkce v rámci *wwwroot*. 
5. Otevírání souborů a upravovat, a přetáhněte ho z vývojového počítače chcete nahrát soubory.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Použít koncový bod aplikace function app SCM (Kudu)
1. Přejděte do: `https://<function_app_name>.scm.azurewebsites.net`.
2. Klikněte na tlačítko **konzole ladění > CMD**.
3. Přejděte do `D:\home\site\wwwroot\` aktualizovat *host.json* nebo `D:\home\site\wwwroot\<function_name>` k aktualizaci souborů funkce.
4. Přetažením souboru, který chcete nahrát do příslušné složky v mřížce souboru. V mřížce souboru, kde lze přetáhnout do souboru existují dvě oblasti. Pro *ZIP* soubory, zobrazí se pole s popiskem "Přetáhněte sem k nahrání a rozbalte ho." U ostatních typů souborů vyřaďte v mřížce soubor, ale mimo pole "rozbalte".

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Použití průběžného nasazování
Postupujte podle pokynů v tématu [průběžné nasazování pro službu Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Paralelní provádění
Dojde-li více spouštěcí události rychleji, než dokáže zpracovat s jedním vláknem funkce modulu runtime, modul runtime může volat funkci více než jednou paralelně.  Pokud používáte aplikaci function app [plán hostování Consumption](functions-scale.md#how-the-consumption-plan-works), aplikace function app může automatické horizontální navýšení kapacity.  Každá instance aplikace function app, jestli aplikace běží na spotřebu hostování plán nebo běžný [plán hostování služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), může zpracovat volání funkce souběžné používání více vláken současně.  Maximální počet souběžných funkce volání v každé instanci aplikace funkce se liší v závislosti na typu triggeru se používají, jakož i prostředky využívané třídou jiné funkce v rámci aplikace function app.

## <a name="functions-runtime-versioning"></a>Správa verzí modulu runtime functions

Můžete nakonfigurovat pomocí funkce modulu runtime verze `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace. Například hodnota "~ 1" označuje, že vaši aplikaci Function App se používat 1 jako jeho hlavní verze. Aplikace Function App jsou upgradovány na každý nový dílčí verze při jejich vydání. Další informace, včetně zobrazování přesnou verzi vaší aplikace function App najdete v části [způsobu cílení verze modulu runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro službu Azure Functions je typu open source a uložená v úložištích GitHub:

* [Modul runtime služby Azure Functions](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portál Azure Functions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Šablony Azure Functions](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Sada Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozšíření Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Tady je tabulka znázorňující všechny podporované vazby.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte problémy s chyby pocházející z vazby? Zkontrolujte [kódy chyb vazeb Azure Functions](functions-bindings-error-pages.md) dokumentaci.

## <a name="reporting-issues"></a>Hlášení problémů s
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Funkce jazyka C# pro vývojáře referenční informace k Azure](functions-reference-csharp.md)
* [Azure Functions F # – reference pro vývojáře](functions-reference-fsharp.md)
* [Referenční informace pro vývojáře Azure Functions NodeJS](functions-reference-node.md)
* [Azure Functions aktivačními událostmi a vazbami](functions-triggers-bindings.md)
* [Služba Azure Functions: Cesta](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) na blogu týmu Azure App Service. Historie jak byla vyvinuta Azure Functions.


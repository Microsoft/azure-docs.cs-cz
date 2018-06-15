---
title: Pokyny pro vývoj Azure Functions | Microsoft Docs
description: Další koncepty Azure Functions a techniky, které potřebujete k vývoji funkce v Azure, ve všech programovací jazyky a vazeb.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: vývojáře průvodce azure funkce, funkce, událostí zpracování, webhooků, dynamické výpočetní, bez serveru architektura
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 461557b415ec816860acb5308e7aeba34468f4ae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121742"
---
# <a name="azure-functions-developers-guide"></a>Příručka pro vývojáře Azure funkce
V Azure Functions se konkrétní funkce sdílet několik klíčových technických konceptech a součásti, bez ohledu na jazyk nebo vazby, které používáte. Před přechodem do učení podrobnosti, které jsou specifické pro daný jazyk nebo vazby, nezapomeňte si přečíst tento přehled, který se vztahuje na všechny z nich.

Tento článek předpokládá, že jste si již přečetli [přehled Azure Functions](functions-overview.md) a jsou obeznámeni s [WebJobs SDK koncepty, jako jsou aktivační události, vazby a JobHost runtime](https://github.com/Azure/azure-webjobs-sdk/wiki). Azure Functions je založena na WebJobs SDK. 

## <a name="function-code"></a>Kód – funkce
A *funkce* je primární koncept v Azure Functions. Psaní kódu pro funkce v jazyce podle vaší volby a uložte kódu a konfiguračních souborů ve stejné složce. Konfigurace má název `function.json`, která obsahuje konfigurační data JSON. Podporuje různé jazyky a každé z nich má mírně odlišné prostředí optimalizován pro daného jazyka. 

Soubor function.json definuje vazby funkcí a dalších nastavení konfigurace. Modul runtime používá tento soubor k určení události k monitorování a jak předat data do a ze spuštění funkce vrátit data. Následující příklad je function.json soubor.

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

Nastavte `disabled` vlastnost `true` zabránit funkci spouštěna.

`bindings` Vlastnost je, kde můžete konfigurovat triggerů a vazeb. Každou vazbu sdílí několik běžných nastavení a některá nastavení, které jsou specifické pro konkrétní typ vazby. Každé vazby vyžaduje následující nastavení:

| Vlastnost | Hodnoty nebo typy | Komentáře |
| --- | --- | --- |
| `type` |řetězec |Typ vazby. Například, `queueTrigger`. |
| `direction` |v out |Určuje, zda vazby pro příjem dat do funkce nebo odeslání dat z funkce. |
| `name` |řetězec |Název, který se používá pro vázaných dat ve funkci. Pro jazyk C# to je název argumentu; pro jazyk JavaScript je klíče v seznamu klíč/hodnota. |

## <a name="function-app"></a>Function app
Funkce aplikace se skládá z jedné nebo více jednotlivých funkcí, které se spravují dohromady službou Azure App Service. Všechny funkce v aplikaci funkce sdílet stejnou cenový plán, průběžné nasazování a verze modulu runtime. Všechny funkce, které jsou napsané v různých jazycích mohou sdílet stejné aplikaci funkce. Funkce aplikace si můžete představit jako způsob, jak uspořádat a souhrnně spravovat funkcí. 

## <a name="runtime-script-host-and-web-host"></a>Modul runtime (hostitel skriptu a webového hostitele)
Modul runtime nebo hostitel skriptu, je základní sada WebJobs SDK hostitele, který naslouchá událostem, shromažďuje a odesílá data a nakonec spustí váš kód. 

Pro usnadnění aktivace protokolu HTTP, je zde také webového hostitele, který je navržený pro sledovat na hostitelský modul pro skripty v produkčních scénářích. Má dva hostitele pomáhá izolovat hostitelský modul pro skripty z před ukončení provoz spravuje webového hostitele.

## <a name="folder-structure"></a>Struktura složek
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Při vytváření projektu pro nasazení funkce do aplikaci funkce ve službě Azure App Service, můžete tuto strukturu považovat kódu lokality. Můžete použít stávající nástroje, například průběžnou integraci a nasazení, nebo vlastní nasazení skriptů pro tuto činnost čas instalace balíčku nasazení nebo code transpilation.

> [!NOTE]
> Zajistěte, aby k nasazení vaší `host.json` souborů a složek přímo na fungovat `wwwroot` složky. Nezahrnovat `wwwroot` složky ve vašem nasazení. Jinak, v níž se `wwwroot\wwwroot` složek. 
> 
> 

## <a id="fileupdate"></a>Postup aktualizace soubory aplikace – funkce
Editor funkce integrovaná v portálu Azure vám umožní aktualizovat *function.json* soubor a soubor kódu pro funkci. Nahrát nebo aktualizovat ostatní soubory, jako *package.json* nebo *project.json* nebo závislosti, budete muset použít jiné metody nasazení.

Funkce aplikace jsou postaveny na služby App Service, takže všechny [možnosti nasazení standardní webových aplikací](../app-service/app-service-deploy-local-git.md) jsou také k dispozici pro funkce aplikace. Tady jsou některé metody, které můžete nahrát, nebo můžete aktualizovat soubory funkce aplikace. 

#### <a name="to-use-app-service-editor"></a>Použití editoru služby aplikace
1. Na portálu Azure Functions, klikněte na tlačítko **funkce**.
2. V **nástroje pro vývoj** klikněte na tlačítko **App Service Editor**.   
   Po načtení Editor služby aplikace se zobrazí *host.json* složek souborů a funkce v části *wwwroot*. 
5. Otevřít soubory, upravit nebo přetáhnout z vývojovém počítači k nahrání souborů.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Použít aplikaci funkce endpoint SCM (Kudu)
1. Přejděte do: `https://<function_app_name>.scm.azurewebsites.net`.
2. Klikněte na tlačítko **ladění konzoly > CMD**.
3. Přejděte na `D:\home\site\wwwroot\` aktualizace *host.json* nebo `D:\home\site\wwwroot\<function_name>` k aktualizaci souborů funkce.
4. A přetáhněte soubor, který chcete nahrát do příslušné složky v mřížce souboru. Existují dvě oblasti v mřížce souboru, kde můžete vložit do souboru. Pro *.zip* soubory, zobrazí se pole s popiskem "Přetáhněte sem odeslání a rozbalte." Pro ostatní typy souborů vyřaďte v mřížce souboru, ale mimo pole "rozbalte".

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Chcete-li použít průběžné nasazování.
Postupujte podle pokynů v tématu [průběžné nasazování pro Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Paralelní provádění
Dojde-li více spouštěcí událostí rychleji, než funkce jednovláknové runtime dokáže zpracovat, může modul runtime vyvolají funkci vícekrát paralelně.  Pokud funkce aplikace používá [spotřeba hostování plán](functions-scale.md#how-the-consumption-plan-works), může automaticky škálovat aplikaci funkce.  Každá instance funkce aplikace, jestli aplikace běží na spotřebu hostování plán nebo běžný [hostování plán služby App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), může zpracovat volání souběžných funkce paralelně pomocí více vláken.  Maximální počet souběžných funkce volání v každé instanci aplikace funkce se liší podle typu používá aktivační událost, jakož i prostředky využívané třídou jiných funkcí v rámci funkce aplikace.

## <a name="functions-runtime-versioning"></a>Verze runtime funkce

Můžete nakonfigurovat verzi modulu runtime její funkce pomocí `FUNCTIONS_EXTENSION_VERSION` nastavení aplikace. Například hodnota "~ 1" označuje, že funkce aplikace bude používat 1 jako jeho hlavní verzi. Funkce aplikace upgradují na každý nový podverze při jejich vydání. Další informace, včetně toho, jak chcete zobrazit přesnou verzi funkce aplikace, najdete v části [jak mít verze modulu runtime Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Úložiště
Kód pro Azure Functions je open source a uložené v úložišť GitHub:

* [Azure Functions runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions na portálu](https://github.com/projectkudu/AzureFunctionsPortal)
* [Šablony funkcí Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Sada Azure WebJobs SDK rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Vazby
Zde je tabulku všechny podporované vazby.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Máte potíže s chybami pocházejících z vazby? Zkontrolujte [kódy chyb vazby funkcí Azure](functions-bindings-error-pages.md) dokumentaci.

## <a name="reporting-issues"></a>Hlášení problémů
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících materiálech:

* [Osvědčené postupy pro službu Azure Functions](functions-best-practices.md)
* [Azure funkcí jazyka C# referenční informace pro vývojáře](functions-reference-csharp.md)
* [Azure funkce F # referenční informace pro vývojáře](functions-reference-fsharp.md)
* [Referenční informace pro vývojáře Azure funkce NodeJS](functions-reference-node.md)
* [Azure funkce triggerů a vazeb](functions-triggers-bindings.md)
* [Azure Functions: Cesty](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) na blog týmu Azure App Service. Historie jak byla vyvinuta Azure Functions.


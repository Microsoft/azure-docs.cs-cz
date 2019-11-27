---
title: Azure Functions – přehled verzí modulu runtime
description: Azure Functions podporuje více verzí modulu runtime. Přečtěte si o rozdílech mezi nimi a o tom, jak zvolit tu, která je pro vás nejvhodnější.
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 53da5869b4768c95fd225fb15db60f4301e537d4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226536"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions – přehled verzí modulu runtime

Hlavní verze modulu runtime Azure Functions souvisejí s verzí rozhraní .NET, na které je modul runtime založen. V následující tabulce je uvedeno aktuální verze modulu runtime, úroveň verze a související verze rozhraní .NET. 

| Verze modulu runtime | Úroveň verze<sup>1</sup> | Verze rozhraní .NET | 
| --------------- | ------------- | ------------ |
| 3.x  | preview | .NET Core 3. x | 
| 2.x | GA | .NET Core 2.2 |
| verze | GA<sup>2</sup> | .NET Framework 4,6<sup>3</sup> |

<sup>1</sup> Verze GA se podporují pro produkční scénáře.   
<sup>2</sup> . Verze 1. x je v režimu údržby. Vylepšení jsou k dispozici pouze v novějších verzích.   
<sup>3</sup> . Podporuje pouze vývoj v Azure Portal nebo lokálně na počítačích se systémem Windows.

>[!NOTE]  
> Verze 3. x modulu runtime Functions je ve verzi Preview a není podporovaná v produkčních prostředích. Další informace o vyzkoušení verze 3. x najdete v [tomto oznámení](https://dev.to/azure/develop-azure-functions-using-net-core-3-0-gcm).

Tento článek podrobně popisuje některé rozdíly mezi různými verzemi, způsob, jakými můžete vytvořit jednotlivé verze, a postup změny verzí.

## <a name="languages"></a>Jazyky

Počínaje verzí 2. x používá modul runtime model rozšiřitelnosti jazyka a všechny funkce ve Function App musí sdílet stejný jazyk. Jazyk funkcí ve Function App se volí při vytváření aplikace a udržuje se v nastavení [functions\_worker\_runtime](functions-app-settings.md#functions_worker_runtime) . 

Azure Functions 1. x experimentální jazyky nemůžou používat nový model, takže se v 2. x nepodporují. Následující tabulka uvádí, které programovací jazyky jsou aktuálně podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [Podporované jazyky](supported-languages.md).

## <a name="creating-1x-apps"></a>Spustit na konkrétní verzi

Ve výchozím nastavení jsou aplikace Function App vytvořené v Azure Portal a Azure CLI nastavené na verzi 2. x. Pokud je to možné, měli byste použít tuto verzi modulu runtime. Pokud potřebujete, můžete pořád spustit aplikaci Function App v modulu runtime verze 1. x. Verzi modulu runtime můžete změnit pouze po vytvoření aplikace Function App, ale před přidáním jakékoli funkce. Informace o tom, jak připnout verzi modulu runtime na 1. x, najdete v tématu [zobrazení a aktualizace aktuální verze modulu runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

Můžete také upgradovat na verzi 3. x modulu runtime, který je ve verzi Preview. Tuto možnost použijte, pokud potřebujete být schopni spustit vaše funkce na .NET Core 3. x. Informace o tom, jak upgradovat na 3. x, najdete v tématu [zobrazení a aktualizace aktuální verze modulu runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-later-versions"></a>Migrace z 1. x na novější verze

Můžete se rozhodnout migrovat existující aplikaci vytvořenou tak, aby používala modul runtime verze 1. x místo toho, aby používala verzi 2. x. Většina změn, které je třeba provést, souvisí se změnami v jazykovém modulu runtime, jako je C# například změna rozhraní API mezi .NET Framework 4,7 a .NET Core 2. Budete také muset zajistit, aby byl kód a knihovny kompatibilní s vámi zvoleným jazykovým modulem runtime. Nakonec si nezapomeňte všimnout všech změn v aktivační události, vazbách a funkcích, které jsou zvýrazněny níže. Pro dosažení nejlepších výsledků migrace byste měli vytvořit novou aplikaci Function App pro verzi 2. x a přenést existující kód funkce verze 1. x do nové aplikace.  

### <a name="changes-in-triggers-and-bindings"></a>Změny v aktivačních událostech a vazbách

Verze 2. x vyžaduje, abyste nainstalovali rozšíření pro konkrétní triggery a vazby používané funkcemi ve vaší aplikaci. Jediná výjimka pro tuto aktivační událost HTTP a časovač, která nevyžaduje rozšíření.  Další informace najdete v tématu [registrace a instalace rozšíření vazby](./functions-bindings-register.md).

Došlo také k několika změnám v `function.json` nebo atributů funkce mezi verzemi. Například vlastnost centra událostí `path` je nyní `eventHubName`. V [existující tabulce vazeb](#bindings) najdete odkazy na dokumentaci pro každou vazbu.

### <a name="changes-in-features-and-functionality"></a>Změny funkcí a funkcí

Několik funkcí, které byly také odebrány, aktualizovány nebo nahrazeny v nové verzi. Tato část podrobně popisuje změny, které vidíte ve verzi 2. x po použití verze 1. x.

Ve verzi 2. x byly provedeny následující změny:

* Klíče pro volání koncových bodů HTTP se vždycky ukládají šifrovaným v úložišti objektů BLOB v Azure. Ve verzi 1. x jsou klíče uložené ve službě Azure File Storage jako výchozí. Při upgradu aplikace z verze 1. x na verzi 2. x jsou obnoveny stávající tajné klíče v úložišti souborů.

* Modul runtime verze 2. x neobsahuje integrovanou podporu pro poskytovatele webhooků. Tato změna byla provedena za účelem zvýšení výkonu. Pro Webhooky můžete pořád používat triggery HTTP jako koncové body.

* Konfigurační soubor hostitele (host. JSON) by měl být prázdný nebo obsahovat řetězec `"version": "2.0"`.

* Pro zlepšení monitorování se řídicí panel WebJobs na portálu, který použil [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) nastavení, nahradí pomocí Azure Application Insights, který používá nastavení [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) . Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

* Všechny funkce ve Function App musí sdílet stejný jazyk. Při vytváření aplikace Function App musíte zvolit zásobník modulu runtime pro aplikaci. Zásobník modulu runtime je určen hodnotou [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) v nastavení aplikace. Tento požadavek byl přidaný, aby se zlepšila nároky a čas spuštění. Při vývoji místně musíte také zahrnout toto nastavení do [souboru Local. Settings. JSON](functions-run-local.md#local-settings-file).

* Výchozí časový limit pro funkce v plánu App Service se změnil na 30 minut. Můžete ručně změnit časový limit zpět na neomezený pomocí nastavení [functionTimeout](functions-host-json.md#functiontimeout) v Host. JSON.

* Omezení Concurrency protokolu HTTP jsou ve výchozím nastavení implementovaná pro funkce plánu spotřeby s výchozí 100 souběžných požadavků na instanci. To můžete změnit v nastavení [`maxConcurrentRequests`](functions-host-json.md#http) v souboru Host. JSON.

* Z důvodu [omezení rozhraní .NET Core](https://github.com/Azure/azure-functions-host/issues/3414)byla odebrána F# Podpora funkcí skriptu (. FSX). Zkompilované F# funkce (. FS) jsou stále podporovány.

* Formát adresy URL webhooků triggeru Event Grid se změnil na `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrace místně vyvinuté aplikace

Můžete mít existující projekty Function App, které jste vyvinuli místně pomocí modulu runtime verze 1. x. Chcete-li upgradovat na verzi 2. x, měli byste vytvořit projekt místní aplikace Functions na verzi 2. x a přenést svůj existující kód do nové aplikace. Můžete ručně aktualizovat existující projekt a kód, a to tak, aby byl proveden upgrade na místě. Existuje však několik dalších vylepšení mezi verzemi 1. x a verze 2. x, kterou je stále nutné provést. Například v C# objektu ladění se změnil z `TraceWriter` na `ILogger`. Vytvořením nového projektu verze 2. x se spustí aktualizované funkce založené na nejnovějších šablonách verze 2. x.

#### <a name="visual-studio-runtime-versions"></a>Verze Visual Studio runtime

V aplikaci Visual Studio vyberete verzi modulu runtime při vytváření projektu. Nástroje Azure Functions pro Visual Studio podporují hlavní verze modulu runtime. Správná verze se používá při ladění a publikování na základě nastavení projektu. Nastavení verze jsou definována v souboru `.csproj` v následujících vlastnostech:

##### <a name="version-1x"></a>Verze 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Verze 2. x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Při ladění nebo publikování projektu se používá správná verze modulu runtime.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code a Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) se používá pro vývoj na příkazovém řádku a také [rozšířením Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. Pro vývoj v rámci verze 2. x nainstalujte základní nástroje verze 2. x. Vývoj verze 1. x vyžaduje verzi 1. x základních nástrojů. Další informace najdete v tématu [instalace Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

V případě vývoje Visual Studio Code možná budete muset také aktualizovat uživatelské nastavení `azureFunctions.projectRuntime` tak, aby odpovídalo verzi nainstalovaných nástrojů.  Toto nastavení také aktualizuje šablony a jazyky používané při vytváření aplikace Function App.

### <a name="changing-version-of-apps-in-azure"></a>Změna verze aplikací v Azure

Verze modulu runtime Functions používaná publikovanými aplikacemi v Azure je využívána nastavením [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) aplikace. Hodnota `~2` cílí na modul runtime verze 2. x a `~1` cílí na modul runtime verze 1. x. Toto nastavení neměňte, protože další změny nastavení aplikace a změny kódu ve vašich funkcích jsou pravděpodobně požadovány. Další informace o doporučeném způsobu migrace aplikace Function App do jiné verze modulu runtime najdete v tématu [jak cílit na Azure Functions verze modulu runtime](set-runtime-version.md).

## <a name="bindings"></a>Vazby

Počínaje verzí 2. x používá modul runtime nový [model rozšiřitelnosti vazby](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , který nabízí tyto výhody:

* Podpora rozšíření vazby třetích stran.

* Oddělení runtime a vazeb. Tato změna umožňuje, aby rozšíření vazby byla vydaná verzí a uvolněna nezávisle. Můžete například chtít upgradovat na verzi rozšíření, která spoléhá na novější verzi základní sady SDK.

* Jednodušší prostředí pro spuštění, kde pouze používané vazby jsou známy a načteny modulem runtime.

S výjimkou aktivačních událostí protokolu HTTP a časovače musí být všechny vazby explicitně přidány do projektu Function App nebo registrovány na portálu. Další informace najdete v tématu [Registrace rozšíření vazby](./functions-bindings-expressions-patterns.md).

Následující tabulka ukazuje, které vazby jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících zdrojích:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak cílit na verze modulu runtime Azure Functions](set-runtime-version.md)
* [Poznámky k verzi](https://github.com/Azure/azure-functions-host/releases)

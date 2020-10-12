---
title: Azure Functions – přehled verzí modulu runtime
description: Azure Functions podporuje více verzí modulu runtime. Přečtěte si o rozdílech mezi nimi a o tom, jak zvolit tu, která je pro vás nejvhodnější.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 6641461e63d7c9452351f3b0b99a274d2714a92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208101"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions – přehled verzí modulu runtime

Azure Functions aktuálně podporuje tři verze hostitele modulu runtime: 1. x, 2. x a 3. x. Všechny tři verze jsou podporovány v produkčních scénářích.  

> [!IMPORTANT]
> Verze 1. x je v režimu údržby a podporuje pouze vývoj v Azure Portal nebo lokálně na počítačích se systémem Windows. Vylepšení jsou k dispozici pouze v novějších verzích. 

Tento článek podrobně popisuje některé rozdíly mezi různými verzemi, způsob, jakými můžete vytvořit jednotlivé verze, a postup změny verzí.

## <a name="languages"></a>Jazyky

Počínaje verzí 2. x používá modul runtime model rozšiřitelnosti jazyka a všechny funkce ve Function App musí sdílet stejný jazyk. Jazyk funkcí ve Function App se při vytváření aplikace zvolí a udržuje se v nastavení [ \_ \_ modulu runtime Functions Worker](functions-app-settings.md#functions_worker_runtime) . 

Následující tabulka uvádí, které programovací jazyky jsou aktuálně podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v části o [podporovaných jazycích](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Spustit na konkrétní verzi

Ve výchozím nastavení jsou aplikace Function App vytvořené v Azure Portal a pomocí Azure CLI nastavené na verzi 3. x. Tuto verzi můžete podle potřeby upravit. Verzi modulu runtime můžete změnit až na 1. x po vytvoření aplikace Function App, ale před přidáním jakýchkoli funkcí.  Přesun mezi 2. x a 3. x je povolený i u aplikací, které mají funkce, ale přesto se doporučuje nejdřív otestovat novou aplikaci.

## <a name="migrating-from-1x-to-later-versions"></a>Migrace z 1. x na novější verze

Můžete se rozhodnout migrovat existující aplikaci vytvořenou tak, aby používala modul runtime verze 1. x místo toho, aby používala novější verzi. Většina změn, které je třeba provést, souvisí se změnami v jazykovém modulu runtime, jako je například změna rozhraní API jazyka C# mezi .NET Framework 4,7 a .NET Core. Budete také muset zajistit, aby byl kód a knihovny kompatibilní s vámi zvoleným jazykovým modulem runtime. Nakonec si nezapomeňte všimnout všech změn v aktivační události, vazbách a funkcích, které jsou zvýrazněny níže. Pro dosažení nejlepších výsledků migrace byste měli vytvořit novou aplikaci Function App v nové verzi a portovat svůj existující kód funkce verze 1. x do nové aplikace.  

I když je možné provést upgrade na místě tak, že ručně aktualizujete konfiguraci aplikace a z 1. x na vyšší verzi dojde k nějakým změnám. Například v jazyce C# je objekt ladění změněn z `TraceWriter` na `ILogger` . Vytvořením nového projektu verze 3. x můžete začít s aktualizovanými funkcemi založenými na nejnovějších šablonách verze 3. x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Změny aktivačních událostí a vazeb po verzi 1. x

Od verze 2. x je nutné nainstalovat rozšíření pro konkrétní triggery a vazby používané funkcemi ve vaší aplikaci. Jediná výjimka pro tuto aktivační událost HTTP a časovač, která nevyžaduje rozšíření.  Další informace najdete v tématu [registrace a instalace rozšíření vazby](./functions-bindings-register.md).

K dispozici je také několik změn v *function.jsna* nebo atributů funkce mezi verzemi. Například vlastnost centra událostí `path` je nyní `eventHubName` . V [existující tabulce vazeb](#bindings) najdete odkazy na dokumentaci pro každou vazbu.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Změny funkcí a funkcí po verzi 1. x

Několik funkcí bylo odebráno, aktualizováno nebo nahrazeno po verzi 1. x. Tato část podrobně popisuje změny, které vidíte v pozdějších verzích, a to po použití verze 1. x.

Ve verzi 2. x byly provedeny následující změny:

* Klíče pro volání koncových bodů HTTP se vždycky ukládají šifrovaným v úložišti objektů BLOB v Azure. Ve výchozím nastavení ve verzi 1. x byly klíče ve výchozím nastavení uloženy ve službě Azure File Storage. Při upgradu aplikace z verze 1. x na verzi 2. x jsou obnoveny stávající tajné klíče v úložišti souborů.

* Modul runtime verze 2. x neobsahuje integrovanou podporu pro poskytovatele webhooků. Tato změna byla provedena za účelem zvýšení výkonu. Pro Webhooky můžete pořád používat triggery HTTP jako koncové body.

* Konfigurační soubor hostitele (host.json) by měl být prázdný nebo obsahovat řetězec `"version": "2.0"` .

* Pro zlepšení monitorování se řídicí panel WebJobs na portálu, který použil nastavení, [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) nahradí pomocí Azure Application Insights, který toto [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) nastavení používá. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

* Všechny funkce ve Function App musí sdílet stejný jazyk. Při vytváření aplikace Function App musíte zvolit zásobník modulu runtime pro aplikaci. Zásobník modulu runtime je určen [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) hodnotou v nastavení aplikace. Tento požadavek byl přidaný, aby se zlepšila nároky a čas spuštění. Při vývoji v místním prostředí musíte také zahrnout toto nastavení do [local.settings.jsv souboru](functions-run-local.md#local-settings-file).

* Výchozí časový limit pro funkce v plánu App Service se změnil na 30 minut. Můžete ručně změnit časový limit zpět na neomezený pomocí nastavení [functionTimeout](functions-host-json.md#functiontimeout) v host.jszapnuto.

* Omezení Concurrency protokolu HTTP jsou ve výchozím nastavení implementovaná pro funkce plánu spotřeby s výchozí 100 souběžných požadavků na instanci. To můžete změnit v [`maxConcurrentRequests`](functions-host-json.md#http) nastavení host.jsv souboru.

* Z důvodu [omezení rozhraní .NET Core](https://github.com/Azure/azure-functions-host/issues/3414)byla odebrána podpora funkcí skriptu jazyka F # (. FSX). Zkompilované funkce F # (. FS) jsou pořád podporované.

* Formát adresy URL Event Grid webhookech triggeru byl změněn na `https://{app}/runtime/webhooks/{triggerName}` .

## <a name="migrating-from-2x-to-3x"></a>Migrace z 2. x na 3. x

Verze Azure Functions 3. x je vysoce zpětně kompatibilní s verzí 2. x.  Mnohé aplikace by měly být schopné bezpečně upgradovat na 3. x bez jakýchkoli změn kódu.  Při přechodu na 3. x je doporučováno, nezapomeňte spustit rozsáhlé testy před změnou hlavní verze v produkčních aplikacích.

### <a name="breaking-changes-between-2x-and-3x"></a>Přerušení změn mezi 2. x a 3. x

Níže jsou uvedené změny, které je třeba znát před upgradem aplikace 2. x na 3. x.

#### <a name="javascript"></a>JavaScript

* Výstupní vazby přiřazené prostřednictvím `context.done` nebo vrácených hodnot se nyní chovají stejně jako nastavení v `context.bindings` .

* Objekt triggeru časovače je camelCase místo PascalCase

* Funkce aktivované centrem událostí s `dataType` binárním zobrazením obdrží pole `binary` místo `string` .

* Datová část požadavku HTTP nemůže být již k dispozici prostřednictvím `context.bindingData.req` .  Stále je k němu možné přistupovat jako vstupní parametr, `context.req` a v `context.bindings` .

* Node.js 8 již není podporováno a nebude spuštěno ve 3. x funkcích.

#### <a name="net"></a>.NET

* [Synchronní operace serveru jsou ve výchozím nastavení zakázané](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Změna verze aplikací v Azure

Verze modulu runtime Functions používané publikovanými aplikacemi v Azure je využívána [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) nastavením aplikace. Podporovány jsou následující hlavní hodnoty běhové verze:

| Hodnota | Cíl modulu runtime |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | verze |

>[!IMPORTANT]
> Neměňte toto nastavení, protože může být vyžadováno jiné změny nastavení aplikace a změny kódu funkce.

### <a name="locally-developed-application-versions"></a>Místně vyvinuté verze aplikací

Můžete provést následující aktualizace pro aplikace Functions, aby se místně změnily cílové verze.

#### <a name="visual-studio-runtime-versions"></a>Verze Visual Studio runtime

V aplikaci Visual Studio vyberete verzi modulu runtime při vytváření projektu. Nástroje Azure Functions pro Visual Studio podporují tři hlavní verze modulu runtime. Správná verze se používá při ladění a publikování na základě nastavení projektu. Nastavení verze jsou definována v `.csproj` souboru v následujících vlastnostech:

##### <a name="version-1x"></a>Verze 1. x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Verze 2. x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Verze 3. x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3. x a .NET vyžaduje `Microsoft.NET.Sdk.Functions` minimálně rozšíření `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aktualizace 2. x aplikací na 3. x v aplikaci Visual Studio

Můžete otevřít existující funkci cílící na 2. x a přesunout na 3. x tím, že `.csproj` soubor upravíte a aktualizujete výše uvedené hodnoty.  Visual Studio spravuje běhové verze automaticky pro vás na základě metadat projektu.  Je však možné, pokud jste ještě nevytvořili aplikaci 3. x ještě předtím, než aplikace Visual Studio ještě nemá šablony a modul runtime pro 3. x na vašem počítači.  Tato chyba se může vyskytovat s chybou, jako je například "není k dispozici modul runtime" bez funkcí, který odpovídá verzi zadané v projektu. "  Pokud chcete načíst nejnovější šablony a modul runtime, Projděte si prostředí a vytvořte nový projekt funkce.  Až se dostanete k výběru verze a šablony, počkejte, až Visual Studio dokončí načítání nejnovějších šablon.  Jakmile budou k dispozici nejnovější šablony .NET Core 3 a zobrazí se, měli byste být schopni spustit a ladit všechny projekty nakonfigurované pro verzi 3. x.

> [!IMPORTANT]
> Funkce verze 3. x lze vyvíjet pouze v aplikaci Visual Studio, pokud používáte Visual Studio verze 16,4 nebo novější.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code a Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) se používá pro vývoj na příkazovém řádku a také [rozšířením Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. Pro vývoj v rámci verze 3. x nainstalujte základní nástroje verze 3. x. Vývoj verze 2. x vyžaduje základní nástroje, verze 2. x, a tak dále. Další informace najdete v tématu [instalace Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools).

V případě vývoje Visual Studio Code možná budete muset aktualizovat také nastavení uživatele, `azureFunctions.projectRuntime` aby odpovídalo verzi nainstalovaných nástrojů.  Toto nastavení také aktualizuje šablony a jazyky používané při vytváření aplikace Function App.  Pokud chcete vytvářet aplikace v nástroji, `~3` aktualizujte `azureFunctions.projectRuntime` nastavení uživatele na `~3` .

![Nastavení modulu runtime rozšíření Azure Functions](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplikace Maven a Java

Aplikace Java můžete migrovat z verze 2. x na 3. x tak, že [nainstalujete verzi 3. x základních nástrojů](functions-run-local.md#install-the-azure-functions-core-tools) požadovaných ke spuštění místně.  Po ověření, že vaše aplikace funguje správně místně ve verzi 3. x, aktualizujte soubor aplikace `POM.xml` a upravte `FUNCTIONS_EXTENSION_VERSION` nastavení na `~3` , jako v následujícím příkladu:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

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

Další informace naleznete v následujících zdrojích:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak cílit na verze modulu runtime Azure Functions](set-runtime-version.md)
* [Zpráva k vydání verze](https://github.com/Azure/azure-functions-host/releases)

---
title: Přehled verzí runtime Azure Functions
description: Azure Functions podporuje více verzí runtime. Naučte se rozdíly mezi nimi a jak si vybrat ten, který je pro vás ten pravý.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 21a7b25087efd5d4adf2154c935636c263df9afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276553"
---
# <a name="azure-functions-runtime-versions-overview"></a>Přehled verzí runtime Azure Functions

Hlavní verze runtime Azure Functions se vztahují k verzi rozhraní .NET, na kterém je runtime založen. V následující tabulce je uvedena aktuální verze běhu, úroveň vydání a související verze rozhraní .NET. 

| Runtime verze | Úroveň vydání<sup>1</sup> | Verze rozhraní .NET | 
| --------------- | ------------- | ------------ |
| 3.x | GA | .NET Jádro 3.1 | 
| 2.x | GA | .NET Core 2.2 |
| 1.x | GA<sup>2</sup> | Rozhraní .NET Framework 4.6<sup>3</sup> |

<sup>Pro</sup> produkční scénáře je podporováno 1 verze GA.   
<sup>2</sup> Verze 1.x je v režimu údržby. Vylepšení jsou k dispozici pouze v novějších verzích.   
<sup>3</sup> Podporuje jenom vývoj na portálu Azure nebo místně v počítačích se systémem Windows.

Tento článek podrobně popisuje některé rozdíly mezi různými verzemi, jak můžete vytvořit jednotlivé verze a jak změnit verze.

## <a name="languages"></a>Jazyky

Počínaje verzí 2.x, runtime používá model rozšiřitelnosti jazyka a všechny funkce v aplikaci funkce musí sdílet stejný jazyk. Jazyk funkcí v aplikaci funkce je vybrán při vytváření aplikace a je udržován v nastavení [FUNCTIONS\_WORKER\_RUNTIME.](functions-app-settings.md#functions_worker_runtime) 

Azure Functions 1.x experimentální jazyky nelze použít nový model, takže nejsou podporovány v 2.x. Následující tabulka označuje, které programovací jazyky jsou aktuálně podporovány v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [Podporované jazyky](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Spuštění v konkrétní verzi

Ve výchozím nastavení jsou aplikace funkcí vytvořené na webu Azure Portal a podle nařízení příkazového příkazu Azure nastaveny na verzi 3.x. Tuto verzi můžete podle potřeby upravit. Verzi runtime můžete změnit pouze na 1.x po vytvoření aplikace funkcí, ale před přidáním všech funkcí.  Přechod mezi 2.x a 3.x je povolen i s aplikacemi, které mají funkce, ale stále se doporučuje nejprve otestovat v nové aplikaci.

## <a name="migrating-from-1x-to-later-versions"></a>Migrace z verze 1.x na novější verze

Můžete se rozhodnout migrovat existující aplikaci zapsanou tak, aby používala runtime verze 1.x, abyste místo toho použili novější verzi. Většina změn, které je třeba provést, souvisí se změnami v běhu jazyka, jako jsou například změny rozhraní API jazyka C# mezi rozhraními .NET Framework 4.7 a .NET Core. Budete se také muset ujistit, že váš kód a knihovny jsou kompatibilní s jazykovým runtimem, který zvolíte. Nakonec nezapomeňte zaznamenat všechny změny v aktivační události, vazbách a funkcích zvýrazněných níže. Chcete-li dosáhnout nejlepších výsledků migrace, měli byste vytvořit novou aplikaci funkcí v nové verzi a přenést stávající kód funkce verze 1.x do nové aplikace.  

I když je možné provést upgrade "na místě" ruční aktualizací konfigurace aplikace, přejdete z 1.x na vyšší verzi, včetně některých narušujících změn. Například v c#, ladicí objekt se `TraceWriter` `ILogger`změní z na . Vytvořením nové verze projektu 3.x začnete s aktualizovanými funkcemi založenými na nejnovějších šablonách verze 3.x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Změny aktivačních událostí a vazeb po verzi 1.x

Počínaje verzí 2.x je nutné nainstalovat rozšíření pro konkrétní aktivační události a vazby používané funkcemi ve vaší aplikaci. Jedinou výjimkou pro tento protokol HTTP a časovač aktivační události, které nevyžadují rozšíření.  Další informace naleznete v [tématu Registrovat a nainstalovat rozšíření vazby](./functions-bindings-register.md).

Existuje také několik změn v *function.json* nebo atributy funkce mezi verzemi. Například vlastnost Event `path` Hub `eventHubName`je nyní . Naleznete [existující vazby tabulka](#bindings) odkazy na dokumentaci pro každou vazbu.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Změny funkcí a funkcí po verzi 1.x

Několik funkcí bylo odebráno, aktualizováno nebo nahrazeno po verzi 1.x. Tato část podrobně popisuje změny, které se zobrazí v novějších verzích po použití verze 1.x.

Ve verzi 2.x byly provedeny následující změny:

* Klíče pro volání koncových bodů HTTP jsou vždy uloženy šifrované v úložišti objektů Blob Azure. Ve verzi 1.x byly klíče ve výchozím nastavení uložené v úložišti souborů Azure. Při upgradu aplikace z verze 1.x na verzi 2.x se obnoví existující tajné klíče, které jsou v úložišti souborů.

* Za běhu verze 2.x neobsahuje integrovanou podporu pro poskytovatele webhooku. Tato změna byla provedena ke zlepšení výkonu. Aktivační události HTTP můžete stále používat jako koncové body pro webhooky.

* Konfigurační soubor hostitele (host.json) by `"version": "2.0"`měl být prázdný nebo by měl mít řetězec .

* Chcete-li zlepšit monitorování, řídicí panel WebJobs [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) na portálu, který používá nastavení [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) je nahrazen Azure Application Insights, který používá nastavení. Další informace naleznete v [tématu Sledování funkcí Azure](functions-monitoring.md).

* Všechny funkce v aplikaci funkce musí sdílet stejný jazyk. Když vytvoříte aplikaci funkcí, musíte pro aplikaci zvolit zásobník za běhu. Zásobník za běhu je [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) určen hodnotou v nastavení aplikace. Tento požadavek byl přidán ke zlepšení stopy a doby spuštění. Při vývoji místně je nutné toto nastavení zahrnout také do [souboru local.settings.json](functions-run-local.md#local-settings-file).

* Výchozí časový limit pro funkce v plánu služby App Service se změní na 30 minut. Časový čas můžete ručně změnit zpět na neomezený pomocí nastavení [functionTimeout](functions-host-json.md#functiontimeout) v host.json.

* Omezení souběžnosti HTTP jsou implementována ve výchozím nastavení pro funkce plánu spotřeby, s výchozí 100 souběžných požadavků na instanci. Tuto změnu [`maxConcurrentRequests`](functions-host-json.md#http) můžete změnit v nastavení v souboru host.json.

* Z důvodu [omezení jádra .NET](https://github.com/Azure/azure-functions-host/issues/3414)byla odebrána podpora funkcí skriptu Jazyka F#. Zkompilované funkce Jazyka F# (.fs) jsou stále podporovány.

* Formát adresy URL webových háků event `https://{app}/runtime/webhooks/{triggerName}`gridu byl změněn na .

## <a name="migrating-from-2x-to-3x"></a>Migrace z 2.x na 3.x

Azure Functions verze 3.x je vysoce zpětně kompatibilní s verzí 2.x.  Mnoho aplikací by mělo být schopno bezpečně upgradovat na 3.x bez jakýchkoli změn kódu.  Při přechodu na 3.x se doporučuje, nezapomeňte spustit rozsáhlé testy před změnou hlavní verze v produkčních aplikacích.

### <a name="breaking-changes-between-2x-and-3x"></a>Nejnovější změny mezi 2.x a 3.x

Následují změny, které je třeba si uvědomit před upgradem aplikace 2.x na 3.x.

#### <a name="javascript"></a>JavaScript

* Výstupní vazby `context.done` přiřazené prostřednictvím nebo vrácené `context.bindings`hodnoty se nyní chovají stejně jako nastavení v .

* Objekt aktivační události časovače je camelCase místo PascalCase

* Funkce spouštěné `dataType` centrem událostí s `binary` binárním souborem obdrží pole aplikace `string`.

* Datové části požadavku HTTP již nelze `context.bindingData.req`přistupovat prostřednictvím aplikace .  Stále je přístupný jako vstupní parametr `context.req`, `context.bindings`a in .

* Soubor Node.js 8 již není podporován a nebude spuštěn ve funkcích 3.x.

#### <a name="net"></a>.NET

* [Synchronní operace serveru jsou ve výchozím nastavení zakázány](https://docs.microsoft.com/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Změna verze aplikací v Azure

Verze běhu funkce používané publikovaných aplikací v Azure je [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) diktována nastavení aplikace. Jsou podporovány následující hlavní hodnoty verzí modulu runtime:

| Hodnota | Cíl runtime |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Toto nastavení libovolně neměňte, protože mohou být vyžadovány další změny nastavení aplikace a změny kódu funkce.

### <a name="locally-developed-application-versions"></a>Místně vyvinuté verze aplikací

Můžete provést následující aktualizace pro funkce aplikací místně změnit cílové verze.

#### <a name="visual-studio-runtime-versions"></a>Runtime verze visual studia

V sadě Visual Studio vyberete při vytváření projektu verzi runtime. Nástroje Azure Functions pro Visual Studio podporuje tři hlavní runtime verze. Správná verze se používá při ladění a publikování na základě nastavení projektu. Nastavení verze jsou definována v souboru `.csproj` v následujících vlastnostech:

##### <a name="version-1x"></a>Verze 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Verze 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Verze 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Azure Functions 3.x a `Microsoft.NET.Sdk.Functions` .NET vyžaduje, aby rozšíření bylo alespoň `3.0.0`.

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aktualizace aplikací 2.x na 3.x v Sadě Visual Studio

Můžete otevřít existující funkci cílení 2.x a přesunout na `.csproj` 3.x úpravou souboru a aktualizací výše uvedených hodnot.  Visual Studio spravuje runtime verze automaticky za vás na základě metadat projektu.  Je to však možné, pokud jste nikdy nevytvořili aplikaci 3.x před tím, než Visual Studio ještě nemá šablony a runtime pro 3.x v počítači.  To může prezentovat s chybou, jako je "žádné funkce runtime k dispozici, který odpovídá verzi zadané v projektu."  Chcete-li načíst nejnovější šablony a runtime, projděte si prostředí a vytvořte nový projekt funkce.  Až se dostanete na obrazovku výběru verze a šablony, počkejte, až Visual Studio dokončí načítání nejnovějších šablon.  Jakmile jsou k dispozici a zobrazeny nejnovější šablony .NET Core 3, měli byste být schopni spustit a ladit jakýkoli projekt nakonfigurovaný pro verzi 3.x.

> [!IMPORTANT]
> Funkce verze 3.x lze vyvinout pouze v sadě Visual Studio, pokud používáte Visual Studio verze 16.4 nebo novější.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Základní nástroje VS Code a Azure Functions

[Nástroje Azure Functions Core Tools](functions-run-local.md) se používají pro vývoj příkazového řádku a také rozšíření Azure [Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro kód Visual Studia. Chcete-li vyvíjet verzi 3.x, nainstalujte verzi 3.x základních nástrojů. Vývoj verze 2.x vyžaduje verzi 2.x základních nástrojů a tak dále. Další informace najdete [v tématu Instalace základních nástrojů funkcí Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Pro vývoj kódu sady Visual Studio může být také `azureFunctions.projectRuntime` nutné aktualizovat nastavení uživatele tak, aby odpovídalo verzi nainstalovaných nástrojů.  Toto nastavení také aktualizuje šablony a jazyky používané při vytváření funkčních aplikací.  Chcete-li `~3` vytvořit aplikace `azureFunctions.projectRuntime` v byste `~3`aktualizovat nastavení uživatele na .

![Nastavení runtime rozšíření Azure Functions](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>Aplikace Maven a Java

Aplikace Java můžete migrovat z verze 2.x na 3.x [instalací verze 3.x základních nástrojů potřebných](functions-run-local.md#install-the-azure-functions-core-tools) ke spuštění místně.  Po ověření, že vaše aplikace funguje správně běží místně ve verzi `POM.xml` 3.x, aktualizujte soubor aplikace upravit `FUNCTIONS_EXTENSION_VERSION` nastavení na `~3`, jako v následujícím příkladu:

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

Počínaje verzí 2.x používá runtime nový [model rozšiřitelnosti vazby,](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) který nabízí tyto výhody:

* Podpora pro rozšíření vazby třetích stran.

* Oddělení runtime a vazeb. Tato změna umožňuje rozšíření vazby, které mají být verzí a uvolněny nezávisle. Můžete se například rozhodnout pro upgrade na verzi rozšíření, která závisí na novější verzi základní sady SDK.

* Lehčí spuštění prostředí, kde jsou známy a načteny pouze vazby v provozu za běhu.

S výjimkou aktivačních událostí protokolu HTTP a časovače musí být všechny vazby explicitně přidány do projektu aplikace funkce nebo registrovány na portálu. Další informace naleznete v [tématu Register binding extensions](./functions-bindings-expressions-patterns.md).

V následující tabulce jsou uvedeny, které vazby jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak cílit na verze runtime Azure Functions](set-runtime-version.md)
* [Poznámky k verzi](https://github.com/Azure/azure-functions-host/releases)

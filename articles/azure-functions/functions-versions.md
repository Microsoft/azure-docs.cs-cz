---
title: Přehled verze modulu runtime Azure Functions
description: Služba Azure Functions podporuje více verzí modulu runtime. Poznejte rozdíly mezi nimi a jak zvolit ten, který je pro vás nejvhodnější.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: f2f1313461fcb58ea48af99aeda2f7005534fe34
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885183"
---
# <a name="azure-functions-runtime-versions-overview"></a>Přehled verze modulu runtime Azure Functions

 Existují dvě hlavní verze modulu runtime Azure Functions: 1.x a 2.x. Aktuální verze, ve kterém jsou prováděny nový práce funkce a vylepšení je 2.x, i když jsou podporované pro produkční scénáře.  Následující podrobnosti některé rozdíly mezi těmito dvěma, jak můžete vytvořit jednotlivé verze a upgradovat z verze 1.x na 2.x.

> [!NOTE]
> Tento článek se týká ke cloudové službě Azure Functions. Informace o produkt ve verzi preview, která umožňuje spouštění Azure Functions na místním prostředí, najdete v článku [přehled modulu Runtime Azure Functions](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Vývoj pro různé platformy

Modul runtime verze 2.x běží na .NET Core 2, která umožňuje spustit na všech platformách podporovaných aplikací .NET Core, včetně systému macOS a Linux. Spouštění v .NET Core umožňují vývoj pro různé platformy a scénáře hostování.

Naproti tomu modul runtime verze 1.x podporuje jenom vývoj a hostování na webu Azure Portal nebo počítačích s Windows.

## <a name="languages"></a>Jazyky

Modul runtime verze 2.x používá nový model rozšiřitelnosti jazyka. Ve verzi 2.x, všechny funkce v aplikaci function app musí sdílet stejný jazyk. Jazykové funkce v aplikaci function app se volí při vytváření aplikace.

Azure Functions 1.x experimentálních jazyků, neaktualizuje se použít nový model, takže nejsou podporovány v 2.x. Následující tabulka uvádí, jaké programovací jazyky, které jsou aktuálně podporované ve verzi každého modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [podporované jazyky](supported-languages.md).

## <a name="creating-1x-apps"></a>Spustit ve verzi 1.x

Ve výchozím nastavení, aplikace function App na webu Azure Portal vytvořili nastavené na verzi 2.x. Pokud je to možné, používejte tuto verzi modulu runtime, ve kterém uskutečněných investic do nové funkce. Pokud potřebujete, můžete stále spuštěn aplikaci function app na modul runtime verze 1.x. Verze modulu runtime lze změnit pouze po vytvoření aplikace function app, ale předtím, než přidáte všechny funkce. Zjistěte, jak připnout verze modulu runtime pro 1.x, najdete v článku [zobrazit a aktualizovat aktuální verze modulu runtime](set-runtime-version.md#view-and-update-the-current-runtime-version).

## <a name="migrating-from-1x-to-2x"></a>Migrace z 1.x na 2.x

Můžete se rozhodnout migrovat stávající aplikace napsané pomocí modulu runtime verze 1.x místo toho použít verzi 2.x. Většina změn, které potřebujete, aby se vztahují na změny v modulu runtime jazyka, jako jsou například změny rozhraní API jazyka C# mezi .NET Framework 4.7 a .NET Core 2. Budete také muset Ujistěte se, že váš kód a knihoven musí být kompatibilní s CLR, kterou zvolíte. A konečně nezapomeňte si poznamenat všechny změny v aktivační události, vazby a funkce, jejichž přehled najdete níže. Nejlepších výsledků migrace, měli byste vytvořit novou aplikaci function app pro verzi 2.x a port existující funkce verzi 1.x kód do nové aplikace.  

### <a name="changes-in-triggers-and-bindings"></a>Změny v aktivačními událostmi a vazbami

Verze 2.x je nutné nainstalovat rozšíření pro konkrétní triggery a vazby používá funkce ve vaší aplikaci. Jedinou výjimkou tohoto protokolu HTTP a časovač aktivačních událostí, které nevyžadují rozšíření.  Další informace najdete v tématu [registrace a instalace rozšíření vazby](./functions-triggers-bindings.md#register-binding-extensions).

Také došlo k několika změnám v `function.json` nebo atributy funkce mezi verzemi. Například centra událostí `path` vlastnost je nyní `eventHubName`. Zobrazit [existující vazby tabulky](#bindings) odkazy na dokumentaci pro každou vazbu.

### <a name="changes-in-features-and-functionality"></a>Změny ve funkcích

Několik funkcí, které se taky odstranila, aktualizovat nebo nahradit v nové verzi. Tato část podrobně popisuje změny, které se zobrazí ve verzi 2.x použili verzi 1.x.

Ve verzi 2.x, byly provedeny následující změny:

* Klíče pro volání koncových bodů HTTP jsou vždy uloženy v zašifrované podobě v úložišti objektů Blob v Azure. Ve verzi 1.x, klíče byly uložené v Azure File storage jako výchozí. Při upgradu z verze aplikace 1.x na verzi 2.x, existující tajné kódy, které jsou ve službě file storage se obnoví.

* Modul runtime verze 2.x neobsahuje integrovanou podporu pro poskytovatele webhooku. Tato změna byla provedena ke zlepšení výkonu. Stále můžete triggerů HTTP jako koncové body pro webhooky.

* Konfigurační soubor hostitele (host.json) by měl být prázdný nebo obsahovat řetězec `"version": "2.0"`.

* Ke zlepšení monitorování na řídicím panelu webjobs nechat na portálu, který používá [ `AzureWebJobsDashboard` ](functions-app-settings.md#azurewebjobsdashboard) nastavení se nahradí pomocí Azure Application Insights, který používá [ `APPINSIGHTS_INSTRUMENTATIONKEY` ](functions-app-settings.md#appinsightsinstrumentationkey) nastavení. Další informace najdete v tématu [monitorování Azure Functions](functions-monitoring.md).

* Všechny funkce v aplikaci function app musí sdílet stejný jazyk. Když vytvoříte aplikaci function app, musíte zvolit zásobník modulu runtime pro aplikace. Zásobník modulu runtime je určená [ `FUNCTIONS_WORKER_RUNTIME` ](functions-app-settings.md#functionsworkerruntime) hodnota v nastavení aplikace. Tento požadavek byl přidán do zrychlit uvádění nároky na místo a spuštění. Při vývoji místně, musíte taky zahrnout toto nastavení [souboru local.settings.json](functions-run-local.md#local-settings-file).

* Výchozí hodnota časového limitu pro funkce v plánu služby App Service se změní na 30 minut. Časový limit můžete ručně změnit zpět na neomezený počet pomocí [functionTimeout](functions-host-json.md#functiontimeout) nastavení v host.json.

* Omezení souběžnosti HTTP jsou implementovány ve výchozím nastavení pro funkce plánu consumption, výchozí hodnota je 100 souběžných požadavků na jednu instanci. Můžete to změnit [ `maxConcurrentRequests` ](functions-host-json.md#http) nastavení v souboru host.json.

* Z důvodu [.NET core omezení](https://github.com/Azure/azure-functions-host/issues/3414), podpora pro F # skript (.fsx) funkce se odebrala. Kompilované funkcí F # (.fs) jsou stále podporovány.

* Formát adresy URL služby Event Grid trigger webhooky se změnil na `https://{app}/runtime/webhooks/{triggerName}`.

### <a name="migrating-a-locally-developed-application"></a>Migrace místní vyvinuté aplikace

Můžete mít existující projekty aplikací vyvinutých místně pomocí modulu runtime verze 1.x fungovat. Upgradovat na verzi 2.x, by měl vytvořit projekt lokální funkce aplikace proti verzi 2.x a port, vaše existující kód do nové aplikace. Může ručně aktualizovat existující projekt a kód, druh "místní" upgrade. Ale existuje mnoho dalších vylepšení mezi verzi 1.x a verze 2.x, stále je nutné provést. V jazyce C#, třeba ladění objektu se změnil z `TraceWriter` k `ILogger`. Tím vytvoříte nový projekt verze 2.x, můžete začít aktualizované funkce založené na nejnovější verzi 2.x šablony.

#### <a name="visual-studio-runtime-versions"></a>Verze modulu runtime Visual Studio

V sadě Visual Studio vyberte verzi modulu runtime při vytváření projektu. Azure Functions tools for Visual Studio podporuje obě verze hlavní modulu runtime. Správná verze se používá při ladění a publikování podle nastavení projektu. Nastavení verze jsou definovány v `.csproj` soubor v následující vlastnosti:

##### <a name="version-1x"></a>Verzi 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Verze 2.x

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Při ladění nebo projekt publikovat, je použít správnou verzi modulu runtime.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code a Azure Functions Core Tools

[Nástroje Azure Functions Core](functions-run-local.md) se používá pro vývoj pro příkazový řádek a také podle [rozšíření Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) pro Visual Studio Code. Vývoj proti verzi 2.x, instalace verze 2.x základní nástroje. Vývoj pro verzi 1.x vyžaduje verzi 1.x základní nástroje. Další informace najdete v tématu [instalace nástrojů Azure Functions Core](functions-run-local.md#install-the-azure-functions-core-tools).

Pro vývoj pro Visual Studio Code, budete také muset aktualizovat nastavení hlavního názvu uživatele pro `azureFunctions.projectRuntime` tak, aby odpovídala verzi nainstalované nástroje.  Toto nastavení rovněž aktualizuje šablony a jazyky používané během vytváření aplikace funkcí.

### <a name="changing-version-of-apps-in-azure"></a>Změna verze aplikace v Azure

Verze modulu runtime Functions používá publikované aplikace v Azure závisí [ `FUNCTIONS_EXTENSION_VERSION` ](functions-app-settings.md#functionsextensionversion) nastavení aplikace. Hodnota `~2` zaměřuje na modul runtime verze 2.x a `~1` zaměřuje na modul runtime verze 1.x. Není libovolně toto nastavení změnit, protože jsou pravděpodobně vyžaduje další změny nastavení aplikace a změny kódu ve vašich funkcí. Další informace o doporučeným způsobem, jak migrovat aplikace function app na různé verze, najdete v článku [způsobu cílení verze modulu runtime Azure Functions](set-runtime-version.md).

## <a name="bindings"></a>Vazby

Používá nový modul runtime verze 2.x [model rozšiřitelnosti vazby](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , který nabízí tyto výhody:

* Podpora pro rozšíření vazby třetích stran.

* Oddělení modulu runtime a vazby. Tato změna umožňuje rozšíření vazby se systémovou správou verzí a vydávají se nezávisle na sobě. Můžete například pokud se rozhodnete k upgradu na verzi rozšíření, které spoléhá na novější verzi základní sadu SDK.

* Světlejší spuštění prostředí, kde jsou pouze vazby používá známé a načten modulem runtime.

S výjimkou aktivační události HTTP a časovač všechny vazby musí být explicitně přidány do projektu aplikace funkcí, nebo registrované na portálu. Další informace najdete v tématu [registraci rozšíření vazby](functions-triggers-bindings.md#register-binding-extensions).

V následující tabulce jsou uvedeny vazby, které jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak se zaměřit na verze modulu runtime Azure Functions](set-runtime-version.md)
* [Poznámky k verzi](https://github.com/Azure/azure-functions-host/releases)

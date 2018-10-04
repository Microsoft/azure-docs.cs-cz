---
title: Přehled verze modulu runtime Azure Functions
description: Služba Azure Functions podporuje více verzí modulu runtime. Poznejte rozdíly mezi nimi a jak zvolit ten, který je pro vás nejvhodnější.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: glenga
ms.openlocfilehash: d4a0d53c3438be9ea4e0229e4a456ed6f5960996
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249056"
---
# <a name="azure-functions-runtime-versions-overview"></a>Přehled verze modulu runtime Azure Functions

 Existují dvě hlavní verze modulu runtime Azure Functions: 1.x a 2.x. Aktuální verze, ve kterém jsou prováděny nový práce funkce a vylepšení je 2.x, i když jsou podporované pro produkční scénáře.  Následující podrobnosti některé rozdíly mezi těmito dvěma, jak můžete vytvořit jednotlivé verze a upgradovat z verze 1.x na 2.x.

> [!NOTE] 
> Tento článek se týká ke cloudové službě Azure Functions. Informace o produkt ve verzi preview, která umožňuje spouštění Azure Functions na místním prostředí, najdete v článku [přehled modulu Runtime Azure Functions](functions-runtime-overview.md).

## <a name="creating-1x-apps"></a>Vytváření aplikací 1.x

Nové aplikace, které jsou vytvořené na webu Azure Portal jsou nastaveny na 2.x ve výchozím nastavení, protože se jedná o nejnovější verzi a kde uskutečněných investic do nové funkce.  V1.x aplikací ale můžete vytvořit pomocí tohoto postupu.

1. Vytvoření funkce Azure na webu Azure Portal
1. Otevřete vytvořenou aplikaci a i když je prázdné otevřete **nastavení – funkce**
1. Změnit verzi ~ 2 až ~ 1.  *Tento přepínač bude zakázáno, pokud máte ve své aplikaci funkce*.
1. Klikněte na Uložit a znovu spusťte aplikaci.  Všechny šablony by měl nyní vytvářet a spouštět v 1.x.

## <a name="cross-platform-development"></a>Vývoj pro různé platformy

Modul runtime 1.x podporuje vývoj a hostování pouze na portálu nebo na Windows. Modul runtime 2.x běží v .NET Core 2, což znamená, že můžete spustit na všech platformách podporovaných aplikací .NET Core, včetně systému macOS a Linux. To umožňuje vývoj pro různé platformy a scénáře hostování.

## <a name="languages"></a>Jazyky

Modul runtime 2.x používá nový model rozšiřitelnosti jazyka. Kromě toho zlepšit výkon a nástroje pro každou aplikaci v 2.x limitují mít v jednom jazyce jenom funkce. C#, F #, JavaScript a Java jsou aktuálně podporované jazyky v 2.x. Azure Functions 1.x experimentálních jazyků neaktualizovaly použít nový model, takže nejsou podporovány v 2.x. Následující tabulka uvádí podporované programovací jazyky v každé verzi modulu runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Další informace najdete v tématu [podporované jazyky](supported-languages.md).

## <a name="migrating-from-1x-to-2x"></a>Migrace z 1.x na 2.x

Můžete chtít přesunout stávající aplikace napsané v 1.x do 2.x.  Většinu aspektů v přesouvání mezi verzemi vyžaduje se vztahují k jazykové změny modulu runtime uvedené výše (například C# přesouvá z rozhraní .NET Framework 4.7 pro .NET Core 2).  Ujistěte se, že váš kód bude potřeba a jsou kompatibilní s jazykové moduly runtime používá knihovny.  Také nezapomeňte si poznamenat všechny změny v aktivační události, vazby a funkce, jejichž přehled najdete níže.

### <a name="changes-in-triggers-and-bindings"></a>Změny v aktivačními událostmi a vazbami

Zatímco většina konfigurací a aktivační události a vazby vlastnosti zůstávají stejné mezi verzemi, v 2.x je potřeba instalovat žádné triggerem nebo vazbou do aplikace. Jedinou výjimkou tohoto je aktivační události HTTP a časovač.  Zobrazit [registrace a instalace rozšíření vazby](./functions-triggers-bindings.md#register-binding-extensions).  Všimněte si, že může být také se změnami `function.json` nebo atributy funkce mezi verzemi (například služby cosmos DB `connection` vlastnost je nyní `ConnectionStringSetting`).  Odkaz [existující vazby tabulky](#bindings) odkazy na dokumentaci pro každou vazbu.

### <a name="changes-in-features-available"></a>Změny ve funkcích, které jsou k dispozici

Kromě změn v jazycích a vazby existují některé funkce, které byly odebrány, aktualizovat nebo nahradit mezi verzemi.  Níže jsou uvedeny některé hlavní důležité informace při od verze 2.x po použití 1.x.  V v2.x byly provedeny následující změny:

* Klíče pro volání funkce bude vždy uloženy v úložišti objektů blob šifrované. V 1.x ve výchozím nastavení by byly ve službě file storage a nich nedají přesunout do objektu blob-li povolit funkce, jako je sloty.  Upgrade aplikace 1.x 2.x a tajné kódy ve službě file storage jsou aktuálně bude obnoven.
* Ke zlepšení výkonu, jsou triggerů typu "webhooku" odebrán a nahrazen parametrem aktivační události "HTTP".
* Konfigurace hostitele (`host.json`) by měla být prázdná ani obsahovat `version` z `2.0` pro jednu z vlastností.
* K vylepšení monitorování a observability řídicím panelu webjobs nechat (`AzureWebJobsDashboard`) je nahrazen [Azure Application Insights](functions-monitoring.md) (`APPINSIGHTS_INSTRUMENTATIONKEY`)
* Nastavení aplikace (`local.settings.json`) požadovat hodnotu pro vlastnost `FUNCTIONS_WORKER_RUNTIME` , která se mapuje na jazyk aplikace `dotnet | node | java | python`.
    * Ke zlepšení času nároky na místo a spuštění aplikace jsou omezené na jeden jazyk. Můžete publikovat víc aplikací má funkce v různých jazycích pro stejné řešení.
* Výchozí hodnota časového limitu pro funkce v plánu služby app service je 30 minut.  To je stále možné ručně nastavit neomezený počet.
* [Z důvodu .NET core omezením](https://github.com/Azure/azure-functions-host/issues/3414), `.fsx` skripty pro funkcí F # se odebraly. Funkce F # jsou stále podporovány.
* Formát založené na webhoocích aktivační události (například služby Event Grid) se změnila na `https://{app}/runtime/webhooks/{triggerName}`

### <a name="upgrading-a-locally-developed-application"></a>Upgrade lokálně vyvinuté aplikace

Pokud vaše aplikace v1.x byla vyvinuta místně, můžete provádět změny aplikace nebo projekt, aby byl kompatibilní s v2.  Doporučujeme vytvořit novou aplikaci a port kód do nové aplikace.  I když existují změny, které by mohly být provedeny do existující aplikace v místě provedení upgradu, existuje mnoho dalších vylepšení mezi v1 a v2, který staršího kódu pravděpodobně není využití výhod (například v jazyce C# změny z `TraceWriter` k `ILogger`) .  

Doporučená cesta je začít od některé ze šablon v2 a přesuňte kód do aplikace nebo nového projektu.

#### <a name="visual-studio-runtime-versions"></a>Verze modulu runtime Visual Studio

V sadě Visual Studio vyberte verzi modulu runtime při vytváření projektu.  Visual Studio obsahuje bity pro oba hlavní verze a můžete dynamicky využívat ten správný pro projekt.  Tato nastavení jsou odvozeny z `.csproj` souboru.  Pro aplikace 1.x projekt má vlastnosti

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

Ve verzi v2 jsou vlastnosti projektu

```xml
<TargetFramework>netstandard2.0</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

Kliknutím na ladění nebo publikování správně nastavit správnou verzi pro nastavení projektu.

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code a Azure Functions Core Tools

Další místní nástroje se spoléhá na Azure Functions Core Tools.  Tyto nástroje jsou nainstalovány na počítač, a obecně jenom jedna verze je nainstalována na vývojovém počítači najednou.  Zobrazit [pokyny postup instalace konkrétní verze základní nástroje](./functions-run-local.md).

Pro VS Code budete také muset aktualizovat nastavení hlavního názvu uživatele pro `azureFunctions.projectRuntime` tak, aby odpovídala verzi nainstalované nástroje.  Tím se aktualizuje i šablon a jazyky, které jsou prezentované při vytváření nových aplikací.

### <a name="changing-version-of-apps-in-azure"></a>Změna verze aplikace v Azure

Verze publikované aplikace, které se konfigurují pomocí nastavení aplikace `FUNCTIONS_EXTENSION_VERSION`.  Je nastavené na `~2` pro aplikace v2 a `~1` pro aplikace v1.  Je doporučena změna verze modulu runtime aplikace, kterou má existující funkce publikovaná beze změny také kódu z těchto funkcí.  Doporučená cesta je vytvoření nové aplikace function app a nastavit na příslušnou verzi, otestovat změny a potom zakázat nebo odstranit předchozí aplikace.

## <a name="bindings"></a>Vazby 

Modul runtime používá nový 2.x [model rozšiřitelnosti vazby](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) , který nabízí tyto výhody:

* Podpora pro rozšíření vazby třetích stran.
* Oddělení modulu runtime a vazby. To umožňuje rozšíření vazby se systémovou správou verzí a vydávají se nezávisle na sobě. Můžete například pokud se rozhodnete k upgradu na verzi rozšíření, které spoléhá na novější verzi základní sadu SDK.
* Světlejší spuštění prostředí, kde jsou pouze vazby používá známé a načten modulem runtime.

Všechny integrované vazeb Azure Functions, si osvojila tento model a jsou již zahrnuté ve výchozím nastavení, s výjimkou Časovacího triggeru a triggeru HTTP. Tahle rozšíření jsou nainstalovány automaticky při vytváření funkce prostřednictvím nástrojů, jako je Visual Studio nebo na portálu.

Následující tabulka uvádí, které vazeb jsou podporovány v každé verzi modulu runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících materiálech:

* [Místní psaní kódu a testování funkcí Azure Functions](functions-run-local.md)
* [Jak se zaměřit na verze modulu runtime Azure Functions](set-runtime-version.md)
* [Poznámky k verzi](https://github.com/Azure/azure-functions-host/releases)

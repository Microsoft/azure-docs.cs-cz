---
title: Registrovat rozšíření vazby Azure Functions
description: Naučte se registrovat rozšíření vazby Azure Functions v závislosti na vašem prostředí.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484719"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrovat rozšíření vazby Azure Functions

V Azure Functions verze 2. x jsou [vazby](./functions-triggers-bindings.md) k dispozici jako samostatné balíčky z modulu runtime Functions. I když rozhraní .NET Functions přistupuje k vazbám prostřednictvím balíčků NuGet, rozšiřující balíčky umožňují ostatním funkcím přístup k všem vazbám prostřednictvím nastavení konfigurace.

Vezměte v úvahu následující položky týkající se rozšíření vazby:

- Rozšíření vazby nejsou explicitně registrována ve funkcích 1. x s výjimkou [Vytvoření knihovny C# tříd pomocí sady Visual Studio](#local-csharp).

- Aktivační události protokolu HTTP a časovače jsou ve výchozím nastavení podporovány a nevyžadují rozšíření.

Následující tabulka uvádí, kdy a jak registrovat vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1. x  |Registrace<br/> ve funkcích 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|Azure Portal|Automaticky|Automaticky|
|Non-.NET jazyky nebo místní vývoj nástrojů Azure Core|Automaticky|[Použití Azure Functions Core Tools a rozšíření sad](#extension-bundles)|
|C#Knihovna tříd pomocí sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|C#Knihovna tříd pomocí Visual Studio Code|NEUŽÍVÁ SE.|[Použít .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>Sady rozšíření pro místní vývoj

Sady rozšíření jsou technologie nasazení, která umožňuje přidat do aplikace Function App kompatibilní sadu funkcí pro vazby. Předdefinovaná sada rozšíření se přidá při sestavování aplikace. Balíčky rozšíření definované v sadě prostředků jsou vzájemně kompatibilní, což pomáhá vyhnout se konfliktům mezi balíčky. V souboru Host. JSON aplikace se povolují balíčky rozšíření.  

Můžete použít sady rozšíření s verzí 2. x a novějšími verzemi modulu runtime Functions. Při vývoji místně se ujistěte, že používáte nejnovější verzi [Azure Functions Core Tools](functions-run-local.md#v2).

Balíčky rozšíření můžete použít pro místní vývoj pomocí Azure Functions Core Tools, Visual Studio Code a při vzdáleném sestavování.

Pokud nepoužíváte sady rozšíření, musíte před instalací rozšíření vazby nainstalovat na svůj místní počítač sadu SDK .NET Core 2. x. Sady rozšíření odstraňují tento požadavek pro místní vývoj. 

Chcete-li použít sady rozšíření, aktualizujte soubor *Host. JSON* tak, aby obsahoval následující položku pro `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="vs"></a>Knihovna tříd jazyka C\# se sadou Visual Studio

V **aplikaci Visual Studio**můžete balíčky nainstalovat z konzoly Správce balíčků pomocí příkazu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku, který se používá pro danou vazbu, je uveden v referenčním článku pro tuto vazbu. Příklad naleznete v [části Packages tématu Service Bus reference Binding](functions-bindings-service-bus.md#functions-1x).

V příkladu nahraďte `<TARGET_VERSION>` konkrétní verzí balíčku, například `3.0.0-beta5`. Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

Pokud použijete `Install-Package` k odkazování na vazbu, nemusíte používat [sady rozšíření](#extension-bundles). Tento přístup je specifický pro knihovny tříd sestavené v aplikaci Visual Studio.

## <a name="vs-code"></a>C# knihovna tříd s Visual Studio Code

V **Visual Studio Code**nainstalujte balíčky pro projekt knihovny C# tříd z příkazového řádku pomocí příkazu [dotnet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) v .NET Core CLI. Následující příklad ukazuje, jak přidat vazbu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI lze použít pouze pro vývoj Azure Functions 2. x.

Nahraďte `<BINDING_TYPE_NAME>` názvem balíčku, který obsahuje vazbu, kterou potřebujete. Požadovaný odkaz na vazbu můžete najít v [seznamu podporovaných vazeb](./functions-triggers-bindings.md#supported-bindings).

V příkladu nahraďte `<TARGET_VERSION>` konkrétní verzí balíčku, například `3.0.0-beta5`. Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Aktivační událost Azure functions a příklad vazby](./functions-bindings-example.md)

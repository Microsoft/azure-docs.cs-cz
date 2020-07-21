---
title: Registrovat rozšíření vazby Azure Functions
description: Naučte se registrovat rozšíření vazby Azure Functions v závislosti na vašem prostředí.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 2dde784e2f67266b2f6c6ccd7da20f01546bbda7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506481"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrovat rozšíření vazby Azure Functions

V Azure Functions verze 2. x jsou [vazby](./functions-triggers-bindings.md) k dispozici jako samostatné balíčky z modulu runtime Functions. I když rozhraní .NET Functions přistupuje k vazbám prostřednictvím balíčků NuGet, rozšiřující balíčky umožňují ostatním funkcím přístup k všem vazbám prostřednictvím nastavení konfigurace.

Vezměte v úvahu následující položky týkající se rozšíření vazby:

- Rozšíření vazby nejsou explicitně registrována ve funkcích 1. x s výjimkou [Vytvoření knihovny tříd C# pomocí sady Visual Studio](#local-csharp).

- Aktivační události protokolu HTTP a časovače jsou ve výchozím nastavení podporovány a nevyžadují rozšíření.

Následující tabulka uvádí, kdy a jak registrovat vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1. x  |Registrace<br/> ve funkcích 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automaticky|Automaticky|
|Non-.NET jazyky nebo místní vývoj nástrojů Azure Core|Automaticky|[Použití Azure Functions Core Tools a rozšíření sad](#extension-bundles)|
|Knihovna tříd C# s využitím sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|Knihovna tříd C# pomocí Visual Studio Code|–|[Použít .NET Core CLI](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Sady rozšíření pro místní vývoj

Sady rozšíření jsou technologie nasazení, která umožňuje přidat do aplikace Function App kompatibilní sadu funkcí pro vazby. Předdefinovaná sada rozšíření se přidá při sestavování aplikace. Balíčky rozšíření definované v sadě prostředků jsou vzájemně kompatibilní, což pomáhá vyhnout se konfliktům mezi balíčky. Balíčky rozšíření povolíte v host.jsaplikace v souboru.  

Můžete použít sady rozšíření s verzí 2. x a novějšími verzemi modulu runtime Functions. Při vývoji místně se ujistěte, že používáte nejnovější verzi [Azure Functions Core Tools](functions-run-local.md#v2).

Balíčky rozšíření můžete použít pro místní vývoj pomocí Azure Functions Core Tools, Visual Studio Code a při vzdáleném sestavování.

Pokud nepoužíváte sady rozšíření, musíte před instalací rozšíření vazby nainstalovat na svůj místní počítač sadu SDK .NET Core 2. x. Sady rozšíření odstraňují tento požadavek pro místní vývoj. 

Pokud chcete použít sady rozšíření, aktualizujte *host.jsv* souboru tak, aby obsahovaly následující položku pro `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Knihovna tříd C se sadou Visual Studio

V **aplikaci Visual Studio**můžete balíčky nainstalovat z konzoly Správce balíčků pomocí příkazu [Install-Package](/nuget/tools/ps-ref-install-package) , jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku, který se používá pro danou vazbu, je uveden v referenčním článku pro tuto vazbu. Příklad naleznete v [části Packages tématu Service Bus reference Binding](functions-bindings-service-bus.md#functions-1x).

`<TARGET_VERSION>`V příkladu nahraďte konkrétní verzí balíčku, třeba `3.0.0-beta5` . Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

Pokud používáte `Install-Package` pro odkazování na vazbu, nemusíte používat [sady rozšíření](#extension-bundles). Tento přístup je specifický pro knihovny tříd sestavené v aplikaci Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Knihovna tříd C# s Visual Studio Code

V **Visual Studio Code**nainstalujte balíčky pro projekt knihovny tříd jazyka C# z příkazového řádku pomocí příkazu [dotnet add Package](/dotnet/core/tools/dotnet-add-package) v .NET Core CLI. Následující příklad ukazuje, jak přidat vazbu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI lze použít pouze pro vývoj Azure Functions 2. x.

Nahraďte `<BINDING_TYPE_NAME>` názvem balíčku, který obsahuje vazbu, kterou potřebujete. Požadovaný odkaz na vazbu můžete najít v [seznamu podporovaných vazeb](./functions-triggers-bindings.md#supported-bindings).

`<TARGET_VERSION>`V příkladu nahraďte konkrétní verzí balíčku, třeba `3.0.0-beta5` . Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Aktivační událost Azure functions a příklad vazby](./functions-bindings-example.md)

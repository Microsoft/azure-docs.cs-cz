---
title: Registrovat rozšíření vazby Azure Functions
description: Naučte se registrovat rozšíření vazby Azure Functions v závislosti na vašem prostředí.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: a045ef0fea70347f168e8ae0cc93e0c359f31dfa
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031114"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrovat rozšíření vazby Azure Functions

Počínaje verzí Azure Functions 2. x jsou [vazby](./functions-triggers-bindings.md) k dispozici jako samostatné balíčky z modulu runtime Functions. I když rozhraní .NET Functions přistupuje k vazbám prostřednictvím balíčků NuGet, rozšiřující balíčky umožňují ostatním funkcím přístup k všem vazbám prostřednictvím nastavení konfigurace.

Vezměte v úvahu následující položky týkající se rozšíření vazby:

- Rozšíření vazby nejsou explicitně registrována ve funkcích 1. x s výjimkou [Vytvoření knihovny tříd C# pomocí sady Visual Studio](#local-csharp).

- Aktivační události protokolu HTTP a časovače jsou ve výchozím nastavení podporovány a nevyžadují rozšíření.

Následující tabulka uvádí, kdy a jak registrovat vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1. x  |Registrace<br/> ve funkcích 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automaticky|Automatické<sup>*</sup>|
|Non-.NET jazyky nebo místní vývoj nástrojů Azure Core|Automaticky|[Použití Azure Functions Core Tools a rozšíření sad](#extension-bundles)|
|Knihovna tříd C# s využitím sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|Knihovna tříd C# pomocí Visual Studio Code|–|[Použít .NET Core CLI](#vs-code)|

<sup>*</sup>Portál používá sady rozšíření.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Sady rozšíření

Sady rozšíření umožňují přidat do aplikace Function App kompatibilní sadu funkcí pro vazby. Při použití sad se při sestavování aplikace přidají předdefinované sady rozšíření. Balíčky rozšíření definované ve svazku jsou ověřené tak, aby byly vzájemně kompatibilní, což pomáhá vyhnout se konfliktům mezi balíčky. Sady rozšíření umožňují vyhnout se nutnosti publikovat kód projektu .NET pomocí projektu non-.NET Functions. Balíčky rozšíření povolíte v host.jsaplikace v souboru.  

Můžete použít sady rozšíření s verzí 2. x a novějšími verzemi modulu runtime Functions. 

Balíčky rozšíření můžete použít pro místní vývoj pomocí Azure Functions Core Tools, Visual Studio Code a při vzdáleném sestavování. Při vývoji místně se ujistěte, že používáte nejnovější verzi [Azure Functions Core Tools](functions-run-local.md#v2). Sady rozšíření se používají také při vývoji funkcí v Azure Portal. 

Pokud nepoužíváte sady rozšíření, musíte nainstalovat sadu .NET Core 2. x SDK na svůj místní počítač předtím, než [explicitně nainstalujete rozšíření vazby](#explicitly-install-extensions). Do projektu se přidá soubor Extensions. csproj, který explicitně definuje požadovaná rozšíření. Sady rozšíření odstraňují tyto požadavky pro místní vývoj. 

Pokud chcete použít sady rozšíření, aktualizujte *host.jsv* souboru tak, aby obsahovaly následující položku pro `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Explicitní instalace rozšíření

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>Balíčky NuGet

Pro projekt funkcí založených na knihovně jazyka C# byste měli instalovat sady rozšíření určené speciálně pro projekty, které nejsou třídy. 

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Knihovna tříd C se sadou Visual Studio

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

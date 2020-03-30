---
title: Registrace rozšíření vazby Azure Functions
description: Naučte se zaregistrovat rozšíření vazby Azure Functions na základě vašeho prostředí.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 1688fe848beb62731391bf4399a0dabec5265320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277515"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrace rozšíření vazby Azure Functions

V Azure Functions verze 2.x [jsou vazby](./functions-triggers-bindings.md) k dispozici jako samostatné balíčky od runtime funkcí. Zatímco .NET funkce přístup vazby prostřednictvím balíčků NuGet, rozšíření svazky umožňují přístup k jiným funkcím přístup ke všem vazbám prostřednictvím nastavení konfigurace.

Zvažte následující položky týkající se rozšíření vazby:

- Rozšíření vazby nejsou explicitně registrovány ve funkcích 1.x s výjimkou při [vytváření knihovny tříd jazyka C# pomocí sady Visual Studio](#local-csharp).

- Aktivační události protokolu HTTP a časovače jsou ve výchozím nastavení podporovány a nevyžadují rozšíření.

V následující tabulce je uvedeno, kdy a jak registrujete vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1.x  |Registrace<br/> ve funkcích 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automaticky|Automaticky|
|Non-.NET vývoj jazyků nebo místních nástrojů Azure Core Tools|Automaticky|[Použití základních nástrojů a rozšiřujících balíčků Azure Functions](#extension-bundles)|
|Knihovna tříd C# pomocí sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|Knihovna tříd C# pomocí kódu sady Visual Studio|Není dostupné.|[Použití rozhraní CLI jádra .NET](#vs-code)|

## <a name="extension-bundles-for-local-development"></a><a name="extension-bundles"></a>Rozšiřující balíčky pro místní rozvoj

Rozšíření balíčky je technologie nasazení, která vám umožní přidat kompatibilní sadu rozšíření funkce vazby do aplikace funkce. Předdefinovaná sada rozšíření se přidá při vytváření aplikace. Balíčky rozšíření definované v sadě jsou vzájemně kompatibilní, což pomáhá vyhnout se konfliktům mezi balíčky. Balíčky rozšíření povolíte v souboru host.json aplikace.  

Můžete použít rozšíření balíčky s verzí 2.x a novější verze funkce runtime. Při místním vývoji se ujistěte, že používáte nejnovější verzi [nástrojů Azure Functions Core Tools](functions-run-local.md#v2).

Pomocí balíčků rozšíření pro místní vývoj pomocí nástrojů Azure Functions Core Tools, Visual Studio Code a při vzdáleném sestavení.

Pokud nepoužíváte balíčky rozšíření, musíte nainstalovat sadu .NET Core 2.x SDK do místního počítače před instalací rozšíření o vazby. Rozšíření svazky odebere tento požadavek pro místní rozvoj. 

Chcete-li použít balíčky rozšíření, aktualizujte soubor *host.json* tak, aby zahrnoval následující položku pro `extensionBundle`:
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>Knihovna tříd C\# s Visual Studio

V **sadě Visual Studio**můžete instalovat balíčky z konzoly Správce balíčků pomocí příkazu [Install-Package,](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku použitého pro danou vazbu je uveden v referenčním článku pro tuto vazbu. Příklad naleznete v [části Balíčky v článku odkaz na vazbu služby Service Bus](functions-bindings-service-bus.md#functions-1x).

Nahraďte `<TARGET_VERSION>` v příkladu konkrétní verzi balíčku, například `3.0.0-beta5`. Platné verze jsou uvedeny na stránkách jednotlivých balíčků na [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkce runtime 1.x nebo 2.x jsou uvedeny v referenčním článku pro vazbu.

Pokud používáte `Install-Package` odkaz na vazbu, není nutné používat [rozšíření svazky](#extension-bundles). Tento přístup je specifický pro knihovny tříd integrované v sadě Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a>Knihovna tříd C# s kódem Visual Studia

V **kódu sady Visual Studio**nainstalujte balíčky pro projekt knihovny tříd jazyka C# z příkazového řádku pomocí příkazu [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) v rozhraní PŘÍKAZU .NET Core CLI. Následující příklad ukazuje, jak přidat vazbu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

Rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core lze použít pouze pro vývoj Azure Functions 2.x.

Nahraďte `<BINDING_TYPE_NAME>` název balíčku, který obsahuje vazbu, kterou potřebujete. Požadovaný odkaz na vazbu naleznete v [seznamu podporovaných vazeb](./functions-triggers-bindings.md#supported-bindings).

Nahraďte `<TARGET_VERSION>` v příkladu konkrétní verzi balíčku, například `3.0.0-beta5`. Platné verze jsou uvedeny na stránkách jednotlivých balíčků na [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkce runtime 1.x nebo 2.x jsou uvedeny v referenčním článku pro vazbu.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Azure function trigger a příklad vazby](./functions-bindings-example.md)

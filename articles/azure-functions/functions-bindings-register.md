---
title: Registrovat rozšíření vazby Azure Functions
description: Naučte se registrovat rozšíření vazby Azure Functions v závislosti na vašem prostředí.
author: craigshoemaker
ms.topic: reference
ms.date: 08/16/2020
ms.author: cshoe
ms.openlocfilehash: 942ca3229808b57894598c3477e9dc97e40e8c80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88689545"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrovat rozšíření vazby Azure Functions

Počínaje verzí Azure Functions 2. x obsahuje modul runtime Functions ve výchozím nastavení pouze triggery protokolu HTTP a časovače. Další [triggery a vazby](./functions-triggers-bindings.md) jsou k dispozici jako samostatné balíčky.

Knihovna tříd .NET Functions Functions Apps používá vazby, které jsou nainstalovány v projektu jako balíčky NuGet. Sady rozšíření umožňují aplikacím non-.NET Functions používat stejné vazby, aniž by museli pracovat s infrastrukturou .NET.

Následující tabulka uvádí, kdy a jak registrovat vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1. x  |Registrace<br/> ve funkcích 3. x/2. x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automaticky|Automatické<sup>*</sup>|
|Jazyky Non-.NET|Automaticky|Používejte [sady rozšíření](#extension-bundles) (doporučeno) nebo [explicitně nainstalujte rozšíření](#explicitly-install-extensions) .|
|Knihovna tříd C# s využitím sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|Knihovna tříd C# pomocí Visual Studio Code|Není k dispozici|[Použít .NET Core CLI](#vs-code)|

<sup>*</sup> Portál používá sady rozšíření.

## <a name="access-extensions-in-non-net-languages"></a>Přístup k rozšířením v non-.NET jazycích

Pro přístup k vazbám doporučujeme používat sady rozšíření pro jazyky Java, JavaScript, PowerShell, Python a Customer Functions. V případech, kdy nelze použít rozšiřující balíčky, můžete explicitně nainstalovat rozšíření vazby.

### <a name="extension-bundles"></a><a name="extension-bundles"></a>Sady rozšíření

Sady rozšíření je způsob, jak přidat kompatibilní sadu rozšíření vazby do aplikace Function App. Balíčky rozšíření povolíte v *host.jsaplikace v* souboru.

Můžete použít sady rozšíření s verzí 2. x a novějšími verzemi modulu runtime Functions.

Sady rozšíření jsou ve verzi. Každá verze obsahuje konkrétní sadu rozšíření vazby, které jsou ověřené k vzájemné spolupráci. Vyberte verzi sady prostředků na základě rozšíření, která potřebujete ve vaší aplikaci.

Pokud chcete přidat rozšiřující sadu do aplikace Function App, přidejte do ní `extensionBundle` oddíl, do *host.js*. V mnoha případech se Visual Studio Code a Azure Functions Core Tools automaticky přidá.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

Následující tabulka uvádí aktuálně dostupné verze výchozí sady *Microsoft. Azure. Functions. ExtensionBundle* a odkazuje na rozšíření, která obsahují.

| Verze sady prostředků | Verze v host.jszapnuta | Zahrnutá rozšíření |
| --- | --- | --- |
| verze | `[1.*, 2.0.0)` | Informace [ o](https://github.com/Azure/azure-functions-extension-bundles/blob/v1.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) vygenerování sady najdete v tématuextensions.js. |
| 2.x | `[2.*, 3.0.0)` | Informace [ o](https://github.com/Azure/azure-functions-extension-bundles/blob/v2.x/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) vygenerování sady najdete v tématuextensions.js. |

> [!NOTE]
> I když můžete zadat rozsah vlastní verze v host.jsna, doporučujeme použít hodnotu verze z této tabulky.

### <a name="explicitly-install-extensions"></a><a name="explicitly-install-extensions"></a>Explicitní instalace rozšíření

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="install-extensions-from-nuget-in-net-languages"></a><a name="local-csharp"></a>Instalace rozšíření z NuGetu v jazycích .NET

Pro projekt funkcí založených na knihovně jazyka C# byste měli nainstalovat rozšíření přímo. Sady rozšíření jsou určeny konkrétně pro projekty, které nejsou založené na knihovně C# třídy.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a>\#Knihovna tříd C se sadou Visual Studio

V **aplikaci Visual Studio**můžete balíčky nainstalovat z konzoly Správce balíčků pomocí příkazu [Install-Package](/nuget/tools/ps-ref-install-package) , jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku, který se používá pro danou vazbu, je uveden v referenčním článku pro tuto vazbu. Příklad naleznete v [části Packages tématu Service Bus reference Binding](functions-bindings-service-bus.md#functions-1x).

`<TARGET_VERSION>`V příkladu nahraďte konkrétní verzí balíčku, třeba `3.0.0-beta5` . Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

Pokud používáte `Install-Package` pro odkazování na vazbu, nemusíte používat [sady rozšíření](#extension-bundles). Tento přístup je specifický pro knihovny tříd sestavené v aplikaci Visual Studio.

### <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Knihovna tříd C# s Visual Studio Code

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

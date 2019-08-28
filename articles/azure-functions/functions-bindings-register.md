---
title: Registrovat rozšíření vazby Azure Functions
description: Naučte se registrovat rozšíření vazby Azure Functions v závislosti na vašem prostředí.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 93ced443a73d5499d8b305770c3c866c26d540f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086466"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrovat rozšíření vazby Azure Functions

V Azure Functions verze 2. x jsou [vazby](./functions-triggers-bindings.md) k dispozici jako samostatné balíčky z modulu runtime Functions. I když rozhraní .NET Functions přistupuje k vazbám prostřednictvím balíčků NuGet, rozšiřující balíčky umožňují ostatním funkcím přístup k všem vazbám prostřednictvím nastavení konfigurace.

Vezměte v úvahu následující položky týkající se rozšíření vazby:

- Rozšíření vazby nejsou explicitně registrována ve funkcích 1. x s výjimkou [Vytvoření knihovny C# tříd pomocí sady Visual Studio](#local-csharp).

- Aktivační události protokolu HTTP a časovače jsou ve výchozím nastavení podporovány a nevyžadují rozšíření.

Následující tabulka uvádí, kdy a jak registrovat vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1. x  |Registrace<br/> ve funkcích 2. x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automatické|Automatické|
|Non-.NET jazyky nebo místní vývoj nástrojů Azure Core|Automatické|[Použití Azure Functions Core Tools a rozšíření sad](#extension-bundles)|
|C#Knihovna tříd pomocí sady Visual Studio|[Použití nástrojů NuGet](#vs)|[Použití nástrojů NuGet](#vs)|
|C#Knihovna tříd pomocí Visual Studio Code|Není k dispozici|[Použít .NET Core CLI](#vs-code)|

## <a name="extension-bundles"></a>Sady rozšíření pro místní vývoj

Sady rozšíření jsou místní technologie pro vývoj pro modul runtime verze 2. x, která umožňuje přidat do projektu Function App kompatibilní sadu funkcí pro vazby. Tyto balíčky rozšíření se pak při nasazení do Azure zahrnou do balíčku pro nasazení. Sady prostředků zpřístupňují všechny vazby, které Microsoft zpřístupňuje prostřednictvím nastavení v souboru *Host. JSON* . Balíčky rozšíření definované v sadě prostředků jsou vzájemně kompatibilní, což pomáhá vyhnout se konfliktům mezi balíčky. Při vývoji místně se ujistěte, že používáte nejnovější verzi [Azure Functions Core Tools](functions-run-local.md#v2).

Balíčky rozšíření používejte pro všechny místní vývojové prostředí pomocí Azure Functions Core Tools nebo Visual Studio Code.

Pokud nepoužíváte sady rozšíření, musíte před instalací rozšíření vazby nainstalovat na svůj místní počítač sadu SDK .NET Core 2. x. Sady odeberou tento požadavek pro místní vývoj. 

Chcete-li použít sady rozšíření, aktualizujte soubor *Host. JSON* tak, aby obsahoval následující `extensionBundle`položku pro:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

V `extensionBundle`nástroji jsou k dispozici následující vlastnosti:

| Vlastnost | Popis |
| -------- | ----------- |
| **`id`** | Obor názvů pro sady rozšíření funkcí Microsoft Azure Functions. |
| **`version`** | Verze sady, která se má nainstalovat Modul runtime Functions vždy vybere maximální přípustnou verzi definovanou rozsahem verze nebo intervalem. Výše uvedená hodnota verze umožňuje všechny verze balíčku od 1.0.0 do až, ale ne včetně 2.0.0. Další informace najdete v části [interval zápisu pro určení rozsahů verzí](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Verze sady prostředků se zvýší jako balíčky v rámci změny sady prostředků. K velkým změnám verze dochází, když se balíčky v sadě zvýší o hlavní verzi, která se obvykle shoduje se změnou hlavní verze běhového modulu Functions.  

Aktuální sada rozšíření nainstalovaná ve výchozím svazku se zobrazí v tomto [souboru Extensions. JSON](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="vs"></a>Knihovna\# tříd C se sadou Visual Studio

V **aplikaci Visual Studio**můžete balíčky nainstalovat z konzoly Správce balíčků pomocí příkazu [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) , jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku, který se používá pro danou vazbu, je uveden v referenčním článku pro tuto vazbu. Příklad naleznete v [části Packages tématu Service Bus reference Binding](functions-bindings-service-bus.md#packages---functions-1x).

V `<TARGET_VERSION>` příkladu nahraďte konkrétní verzí balíčku, `3.0.0-beta5`třeba. Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

Pokud používáte `Install-Package` pro odkazování na vazbu, nemusíte používat [sady rozšíření](#extension-bundles). Tento přístup je specifický pro knihovny tříd sestavené v aplikaci Visual Studio.

## <a name="vs-code"></a>C# knihovna tříd s Visual Studio Code

> [!NOTE]
> K automatické instalaci kompatibilní sady balíčků rozšíření pro vytváření vazeb doporučujeme použití [rozšiřujících sad](#extension-bundles) .

V **Visual Studio Code**nainstalujte balíčky pro projekt knihovny C# tříd z příkazového řádku pomocí příkazu [dotnet Add Package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) v .NET Core CLI. Následující příklad ukazuje, jak přidat vazbu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

.NET Core CLI lze použít pouze pro vývoj Azure Functions 2. x.

Nahraďte `<BINDING_TYPE_NAME>` názvem balíčku, který je k dispozici v referenčním článku, pro požadovanou vazbu. Požadovaný odkaz na vazbu můžete najít v [seznamu podporovaných vazeb](./functions-triggers-bindings.md#supported-bindings).

V `<TARGET_VERSION>` příkladu nahraďte konkrétní verzí balíčku, `3.0.0-beta5`třeba. Platné verze jsou uvedeny na jednotlivých stránkách balíčku na adrese [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají funkcím runtime 1. x nebo 2. x, jsou uvedeny v referenčním článku pro vazbu.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Aktivační událost Azure functions a příklad vazby](./functions-bindings-example.md)

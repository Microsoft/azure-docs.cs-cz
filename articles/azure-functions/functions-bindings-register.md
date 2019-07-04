---
title: Registrace rozšíření vazby Azure Functions
description: Zjistěte, jak zaregistrovat rozšíření vazby funkce Azure podle vašeho prostředí.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480337"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrace rozšíření vazby Azure Functions

V Azure Functions verzi 2.x, [vazby](./functions-triggers-bindings.md) jsou k dispozici jako samostatné balíčky z modul runtime služby functions. Když funkce .NET přístup vazby prostřednictvím balíčků NuGet, sady rozšíření povolit další funkce přístupu k všechny vazby prostřednictvím nastavení konfigurace.

Zvažte následující body související s vazbou rozšíření:

- Rozšíření vazby nejsou explicitně registrován ve funkcích 1.x kromě případů, kdy [vytváření C# knihovny tříd pomocí sady Visual Studio](#local-csharp).

- Aktivační události HTTP a časovač se podporují ve výchozím nastavení a nevyžadují rozšíření.

Následující tabulka udává, kdy a jak zaregistrujete vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1.x  |Registrace<br/> ve funkcích 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automatické|Automatické|
|Jazyky mimo rozhraní .NET nebo místním vývojovém základní nástroje pro Azure|Automatické|[Použití nástrojů Azure Functions Core a rozšíření sady](#extension-bundles)|
|C#knihovny tříd pomocí Visual Studio 2019|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2019)|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2019)|
|Knihovny tříd C# pomocí nástroje Visual Studio Code|neuvedeno|[Použití .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles"></a>Rozšíření sady pro místní vývoj

Rozšíření sady je místní vývojový technologie pro modul runtime verze 2.x, který vám umožní rozšířit sadu funkcí vazby rozšíření pro váš projekt aplikace funkcí. Tyto balíčky rozšíření se pak součástí balíčku pro nasazení při nasazení do Azure. Sady provede všechny vazby publikované společností Microsoft, které jsou k dispozici prostřednictvím nastavení v *host.json* souboru. Balíčky rozšíření, které jsou definovány v sadě jsou navzájem kompatibilní, můžete zamezit konfliktům mezi balíčky. Při vývoji místně, ujistěte se, že používáte nejnovější verzi [nástrojů Azure Functions Core](functions-run-local.md#v2).

Použití rozšíření sady pro všechny místní vývoj pomocí nástrojů Azure Functions Core nebo Visual Studio Code.

Pokud nepoužíváte rozšíření sady, musíte nainstalovat sadu .NET Core 2.x sady SDK v místním počítači před instalací rozšíření žádné vazby. Sady odebere tento požadavek pro místní vývoj. 

Použití rozšíření sady, aktualizujte *host.json* souboru přidejte následující položku pro `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Následující vlastnosti jsou k dispozici v `extensionBundle`:

| Vlastnost | Popis |
| -------- | ----------- |
| **`id`** | Obor názvů pro rozšíření sady Microsoft Azure Functions. |
| **`version`** | Verze sady prostředků pro instalaci. Modul runtime služby Functions vždy vybere maximální povolenou verzi definovaný rozsah verzí nebo intervalu. Vyšší hodnota verze umožňuje všechny verze sady prostředků z 1.0.0 až, ale nezahrnuje 2.0.0. Další informace najdete v tématu [notation interval pro zadání rozsahu verzí](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Zvýšení verze sady prostředků jako balíčky v sadě změn. Při balení v sadě zvýšit hlavní verzi, která se obvykle kryje s změn do hlavní verze modul runtime služby Functions dojde ke změnám hlavní verze.  

Aktuální sadu rozšíření nainstalované pomocí výchozí sady jsou uvedené v tomto [extensions.json souboru](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>C\# knihovny tříd pomocí Visual Studio 2019

V **Visual Studio 2019**, balíčky můžete nainstalovat z konzoly Správce balíčků pro použití [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) příkaz, jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<TARGET_VERSION>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="c-class-library-with-visual-studio-code"></a>Knihovny tříd C# pomocí Visual Studio Code

> [!NOTE]
> Doporučujeme používat [rozšíření sady](#extension-bundles) mít funkce automaticky instalovat sady kompatibilní vazbu balíčky rozšíření.

V **Visual Studio Code**, nainstalovat balíčky pro C# projekt knihovny tříd pomocí příkazového řádku [se příkaz dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) příkaz v rozhraní příkazového řádku .NET Core, jak je znázorněno v následujícím příkladu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Rozhraní příkazového řádku .NET Core jde použít jenom pro vývoj pro Azure Functions 2.x.

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<TARGET_VERSION>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Příklad aktivační události a vazby Azure – funkce](./functions-bindings-example.md)


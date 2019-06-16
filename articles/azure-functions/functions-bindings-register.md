---
title: Registrace rozšíření vazby Azure Functions
description: Zjistěte, jak zaregistrovat rozšíření vazby funkce Azure podle vašeho prostředí.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 53eb5fc9389d913ecacec3729a06e47a1c2bf56b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65864551"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrace rozšíření vazby Azure Functions

V Azure Functions verzi 2.x, [vazby](./functions-triggers-bindings.md) jsou k dispozici jako samostatné balíčky z modul runtime služby functions. Když funkce .NET přístup vazby prostřednictvím balíčků NuGet, sady rozšíření povolit další funkce přístupu k všechny vazby prostřednictvím nastavení konfigurace.

Zvažte následující body související s vazbou rozšíření:

- Rozšíření vazby nejsou explicitně registrován ve funkcích 1.x kromě případů, kdy [vytváření C# knihovny tříd pomocí Visual Studio 2019](#local-csharp).

- Aktivační události HTTP a časovač se podporují ve výchozím nastavení a nevyžadují rozšíření.

Následující tabulka udává, kdy a jak zaregistrujete vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1.x  |Registrace<br/> ve funkcích 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automatické|Automatické|
|Jazyky mimo rozhraní .NET nebo místním vývojovém základní nástroje pro Azure|Automatické|[Použití nástrojů Azure Functions Core a rozšíření sady](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|C#knihovny tříd pomocí Visual Studio 2019|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2019)|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2019)|
|Knihovny tříd C# pomocí nástroje Visual Studio Code|neuvedeno|[Použití .NET Core CLI](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Místní vývoj s Azure Functions Core Tools a rozšíření sady

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2019"></a>C#knihovny tříd pomocí Visual Studio 2019

V **Visual Studio 2019**, balíčky můžete nainstalovat z konzoly Správce balíčků pro použití [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) příkaz, jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<TARGET_VERSION>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="c-class-library-with-visual-studio-code"></a>Knihovny tříd C# pomocí Visual Studio Code

V **Visual Studio Code**, balíčky můžete nainstalovat pomocí příkazového řádku [se příkaz dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) příkaz v rozhraní příkazového řádku .NET Core, jak je znázorněno v následujícím příkladu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

Rozhraní příkazového řádku .NET Core jde použít jenom pro vývoj pro Azure Functions 2.x.

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<TARGET_VERSION>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Příklad aktivační události a vazby Azure – funkce](./functions-bindings-example.md)


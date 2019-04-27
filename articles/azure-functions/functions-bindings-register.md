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
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437850"
---
# <a name="register-azure-functions-binding-extensions"></a>Registrace rozšíření vazby Azure Functions

Služba Azure Functions podporuje protokol HTTP a časovač úprav. Pro práci s dalšími službami, je potřeba nainstalovat nebo *zaregistrovat* [vazby](./functions-triggers-bindings.md) rozšíření. Rozšíření vazby jsou k dispozici prostřednictvím Azure základní nástroje nebo NuGet balíčků. 

Následující tabulka udává, kdy a jak zaregistrujete vazby.

| Vývojové prostředí |Registrace<br/> ve funkcích 1.x  |Registrace<br/> ve funkcích 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|portál Azure|Automaticky|[Automatické s řádku](#azure-portal-development)|
|Jazyky mimo rozhraní .NET nebo místním vývojovém základní nástroje pro Azure|Automaticky|[Pomocí příkazů rozhraní příkazového řádku nástroje Core](#local-development-azure-functions-core-tools)|
|Knihovny tříd C# pomocí sady Visual Studio 2017|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2017)|[Pomocí nástroje NuGet](#c-class-library-with-visual-studio-2017)|
|Knihovny tříd C# pomocí nástroje Visual Studio Code|neuvedeno|[Použití .NET Core CLI](#c-class-library-with-visual-studio-code)|

Výjimky, které nevyžadují explicitní registraci, protože jsou automaticky registrované ve všech verzí a prostředí jsou následující typy vazeb: HTTP a časovač.

> [!IMPORTANT]
> Tento obsah pro zbývající část tohoto článku se týká pouze funkce 2.x. Rozšíření vazby nejsou explicitně registrován ve funkcích 1.x kromě případů, kdy [vytváření C# knihovny tříd pomocí sady Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Vývoj pro Azure portal

Při vytváření funkce nebo přidat vazbu, zobrazí se výzva při rozšíření pro triggerem nebo vazbou vyžaduje registrace. Na výzvy odpovíte kliknutím **nainstalovat** k registraci rozšíření. Instalace může trvat až 10 minut, než v plánu consumption. 

Každé rozšíření musí nainstalovat pouze jednou pro danou funkci aplikace. U podporovaných vazeb, které nejsou k dispozici na portálu nebo aktualizovat nainstalovaného rozšíření můžete také [ruční instalaci nebo aktualizaci vazby rozšíření z portálu Azure Functions](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Místní vývoj nástrojů Azure Functions Core

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Knihovny tříd C# pomocí sady Visual Studio 2017

V **Visual Studio 2017**, balíčky můžete nainstalovat z konzoly Správce balíčků pro použití [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package) příkaz, jak je znázorněno v následujícím příkladu:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<target_version>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="c-class-library-with-visual-studio-code"></a>Knihovny tříd C# pomocí Visual Studio Code

V **Visual Studio Code**, balíčky můžete nainstalovat pomocí příkazového řádku [se příkaz dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) příkaz v rozhraní příkazového řádku .NET Core, jak je znázorněno v následujícím příkladu:

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

Rozhraní příkazového řádku .NET Core jde použít jenom pro vývoj pro Azure Functions 2.x.

Název balíčku pro danou vazbu je uvedené v článku odkaz pro danou vazbu. Příklad najdete v tématu [balíčky článku odkaz vazby služby Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Nahraďte `<target_version>` v příkladu nahraďte konkrétní verzi balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org). Hlavní verze, které odpovídají modul runtime služby Functions 1.x a 2.x jsou uvedeny v článku odkaz pro vazbu.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Příklad aktivační události a vazby Azure – funkce](./functions-bindings-example.md)



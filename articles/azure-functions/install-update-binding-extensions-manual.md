---
title: Ruční instalace nebo aktualizace rozšíření vazby Azure Functions
description: Zjistěte, jak nainstalovat nebo aktualizovat rozšíření vazby Azure Functions pro nasazenou funkci aplikace.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Služba Azure functions, functions, rozšíření NuGet, aktualizace vazeb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 77b863bc32442261e220b5dd3f11c0bd33b4fa7a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809208"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ruční instalace nebo aktualizace rozšíření vazby Azure Functions na portálu

Modul runtime verze 2.x Azure Functions používá rozšíření vazby k implementaci kódu pro triggery a vazby. Rozšíření vazby jsou k dispozici v balíčcích NuGet. K registraci rozšíření, je v podstatě nainstalovat balíček. Při vytváření funkcí, tak, jak nainstalovat rozšíření vazby závisí na vývojové prostředí. Další informace najdete v tématu [registraci rozšíření vazby](functions-triggers-bindings.md#register-binding-extensions) v aktivační události a vazby článku.

V některých případech budete muset ručně nainstalovat nebo aktualizovat rozšíření vazby na webu Azure Portal. Například budete muset aktualizovat registrované vazby na novější verzi. Také můžete potřebovat k registraci podporovanou vazbu, která nejde nainstalovat v **integrace** karta na portálu.

## <a name="install-a-binding-extension"></a>Instalace rozšíření vazby

Pomocí následujících kroků ruční instalace nebo aktualizace rozšíření z portálu.

1. V [webu Azure portal](https://portal.azure.com), vyhledejte aplikaci function app a vyberte ho. Zvolte **přehled** kartě a vyberte **Zastavit**.  Zastavuje se aplikace function app odemkne soubory tak, že lze provést změny.

1. Zvolte **funkce platformy** kartu a v části **nástroje pro vývoj** vyberte **Rozšířené nástroje (Kudu)**. Koncový bod Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) se otevře v novém okně.

1. V okně Kudu vyberte **konzolou pro ladění** > **CMD**.  

1. V příkazovém řádku přejděte do `D:\home\site\wwwroot` a vyberte ikonu Odstranit vedle `bin` se odstranit složku. Vyberte **OK** potvrďte odstranění.

1. Zvolte ikonu úprav vedle `extensions.csproj` soubor, který definuje rozšíření vazby pro danou aplikaci funkcí. Soubor projektu je otevřený v editoru online.

1. Ujistěte se, vyžaduje přidání a aktualizace **PackageReference** položky v **ItemGroup**a pak vyberte **Uložit**. Aktuální seznam podporovaných balíčků verzí najdete v [balíčky, které potřebujeme, abychom?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) článku na wikiwebu. Všechny tři služby Azure Storage vazby vyžadují Microsoft.Azure.WebJobs.Extensions.Storage balíčku.

1. Z `wwwroot` složky, spusťte následující příkaz k opětovnému sestavení odkazovaných sestavení v `bin` složky.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Zpátky **přehled** karta na portálu, zvolte **spustit** restartovat aplikaci function app.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

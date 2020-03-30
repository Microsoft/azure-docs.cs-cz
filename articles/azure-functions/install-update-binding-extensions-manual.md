---
title: Ruční instalace nebo aktualizace rozšíření vazby Azure Functions
description: Zjistěte, jak nainstalovat nebo aktualizovat rozšíření vazby Azure Functions pro nasazené aplikace funkcí.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768856"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ruční instalace nebo aktualizace rozšíření vazby Azure Functions z portálu

Počínaje verzí 2.x používá runtime Azure Functions rozšíření vazby k implementaci kódu pro aktivační události a vazby. Rozšíření vazby jsou k dispozici v balíčcích NuGet. Chcete-li zaregistrovat rozšíření, v podstatě nainstalovat balíček. Při vývoji funkcí závisí způsob instalace rozšíření vazby na vývojovém prostředí. Další informace naleznete v [tématu Register rozšíření vazby](./functions-bindings-register.md) v článku aktivační události a vazby.

Někdy je potřeba ručně nainstalovat nebo aktualizovat rozšíření vazby na webu Azure Portal. Například může být nutné aktualizovat registrovanou vazbu na novější verzi. Možná také budete muset zaregistrovat podporovanou vazbu, kterou nelze nainstalovat na kartě **Integrace** na portálu.

## <a name="install-a-binding-extension"></a>Instalace rozšíření vazby

Pomocí následujících kroků můžete ručně nainstalovat nebo aktualizovat rozšíření z portálu.

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte aplikaci funkcí a vyberte ji. Zvolte kartu **Přehled** a vyberte **Zastavit**.  Zastavení aplikace funkce odemkne soubory, takže lze provést změny.

1. Zvolte kartu **Funkce platformy** a v části **Nástroje pro vývoj** vyberte **Upřesnit nástroje (Kudu).** Koncový bod Kudu`https://<APP_NAME>.scm.azurewebsites.net/`( ) se otevře v novém okně.

1. V okně Kudu vyberte **ladit konzolu** > **CMD**.  

1. V příkazovém okně `D:\home\site\wwwroot` přejděte na ikonu `bin` odstranění vedle a zvolte ikonu odstranění, abyste složku odstranili. Chcete-li odstranění potvrdit, vyberte **ok.**

1. Zvolte ikonu úprav `extensions.csproj` vedle souboru, která definuje rozšíření vazeb pro aplikaci funkce. Soubor projektu se otevře v online editoru.

1. Proveďte požadované dodatky a aktualizace položek **PackageReference** ve **skupině ItemGroup**a vyberte **uložit**. Aktuální seznam podporovaných verzí balíčků naleznete v článku [Co balíčky potřebuji?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) Všechny tři vazby úložiště Azure vyžadují balíček Microsoft.Azure.WebJobs.Extensions.Storage.

1. Ze `wwwroot` složky spusťte následující příkaz k opětovnému `bin` sestavení odkazovaných ve složce.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Na kartě **Přehled** na portálu zvolte **Spustit** a restartujte aplikaci funkcí.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech a vazbách funkcí Azure](functions-triggers-bindings.md)

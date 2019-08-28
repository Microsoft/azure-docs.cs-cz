---
title: Ruční instalace nebo aktualizace rozšíření vazby Azure Functions
description: Přečtěte si, jak nainstalovat nebo aktualizovat rozšíření vazby Azure Functions pro nasazené aplikace Function App.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, Functions, rozšíření vazby, NuGet, aktualizace
ms.service: azure-functions
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 7686a9b2df6df6b54851e9c9957186f76be3fafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085059"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Ruční instalace nebo aktualizace rozšíření vazby Azure Functions z portálu

Azure Functions verze 2. x runtime používá rozšíření vazby k implementaci kódu pro aktivační události a vazby. Rozšíření vazby jsou k dispozici v balíčcích NuGet. K registraci rozšíření v podstatě nainstalujete balíček. Při vývoji funkcí závisí způsob instalace rozšíření vazby na vývojovém prostředí. Další informace najdete v tématu [Registrace rozšíření vazby](./functions-bindings-register.md) v článku triggery a vazby.

Někdy je nutné ručně nainstalovat nebo aktualizovat rozšíření vazby v Azure Portal. Například může být nutné aktualizovat registrovanou vazbu na novější verzi. Je také možné, že budete muset zaregistrovat podporovanou vazbu, která se nedá nainstalovat na kartě **integrace** na portálu.

## <a name="install-a-binding-extension"></a>Instalace rozšíření vazby

Pomocí následujícího postupu můžete ručně nainstalovat nebo aktualizovat rozšíření z portálu.

1. V [Azure Portal](https://portal.azure.com)Najděte aplikaci Function App a vyberte ji. Zvolte kartu **Přehled** a vyberte **zastavit**.  Zastavení aplikace Function App odemkne soubory, aby bylo možné provádět změny.

1. Zvolte kartu **funkce platformy** a v části **vývojové nástroje** vyberte **Rozšířené nástroje (Kudu)** . Koncový bod Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) se otevře v novém okně.

1. V okně Kudu vyberte **ladit konzolu** > **cmd**.  

1. V příkazovém okně přejděte na `D:\home\site\wwwroot` a vyberte ikonu Odstranit `bin` vedle pro odstranění složky. Kliknutím na **OK** potvrďte odstranění.

1. Klikněte na ikonu Upravit vedle `extensions.csproj` souboru, který definuje rozšíření vazby pro aplikaci Function App. Soubor projektu je otevřen v online editoru.

1. Proveďte požadované dodatky a aktualizace položek **PackageReference** ve všech položkách a pak vyberte **Save (Uložit**). Aktuální seznam podporovaných verzí balíčku najdete v článku [Jaké balíčky](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) potřebuji? článek na wikiwebu. Všechny tři Azure Storage vazby vyžadují balíček Microsoft. Azure. WebJobs. Extensions. Storage.

1. Ze složky spusťte následující příkaz pro opětovné sestavení odkazovaného sestavení `bin` ve složce. `wwwroot`

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Zpátky na kartě **Přehled** na portálu klikněte na tlačítko **Start** a restartujte aplikaci Function App.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)

---
title: Vytvoření C# webové aplikace ASP.NET Core – Azure App Service | Microsoft Docs
description: Naučte se spouštět webové aplikace v Azure App Service nasazením výchozí C# webové aplikace ASP.NET Core.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/30/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e8c6c21efd1ea406c8293819a2889e198e8455bd
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241989"
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Core v Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v .NET Core ve službě App Service v Linuxu](./containers/quickstart-dotnetcore.md).
>

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.

V tomto rychlém startu se dozvíte, jak nasadit první webovou aplikaci ASP.NET Core do Azure App Service. Až budete hotovi, budete mít skupinu prostředků, která se skládá z plánu App Service a App Service aplikace s nasazenou webovou aplikací.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a web** .

Pokud jste již nainstalovali Visual Studio 2019:

- Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu** > vyhledat**aktualizace**.
- Přidejte úlohy tak, že vyberete **nástroje** > **získat nástroje a funkce**.

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí následujících kroků vytvořte webovou aplikaci ASP.NET Core:

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.

1. V v možnosti **vytvořit nový projekt**vyhledejte a vyberte **ASP.NET Core webové aplikace** pro C#a pak vyberte **Další**.

1. V části **Konfigurovat nový projekt**pojmenujte aplikaci _myFirstAzureWebApp_a pak vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Pro tento rychlý Start vyberte šablonu **webové aplikace** . Ujistěte se, že je ověřování nastaveno na **bez ověřování** a není vybrána žádná jiná možnost. Vyberte **Vytvořit**.

   ![Pro tento kurz vyberte ASP.NET Core Razor Pages](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

    Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET Core.

1. V nabídce sady Visual Studio vyberte **ladit** > **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   ![Místní spuštění aplikace](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**.

1. Zvolte **App Service** a pak vyberte **publikovat**.

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. V **App Service vytvořit nové**jsou možnosti závislé na tom, zda jste přihlášeni k Azure, a zda máte účet sady Visual Studio propojený s účtem Azure. Vyberte buď **Přidat účet** , nebo se **přihlaste** , abyste se mohli přihlásit k předplatnému Azure. Pokud jste už přihlášení, vyberte účet, který chcete.

   > [!NOTE]
   > Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
   >

   ![Přihlášení k Azure](./media/app-service-web-get-started-dotnet/sign-in-azure-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. V případě **skupiny prostředků**vyberte **Nový**.

1. Do **nového názvu skupiny prostředků**zadejte *MyResourceGroup* a vyberte **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Pro možnost **plán hostování**vyberte možnost **Nový**.

1. V dialogu **Konfigurovat plán hostování** zadejte hodnoty z následující tabulky a pak vyberte **OK**.

   | Nastavení | Navrhovaná hodnota | Popis |
   |-|-|-|
   |Plán služby App Service| myAppServicePlan | Název plánu služby App Service. |
   | Location | Západní Evropa | Datacentrum, které je hostitelem webové aplikace. |
   | Size | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

   ![Vytvoření plánu služby App Service](./media/app-service-web-get-started-dotnet/app-service-plan-vs2019.png)

1. Do **pole název**zadejte jedinečný název aplikace, který bude obsahovat pouze `a-z`platné znaky:, `A-Z` `0-9`, a `-`. Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je `http://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

   ![Konfigurace názvu aplikace](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

Průvodce po dokončení publikuje webovou aplikaci ASP.NET Core do Azure a potom tuto aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Název aplikace zadaný ve **App Service vytvořit novou** stránku se používá jako předpona adresy URL ve formátu `http://<app_name>.azurewebsites.net`.

**Blahopřejeme!** Vaše webová aplikace v ASP.NET Core běží živě v Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

1. V **Průzkumník řešení**v rámci projektu otevřete **stránky** > **index. cshtml**.

1. Dvě značky `<div>` nahraďte následujícím kódem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

1. Na stránce **publikovat** Souhrn vyberte **publikovat**.

   ![Souhrnná stránka publikování sady Visual Studio](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

1. Pokud chcete webovou aplikaci spravovat, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. V nabídce vlevo vyberte **App Services**a pak vyberte název aplikace Azure.

   ![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

   Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění.

   ![App Service v Azure Portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

   Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

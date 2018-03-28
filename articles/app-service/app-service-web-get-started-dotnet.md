---
title: "Vytvoření webové aplikace ASP.NET Core v Azure | Dokumentace Microsoftu"
description: "Nasazením ukázkové webové aplikace ASP.NET se naučíte, jak spouštět webové aplikace ve službě Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/14/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 698f23507da0707a4612f8d33fe7e2995429f361
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
---
# <a name="create-an-aspnet-core-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Core v Azure

> [!NOTE]
> Tento článek nasadí aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v .NET Core ve službě App Service v Linuxu](./containers/quickstart-dotnetcore.md).
>

[Azure Web Apps](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto kurzu Rychlý start se dozvíte, jak nasadit svoji první webovou aplikaci ASP.NET Core do služby Azure Web Apps. Po dokončení kurzu budete mít skupinu prostředků, která se bude skládat z plánu služby App Service a webové aplikace Azure s nasazenou webovou aplikací.

> [!NOTE]
> Pokud hledáte, jak postupovat při sestavování a nasazování webové aplikace v ASP.NET Frameworku, příslušný článek najdete [tady](app-service-web-get-started-dotnet-framework.md). 
>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

* Nainstalovat <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> s následujícími sadami funkcí:
    - **Vývoj pro ASP.NET a web**
    - **Azure – vývoj**

    ![Vývoj pro ASP.NET a Azure – vývoj (v části Web a cloud)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Ve Visual Studiu vytvořte projekt tak, že vyberete **Soubor > Nový > Projekt**. 

V dialogovém okně **Nový projekt** vyberte **Visual C# > Web > Webová aplikace ASP.NET Core**.

Aplikaci pojmenujte _myFirstAzureWebApp_ a pak vyberte **OK**.
   
![Dialogové okno Nový projekt](./media/app-service-web-get-started-dotnet/new-project.png)

Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET Core. V tomto kurzu Rychlý start vyberte šablonu **Webová aplikace** a ujistěte se, že u ověřování je nastavena možnost **Bez ověření**.
      
Vyberte **OK**.

![Dialogové okno Nový projekt ASP.NET](./media/app-service-web-get-started-dotnet/razor-pages-aspnet-dialog.png)

Jakmile se vytvoří projekt ASP.NET Core, zobrazí se úvodní stránka ASP.NET Core s řadou odkazů, které vám pomohou v začátcích. 

![Úvodní stránka](./media/app-service-web-get-started-dotnet/aspnet-core-welcome-page.png)

V nabídce vyberte **Ladit > Spustit bez ladění** a spusťte tak webovou aplikaci místně.

![Místní spuštění aplikace](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberte možnost **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-get-started-dotnet/right-click-publish.png)

Zkontrolujte, že je vybrána možnost **Microsoft Azure App Service** a vyberte **Publikovat**.

![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-dotnet/publish-to-app-service.png)

Tím se otevře dialogové okno **Vytvořit plán App Service**, které vám pomůže vytvořit všechny prostředky Azure potřebné ke spuštění vaší webové aplikace ASP.NET Core v Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

V dialogovém okně **Vytvořit plán Aplikační služby** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášeni, vyberte z rozevíracího seznamu účet, který obsahuje požadované předplatné.

> [!NOTE]
> Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
>
>
   
![Přihlášení k Azure](./media/app-service-web-get-started-dotnet/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Skupinu prostředků pojmenujte **myResourceGroup** a vyberte **OK**.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Vedle pole **Plán služby App Service** vyberte **Nový**. 

V dialogovém okně **Konfigurovat plán Aplikační služby** použijte nastavení podle tabulky následující po snímku obrazovky.

![Vytvoření plánu služby App Service](./media/app-service-web-get-started-dotnet/configure-app-service-plan.png)

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| myAppServicePlan | Název plánu služby App Service. |
| Umístění | Západní Evropa | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

Vyberte **OK**.

## <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V části **Název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou `a-z`, `0-9` a `-`) nebo přijměte automaticky vygenerovaný jedinečný název. Adresa URL webové aplikace je `http://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší webové aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

![Nastavení názvu webové aplikace](./media/app-service-web-get-started-dotnet/web-app-name.png)

Průvodce po dokončení publikuje webovou aplikaci ASP.NET Core do Azure a potom tuto aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

Název webové aplikace zadaný v [kroku vytvoření a publikování](#create-and-publish-the-web-app) se použije jako předpona adresy URL ve formátu `http://<app_name>.azurewebsites.net`.

Blahopřejeme, vaše webová aplikace ASP.NET Core je spuštěná ve službě Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Z **Průzkumníku řešení** otevřete _Pages/Index.cshtml_.

Najděte HTML značku `<div id="myCarousel" class="carousel slide" data-ride="carousel" data-interval="6000">` poblíž začátku a nahraďte celý element následujícím kódem:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

Na stránce publikování vyberte **Publikovat**.

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## <a name="manage-the-azure-web-app"></a>Správa webové aplikace Azure

Pokud chcete webovou aplikaci spravovat, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levé nabídce klikněte na **App Services** a potom vyberte název své webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/app-service-web-get-started-dotnet/access-portal.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Okno App Service na webu Azure Portal](./media/app-service-web-get-started-dotnet/web-app-blade.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

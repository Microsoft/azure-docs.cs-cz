---
title: Vytvoření webové aplikace ASP.NET Framework v Azure | Dokumentace Microsoftu
description: Nasazením ukázkové webové aplikace ASP.NET se naučíte, jak spouštět webové aplikace ve službě Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc, devcenter
ms.openlocfilehash: 125377b91514b1615cfbb488f7ddfe59fbc016f5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Framework v Azure

[Azure Web Apps](app-service-web-overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  V tomto kurzu Rychlý start se dozvíte, jak nasadit svoji první webovou aplikaci ASP.NET pomocí služby Azure Web Apps. Po dokončení kurzu budete mít skupinu prostředků, která se bude skládat z plánu služby App Service a webové aplikace Azure s nasazenou webovou aplikací.

Podívejte se na video s tímto rychlým startem v akci a potom sami proveďte příslušné kroky a publikujte svou první aplikaci .NET v Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu je potřeba:

* Nainstalovat <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> s následujícími sadami funkcí:
    - **Vývoj pro ASP.NET a web**
    - **Azure – vývoj**

    ![Vývoj pro ASP.NET a Azure – vývoj (v části Web a cloud)](media/app-service-web-tutorial-dotnet-sqldatabase/workloads.png)

Pokud jste už sadu Visual Studio nainstalovali, přidejte do ní sady funkcí kliknutím na **Nástroje** > **Získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

Ve Visual Studiu vytvořte projekt tak, že vyberete **Soubor > Nový > Projekt**. 

V dialogovém okně **Nový projekt** vyberte **Visual C# > Web > Webová aplikace ASP.NET (.NET Framework)**.

Aplikaci pojmenujte _myFirstAzureWebApp_ a pak vyberte **OK**.
   
![Dialogové okno Nový projekt](./media/app-service-web-get-started-dotnet-framework/new-project.png)

Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. V tomto kurzu Rychlý start vyberte šablonu **MVC** a ujistěte se, že u ověřování je nastavena možnost **Bez ověření**.
      
Vyberte **OK**.

![Dialogové okno Nový projekt ASP.NET](./media/app-service-web-get-started-dotnet-framework/select-mvc-template.png)

V nabídce vyberte **Ladit > Spustit bez ladění** a spusťte tak webovou aplikaci místně.

![Místní spuštění aplikace](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-to-azure"></a>Publikování aplikací do Azure

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberte možnost **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

Zkontrolujte, že je vybrána možnost **Microsoft Azure App Service** a vyberte **Publikovat**.

![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

Tím se otevře dialogové okno **Vytvořit plán Aplikační služby**, které vám pomůže vytvořit všechny prostředky služby Azure potřebné ke spuštění webové aplikace ASP.NET ve službě Azure.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

V dialogovém okně **Vytvořit plán Aplikační služby** vyberte **Přidat účet** a přihlaste se ke svému předplatnému Azure. Pokud už jste přihlášeni, vyberte z rozevíracího seznamu účet, který obsahuje požadované předplatné.

> [!NOTE]
> Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
>
>
   
![Přihlášení k Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure.png)

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Vedle pole **Skupina prostředků** vyberte **Nová**.

Skupinu prostředků pojmenujte **myResourceGroup** a vyberte **OK**.

## <a name="create-an-app-service-plan"></a>Vytvoření plánu služby App Service

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

Vedle pole **Plán služby App Service** vyberte **Nový**. 

V dialogovém okně **Konfigurovat plán Aplikační služby** použijte nastavení podle tabulky následující po snímku obrazovky.

![Vytvoření plánu služby App Service](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| myAppServicePlan | Název plánu služby App Service. |
| Umístění | Západní Evropa | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

Vyberte **OK**.

## <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V části **Název webové aplikace** zadejte jedinečný název aplikace (platné znaky jsou `a-z`, `0-9` a `-`) nebo přijměte automaticky vygenerovaný jedinečný název. Adresa URL webové aplikace je `http://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší webové aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

![Nastavení názvu webové aplikace](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Průvodce po dokončení publikuje webovou aplikaci ASP.NET do služby Azure a pak aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Název webové aplikace zadaný v [kroku vytvoření a publikování](#create-and-publish-the-web-app) se použije jako předpona adresy URL ve formátu `http://<app_name>.azurewebsites.net`.

Blahopřejeme, vaše webová aplikace ASP.NET je veřejně spuštěná ve službě Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Z **Průzkumníku řešení** otevřete _Views\Home\Index.cshtml_.

Najděte HTML značku `<div class="jumbotron">` poblíž začátku a nahraďte celý element následujícím kódem:

```HTML
<div class="jumbotron">
    <h1>ASP.NET in Azure!</h1>
    <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
</div>
```

Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

Na stránce publikování vyberte **Publikovat**.

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-web-app"></a>Správa webové aplikace Azure

Pokud chcete webovou aplikaci spravovat, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V levé nabídce klikněte na **App Services** a potom vyberte název své webové aplikace Azure.

![Navigace portálem k webové aplikaci Azure](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Okno App Service na webu Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [ASP.NET s databází SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

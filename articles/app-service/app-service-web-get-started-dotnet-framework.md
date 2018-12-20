---
title: Vytvoření C# webové aplikace ASP.NET Framework - službě Azure App Service | Dokumentace Microsoftu
description: Nasazením výchozí webové aplikace C# ASP.NET se naučíte, jak spouštět webové aplikace ve službě Azure App Service.
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
ms.date: 09/05/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 8dc062a1c9490a03aa5369dc103db750d7531140
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635268"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Framework v Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.  Tento rychlý start ukazuje, jak nasadit první webovou aplikaci ASP.NET do služby Azure App Service. Jakmile budete hotovi, budete mít skupinu prostředků, která se skládá z plánu služby App Service a aplikaci služby App Service s nasazenou webovou aplikací.

![](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu nainstalujte sadu <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> se sadou funkcí **Vývoj pro ASP.NET a web**.

Pokud už máte sadu Visual Studio 2017 nainstalovanou:

- Nainstalujte nejnovější aktualizace v sadě Visual Studio kliknutím na **Nápověda** > **Vyhledat aktualizace**.
- Klikněte na **Nástroje** > **Získat nástroje a funkce** a přidejte příslušnou sadu funkcí.

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

## <a name="launch-the-publish-wizard"></a>Spuštění průvodce publikováním

V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberte možnost **Publikovat**.

![Publikování z Průzkumníka řešení](./media/app-service-web-get-started-dotnet-framework/solution-explorer-publish.png)

Průvodce publikováním se spustí automaticky. Výběrem **App Service** > **Publikovat** otevřete dialogové okno **Vytvořit plán App Service**.

![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-dotnet-framework/publish-to-app-service.png)

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

Vedle položky **Plán hostování** vyberte **Nový**. 

V dialogovém okně **Konfigurovat plán hostování** použijte nastavení podle tabulky následující po snímku obrazovky.

![Vytvoření plánu služby App Service](./media/app-service-web-get-started-dotnet-framework/configure-app-service-plan.png)

| Nastavení | Navrhovaná hodnota | Popis |
|-|-|-|
|Plán služby App Service| myAppServicePlan | Název plánu služby App Service. |
| Umístění | Západní Evropa | Datacentrum, které je hostitelem webové aplikace. |
| Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

Vyberte **OK**.

## <a name="create-and-publish-the-web-app"></a>Vytvoření a publikování webové aplikace

V části **Název aplikace** zadejte jedinečný název aplikace (platné znaky jsou `a-z`, `0-9` a `-`) nebo přijměte automaticky vygenerovaný jedinečný název. Adresa URL webové aplikace je `http://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

![Konfigurace názvu aplikace](./media/app-service-web-get-started-dotnet-framework/web-app-name.png)

Průvodce po dokončení publikuje webovou aplikaci ASP.NET do služby Azure a pak aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Název aplikace zadaný v [kroku vytvoření a publikování](#create-and-publish-the-web-app) se použije jako předpona adresy URL ve formátu `http://<app_name>.azurewebsites.net`.

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
![Stránka souhrnu publikování v sadě Visual Studio](./media/app-service-web-get-started-dotnet-framework/publish-summary-page.png)

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Pokud chcete webovou aplikaci spravovat, přejděte na web <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

V nabídce vlevo vyberte **App Services**a potom vyberte název aplikace Azure.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-dotnet-framework/access-portal.png)

Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní úlohy správy, jako je procházení, zastavení, spuštění, restartování a odstranění. 

![Okno App Service na webu Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-blade.png)

Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace. 

## <a name="video"></a>Video

Podívejte se na video s tímto rychlým startem v akci a potom sami proveďte příslušné kroky a publikujte svou první aplikaci .NET v Azure.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-for-NET-Developers/Create-a-NET-app-in-Azure-Quickstart/player]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [ASP.NET s databází SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

---
title: 'Úvodní příručka: Vytvoření aplikace c# ASP.NET'
description: Zjistěte, jak spouštět webové aplikace ve službě Azure App Service nasazením výchozí šablony webové aplikace C# ASP.NET z Visual Studia.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 10/21/2019
ms.custom: mvc, devcenter, seodec18
ms.openlocfilehash: 4688cc358ec6ff792be58254b0607f5416422a21
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047657"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Framework v Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.

Tento úvodní příručka ukazuje, jak nasadit první ASP.NET webovou aplikaci do služby Azure App Service. Až budete hotovi, budete mít plán služby App Service. Budete mít také aplikaci Služby App Service s nasazenou webovou aplikací.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s **ASP.NET a zatížení vývoje webu.**

Pokud už jste Visual Studio 2019 nainstalovali:

- Nainstalujte nejnovější aktualizace v sadě Visual Studio výběrem **možnosti Kontrola nápovědy** > **k aktualizacím**.
- Přidejte pracovní vytížení výběrem **nástroje** > **získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření ASP.NET webové aplikace<a name="create-and-publish-the-web-app"></a>

Vytvořte ASP.NET webovou aplikaci takto:

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

2. V **okně Vytvořit nový projekt**vyhledejte a zvolte ASP.NET **webovou aplikaci (.NET Framework)** a vyberte **možnost Další**.

3. V **části Konfigurace nového projektu**pojmenujte aplikaci _myFirstAzureWebApp_a pak vyberte **Vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-dotnet-framework/configure-web-app-project-framework.png)

4. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. Pro tento rychlý start zvolte šablonu **MVC.**

5. Ujistěte se, že je ověřování nastaveno na **možnost Žádné ověřování**. Vyberte **Vytvořit**.

   ![Vytvořit ASP.NET webovou aplikaci](./media/app-service-web-get-started-dotnet-framework/select-mvc-template-vs2019.png)

6. V nabídce Visual Studio vyberte **Ladění** > **start bez ladění** spustit webovou aplikaci místně.

   ![Místní spuštění aplikace](./media/app-service-web-get-started-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace<a name="launch-the-publish-wizard"></a>

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **myFirstAzureWebApp** a vyberte **publikovat**.

1. Zvolte **App Service** a pak **změňte vytvořit profil** publikovat . **Publish**

   ![Publikování ze stránky přehledu projektu](./media/app-service-web-get-started-dotnet-framework/publish-app-framework-vs2019.png)

1. V **aplikaci App Service Vytvořit nové**, vaše možnosti závisí na tom, zda jste již přihlášení k Azure a zda máte účet Visual Studio propojený s účtem Azure. Chcete-li se přihlásit k **předplatnému** Azure, vyberte přidat účet nebo **přihlásit.** Pokud jste již přihlášeni, vyberte požadovaný účet.

   > [!NOTE]
   > Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
   >
   >

   ![Přihlášení k Azure](./media/app-service-web-get-started-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Ve **skupině Prostředků**vyberte **nový**.

1. Do **pole Nový název skupiny prostředků**zadejte *myResourceGroup* a vyberte **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Pro **hosting plán**, vyberte **Nový**.

1. V dialogovém okně **Konfigurovat hostitelský plán** zadejte hodnoty z následující tabulky a pak vyberte **OK**.

   | Nastavení | Navrhovaná hodnota | Popis |
   |-|-|-|
   |Plán služby App Service| myAppServicePlan | Název plánu služby App Service. |
   | Umístění | Západní Evropa | Datacentrum, které je hostitelem webové aplikace. |
   | Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

   ![Vytvoření plánu služby App Service](./media/app-service-web-get-started-dotnet-framework/app-service-plan-framework-vs2019.png)

1. V **části Název**zadejte jedinečný název aplikace, `a-z` `A-Z`který `0-9`obsahuje `-`pouze platné znaky , jsou , , a . Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je `http://<app_name>.azurewebsites.net`, kde `<app_name>` je název vaší aplikace.

2. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

   ![Konfigurace názvu aplikace](./media/app-service-web-get-started-dotnet-framework/web-app-name-framework-vs2019.png)

Průvodce po dokončení publikuje webovou aplikaci ASP.NET do služby Azure a pak aplikaci spustí ve výchozím prohlížeči.

![Publikovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/published-azure-web-app.png)

Název aplikace zadaný na nové stránce **Vytvoření nové služby** aplikace `http://<app_name>.azurewebsites.net`se používá jako předpona adresy URL ve formátu .

**Blahopřejeme!** Vaše ASP.NET webová aplikace běží živě ve službě Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

1. V **Průzkumníku řešení**otevřete v rámci projektu **zobrazení** > **home** > **index.cshtml**.

1. Najděte HTML značku `<div class="jumbotron">` poblíž začátku a nahraďte celý element následujícím kódem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**. Potom vyberte **Publikovat**.

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace ASP.NET v Azure](./media/app-service-web-get-started-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

1. Pokud chcete webovou aplikaci spravovat, přejděte na [portál Azure](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

   ![Vybrat služby aplikace](./media/app-service-web-get-started-dotnet-framework/app-services.png)

2. Na stránce **Služby aplikací** vyberte název webové aplikace.

   ![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-dotnet-framework/access-portal-framework-vs2019.png)

   Zobrazí se stránka s přehledem vaší webové aplikace. Zde můžete provést základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění.

   ![Přehled služby App Service na webu Azure Portal](./media/app-service-web-get-started-dotnet-framework/web-app-general-framework-vs2019.png)

   Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [ASP.NET s databází SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

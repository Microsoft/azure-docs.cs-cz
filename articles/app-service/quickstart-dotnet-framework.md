---
title: 'Rychlý Start: Vytvoření aplikace ASP.NET v C#'
description: Naučte se spouštět webové aplikace v Azure App Service nasazením výchozí šablony webové aplikace ASP.NET v C# ze sady Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: 5bf774f2b5cf4a6275dc04248aadfd9a94f5122d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88212560"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Vytvoření webové aplikace ASP.NET Framework v Azure

[Azure App Service ](overview.md) je vysoce škálovatelná služba s automatickými opravami pro hostování webů.

V tomto rychlém startu se dozvíte, jak nasadit první webovou aplikaci v ASP.NET do Azure App Service. Až budete hotovi, budete mít App Service plán. Máte také aplikaci App Service s nasazenou webovou aplikací.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a web** .

Pokud jste již nainstalovali Visual Studio 2019:

- Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu**vyhledat  >  **aktualizace**.
- Přidejte úlohy tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace v ASP.NET <a name="create-and-publish-the-web-app"></a>

Pomocí následujících kroků vytvořte webovou aplikaci v ASP.NET:

1. Otevřete Visual Studio a pak vyberte **vytvořit nový projekt**.

2. V možnosti **vytvořit nový projekt**vyhledejte a zvolte **Webová aplikace ASP.NET (.NET Framework)** a pak vyberte **Další**.

3. V části **Konfigurovat nový projekt**pojmenujte aplikaci _myFirstAzureWebApp_a pak vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Do Azure můžete nasadit jakýkoli typ webové aplikace ASP.NET. Pro tento rychlý Start vyberte šablonu **MVC** .

5. Ujistěte se, že je ověřování nastaveno na **bez ověřování**. Vyberte **Vytvořit**.

   ![Vytvoření webové aplikace v ASP.NET](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se webová aplikace spouštěla místně.

   ![Místní spuštění aplikace](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace <a name="launch-the-publish-wizard"></a>

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**.

1. Zvolte **App Service** a vyberte **vytvořit profil**.

   ![Publikování ze stránky přehledu projektu](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png)

1. V **App Service vytvořit nové**jsou možnosti závislé na tom, zda jste přihlášeni k Azure, a zda máte účet sady Visual Studio propojený s účtem Azure. Vyberte buď **Přidat účet** , nebo se **přihlaste** , abyste se mohli přihlásit k předplatnému Azure. Pokud jste už přihlášení, vyberte účet, který chcete.

   > [!NOTE]
   > Pokud už jste přihlášení, nevybírejte zatím možnost **Vytvořit**.
   >
   >

   ![Přihlášení k Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. V případě **skupiny prostředků**vyberte **Nový**.

1. Do **nového názvu skupiny prostředků**zadejte *MyResourceGroup* a vyberte **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Pro možnost **plán hostování**vyberte možnost **Nový**.

1. V dialogu **Konfigurovat plán hostování** zadejte hodnoty z následující tabulky a pak vyberte **OK**.

   | Nastavení | Navrhovaná hodnota | Popis |
   |-|-|-|
   | Plán Hosting| myAppServicePlan | Název plánu služby App Service. |
   | Umístění | West Europe | Datacentrum, které je hostitelem webové aplikace. |
   | Velikost | Free | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |

   ![Vytvoření plánu služby App Service](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Do pole **název**zadejte jedinečný název aplikace, který bude obsahovat pouze platné znaky `a-z` :,, `A-Z` `0-9` a `-` . Můžete přijmout automaticky generovaný jedinečný název. Adresa URL webové aplikace je `http://<app-name>.azurewebsites.net`, kde `<app-name>` je název vaší aplikace.

2. Výběrem možnosti **Vytvořit** spustíte vytváření prostředků Azure.

   ![Konfigurace názvu aplikace](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Po dokončení průvodce se prostředky Azure vytvoří za vás a budete připraveni ho publikovat.

3. Na stránce **publikovat** klikněte na **publikovat**. Visual Studio sestaví, zabalí a publikuje aplikaci do Azure a pak aplikaci spustí ve výchozím prohlížeči.

    ![Publikovaná webová aplikace ASP.NET v Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

Název aplikace zadaný ve **App Service vytvořit novou** stránku se používá jako předpona adresy URL ve formátu `http://<app-name>.azurewebsites.net` .

**Blahopřejeme!** Vaše webová aplikace v ASP.NET běží živě v Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

1. V **Průzkumník řešení**v rámci projektu otevřete **zobrazení**  >  **Domů**  >  **index. cshtml**.

1. Najděte HTML značku `<div class="jumbotron">` poblíž začátku a nahraďte celý element následujícím kódem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**. Pak vyberte **publikovat**.

    Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

    ![Aktualizovaná webová aplikace ASP.NET v Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

1. Pokud chcete webovou aplikaci spravovat, otevřete [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

   ![Vybrat služby App Services](./media/quickstart-dotnet-framework/app-services.png)

2. Na stránce **App Services** vyberte název vaší webové aplikace.

   ![Přechod do aplikace Azure na portálu](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   Zobrazí se stránka s přehledem vaší webové aplikace. Tady můžete provádět základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění.

   ![Přehled App Service v Azure Portal](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   Levá nabídka obsahuje odkazy na různé stránky pro konfiguraci vaší aplikace.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [ASP.NET s databází SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET](configure-language-dotnet-framework.md)

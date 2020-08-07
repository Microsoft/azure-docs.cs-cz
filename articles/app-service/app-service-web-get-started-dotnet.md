---
title: 'Rychlý Start: Vytvoření aplikace ASP.NET Core v C#'
description: Naučte se spouštět webové aplikace v Azure App Service tím, že nasadíte výchozí šablonu ASP.NET Core webové aplikace v jazyce C# ze sady Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 08/06/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 77ff1e907b15b129ef03d7ce799631d6d0a9671d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986181"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Rychlý Start: Vytvoření webové aplikace v ASP.NET Core v Azure

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první webovou aplikaci ASP.NET Core do [Azure App Service](overview.md). 

Až budete hotovi, budete mít skupinu prostředků Azure, která se skládá z App Service plánu hostování a App Service s nasazenou webovou aplikací.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- V tomto rychlém startu se nasadí aplikace, která se App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v .NET Core ve službě App Service v Linuxu](./containers/quickstart-dotnetcore.md).
- Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje ASP.NET a webu** .

  Pokud jste již nainstalovali Visual Studio 2019:

  - Nainstalujte nejnovější aktualizace v aplikaci Visual Studio tak, že vyberete **nápovědu**vyhledat  >  **aktualizace**.
  - Přidejte úlohy tak, že vyberete **nástroje**  >  **získat nástroje a funkce**.


## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Pomocí následujících kroků vytvořte webovou aplikaci ASP.NET Core v aplikaci Visual Studio:

1. Otevřete Visual Studio a vyberte **vytvořit nový projekt**.

1. V části **vytvořit nový projekt**vyberte **ASP.NET Core webová aplikace** a ověřte, že je **jazyk C#** uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **konfiguraci nového projektu**zadejte název projektu webové aplikace *MyFirstAzureWebApp*a vyberte **vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Do Azure můžete nasadit jakýkoli typ ASP.NET Core webové aplikace, ale pro účely tohoto rychlého startu vyberte šablonu **webové aplikace** . Ujistěte se, že je **ověřování** nastaveno na **bez ověřování**a že není vybrána žádná jiná možnost. Potom vyberte **Vytvořit**.

   ![Vytvoření nové ASP.NET Core webové aplikace](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. V nabídce sady Visual Studio vyberte **ladit**  >  **Spustit bez ladění** , aby se vaše webová aplikace spouštěla místně.

   ![Webová aplikace spuštěná místně](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace

Pokud chcete publikovat webovou aplikaci, musíte nejdřív vytvořit a nakonfigurovat nové App Service, do které můžete aplikaci publikovat. 

Jako součást nastavení App Service vytvoříte:

- Nová [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) , která bude obsahovat všechny prostředky Azure pro službu.
- Nový [plán hostování](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) , který určuje umístění, velikost a funkce webové serverové farmy, která je hostitelem vaší aplikace.

Při vytváření App Service a publikování webové aplikace postupujte podle těchto kroků:

1. V **Průzkumník řešení**klikněte pravým tlačítkem na projekt **MyFirstAzureWebApp** a vyberte **publikovat**. Pokud jste se ještě přihlásili ke svému účtu Azure ze sady Visual Studio, vyberte buď **Přidat účet** , nebo **se přihlaste**. Můžete si také vytvořit bezplatný účet Azure.

1. V dialogovém okně **vybrat cíl publikování** zvolte možnost **App Service**, vyberte **vytvořit novou**a potom vyberte **vytvořit profil**.

   ![Výběr cíle publikování](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. V dialogovém okně **App Service: vytvořit nové** zadejte globálně jedinečný **název** vaší aplikace, a to tak, že přijmete výchozí název nebo zadáte nový název. Platné znaky jsou: `a-z` , `A-Z` , a `0-9` `-` . Tento **název** se používá jako předpona adresy URL vaší webové aplikace ve formátu `http://<app_name>.azurewebsites.net` .

1. U **předplatného**přijměte uvedené předplatné nebo v rozevíracím seznamu vyberte nový.

1. V **skupiny prostředků**vyberte **Nový**. Do **nového názvu skupiny prostředků**zadejte *MyResourceGroup* a vyberte **OK**. 

1. V případě **plánu hostování**vyberte možnost **Nový**. 

1. V dialogovém okně **plán hostování: vytvořit nové** zadejte hodnoty uvedené v následující tabulce:

   | Nastavení  | Navrhovaná hodnota | Popis |
   | -------- | --------------- | ----------- |
   | **Plán hostování**  | *myFirstAzureWebAppPlan* | Název plánu služby App Service. |
   | **Umístění**      | *Západní Evropa* | Datacentrum, které je hostitelem webové aplikace. |
   | **Velikost**          | Zadejte možnost pro *bezplatnou* SKU. | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |
   
   ![Vytvořit nový plán hostování](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Ponechte **Application Insights** nastavenou na *žádný*.

1. V dialogovém okně **App Service: vytvořit nové** vyberte **vytvořit** a začněte vytvářet prostředky Azure.

   ![Vytvořit novou službu App Service](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Po dokončení průvodce vyberte **publikovat**.

   ![Publikování webové aplikace do Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publikuje vaši webovou aplikaci v ASP.NET Core do Azure a spustí ji ve výchozím prohlížeči. 

   ![Publikovaná webová aplikace v ASP.NET běžící v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Blahopřejeme!** Vaše webová aplikace v ASP.NET Core běží živě v Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Pomocí těchto kroků aktualizujete a znovu nasadíte webovou aplikaci:

1. V **Průzkumník řešení**v rámci projektu otevřete **stránky**  >  **index. cshtml**.

1. Celou značku nahraďte `<div>` následujícím kódem:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

1. Na stránce **publikovat** Souhrn vyberte **publikovat**.

   ![Publikování aktualizace webové aplikace](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná webová aplikace v ASP.NET běžící v Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Pokud chcete spravovat webovou aplikaci, použijte [Azure Portal](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

![Vyberte App Services](./media/app-service-web-get-started-dotnet/app-services.png)

Na stránce **App Services** vyberte název vaší webové aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-dotnet/select-app-service.png)

Stránka **Přehled** pro vaši webovou aplikaci obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V nabídce vlevo najdete další stránky pro konfiguraci vaší aplikace.

![App Service v Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili aplikaci Visual Studio k vytvoření a nasazení webové aplikace ASP.NET Core k Azure App Service.

V dalším článku se dozvíte, jak vytvořit aplikaci .NET Core a jak ji připojit k SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

> [!div class="nextstepaction"]
> [Konfigurace aplikace ASP.NET Core](configure-language-dotnetcore.md)

Chcete optimalizovat a uložit své útraty do cloudu?

> [!div class="nextstepaction"]
> [Zahájení analýzy nákladů pomocí Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

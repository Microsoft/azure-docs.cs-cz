---
title: 'Úvodní příručka: Vytvoření aplikace C# ASP.NET Core'
description: Zjistěte, jak spouštět webové aplikace ve službě Azure App Service nasazením výchozí šablony webové aplikace C# ASP.NET Core z Visual Studia.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 04/22/2020
ms.custom: mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 6c7ab1290033792cf356c4882811670011df5efe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086041"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Úvodní příručka: Vytvoření webové aplikace ASP.NET Core v Azure

V tomto rychlém startu se dozvíte, jak vytvořit a nasadit první ASP.NET webové aplikace Core do [služby Azure App Service](overview.md). 

Po dokončení budete mít skupinu prostředků Azure skládající se z hostování služby App Service a služby App Service s nasazenou webovou aplikací.

## <a name="prerequisites"></a>Požadované součásti

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/dotnet/).
- Tento rychlý start nasazuje aplikaci do služby App Service ve Windows. Nasazení do služby App Service v _Linuxu_ je popsané v tématu [Vytvoření webové aplikace v .NET Core ve službě App Service v Linuxu](./containers/quickstart-dotnetcore.md).
- Nainstalujte <a href="https://www.visualstudio.com/downloads/" target="_blank">Sivisual Studio 2019</a> s **ASP.NET a zatížením vývoje webu.**

  Pokud už jste Visual Studio 2019 nainstalovali:

  - Nainstalujte nejnovější aktualizace v sadě Visual Studio výběrem **možnosti Kontrola nápovědy** > **k aktualizacím**.
  - Přidejte pracovní vytížení výběrem **nástroje** > **získat nástroje a funkce**.


## <a name="create-an-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

Vytvořte ASP.NET základní webovou aplikaci ve Visual Studiu takto:

1. Otevřete Visual Studio a vyberte **Vytvořit nový projekt**.

1. V **části Vytvořit nový projekt**vyberte ASP.NET základní **webovou aplikaci** a potvrďte, že **c#** je uveden v jazycích pro tuto volbu, a pak vyberte **Další**.

1. V **okně Konfigurace nového projektu**pojmenujte projekt webové aplikace *myFirstAzureWebApp*a vyberte **Vytvořit**.

   ![Konfigurace projektu webové aplikace](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Do Azure můžete nasadit libovolný typ webové aplikace ASP.NET Core, ale pro tento rychlý start zvolte šablonu **webové aplikace.** Zkontrolujte, zda je **možnost Ověřování** nastavena na **možnost Žádné ověřování**a zda není vybrána žádná jiná možnost. Potom vyberte **Create** (Vytvořit).

   ![Vytvoření nové webové aplikace ASP.NET Core](./media/app-service-web-get-started-dotnet/create-aspnet-core-web-app.png) 
   
1. V nabídce Visual Studio vyberte **Ladění** > **start bez ladění** spustit webovou aplikaci místně.

   ![Webová aplikace spuštěná místně](./media/app-service-web-get-started-dotnet/web-app-running-locally.png)

## <a name="publish-your-web-app"></a>Publikování webové aplikace

Chcete-li publikovat webovou aplikaci, musíte nejprve vytvořit a nakonfigurovat novou službu App Service, do které můžete aplikaci publikovat. 

V rámci nastavení služby App Service vytvoříte:

- Nová [skupina prostředků,](https://docs.microsoft.com/azure/azure-resource-manager/management/overview#terminology) která obsahuje všechny prostředky Azure pro službu.
- Nový [hostingový plán,](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) který určuje umístění, velikost a funkce farmy webového serveru, která hostuje vaši aplikaci.

Chcete-li vytvořit službu App Service a publikovat webovou aplikaci, postupujte takto:

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt **myFirstAzureWebApp** a vyberte **publikovat**. Pokud jste se ještě nepřihlásili ke svému účtu Azure z Visual Studia, vyberte Přidat **účet** nebo **Přihlásit se**. Můžete si taky vytvořit bezplatný účet Azure.

1. V **dialogovém** okně Vybrat cíl publikování zvolte **App Service**, vyberte **Create New**a pak vyberte Vytvořit **profil**.

   ![Výběr cíle publikování](./media/app-service-web-get-started-dotnet/pick-publish-target-vs2019.png)

1. V **app service: Vytvořte nový** dialog, zadejte globálně jedinečný **název** pro vaši aplikaci buď přijetívýchozí název nebo zadáním nového názvu. Platné znaky `a-z`jsou: `0-9`, `-` `A-Z`, a . Tento **název se** používá jako předpona adresy `http://<app_name>.azurewebsites.net`URL webové aplikace ve formátu .

1. V **části Předplatné**přijměte předplatné, které je v seznamu uvedeno, nebo vyberte nové předplatné z rozevíracího seznamu.

1. Ve **skupině Prostředky**vyberte **nový**. Do **pole Nový název skupiny prostředků**zadejte *myResourceGroup* a vyberte **OK**. 

1. V **možnosti Hosting Plan**vyberte **Nový**. 

1. V **hostitelském plánu: Vytvořit nové** dialogové okno zadejte hodnoty zadané v následující tabulce:

   | Nastavení  | Navrhovaná hodnota | Popis |
   | -------- | --------------- | ----------- |
   | **Plán hostování**  | *myFirstAzureWebAppPlan* | Název plánu služby App Service. |
   | **Umístění**      | *Západní Evropa* | Datacentrum, které je hostitelem webové aplikace. |
   | **Velikost**          | *Free* | [Cenová úroveň](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) určuje funkce hostování. |
   
   ![Vytvořit nový hostingový plán](./media/app-service-web-get-started-dotnet/create-new-hosting-plan-vs2019.png)

1. Ponechat **přehledy aplikací** *nastavené*na žádný .

1. Ve **službě App Service: Vytvořit nové** dialogové okno, vyberte **Vytvořit** a začněte vytvářet prostředky Azure.

   ![Vytvořit novou službu aplikace](./media/app-service-web-get-started-dotnet/create-new-app-service-vs2019.png)

1. Po dokončení průvodce vyberte **Publikovat**.

   ![Publikování webové aplikace do Azure](./media/app-service-web-get-started-dotnet/publish-web-app-vs2019.png)

   Visual Studio publikuje vaši ASP.NET webovou aplikaci Core do Azure a spustí ji ve výchozím prohlížeči. 

   ![Publikovaná ASP.NET webová aplikace spuštěná v Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

**Blahopřejeme!** Vaše webová aplikace ASP.NET Core běží živě ve službě Azure App Service.

## <a name="update-the-app-and-redeploy"></a>Aktualizace a opětovné nasazení aplikace

Podle následujících kroků aktualizujte a znovu nasaďte webovou aplikaci:

1. V **Průzkumníku řešení**otevřete v rámci projektu **stránku** > **Index.cshtml**.

1. Nahraďte `<div>` celou značku následujícím kódem:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Opětovné nasazení do služby Azure provedete tak, že v **Průzkumníku řešení** kliknete pravým tlačítkem na projekt **myFirstAzureWebApp** a vyberete **Publikovat**.

1. Na stránce **Publikovat** souhrn vyberte **Publikovat**.

   ![Publikovat aktualizaci webové aplikace](./media/app-service-web-get-started-dotnet/publish-update-to-web-app-vs2019.png)

Po dokončení publikování spustí Visual Studio prohlížeč na adrese URL webové aplikace.

![Aktualizovaná ASP.NET webová aplikace spuštěná v Azure](./media/app-service-web-get-started-dotnet/updated-web-app-running-live.png)

## <a name="manage-the-azure-app"></a>Správa aplikace Azure

Pokud chcete spravovat webovou aplikaci, přejděte na [portál Azure](https://portal.azure.com)a vyhledejte a vyberte **App Services**.

![Vybrat služby aplikace](./media/app-service-web-get-started-dotnet/app-services.png)

Na stránce **Služby aplikací** vyberte název webové aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-web-get-started-dotnet/select-app-service.png)

Stránka **Přehled** webové aplikace obsahuje možnosti pro základní správu, jako je procházení, zastavení, spuštění, restartování a odstranění. V levé nabídce jsou uvedeny další stránky pro konfiguraci aplikace.

![Služba App Service na webu Azure Portal](./media/app-service-web-get-started-dotnet/web-app-overview-page.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí Visual Studia vytvořili a nasadili ASP.NET webovou aplikaci Core do služby Azure App Service.

Přejdete k dalšímu článku, kde se dozvíte, jak vytvořit aplikaci .NET Core a připojit ji k databázi SQL:

> [!div class="nextstepaction"]
> [ASP.NET Core s SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)

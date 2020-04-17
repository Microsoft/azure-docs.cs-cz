---
title: zahrnout soubor
description: zahrnout soubor
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461990"
---
1. Stáhněte si rychlé starty sady SDK klienta pro následující platformy:
    
    [iOS (cíl-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Systém Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Pokud používáte projekt iOS, musíte si stáhnout "azuresdk-iOS-\*.zip" z nejnovější verze [GitHubu](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Rozbalte a `MicrosoftAzureMobile.framework` přidejte soubor do kořenového adresáře projektu.
    >

2. Budete muset přidat připojení k databázi nebo se připojit k existujícímu připojení. Nejprve určete, zda vytvoříte úložiště dat nebo použijete existující.

    - **Vytvoření nového úložiště dat**: Pokud chcete vytvořit úložiště dat, použijte následující rychlý start:

        [Úvodní příručka: Začínáme s jednotlivými databázemi v Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Existující zdroj dat**: Postupujte podle následujících pokynů, pokud chcete použít existující připojení k databázi

        1. Formát připojovacího řetězce databáze SQL -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Název serveru, to lze nalézt na stránce přehled pro vaši databázi a je obvykle ve formě "server_name.database.windows.net".
            **{port}** obvykle 1433.
            **{your_catalogue}** Název databáze.
            **{your_username}** Uživatelské jméno pro přístup k databázi.
            **{your_password}** Heslo pro přístup k databázi.

            [Další informace o formátu připojovacího řetězce SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Přidejte připojovací řetězec do **mobilní aplikace** V app service, můžete spravovat připojovací řetězce pro vaši aplikaci pomocí **možnosti Konfigurace** v nabídce.

            Přidání připojovacího řetězce:

            1. Klikněte na kartu **Nastavení aplikace.**

            2. Klikněte na **[+] Nový připojovací řetězec**.

            3. Budete muset zadat **Název**, **Hodnota** a **Typ** pro připojovací řetězec.

            4. **Název** typu jako`MS_TableConnectionString`

            5. Hodnota by měla být spojovací řetězec, který jste vytvořili v kroku před.

            6. Pokud přidáváte připojovací řetězec do databáze SQL Azure, zvolte **SQLAzure** v části **typ**.

3. Azure Mobile Apps má sady SDK pro back-endy .NET a Node.js.

   - **Back-end Node.js**
    
     Pokud budete používat aplikaci Node.js quickstart, postupujte podle následujících pokynů.
     
        1. Vytvořte nové rozhraní API – můžete buď provést změny přímo na webu Azure Portal, nebo upravit kód místně ve vývojovém prostředí a pak publikovat do Azure. Klikněte `App Service Editor (Preview)` na `Development Tools` pod nabídku, která poskytuje prostředí pro úpravy kódu aplikace v prohlížeči.
        
        2. Klikněte `Go` na a po otevření Editoru služby App Service máte plnou kontrolu nad zdrojovým kódem. Za předpokladu, že jste již nainstalovali balíček express a azure-mobile-apps s příkazem npm install, klikněte na složku API pod WEBROOT a vytvořte nebo upravte vlastní rozhraní API. Proveďte změny v souboru kódu a změny se automaticky uloží.
        
        3. Máte úplné řízení v Azure SQL Database slouží k ukládání dat aplikace. V databázi můžete snadno vytvářet nové tabulky.
 
   - **Back-end .NET**
    
        Pokud budete používat aplikaci rychlého spuštění .NET, postupujte podle následujících pokynů.

        1. Stáhněte si projekt serveru Azure Mobile Apps .NET z [úložiště azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Vytvořte projekt serveru .NET místně v sadě Visual Studio.

        3. V sadě Visual Studio otevřete Průzkumníka řešení, klikněte pravým tlačítkem myši na `ZUMOAPPNAMEService` projekt, klikněte na **Publikovat**, zobrazí se `Publish to App Service` okno. Pokud pracujete na Macu, podívejte se na další způsoby nasazení aplikace [zde](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publikování ve visual studiu](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Vyberte **Služba App Service** jako cíl publikování a pak klikněte na Vybrat **existující**a potom klikněte na tlačítko **Publikovat** v dolní části okna.

        5. Budete se muset nejprve přihlásit do Visual Studia pomocí předplatného Azure. Vyberte `Subscription` `Resource Group`možnost , a pak vyberte název aplikace. Až budete připraveni, klikněte na **OK**, to bude nasazovat projekt serveru .NET, který máte místně do back-endu služby App Service. Po dokončení nasazení budete přesměrováni `http://{zumoappname}.azurewebsites.net/` v prohlížeči.                   

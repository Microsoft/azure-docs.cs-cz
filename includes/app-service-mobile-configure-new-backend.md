---
title: zahrnout soubor
description: zahrnout soubor
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 99ca7e82a11687d25355589e7ea539a14cdb493b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420789"
---
1. Stáhněte si klienta SDK rychlých startů pro tyto platformy:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Pokud používáte projektu pro iOS budete muset stáhnout "azuresdk-iOS -\*.zip" z [nejnovější vydaná verze Githubu](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Rozbalte ho a přidejte `MicrosoftAzureMobile.framework` souboru do kořenového adresáře projektu.
    >

2. Budete muset přidat připojení k databázi nebo připojení k existující připojení. Nejdřív zjistěte, zda bude vytvoření úložiště dat nebo použijte již existující.

    - **Vytvořit nové úložiště dat**: Pokud se chystáte vytvořit úložiště dat, použijte následující rychlý start:

        [Rychlé zprovoznění: Začínáme s izolované databáze ve službě Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Stávající zdroj dat**: Postupujte podle pokynů níže, pokud chcete použít existující připojení databáze

        1. Formát připojovacího řetězce SQL databáze: `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Název serveru, to lze nalézt v stránka s přehledem pro vaši databázi a je obvykle ve formě "server_name.database.windows.net".
            **{port}**  obvykle 1433.
            **{your_catalogue}**  Název databáze.
            **{your_username}**  Uživatelské jméno pro přístup k vaší databázi.
            **{your_password}**  Heslo pro přístup k vaší databázi.

            [Další informace o formátu připojovacího řetězce SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Přidat připojovací řetězec pro váš **mobilní aplikace** ve službě App Service, můžete spravovat připojovací řetězce pro vaši aplikaci s použitím **konfigurace** v nabídce.

            Chcete-li přidat připojovací řetězec:

            1. Klikněte na **nastavení aplikace** kartu.

            2. Klikněte na **[+] nový připojovací řetězec**.

            3. Budete muset poskytnout **název**, **hodnotu** a **typ** pro připojovací řetězec.

            4. Typ **název** jako `MS_TableConnectionString`

            5. Hodnota by měla být připojovacím řetězci, který je vytvořen v kroku před.

            6. Pokud chcete přidat připojovací řetězec k databázi SQL Azure vyberte **SQLAzure** pod **typ**.

3. Azure Mobile Apps má sady SDK pro .NET a Node.js back-EndY.

   - **Back-end Node.js**
    
     Pokud se chystáte používat aplikace v Node.js rychlý start, postupujte podle pokynů níže.

     1. Na webu Azure Portal, přejděte na **jednoduché tabulky**, zobrazí se tato obrazovka.
      
        ![Jednoduché tabulky uzlu](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Ujistěte se, že připojovací řetězec SQL je již přidána do **konfigurace** kartu. Zaškrtněte políčko **beru na vědomí, že tato akce přepíše veškerý obsah webu** a klikněte na tlačítko **vytvořit tabulku TodoItem** tlačítko.
     
        ![Konfigurace uzlu jednoduché tabulky](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. V **jednoduché tabulky**, klikněte na tlačítko **+ přidat** tlačítko.
    
        ![Tlačítko pro přidání uzlu jednoduché tabulky](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Vytvoření `TodoItem` tabulku s anonymní přístup.
      
        ![Jednoduché tabulky uzlu přidat tabulku](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Back-end .NET**
    
        Pokud se chystáte používat aplikaci rychlý start pro .NET, postupujte podle pokynů níže.

        1. Stáhněte serverový projekt Azure Mobile Apps pro .NET ze [úložiště azure-mobile aplikací – rychlé starty](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Sestavte projekt .NET server místně v sadě Visual Studio.

        3. V sadě Visual Studio, otevřete Průzkumníka řešení, klikněte pravým tlačítkem na `ZUMOAPPNAMEService` projektu, klikněte na tlačítko **publikovat**, zobrazí se `Publish to App Service` okna. Pokud pracujete na počítači Mac, přečtěte si další způsoby jak nasadit aplikaci [tady](https://docs.microsoft.com/en-us/azure/app-service/deploy-local-git).
        
           ![Publikování aplikace Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Vyberte **služby App Service** jako cíl publikování, pak klikněte na **vybrat existující**, klikněte **publikovat** tlačítko v dolní části okna.

        5. Budete potřebovat k přihlášení do sady Visual Studio s předplatným Azure nejprve. Vyberte `Subscription`, `Resource Group`a pak vyberte název vaší aplikace. Až budete připravení, klikněte na tlačítko **OK**, to nasadí serverový projekt .NET, který máte místně na back-end služby App Service. Jakmile se nasazení dokončí, budete přesměrováni na `http://{zumoappname}.azurewebsites.net/` v prohlížeči.
        
           ![Je back-endu](./media/app-service-mobile-configure-new-backend/backend-is-up.png)

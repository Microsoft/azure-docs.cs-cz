---
title: Jak migrovat a publikovat webovou aplikaci pro cloudové služby Azure ze sady Visual Studio | Microsoft Docs
description: Zjistěte, jak migrovat a publikovat webovou aplikaci na cloudové služby Azure pomocí sady Visual Studio
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: 682be267bbb1cf495d85af5786309a98f885fc23
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Postupy: migrace a publikovat webovou aplikaci pro cloudové služby Azure ze sady Visual Studio

Pokud chcete využít výhod hostitelských služeb a schopnost škálování Azure, můžete migrovat a nasadit webovou aplikaci do cloudové služby Azure. Je potřeba jenom minimální změny. Tento článek se zabývá nasazení do cloudové služby pouze; služby App Service naleznete v části [nasazení webové aplikace v Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Tato migrace je podporovaná jenom pro konkrétní projekty ASP.NET, Silverlight, WCF a pracovních postupů WCF. Není podporována pro projekty ASP.NET Core. V tématu [podporované šablony projektů](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrace projektu do cloudové služby

1. Klikněte pravým tlačítkem na projekt webové aplikace a vyberte **převést > převést na projekt cloudové služby Microsoft Azure**. (Všimněte si, že tento příkaz nezobrazí, pokud už máte projekt webové role v řešení.)
1. Visual Studio vytvoří projekt cloudové služby v řešení, který obsahuje požadované webovou roli. Název tohoto projektu je stejný jako projekt aplikace pomocí plus přípona `.Azure`.
1. Visual Studio také nastaví **místní kopie** vlastnost na hodnotu true pro všechny sestavení, které jsou požadovány pro MVC 2, MVC 3, MVC 4 a Silverlight obchodních aplikací. Tato vlastnost přidá do balíčku služby, který se používá pro nasazení těchto sestavení.

   > [!Important]
   > Pokud máte další sestavení nebo soubory, které jsou požadovány pro tuto webovou aplikaci, je nutné ručně nastavit vlastnosti u těchto souborů. Informace o tom, jak nastavení těchto vlastností najdete v tématu [souborů v balíčku služby](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Chyby a upozornění

Všechna upozornění a chyb vzniklých znamenat problémy opravit před nasazením do Azure, jako je chybějící sestavení.

Pokud sestavit aplikaci, spusťte ho místně pomocí emulátoru služby výpočty v nebo jej publikujte do Azure, může se zobrazit chyba: "Zadaná cesta a název souboru jsou příliš dlouhé." Tato chyba znamená, že délka názvu plně kvalifikovaný projektu Azure překračuje 146 znaků. Chcete-li tento problém, přesunete do jiné složky s kratší cestou řešení.

Další informace o tom, jak považovat všechna upozornění, chyby najdete v tématu [konfigurace projektu Azure Cloud Service pomocí sady Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Test migrace místně

1. V sadě Visual Studio **Průzkumníku řešení**, klikněte pravým tlačítkem na projektu přidané cloudové služby a vyberte **nastavit jako spouštěný projekt**.
1. Vyberte **ladění > Spustit ladění** (F5) spusťte ladění prostředí Azure. Toto prostředí poskytuje konkrétně emulace různých služeb Azure.

### <a name="use-an-azure-sql-database-for-your-application"></a>Použít databázi SQL Azure pro vaši aplikaci

Pokud máte připojovací řetězec pro vaše webové aplikace, která používá databázi SQL serveru místní, musíte místo toho provést migraci databáze k databázi SQL Azure a aktualizovat připojovací řetězec. Pokyny k tomuto procesu najdete v tématu v těchto tématech:

- [Migrace databáze SQL serveru do databáze SQL v cloudu](sql-database/sql-database-cloud-migrate.md)
- [Použití .NET (C#) v sadě Visual Studio k připojení a dotazování a Azure SQL database](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publikování aplikace do Azure Cloud Service

1. Vytvořte potřebné cloudové služby a úložiště účty ve vašem předplatném Azure jak je popsáno na [Příprava na publikování nebo nasadit aplikaci Azure ze sady Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt aplikace a vyberte **publikování do služby Microsoft Azure...**  (který se liší od příkaz "Publikování...".).
1. V **publikování aplikaci Azure** které se zobrazí, přihlaste se pomocí účtu s předplatným Azure a vyberte **Další >**.
1. V **Nastavení > společná nastavení** , vyberte cíl cloudové služby z **Cloudová služba** rozevíracího seznamu, spolu s zvolené prostředí a konfigurace. 
1. V **Nastavení > Upřesnit nastavení**, vyberte účet úložiště, který chcete použít a pak vyberte **Další >**.
1. V **diagnostiky**, vyberte, zda chcete odesílat informace do služby Application Insights.
1. Vyberte **Další >** k zobrazení souhrnu, pak vyberte **publikovat** ke spuštění nasazení.
1. Visual Studio otevře okno s aktivity protokolu, kde můžete sledovat průběh:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Volitelné) Pokud chcete zrušit proces nasazení, klikněte pravým tlačítkem na řádku položky v protokolu aktivit a zvolte **zrušit a odeberte**. Tento příkaz zastaví proces nasazení a odstraní prostředí nasazení z Azure. Poznámka: Pokud chcete odebrat toto nasazení prostředí poté, co byla nasazena, musíte použít [portál Azure](https://portal.azure.com).
1. (Volitelné) Po spuštění instance role mít Visual Studio automaticky zobrazí prostředí nasazení v **Průzkumníka serveru > cloudové služby** uzlu. Odtud můžete zobrazit stav instance jednotlivých rolí.
1. Pro přístup k aplikaci po nasazení, zvolte na šipku vedle vašeho nasazení, když stav **dokončeno** se zobrazí v **protokol činnosti Azure** společně s adresu URL. Najdete v následující tabulce najdete podrobnosti o tom, jak spustit určitý typ webové aplikace z Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Pomocí emulátoru služby výpočty v a spuštění aplikace v Azure

Všechny typy aplikací lze spustit v prohlížeči připojené k sadě Visual Studio debugger výběrem **ladění > Spustit ladění** (F5). S projektem prázdný webové aplikace ASP.NET, je nejprve nutno přidat `.aspx` stránky v aplikaci a nastavte jej jako úvodní stránky pro webový projekt.

Následující tabulka obsahuje podrobnosti o spouštění aplikace v Azure:

   | Typ webové aplikace | spuštění v Azure |
   | --- | --- | --- |
   | Webová aplikace ASP.NET<br/>(včetně MVC 2, MVC 3, MVC 4) | Vyberte adresu URL v **nasazení** kartě pro **protokol činnosti Azure**. |
   | Prázdná webová aplikace ASP.NET | Pokud máte výchozí `.aspx` stránky v aplikaci, vyberte adresu URL v **nasazení** kartě pro **protokol činnosti Azure**. Přejít na jinou stránku, zadejte v prohlížeči adresu URL v následujícím formátu: `<deployment_url>/<page_name>.aspx` |
   | Aplikace Silverlight<br/>Obchodní aplikace Silverlight<br/>Navigace aplikace Silverlight | Přejděte na stránku specifické pro vaši aplikaci pomocí následující tvar adresy URL: `<deployment_url>/<page_name>.aspx` |
    Aplikace služby WCF<br/>Aplikace služby pracovního postupu WCF | Nastavte `.svc` souboru jako úvodní stránky pro svůj projekt služby WCF. Potom přejděte na `<deployment_url>/<service_file>.svc` |
   | Dynamické ASP.NET Entity<br/>Linq to SQL ASP.NET dynamických dat | Aktualizujte připojovací řetězec, jak je popsáno v následující části. Potom přejděte na `<deployment_url>/<page_name>.aspx`. Pro technologie Linq to SQL musíte použít Azure SQL database. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Aktualizovat připojovací řetězec pro ASP.NET dynamické entity

1. Vytvořte databázi SQL Azure pro webovou aplikaci ASP.NET dynamické entity, jak je popsáno výše v (#use-an-azuresql-database-for-your-application).
1. Přidání tabulek a polí, které je nutné pro tuto databázi z portálu Azure.
1. Zadejte připojovací řetězec `web.config` soubor v následujícím formátu a soubor uložte:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Aktualizace *connectionString* hodnotu s připojovací řetězec ADO.NET pro vaši databázi SQL Azure následujícím způsobem:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Šablony projektů podporované

Aplikace, které můžete migrovat a publikované na cloudové služby musí používat jednu z šablon v následující tabulce. ASP.NET Core není podporována.

| Skupina šablon | Šablona projektu |
| --- | --- |
| Web | ASP.NET – webové aplikace (rozhraní .NET Framework) |
| Web | Webová aplikace ASP.NET MVC 2 |
| Web | Webové aplikace ASP.NET MVC 3 |
| Web | Webové aplikace ASP.NET MVC4 |
| Web | Webové aplikace ASP.NET prázdný (nebo webu) |
| Web | Prázdná webová aplikace ASP.NET MVC 2 |
| Web | Webovou aplikaci ASP.NET dynamické dat entity |
| Web | Linq to SQL webové aplikace ASP.NET dynamických dat |
| Silverlight | Aplikace Silverlight |
| Silverlight | Obchodní aplikace Silverlight |
| Silverlight | Navigace aplikace Silverlight |
| WCF | Aplikace služby WCF |
| WCF | Aplikace služby pracovního postupu WCF |
| Pracovní postup | Aplikace služby pracovního postupu WCF |

## <a name="next-steps"></a>Další postup

- [Příprava na publikování nebo nasadit aplikaci Azure ze sady Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Nastavení s názvem pověření pro ověření](vs-azure-tools-setting-up-named-authentication-credentials.md).

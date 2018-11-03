---
title: Jak migrovat a publikovat webovou aplikaci do cloudové služby Azure z Visual Studia | Dokumentace Microsoftu
description: Další informace o migraci a publikování vašich webových aplikací na cloudové služby Azure pomocí sady Visual Studio
services: visual-studio-online
author: ghogen
manager: douge
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ghogen
ms.openlocfilehash: df4fab213d5bf3f947d696e0a45c27fe1bcf597b
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969570"
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Postupy: migrace a publikovat webovou aplikaci do cloudové služby Azure ze sady Visual Studio

Pokud chcete využít výhod hostitelských služeb a schopnost škálování Azure, může být vhodné k migraci a nasazení vaší webové aplikace do cloudové služby Azure. Je nutné provést jenom minimální změny. Tento článek se týká nasazení do cloudové služby. App Service, najdete v části [nasazení webové aplikace ve službě Azure App Service](app-service/app-service-deploy-local-git.md).

> [!Important]
> Takováto migrace je podporovaná jenom pro konkrétní projekty ASP.NET, Silverlight, WCF a pracovního postupu WCF. Není podporována pro projekty ASP.NET Core. Zobrazit [podporované šablony projektu](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrace projektu do cloudové služby

1. Klikněte pravým tlačítkem na projekt webové aplikace a vyberte **převést > převést na projekt cloudové služby Microsoft Azure**. (Všimněte si, že tento příkaz nezobrazí, pokud již máte projekt webové role v řešení.)
1. Visual Studio vytvoří projekt cloudové služby v řešení, který obsahuje požadované webové role. Název tohoto projektu je stejný jako projekt aplikace s plus přípona `.Azure`.
1. Visual Studio také nastaví **Kopírovat místně** vlastnost na hodnotu true pro všechna sestavení, které jsou požadovány pro MVC 2, MVC 3, MVC 4 a obchodní aplikace Silverlight. Tato vlastnost přidá do balíčku služby, který se používá pro nasazení těchto sestavení.

   > [!Important]
   > Pokud máte jiné sestavení nebo soubory, které jsou požadovány pro tuto webovou aplikaci, musíte ručně nastavit vlastnosti těchto souborů. Informace o nastavení těchto vlastností najdete v tématu [vložených souborů v balíčku služby](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Chyby a upozornění

Žádná upozornění ani chyby, ke kterým dochází signalizují potíže vyřešit před nasazením do Azure, jako je například chybějící sestavení.

Pokud svou aplikaci, spusťte ho místně pomocí emulátoru služby výpočty nebo ji publikovat na Azure, může se zobrazit chyba: "Zadaná cesta, název souboru nebo obojí jsou příliš dlouhé." Tato chyba označuje, že délka názvu úplný projekt Azure překračuje 146 znaků. Chcete-li tento problém, přesuňte do jiné složky s kratší cestou vašeho řešení.

Další informace o tom, jak zpracovávat všechna upozornění jako chyby najdete v tématu [konfigurace projektu cloudové služby Azure pomocí sady Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Testovací migrace místně

1. V sadě Visual Studio **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt přidání cloudové služby a vyberte **nastavit jako spouštěný projekt**.
1. Vyberte **ladit > Spustit ladění** (F5) a spusťte ladění prostředí Azure. Toto prostředí poskytuje konkrétně emulace různé služby Azure.

### <a name="use-an-azure-sql-database-for-your-application"></a>Použití služby Azure SQL Database pro vaši aplikaci

Pokud máte připojovací řetězec pro vaši webovou aplikaci, která používá k místní databázi SQL serveru, je nutné místo toho migrace databáze do služby Azure SQL Database a aktualizovat připojovací řetězec. Pokyny k tomuto procesu najdete v následujících tématech:

- [Migrace databáze SQL serveru do služby SQL Database v cloudu](sql-database/sql-database-cloud-migrate.md)
- [.NET (C#) pomocí sady Visual Studio k připojení a dotazování a Azure SQL database](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Publikujte aplikaci do služby Azure Cloud Service

1. Vytvořte nezbytné cloudové účty služeb a úložiště ve vašem předplatném Azure podle pokynů na [Příprava k publikování nebo nasazení aplikace Azure ze sady Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt aplikace a vyberte **publikování ve službě Microsoft Azure...**  (která se liší od příkazu "Publikovat...".).
1. V **publikování aplikaci Azure** , který se zobrazí, přihlaste se pomocí účtu s předplatným Azure a vyberte **Další >**.
1. V **Nastavení > Obecná nastavení** kartu, vyberte cílovou cloudovou službou od **Cloudovou službu** rozevíracího seznamu, spolu s vybrané prostředí a konfiguracemi. 
1. V **Nastavení > Upřesnit nastavení**, vyberte účet úložiště, který chcete použít, vyberte možnost **Další >**.
1. V **diagnostiky**, vyberte, jestli chcete posílat informace do Application Insights.
1. Vyberte **Další >** Chcete-li zobrazit souhrn, zvolte **publikovat** ke spuštění nasazení.
1. Visual Studio se otevře okno Protokol aktivit ve kterém můžete sledovat průběh:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Volitelné) Chcete-li zrušit proces nasazení, klikněte pravým tlačítkem na položky v protokolu aktivit a zvolte **zrušit a odebrat**. Tento příkaz celý proces nasazení zastaví a odstraní prostředí nasazení z Azure. Poznámka: Pokud chcete odebrat tohoto prostředí nasazení po nasazení, musíte použít [webu Azure portal](https://portal.azure.com).
1. (Volitelné) Po spuštění mají vaše instance rolí, sada Visual Studio automaticky zobrazí prostředí nasazení se **Průzkumník serveru > cloudové služby** uzlu. Odsud můžete zobrazit stav instance jednotlivých rolí.
1. Pro přístup k aplikaci po nasazení, vyberte šipku vedle vašeho nasazení, když ve stavu **dokončeno** se zobrazí v **protokol aktivit Azure** spolu s adresu URL. Najdete v následující tabulce najdete podrobnosti o tom, jak spustit konkrétní typ webové aplikace z Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Pomocí emulátoru služby výpočty a spuštění aplikace v Azure

Všechny typy aplikací můžete spustit v prohlížeči připojení k ladicímu programu sady Visual Studio tak, že vyberete **ladit > Spustit ladění** (F5). S projektem prázdná webová aplikace ASP.NET, je nutné nejprve přidat `.aspx` stránek ve vaší aplikaci a nastavte ji jako úvodní stránku pro webový projekt.

Následující tabulka obsahuje podrobnosti o spuštění aplikace v Azure:

   | Typ webové aplikace | Běžící v Azure |
   | --- | --- | --- |
   | Webová aplikace ASP.NET<br/>(včetně MVC 2, MVC 3 a MVC 4) | Vyberte adresu URL v **nasazení** kartu **protokol aktivit Azure**. |
   | Prázdná webová aplikace ASP.NET | Pokud máte výchozí `.aspx` stránek ve vaší aplikaci, vyberte adresu URL v **nasazení** kartu **protokol aktivit Azure**. Přejít na jinou stránku, zadejte v prohlížeči adresu URL v následujícím formátu: `<deployment_url>/<page_name>.aspx` |
   | Aplikace Silverlight<br/>Obchodní aplikace Silverlight<br/>Navigační aplikace Silverlight | Přejdete na konkrétní stránku pro vaši aplikaci s využitím v následujícím formátu adresy URL: `<deployment_url>/<page_name>.aspx` |
    Aplikace služby WCF<br/>Aplikace služeb pracovního postupu WCF | Nastavte `.svc` soubor jako úvodní stránku pro svůj projekt služby WCF. Přejděte na `<deployment_url>/<service_file>.svc` |
   | ASP.NET s dynamickými entitami<br/>ASP.NET s dynamickým datovým Linq na SQL | Aktualizujte připojovací řetězec, jak je popsáno v další části. Přejděte na `<deployment_url>/<page_name>.aspx`. Pro funkci Linq to SQL musíte použít službu Azure SQL database. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Aktualizujte připojovací řetězec pro ASP.NET s dynamickými entity

1. Vytvořte databázi SQL Azure pro webovou aplikaci ASP.NET, dynamické entit, jak je popsáno výše (#use-an-azuresql-database-for-your-application).
1. Přidání tabulek a polí, které potřebujete pro tuto databázi na webu Azure Portal.
1. Zadejte připojovací řetězec v `web.config` soubor v následujícím formátu a soubor uložte:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Aktualizace *connectionString* hodnotu připojovací řetězec ADO.NET pro vaši databázi SQL Azure následujícím způsobem:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Šablony projektů podporované

Aplikace, které můžete migrovat a publikované ve službě cloud services musíte použít některou ze šablon v následující tabulce. ASP.NET Core se nepodporuje.

| Šablony skupiny | Šablona projektu |
| --- | --- |
| Web | Webová aplikace ASP.NET (.NET Framework) |
| Web | Webové aplikace ASP.NET MVC 2 |
| Web | Webové aplikace ASP.NET MVC 3 |
| Web | Webová aplikace ASP.NET MVC4 |
| Web | Prázdná webová aplikace ASP.NET (nebo webu) |
| Web | Prázdná webová aplikace ASP.NET MVC 2 |
| Web | Webová aplikace ASP.NET s dynamickými datovými entitami |
| Web | ASP.NET s dynamickým datovým Linq na SQL webovou aplikaci |
| Silverlight | Aplikace Silverlight |
| Silverlight | Obchodní aplikace Silverlight |
| Silverlight | Navigační aplikace Silverlight |
| WCF | Aplikace služby WCF |
| WCF | Aplikace služeb pracovního postupu WCF |
| Pracovní postup | Aplikace služeb pracovního postupu WCF |

## <a name="next-steps"></a>Další postup

- [Příprava k publikování nebo nasazení aplikace Azure ze sady Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Nastavení s názvem přihlašovací údaje pro ověření](vs-azure-tools-setting-up-named-authentication-credentials.md).

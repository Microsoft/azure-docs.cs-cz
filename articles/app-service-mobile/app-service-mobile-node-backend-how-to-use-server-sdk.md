---
title: Jak pracovat s Node.js back-end Server SDK pro Mobile Apps | Microsoft Docs
description: Naučte se pracovat s Node.js back-end Server SDK pro Azure App Service Mobile Apps.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 335186deccaa82b9a8d262d62dd8ce5d620446b6
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak používat sadu SDK Mobile aplikace Node.js
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Tento článek obsahuje podrobné informace a příklady, které ukazují, jak pracovat Node.js back-end v funkce Mobile Apps služby Azure App Service.

## <a name="Introduction"></a>Úvod
Mobile Apps poskytuje možnost Přidat mobilní optimalizovaná data přístup webového rozhraní API k webové aplikaci. Mobile Apps SDK se poskytuje pro webové aplikace ASP.NET a Node.js. Sada SDK poskytuje následující operace:

* Tabulka operací (čtení, vložit, aktualizovat, odstraňovat) pro přístup k datům
* Operace vlastní rozhraní API

Obě operace zajištění ověřování napříč všech zprostředkovatelů identity, které umožňuje službě Azure App Service. Tyto zprostředkovatele patří zprostředkovatelé sociálních identity jako je Facebook, Twitter, Google a Microsoft, jakož i Azure Active Directory pro podnikové identity.

Můžete najít ukázky pro každý případ použití v [ukázky adresáře na Githubu].

## <a name="supported-platforms"></a>Podporované platformy
Mobilní aplikace Node.js SDK podporuje aktuální verzi LTS uzlu a novější. V současné době je nejnovější verze LTS v4.5.0 uzlu. Jiné verze uzlu může fungovat, ale nejsou podporovány.

Mobile Apps Node.js SDK podporuje dvě databáze ovladače: 

* Uzel mssql ovladač podporuje Azure SQL Database a místní instance systému SQL Server.  
* Ovladač sqlite3 podporuje pouze do jedné instance databáze SQLite.

### <a name="howto-cmdline-basicapp"></a>Vytvořit základní Node.js back-end pomocí příkazového řádku
Všechny mobilní aplikace Node.js back-endu spustí jako ExpressJS aplikace. ExpressJS je nejoblíbenější webové služby rozhraní k dispozici pro Node.js. Můžete vytvořit základní [Express] aplikace následujícím způsobem:

1. V příkazu nebo v okně prostředí PowerShell vytvořte adresář projektu:

        mkdir basicapp
2. Spustit `npm init` inicializovat struktura balíčku:

        cd basicapp
        npm init

   `npm init` Příkaz požádá sadu otázky k chybě při inicializaci projektu. Najdete příklad výstupu:

   ![Init – výstup npm][0]
3. Nainstalujte `express` a `azure-mobile-apps` knihovny z npm úložiště:

        npm install --save express azure-mobile-apps
4. Vytvoření souboru app.js k implementaci základní mobilní serveru:

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table.
        mobile.tables.add('TodoItem');

        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);

Tato aplikace vytvoří optimalizované mobilní rozhraní Web API s jeden koncový bod (`/tables/TodoItem`), který umožňuje přístup bez ověřování pro základní úložiště dat SQL pomocí dynamické schématu. Je vhodné pro následující elementy QuickStart knihovny klienta:

* [Rychlý start Android klienta]
* [Rychlé spuštění klienta Apache Cordova]
* [iOS klienta rychlý start]
* [Rychlé spuštění klienta Windows Store]
* [Rychlé spuštění klienta Xamarin.iOS]
* [Rychlé spuštění klienta Xamarin.Android]
* [Rychlé spuštění klienta Xamarin.Forms]

Můžete najít kód pro tuto základní aplikaci v [basicapp ukázce na Githubu].

### <a name="howto-vs2015-basicapp"></a>Vytvoření back-end Node.js pomocí sady Visual Studio 2015
Visual Studio 2015 vyžaduje rozšíření při vývoji aplikací Node.js v prostředí IDE. Chcete-li začít, nainstalujte [Node.js Tools 1.1 pro sadu Visual Studio]. Po dokončení instalace, vytvoření aplikace Express 4.x:

1. Otevřete **nový projekt** dialogové okno (z **soubor** > **nový** > **projektu**).
2. Rozbalte položku **šablony** > **JavaScript** > **Node.js**.
3. Vyberte **aplikace základní Azure Node.js Express 4**.
4. Zadejte název projektu. Vyberte **OK**.

   ![Nový projekt Visual Studio 2015][1]
5. Klikněte pravým tlačítkem myši **npm** uzel a vyberte možnost **nainstalovat nové balíčky npm**.
6. Možná budete muset aktualizovat katalog npm po vytvoření vaší první aplikace Node.js. Vyberte **aktualizovat** v případě potřeby.
7. Zadejte **aplikace azure mobile** do vyhledávacího pole. Vyberte **azure mobile apps 2.0.0** balíček a potom vyberte **instalovat balíček**.

   ![Instalace nové balíčky npm][2]
8. Vyberte **Zavřít**.
9. Otevřete soubor app.js přidání podpory pro Mobile Apps SDK. Na řádku 6 at dolní knihovny `require` příkazy, přidejte následující kód:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

   27 na přibližně řádek po druhé `app.use` příkazy, přidejte následující kód:

        app.use('/users', users);

        // Mobile Apps initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

   Uložte soubor.
10. Buď místní spuštění aplikace (rozhraní API je zpracování na http://localhost:3000) nebo publikovat ve službě Azure.

### <a name="create-node-backend-portal"></a>Vytvoření back-end Node.js pomocí portálu Azure
Můžete vytvořit Mobile Apps back-end přímo v [portál Azure]. Můžete provést následující kroky nebo vytvořit pomocí následujících klientských a serverových společně [vytvoření mobilní aplikace](app-service-mobile-ios-get-started.md) kurzu. Tento kurz obsahuje zjednodušenou verzi tyto pokyny a je nejvhodnější pro projekty testování konceptu.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpět v **Začínáme** podokně v části **vytvořit tabulku rozhraní API**, zvolte **Node.js** jazyka back-end.
Vyberte pole **beru na vědomí, že tato akce přepíše veškerý obsah webu**a potom vyberte **vytvořit tabulku TodoItem**.

### <a name="download-quickstart"></a>Stažení projektu kódu rychlý start back-end Node.js pomocí Git
Když vytvoříte back-end mobilní aplikace Node.js pomocí portálu společnosti **úvodní** podokně projekt Node.js se vytvoří a nasadí na váš web. Na portálu můžete přidat tabulky a rozhraní API a upravit soubory kódu pro back-end Node.js. Můžete také různé nástroje pro nasazení ke stažení projektu back-end, aby mohli přidat nebo upravit tabulek a rozhraní API a poté jej znovu publikovat projektu. Další informace najdete v tématu [Průvodce nasazením služby Azure App Service]. 

Následující postup používá úložiště Git ke stažení kód projektu rychlý start:

1. Pokud jste tak již neučinili, nainstalujte Git. Kroky potřebné k instalaci Git lišit podle operačních systémů. Distribuce specifické pro operační systém a instalační pokyny najdete v tématu [instalace Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
2. V tématu [Příprava úložiště](../app-service/app-service-deploy-local-git.md#prepare-your-repository) povolit úložiště Git pro vaši lokalitu back-end. Poznamenejte si nasazení uživatelské jméno a heslo.
3. V podokně pro Mobile Apps back-end, poznamenejte si **adresy URL pro klon Git** nastavení.
4. Spuštění `git clone` příkaz pomocí adresy URL pro klon Git. Zadejte své heslo, pokud jsou povinné, jako v následujícím příkladu:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Přejděte do místního adresáře (`/todolist` v předchozím příkladu) a Všimněte si, že byly staženy soubory projektu. Vyhledejte soubor todoitem.json v `/tables` adresáře. Tento soubor definuje oprávnění v tabulce. Také najdete soubor todoitem.js ve stejném adresáři. Definuje skripty operace CRUD pro tabulku.
6. Po provedení změn do souborů projektu, spusťte následující příkazy, které chcete přidat, potvrzení a odešlete změny do lokality:

        $ git commit -m "updated the table script"
        $ git push origin master

   Když přidáte nové soubory do projektu, musíte nejprve spustit `git add .` příkaz.

Pokaždé, když novou sadu potvrzení vložena do lokality, je znovu publikovat web.

### <a name="howto-publish-to-azure"></a>Publikování vaši Node.js back-end v Azure
Microsoft Azure poskytuje mnoho mechanismy pro publikování vaši mobilní aplikace Node.js back-end ve službě Azure. Mezi tyto mechanismy patří nástroje pro nasazení, které jsou integrované do sady Visual Studio, nástroje příkazového řádku a průběžné nasazování pro různé zdrojového kódu. Další informace najdete v tématu [Průvodce nasazením služby Azure App Service].

Azure App Service nabízí konkrétní doporučení pro Node.js aplikace, které byste si měli projít před publikováním back-end:

* Postup [zadejte verzi uzlu]
* Postup [použijte moduly uzlu]

### <a name="howto-enable-homepage"></a>Povolit domovskou stránku pro aplikaci
Mnoho aplikací jsou kombinací webových a mobilních aplikací. Rozhraní framework ExpressJS můžete kombinovat dvě omezující vlastnosti. V některých případech ale můžete chtít pouze mobilní rozhraní implementovat. Je užitečné k poskytování domovskou stránku a ujistěte se, zda je služba aplikace a spuštěná. Můžete zadat vlastní domovské stránky, nebo povolit dočasné domovskou stránku. Pokud chcete povolit dočasné domovskou stránku, použijte následující kód k vytvoření instance mobilní aplikace:

    var mobile = azureMobileApps({ homePage: true });

Pokud chcete pouze tuto možnost k dispozici při vývoji místně, můžete přidat do souboru azureMobile.js toto nastavení.

## <a name="TableOperations"></a>Operace s tabulkou
Server SDK pro Node.js aplikace azure mobile poskytuje mechanismus ke zveřejnění tabulek dat uložených ve službě Azure SQL Database jako webového rozhraní API. Poskytuje pět operace:

| Operace | Popis |
| --- | --- |
| GET /tables/*tablename* |Získáte všechny záznamy v tabulce. |
| GET /tables/*tablename*/:id |Získejte konkrétní záznam v tabulce. |
| POST /tables/*tablename* |Vytvořte záznam v tabulce. |
| Oprava /tables/*tablename*/:id |Aktualizujte záznam v tabulce. |
| ODSTRANĚNÍ /tables/*tablename*/:id |Odstraňte záznam v tabulce. |

Toto webové rozhraní API podporuje [OData] a rozšiřuje schéma tabulky pro podporu [synchronizaci dat offline].

### <a name="howto-dynamicschema"></a>Definice tabulky pomocí dynamické schématu
Než budete moct použít tabulku, je nutné zadat. Můžete definovat tabulky pomocí statické schématu (kde definujete sloupců ve schématu), nebo dynamicky (Pokud sada SDK ovládací prvky založené na příchozí požadavky schématu). Kromě toho můžete řídit konkrétních aspektů webového rozhraní API přidáním kódu jazyka JavaScript do definice.

Jako osvědčený postup, byste měli definovat každá tabulka v souboru jazyka JavaScript v `tables` adresáře a pak použijte `tables.import()` metoda importovat tabulky. Rozšíření ukázkové aplikace basic, by úprava souboru app.js:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed.
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined.
    mobile.tables.initialize().then(function () {
        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);
    });

Definice tabulky. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here.

    module.exports = table;

Tabulky ve výchozím nastavení používá dynamické schématu. Chcete-li vypnout dynamické schématu globálně, nastavte `MS_DynamicSchema` nastavení aplikace nastavte na hodnotu false na portálu Azure.

Kompletní příklad v můžete najít [todo ukázce na Githubu].

### <a name="howto-staticschema"></a>Definice tabulky pomocí statické schématu
Můžete definovat explicitně sloupce, které chcete zpřístupnit prostřednictvím webového rozhraní API. Aplikace azure mobile Node.js SDK automaticky přidá žádné další sloupce požadované pro synchronizaci dat offline do seznamu, který zadáte. Například klientské aplikace rychlý start vyžadovat tabulku s dva sloupce: `text` (řetězec) a `complete` (Boolean).  
V tabulce lze definovat v souboru JavaScript definice tabulky (umístěný ve `tables` directory) následujícím způsobem:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    module.exports = table;

Pokud definujete tabulky staticky, musíte také zavolat `tables.initialize()` metodu pro vytvoření schématu databáze při spuštění. `tables.initialize()` Metoda vrátí [promise] tak, aby webové služby neobsluhuje požadavky před inicializací databáze.

### <a name="howto-sqlexpress-setup"></a>Použít SQL Server Express jako úložiště dat vývoj v místním počítači
Mobile Apps Node.js SDK poskytuje tři možnosti pro obsluhující data z pole:

* Použití **paměti** ovladače k poskytování úložiště dočasnou příklad.
* Použití **mssql** ovladače k poskytování úložiště dat serveru SQL Server Express pro vývoj.
* Použití **mssql** ovladače k poskytování úložiště dat služby Azure SQL Database pro produkční prostředí.

Mobile Apps Node.js SDK používá [mssql Node.js balíček] vytvořit a používat připojení k systému SQL Server Express a SQL Database. Tento balíček vyžaduje, abyste povolili připojení TCP ve vaší instanci systému SQL Server Express.

> [!TIP]
> Ovladač paměti neposkytuje kompletní sadu zařízení pro testování. Pokud chcete testovat back-end vašeho místně, doporučujeme použít úložiště dat serveru SQL Server Express a ovladač mssql.
>
>

1. Stáhněte a nainstalujte [Microsoft SQL Server 2014 Express]. Ujistěte se, nainstalovat systém SQL Server 2014 Express s edicí nástroje. Pokud požadujete explicitně podpora 64bitových technologií, 32bitová verze spotřebovává méně paměti při spuštění.
2. Spusťte Správce konfigurace systému SQL Server 2014:

   a. Rozbalte **konfigurace sítě serveru SQL Server** uzlu v nabídce stromu.

   b. Vyberte **protokoly pro funkci SQLEXPRESS**.

   c. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **povolit**. Vyberte **OK** v dialogovém okně automaticky otevírané okno.

   d. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **vlastnosti**.

   e. Vyberte **IP adresy** kartě.

   f. Najít **IPAll** uzlu. V **TCP Port** zadejte **1433**.

      ![Nakonfigurujte systém SQL Server Express pro protokol TCP/IP][3]

   g. Vyberte **OK**. Vyberte **OK** v dialogovém okně automaticky otevírané okno.

   h. Vyberte **služby SQL Server** v nabídce stromu.

   i. Klikněte pravým tlačítkem na **SQL Server (SQLEXPRESS)** a vyberte **restartujte**.

   j. Zavřete Správce konfigurace systému SQL Server 2014.
3. Spusťte SQL Server 2014 Management Studio a připojte se k místní instanci systému SQL Server Express:

   1. Klikněte pravým tlačítkem na vaší instance v Průzkumníku objektů a vyberte **vlastnosti**.
   2. Vyberte **zabezpečení** stránky.
   3. Ujistěte se, že **systému SQL Server a ověřování systému Windows režimu** je vybrána.
   4. Vyberte **OK**.

      ![Konfigurace ověřování serveru SQL Server Express][4]
   5. Rozbalte položku **zabezpečení** > **přihlášení** v Průzkumníku objektů.
   6. Klikněte pravým tlačítkem na **přihlášení** a vyberte **nového přihlašovacího jména**.
   7. Zadejte přihlašovací jméno. Vyberte **Ověřování SQL Serveru**. Zadejte heslo a pak zadejte stejné heslo v **potvrzení hesla**. Heslo musí splňovat požadavky na složitost systému Windows.
   8. Vyberte **OK**.

      ![Přidání nového uživatele do systému SQL Server Express][5]
   9. Klikněte pravým tlačítkem na nové přihlášení a vyberte **vlastnosti**.
   10. Vyberte **role serveru** stránky.
   11. Zaškrtněte políčko pro **dbcreator** role serveru.
   12. Vyberte **OK**.
   13. Zavřete SQL Server 2015 Management Studio.

Ujistěte se, že záznam uživatelské jméno a heslo, které jste vybrali. Možná budete muset přiřadit další role serveru nebo oprávnění, v závislosti na požadavcích vaší databáze.

Čtení aplikace Node.js `SQLCONNSTR_MS_TableConnectionString` proměnnou prostředí pro připojovací řetězec pro tuto databázi. Tuto proměnnou lze nastavit ve vašem prostředí. Můžete například použít PowerShell k nastavení této proměnné prostředí:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Přístup k databázi přes připojení TCP/IP. Zadejte uživatelské jméno a heslo pro připojení.

### <a name="howto-config-localdev"></a>Nakonfigurujete svůj projekt pro místní vývoj
Mobile Apps přečte soubor JavaScript s názvem *azureMobile.js* z místního systému souborů. Nepoužívejte tento soubor ke konfiguraci SDK mobilních aplikací v provozním prostředí. Místo toho použijte **nastavení aplikace** v [portál Azure]. 

Soubor azureMobile.js třeba exportovat objekt konfigurace. Nejběžnější nastavení jsou:

* Nastavení databáze
* Nastavení protokolování diagnostiky
* Alternativní nastavení CORS

Tento příklad azureMobile.js souboru implementuje předchozí nastavení databáze:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Doporučujeme přidat do souboru .gitignore azureMobile.js (nebo jiné zdrojového kódu ignorovat souboru) zabránit hesla ukládají v cloudu. Vždy konfigurujte nastavení produkční v **nastavení aplikace** v rámci [portál Azure].

### <a name="howto-appsettings"></a>Konfigurovat nastavení aplikace pro mobilní aplikace
Většina nastavení v souboru azureMobile.js mají nastavení ekvivalentní aplikace v [portál Azure]. Použít ke konfiguraci vaší aplikace v seznamu následující **nastavení aplikace**:

| Nastavení aplikace | azureMobile.js setting | Popis | Platné hodnoty |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |jméno |Název aplikace |řetězec |
| **MS_MobileLoggingLevel** |logging.level |Úroveň minimální protokolu zpráv do protokolu |Chyba, upozornění, informace o podrobné nastavení, ladění, i |
| **MS_DebugMode** |ladit |Povolí nebo zakáže režim ladění |Hodnota TRUE, false |
| **MS_TableSchema** |data.schema |Výchozí název schématu pro tabulky SQL |String (výchozí: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Povolí nebo zakáže režim ladění |Hodnota TRUE, false |
| **MS_DisableVersionHeader** |verze (je nastavený na nedefinované) |Zakáže hlavičky X-záhlaví ZUMO-Server-Version |Hodnota TRUE, false |
| **MS_SkipVersionCheck** |skipversioncheck |Zakáže Kontrola verze klientského rozhraní API |Hodnota TRUE, false |

Nastavení aplikace nastavit:

1. Přihlaste se k [portál Azure].
2. Vyberte **všechny prostředky** nebo **App Services**a pak vyberte název vaší mobilní aplikace.
3. **Nastavení** podokně otevře ve výchozím nastavení. Pokud není, vyberte **nastavení**.
4. Na **Obecné** nabídce vyberte možnost **nastavení aplikace**.
5. Přejděte k položce **nastavení aplikace** části.
6. Pokud vaše aplikace, nastavení už existuje, vyberte hodnotu nastavení aplikace a příslušnou hodnotu upravte.
   Pokud vaše aplikace nastavení neexistuje, zadejte v nastavení aplikace nastavte **klíč** pole a hodnotu v **hodnotu** pole.
8. Vyberte **Uložit**.

Změna většinu nastavení aplikace vyžaduje restartování služby.

### <a name="howto-use-sqlazure"></a>Úložiště databáze SQL pro použití jako provozních dat.
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Používání Azure SQL Database jako úložiště dat je stejný jako mezi všechny typy aplikací Azure App Service. Pokud jste to ještě neudělali, postupujte podle těchto kroků můžete vytvořit back-end mobilní aplikace:

1. Přihlaste se k [portál Azure].
2. V levé horní části okna vyberte **+ nový** tlačítko > **Web + mobilní** > **mobilní aplikace**a pak zadejte název pro back-end mobilní aplikace.
3. V **skupiny prostředků** zadejte stejný název jako vaše aplikace.
4. Je vybraný výchozí plán služby App Service. Pokud chcete změnit plán služby App Service:

   a. Vyberte **plán služby App Service** > **+ vytvořit nový**. 
   
   b. Zadejte název nového plánu služby App Service a vyberte požadované místo. 
   
   c. Vyberte odpovídající cenovou úroveň pro službu. Vyberte **zobrazit všechny** do zobrazení více ceny možnosti, jako například **volné** a **sdílené**. 
   
   d. Klikněte **vyberte** tlačítko. 
   
   e. Zpět v **plán služby App Service** podokně, vyberte **OK**.
5. Vyberte **Vytvořit**. 

Zřizování mobilních aplikací back-end může trvat několik minut. Po Mobile Apps back end je zřízený, portál ho otevře **nastavení** podokno back-end mobilní aplikace.

Můžete se připojit k Mobile Apps back-end vašeho existující databázi SQL nebo vytvořit novou databázi SQL. V této části vytvoříme databázi SQL.

> [!NOTE]
> Pokud už máte databázi ve stejném umístění jako back-end mobilní aplikace, můžete místo toho vybrat **použít existující databázi** a pak vyberte tuto databázi. Z důvodu vyšší latence nedoporučujeme používání databáze v jiném umístění.
>
>

1. V nové Mobile Apps back-endu, vyberte **nastavení** > **mobilní aplikace** > **Data** > **+ přidat**.
2. V **přidat datové připojení** podokně, vyberte **SQL Database – nakonfigurujte požadovaná nastavení** > **vytvořit novou databázi**. Zadejte název nové databáze v **název** pole.
3. Vyberte **Server**. V **nový server** podokně, zadejte název jedinečné serveru v **název serveru** pole a zadejte vhodný server pro správu přihlašovací jméno a heslo. Ujistěte se, že **povolit službám azure přístup k serveru** je vybrána. Vyberte **OK**.

   ![Vytvoření databáze SQL Azure][6]
4. V **novou databázi** podokně, vyberte **OK**.
5. Zpět v **přidat datové připojení** podokně, vyberte **připojovací řetězec**a zadejte přihlašovací jméno a heslo, které jste zadali při vytváření databáze. Pokud použijete existující databázi, zadejte přihlašovací údaje pro tuto databázi. Vyberte **OK**.
6. Zpět v **přidat datové připojení** podokně, vyberte **OK** k vytvoření databáze.

<!--- END OF ALTERNATE INCLUDE -->

Vytváření databáze může trvat několik minut. Použití **oznámení** oblasti můžete sledovat průběh nasazení. Není průběhu až do databáze se úspěšně nasazena. Po nasazení databáze je pro instanci databáze SQL v nastaveních app back-end mobilní aplikace vytvořit připojovací řetězec. Zobrazí se toto nastavení aplikace v **nastavení** > **nastavení aplikace** > **připojovací řetězce**.

### <a name="howto-tables-auth"></a>Ověřování vyžadovat pro přístup k tabulkám
Pokud chcete použít ověřování aplikace služby s `tables` koncový bod, musíte nakonfigurovat aplikaci služby ověřování v [portál Azure] první. Další informace naleznete v průvodci Konfigurace zprostředkovatele identity, který chcete používat:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování Facebook]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Nakonfigurujte ověřování služby Twitter.]

Každá tabulka měla vlastností, které můžete použít k řízení přístupu k tabulce. Následující příklad ukazuje staticky definovaných tabulku s je vyžadováno ověření.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Vlastnost přístupu může trvat jednu ze tří hodnot:

* *Anonymní* označuje, že klientská aplikace může číst data bez ověřování.
* *ověření* indikuje, že klientská aplikace, musí poslat platný ověřovací token k požadavku.
* *zakázané* označuje, že tato tabulka je aktuálně zakázaná.

Pokud vlastnost přístupu není definován, je povolený přístup bez ověřování.

### <a name="howto-tables-getidentity"></a>Použití ověřování deklarací identity s tabulkami
Můžete nastavit různé deklarace identity, které jsou požadovány při nastavení ověřování. Tyto deklarace nejsou obvykle dostupné prostřednictvím `context.user` objektu. Ale můžete je znovu načíst pomocí `context.user.getIdentity()` metoda. `getIdentity()` Metoda vrátí promise, který se přeloží na objekt. Objekt se ukládá do klíčů metodou ověřování (`facebook`, `google`, `twitter`, `microsoftaccount`, nebo `aad`).

Například pokud jste nastavili ověření účtu Microsoft a požadavek, který deklarace identity e-mailové adresy, můžete přidat e-mailovou adresu na záznam s řadičem následující tabulku:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition.
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request.
    // READ: only return records that belong to the authenticated user.
    table.read(queryContextForEmail);

    // CREATE: add or overwrite the userId based on the authenticated user.
    table.insert(addEmailToContext);

    // UPDATE: only allow updating of records that belong to the authenticated user.
    table.update(queryContextForEmail);

    // DELETE: only allow deletion of records that belong to the authenticated user.
    table.delete(queryContextForEmail);

    module.exports = table;

Pokud chcete zobrazit, jaké deklarace identity jsou k dispozici, zobrazíte pomocí webového prohlížeče `/.auth/me` koncový bod vašeho webu.

### <a name="howto-tables-disabled"></a>Zakázat přístup k určité tabulky operací
Kromě zobrazování v tabulce, vlastnost přístupu slouží k řízení jednotlivých operací. Existují čtyři operace:

* `read` je operace RESTful GET v tabulce.
* `insert` je operace RESTful POST v tabulce.
* `update` je operace RESTful opravy v tabulce.
* `delete` je operace RESTful odstranění na tabulce.

Můžete například chtít zadat jen pro čtení neověřené tabulku:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-only table. Only allow READ operations.
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Upravit dotaz, který se používá s operace s tabulkou
Běžné požadavky pro operace s tabulkou je poskytnout omezeným zobrazením data. Můžete například zadat tabulku, která je označené ID ověřeného uživatele tak, že můžete pouze pro čtení nebo aktualizovat vlastní záznamy. Následující definice tabulky obsahuje tato funkce:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table.
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table.
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved.
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user.
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Operace, které obvykle spuštění dotazu mají vlastnost dotazu, můžete upravit pomocí `where` klauzule. Vlastnost dotaz je [QueryJS] objekt, který se používá k převodu dotaz OData na něco, že data back-end může zpracovat. Jednoduché rovnosti případech (např. předchozí jeden) můžete použít mapu. Můžete také přidat konkrétní klauzule SQL:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Konfigurace obnovitelného odstranění na tabulce
Obnovitelného odstranění neodstraní ve skutečnosti záznamy. Místo toho se označí je jako v databázi odstranit nastavením odstraněný sloupec na hodnotu true. Dočasně odstraněné záznamy Mobile Apps SDK automaticky odebere z výsledků, pokud používá Mobile Client SDK `IncludeDeleted()`. Pokud chcete nakonfigurovat tabulku pro obnovitelného odstranění, nastavte `softDelete` vlastnost v souboru definice tabulky:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Turn on soft delete.
    table.softDelete = true;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Byste měli zavést mechanismus pro odstraňování záznamů: klientskou aplikaci, webovou úlohu, Azure funkce nebo vlastní rozhraní API.

### <a name="howto-tables-seeding"></a>Počáteční hodnoty databázi daty
Při vytváření nové aplikace, můžete chtít počáteční hodnoty tabulku s daty. To provedete v rámci soubor JavaScript definice tabulky následujícím způsobem:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Synchronizace replik indexů dat dochází pouze v případě, že Mobile Apps SDK jste použili k vytvoření tabulky. Pokud v tabulce již existuje v databázi, je do tabulky vložit žádná data. Pokud je zapnutá dynamická schéma, schéma je odvozeno z dosazená data.

Doporučujeme, aby explicitně volání `tables.initialize()` metodu pro vytvoření tabulky při spuštění služby.

### <a name="Swagger"></a>Povolit podporu Swagger
Mobilní aplikace se dodává s integrovanou [Swagger] podporovat. Chcete-li povolit podporu Swagger, nejprve nainstalujte uživatelského rozhraní swagger jako závislost:

    npm install --save swagger-ui

Potom můžete povolit podporu Swagger v konstruktoru mobilní aplikace:

    var mobile = azureMobileApps({ swagger: true });

Budete pravděpodobně pouze chcete povolit podporu Swagger v edicích vývoj. Můžete to provést pomocí `NODE_ENV` nastavení aplikace:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

`swagger` Koncový bod se nachází v http://*yoursite*.azurewebsites.net/swagger. Dostanete uživatelské rozhraní Swagger pomocí `/swagger/ui` koncový bod. Pokud budete chtít vyžadovat ověřování napříč celou aplikaci, vytvoří Swagger k chybě. Nejlepších výsledků dosáhnete, zvolit, aby neověřené požadavky v nastavení ověřování/autorizace Azure App Service a poté řídit ověřování pomocí `table.access` vlastnost.

Pokud chcete pouze Swagger podporu pro vývoj místně, můžete přidat také možnost Swagger do souboru azureMobile.js.

## <a name="a-namepushpush-notifications"></a><a name="push">Nabízená oznámení
Mobilní aplikace se integruje s Azure Notification Hubs, tak cílové nabízená oznámení můžete odesílat na miliony zařízení pro všechny hlavní platformy. Pomocí centra oznámení můžete odesílat nabízená oznámení iOS, Android a Windows zařízení. Další informace o všech, že lze provádět pomocí Notification Hubs najdete v tématu [Notification Hubs přehled](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Odesílání nabízených oznámení
Následující kód ukazuje způsob použití `push` objekt, který chcete poslat registrovaná zařízení iOS vysílání nabízených oznámení:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured.
    if (context.push) {
        // Send a push notification by using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Vytvořením šablony nabízené registrace z klienta, můžete místo toho odeslat zprávu nabízené šablony do zařízení na všech podporovaných platformách. Následující kód ukazuje, jak odeslat oznámení šablony:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured.
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Odesílání nabízených oznámení do ověřeného uživatele pomocí značek
Pokud ověřený uživatel zaregistruje pro nabízená oznámení, se automaticky přidá značku ID uživatele pro registraci. Pomocí této značky možné posílat nabízená oznámení pro všechna zařízení zaregistrovat podle konkrétního uživatele. Následující kód načte identifikátor SID uživatele, kteří odeslala žádost a odešle šablony nabízených oznámení do každé registrace zařízení pro tohoto uživatele:

    // Only do the push if configured.
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Pokud jste registrace nabízených oznámení z ověřený klient, zajistěte, aby že před dalším pokusem registrace dokončením ověřování.

## <a name="CustomAPI"></a> Vlastní rozhraní API
### <a name="howto-customapi-basic"></a>Definovat vlastní rozhraní API
Kromě rozhraní API katalogu Data přístup prostřednictvím `/tables` koncový bod, mobilní aplikace můžete zadat vlastní pokrytí rozhraní API. Vlastní rozhraní API jsou definovány podobným způsobem jako definice tabulek a stejná přístup zařízení, včetně ověřování.

Pokud chcete použít ověřování aplikace služby s vlastní rozhraní API, musíte nakonfigurovat aplikaci služby ověřování v [portál Azure] první. Další informace naleznete v průvodci Konfigurace zprostředkovatele identity, který chcete používat:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování Facebook]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Nakonfigurujte ověřování služby Twitter.]

Vlastní rozhraní API jsou definovány stejným způsobem jako rozhraní API tabulky:

1. Vytvoření `api` adresáře.
2. Vytvořte soubor JavaScript definice rozhraní API v `api` adresáře.
3. Použít metodu import pro import `api` adresáře.

Tady je prototypu definice rozhraní API, které jsou založeny na basic aplikace vzorku, který jsme použili dříve:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Podívejme příklad rozhraní API, které vrátí datum server pomocí `Date.now()` metoda. Tady je soubor api/date.js:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Každý parametr je jedním z standardní příkazy RESTful: GET, POST, PATCH nebo odstranit. Metoda je standard [ExpressJS middleware] funkce, která odesílá požadované výstup.

### <a name="howto-customapi-auth"></a>Ověřování vyžadovat pro přístup k vlastní rozhraní API
Mobile Apps SDK implementuje ověřování stejným způsobem pro oba `tables` koncový bod a vlastní rozhraní API. Chcete-li přidat ověřování do rozhraní API vyvinuté v předchozí části, přidejte `access` vlastnost:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Můžete také zadat ověřování na konkrétní operace:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Stejný token, který se používá pro `tables` koncový bod se musí použít pro vlastní rozhraní API, které vyžadují ověřování.

### <a name="howto-customapi-auth"></a>Zpracování nahrávání velkých souborů
Mobile Apps SDK používá [textu analyzátor middleware](https://github.com/expressjs/body-parser) přijmout a dekódování obsah textu v vaše žádost. Text analyzátor, který má přijmout nahrávání souborů větší lze předem nakonfigurovat:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling.
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);

Soubor je před samotným přenosem kódování base-64. Toto kódování zvětšuje velikost skutečné nahrávání (a velikost, které musíte vzít v úvahu pro).

### <a name="howto-customapi-sql"></a>Spuštění vlastní příkazů SQL
Mobile Apps SDK umožňuje přístup k celé kontextu prostřednictvím objektu žádosti. Snadno můžete spustit parametrizované příkazy SQL k poskytovateli definované dat:

    var api = {
        get: function (request, response, next) {
            // Check for parameters. If not there, pass on to a later API call.
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query. Anything that the mssql
            // driver can handle is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query. The context for Mobile Apps is available through
            // request.azureMobile. The data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Ladění, snadno tabulek a snadno rozhraní API
### <a name="howto-diagnostic-logs"></a>Ladění, Diagnostika a řešení potíží s Mobile Apps
Azure App Service poskytuje několik ladění a řešení potíží s techniky pro aplikace Node.js.
Začněte v Node.js Mobile Apps back-end vašeho řešení potíží, najdete v následujících článcích:

* [Monitorování služby Azure App Service]
* [Povolit protokolování diagnostiky v Azure App Service]
* [Řešení potíží s Azure App Service v sadě Visual Studio]

Node.js aplikací mít přístup k širokou škálu protokolů diagnostiky nástroje. Interně používá Mobile Apps Node.js SDK [Winstona] pro protokolování diagnostiky. Protokolování je automaticky povolené, pokud povolíte ladění režimu nebo nastaví `MS_DebugMode` nastavení aplikace nastavte na hodnotu true v [portál Azure]. Vygenerovaný protokolů se objeví v diagnostických protokolů v [portál Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Práce s tabulkami snadno na portálu Azure
Můžete snadno tabulky k vytváření a práci s tabulkami přímo na portálu. Datové sady můžete uložit do snadno tabulky ve formátu CSV. Všimněte si, že nemůžete použít názvy vlastností (v datovou sadu sdíleného svazku clusteru), které je v konfliktu s názvy vlastností systému back-end mobilní aplikace. Názvy vlastností systému jsou:
* CreatedAt
* updatedAt
* odstraněna
* verze

Operace s tabulkou ani můžete upravit pomocí editoru služby aplikace. Když vyberete **snadno tabulky** v nastavení back-end serveru, můžete přidávat, upravovat nebo odstranění tabulky. Můžete také zobrazit data v tabulce.

![Práce s tabulkami snadno](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Na panelu příkazů pro tabulku k dispozici jsou následující příkazy:

* **Změna oprávnění**: změnit oprávnění pro čtení, vložit, aktualizovat a odstranit operací v tabulce.
 Možnosti jsou k povolení anonymního přístupu, vyžadují ověřování, nebo zakázat veškerý přístup k operaci.
* **Upravte skript**: otevření souboru skriptu pro tabulku v aplikaci služby editoru.
* **Správa schématu**: Přidání nebo odstranění sloupce nebo změně indexu tabulky.
* **Odstranit tabulku**: zkrátit existující tabulky odstraněním všechny řádky dat, ale ponechat schématu beze změny.
* **Odstranit řádky**: odstranění jednotlivých řádků data.
* **Protokoly streamování zobrazení**: připojit ke službě streamování protokolu pro váš web.

### <a name="work-easy-apis"></a>Práci s rozhraními API snadno na portálu Azure
Můžete snadno rozhraní API pro vytváření a práci s vlastní rozhraní API přímo na portálu. Rozhraní API skripty můžete upravit pomocí editoru služby aplikace.

Když vyberete **rozhraní API pro snadný** v nastavení back-end serveru, můžete přidávat, upravovat nebo odstranit vlastní koncový bod rozhraní API.

![Práci s rozhraními API snadno](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Na portálu můžete změnit oprávnění k přístupu pro akce HTTP, upravte soubor skriptu rozhraní API v App Service editoru nebo zobrazení protokolů streamování.

### <a name="online-editor"></a>Úpravy kódu v editoru služby aplikace
Pomocí portálu Azure, můžete upravit soubory skriptu back-end Node.js v aplikaci služby editoru aniž byste museli stáhnout projektu do místního počítače. Postup úpravy souborů skriptů v editoru online:

1. V podokně pro Mobile Apps back-end, vyberte **všechna nastavení** > buď **snadno tabulky** nebo **rozhraní API pro snadný**. Vyberte tabulku nebo rozhraní API a pak vyberte **upravte skript**. Soubor skriptu se otevře v editoru služby aplikace.

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. Provedené změny do souboru kódu v editoru online. Změny se při psaní automaticky uloží.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Rychlý start Android klienta]: app-service-mobile-android-get-started.md
[Rychlé spuštění klienta Apache Cordova]: app-service-mobile-cordova-get-started.md
[iOS klienta rychlý start]: app-service-mobile-ios-get-started.md
[Rychlý start Xamarin.iOS klienta]: app-service-mobile-xamarin-ios-get-started.md
[Rychlé spuštění klienta Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Rychlý start Xamarin.Forms klienta]: app-service-mobile-xamarin-forms-get-started.md
[Rychlé spuštění klienta Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[synchronizaci dat offline]: app-service-mobile-offline-data-sync.md
[Konfigurace ověřování Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurace ověřování Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurace ověřování Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Konfigurace ověřování společnosti Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Nakonfigurujte ověřování služby Twitter.]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Průvodce nasazením služby Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Monitorování služby Azure App Service]: ../app-service/web-sites-monitor.md
[Povolit protokolování diagnostiky v Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Řešení potíží s Azure App Service v sadě Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[zadejte verzi uzlu]: ../nodejs-specify-node-version-azure-apps.md
[použijte moduly uzlu]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[portál Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp ukázce na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo ukázce na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[ukázky adresáře na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 pro sadu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js balíček]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: http://expressjs.com/guide/using-middleware.html
[Winstona]: https://github.com/winstonjs/winston

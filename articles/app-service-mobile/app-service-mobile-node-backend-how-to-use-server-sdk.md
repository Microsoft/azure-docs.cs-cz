---
title: Jak pracovat s Node.js SDK serveru back-end pro Mobile Apps | Dokumentace Microsoftu
description: Zjistěte, jak pracovat se sadou SDK serveru back-end Node.js pro Azure App Service Mobile Apps.
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
ms.openlocfilehash: cbedb17bb7563620d0d9db81333d9a79301b4ee0
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817504"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Jak používat Mobile Apps Node.js SDK

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Tento článek obsahuje podrobné informace a příklady, které ukazují, jak pracovat s Node.js back-endu funkce Mobile Apps služby Azure App Service.

## <a name="Introduction"></a>Úvod

Mobile Apps poskytuje možnosti přidávat data optimalizovaných pro mobilní zařízení přístup k webovým rozhraním API pro webové aplikace. Sady Mobile Apps SDK je k dispozici pro webové aplikace ASP.NET a Node.js. Sada SDK poskytuje následující operace:

* Operace tabulek (čtení, vkládat, aktualizovat a odstraňovat) pro přístup k datům
* Vlastní rozhraní API operace

Operace zajištění ověřování ve všech zprostředkovatelů identity, které umožňuje službě Azure App Service. Tito poskytovatelé zahrnují zprostředkovatele sociální identity jako je Facebook, Twitter, Google a Microsoft, jakož i Azure Active Directory pro podnikové identitě.

Můžete najít ukázky pro každý případ použití v [adresáře ukázky na Githubu].

## <a name="supported-platforms"></a>Podporované platformy

Sady Mobile Apps Node.js SDK podporuje aktuální verzi LTS uzlu a novější. V současné době je nejnovější verze LTS v4.5.0 uzlu. Jiné verze uzlu je možné, ale nejsou podporovány.

Sady Mobile Apps Node.js SDK podporuje dvě databáze ovladače: 

* Ovladačů mssql uzel podporuje Azure SQL Database a místní instance systému SQL Server.  
* Ovladač sqlite3 podporuje pouze do jedné instance databáze SQLite.

### <a name="howto-cmdline-basicapp"></a>Vytvoření základní Node.js back-end pomocí příkazového řádku

Každý Mobile Apps Node.js back-endu spustí jako ExpressJS aplikace. ExpressJS je nejoblíbenější webové služby rozhraní k dispozici pro Node.js. Můžete vytvořit základní [Express] aplikace následujícím způsobem:

1. V příkazu nebo v okně Powershellu vytvořte adresář pro projekt:

        mkdir basicapp

1. Spustit `npm init` inicializace struktury balíčku:

        cd basicapp
        npm init

   `npm init` Příkaz zeptá sadu otázek k inicializaci projektu. Viz příklad výstupu:

   ![Výstup init npm][0]

1. Nainstalujte `express` a `azure-mobile-apps` knihovny z úložiště npm:

        npm install --save express azure-mobile-apps

1. Vytvoření souboru app.js k implementaci základní mobilní server:

    ```javascript
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
    ```

Tato aplikace vytváří Web API optimalizovaných pro mobilní zařízení pomocí jednoho koncového bodu (`/tables/TodoItem`), který obsahuje neověřený přístup k základnímu úložišti dat. SQL s použitím dynamické schéma. Je vhodný pro následující knihovny klienta, šablony rychlý start:

* [Rychlý start s androidem klienta]
* [Rychlý start klientů Apache Cordova]
* [Rychlý start klientů s Iosem]
* [Rychlý start klientů Windows Store]
* [Rychlé spuštění klienta Xamarin.iOS]
* [Rychlý start klientů Xamarin.Android]
* [Rychlé spuštění klienta Xamarin.Forms]

Můžete najít kód pro tuto základní aplikaci v [basicapp ukázka na Githubu].

### <a name="howto-vs2015-basicapp"></a>Vytvoření back-end Node.js pomocí sady Visual Studio 2015

Visual Studio 2015 vyžaduje rozšíření pro vývoj aplikací Node.js v prostředí IDE. Pokud chcete začít, nainstalujte [Node.js Tools 1.1 pro sadu Visual Studio]. Po dokončení instalace, vytvořte aplikaci 4.x Express:

1. Otevřít **nový projekt** dialogové okno (z **souboru** > **nový** > **projektu**).
1. Rozbalte **šablony** > **JavaScript** > **Node.js**.
1. Vyberte **základní Azure aplikace Node.js Express 4**.
1. Zadejte název projektu. Vyberte **OK**.

   ![Nový projekt sady Visual Studio 2015][1]
1. Klikněte pravým tlačítkem myši **npm** uzel a vyberte možnost **nainstalovat nové balíčky npm**.
1. Vám může být nutné aktualizovat katalog npm, jakmile vytvoříte svou první aplikaci Node.js. Vyberte **aktualizovat** v případě potřeby.
1. Zadejte **azure – mobile apps** do vyhledávacího pole. Vyberte **azure mobile apps 2.0.0** balíček a pak vyberte **instalovat balíček**.

   ![Nainstalovat nové balíčky npm][2]
1. Vyberte **Zavřít**.
1. Otevření souboru app.js s přidanou podporou pro Mobile Apps SDK. Na řádku 6 at dolní části knihovny `require` příkazy, přidejte následující kód:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    27 at přibližně řádku za sebou `app.use` příkazy, přidejte následující kód:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Uložte soubor.

1. Buď místní spuštění aplikace (rozhraní API je doručen http://localhost:3000) nebo publikovat do Azure.

### <a name="create-node-backend-portal"></a>Vytvoření back-end Node.js s využitím webu Azure portal

Můžete vytvářet mobilní aplikace back-end přímo v [Azure Portal]. Můžete provést následující kroky nebo vytvořit pomocí následujícího klienta a serveru společně [vytvoření mobilní aplikace](app-service-mobile-ios-get-started.md) kurzu. Tento kurz obsahuje zjednodušenou verzi těchto pokynů a je vhodné pro testování konceptů projektů.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpátky **Začínáme** podokně v části **vytvořit rozhraní API tabulky**, zvolte **Node.js** jako jazyk back-end.
Zaškrtněte políčko pro **beru na vědomí, že tato akce přepíše veškerý obsah webu**a pak vyberte **vytvořit tabulku TodoItem**.

### <a name="download-quickstart"></a>Stáhněte si projekt kódu rychlý start back-end Node.js pomocí Gitu

Když vytvoříte back-end Node.js Mobile Apps s použitím portálu společnosti **úvodní** podokně projekt Node.js je pro vás vytvořili a nasadili na váš web. Na portálu můžete přidat tabulky a rozhraní API a upravit soubory s kódem pro back-end Node.js. Chcete-li stáhnout back-end projektu můžete přidat nebo upravit tabulky a rozhraní API a pak znovu publikujte projekt můžete také použít různé nástroje nasazení. Další informace najdete v tématu [Průvodce nasazením služby Azure App Service].

Následující postup používá úložiště Git pro stažení projektu kódu tohoto rychlého startu:

1. Nainstalujte Git, pokud jste tak již neučinili. Kroky potřebné k instalaci Gitu se liší mezi operačními systémy. Distribuce specifické pro operační systém a instalační pokyny najdete v tématu [instalace Git](http://git-scm.com/book/en/Getting-Started-Installing-Git).
1. Zobrazit [Příprava vašeho úložiště](../app-service/app-service-deploy-local-git.md#prepare-your-repository) povolit úložiště Git pro váš back-end serveru. Poznamenejte si uživatelské jméno pro nasazení a heslo.
1. V podokně pro back-endu Mobile Apps, poznamenejte si, **adresa URL klonu Git** nastavení.
1. Spustit `git clone` příkaz s použitím adresy URL pro klon Git. Zadejte svoje heslo, pokud jsou povinné, jako v následujícím příkladu:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

1. Přejděte do místního adresáře (`/todolist` v předchozím příkladu) a Všimněte si, že byly staženy soubory projektu. Vyhledejte soubor todoitem.json v `/tables` adresáře. Tento soubor definuje oprávnění v tabulce. Také najdete soubor todoitem.js ve stejném adresáři. Definuje skripty operace CRUD pro tabulku.
1. Když provedete změny do souborů projektu, spusťte následující příkazy pro přidání, potvrzení a potom odešlete změny do lokality:

        $ git commit -m "updated the table script"
        $ git push origin master

   Když přidáte nové soubory do projektu, musíte nejprve spustit `git add .` příkazu.

Pokaždé, když se nová sada potvrzení změn se vloží do lokality, je znovu publikovat webu.

### <a name="howto-publish-to-azure"></a>Publikování back-endu Node.js do Azure

Microsoft Azure poskytuje mnoho mechanismů pro publikování vaší aplikace Node.js mobilní back-endu do služby Azure. Tyto mechanismy patří nástroje nasazení, které jsou integrované do sady Visual Studio, nástroje příkazového řádku a průběžné nasazování možnosti založené na správy zdrojového kódu. Další informace najdete v tématu [Průvodce nasazením služby Azure App Service].

Azure App Service nabízí konkrétní Rady, jak aplikace Node.js, které byste měli zkontrolovat před publikováním back-endu:

* Jak [zadejte verzi uzlu]
* Jak [použijte moduly Node]

### <a name="howto-enable-homepage"></a>Povolit domovské stránce pro vaši aplikaci

Mnoho aplikací jsou kombinací webových a mobilních aplikací. Rozhraní ExpressJS můžete kombinovat dva omezujících vlastností. V některých případech však můžete chtít pouze implementovat mobilního rozhraní. Je užitečný k zadání domovskou stránku a ujistěte se, že je služba app a spuštěna. Můžete zadat vlastní domovskou stránku nebo povolit dočasné domovskou stránku. Povolit dočasné domovskou stránku, použijte následující kód k vytvoření instance Mobile Apps:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Pokud chcete pouze tuto možnost k dispozici při vývoji místně, můžete přidat toto nastavení do souboru azureMobile.js.

## <a name="TableOperations"></a>Operace s tabulkou

Azure – mobile apps Node.js Server SDK poskytuje mechanismus ke zveřejnění tabulek dat uložených ve službě Azure SQL Database jako webové rozhraní API. Poskytuje pět operace:

| Operace | Popis |
| --- | --- |
| GET /tables/*tablename* |Získáte všechny záznamy v tabulce. |
| GET /tables/*tablename*/:id |Získání konkrétní záznam v tabulce. |
| PŘÍSPĚVEK /tables/*tablename* |Vytvořte záznam v tabulce. |
| Oprava /tables/*tablename*/:id |Aktualizujte záznam v tabulce. |
| Odstranit /tables/*tablename*/:id |Odstraňte záznam v tabulce. |

Toto webové rozhraní API podporuje [OData] a rozšiřuje schéma tabulky pro podporu [synchronizaci offline dat].

### <a name="howto-dynamicschema"></a>Definování tabulek pomocí dynamické schéma

Před použitím tabulky, je nutné definovat. Tabulky můžete definovat pomocí statické schéma (kdy definujete sloupců ve schématu) nebo dynamicky (Pokud je sada SDK Určuje schéma založené na příchozí požadavky). Kromě toho můžete řídit specifických aspektů webového rozhraní API přidáním kódu jazyka JavaScript na definici.

Jako osvědčený postup byste měli definovat každou tabulku v souboru jazyka JavaScript v `tables` adresáře a pak `tables.import()` metoda importovat tabulky. Rozšíření ukázky základní aplikaci, by úprava souboru app.js:

```javascript
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
```

Definujte tabulku ve. / tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Dynamické schéma tabulky používají ve výchozím nastavení. Chcete-li dynamické schéma vypnout globálně, nastavte `MS_DynamicSchema` nastavení aplikace nastavte na hodnotu false na webu Azure Portal.

Kompletní příklad v lze najít [todo ukázka na Githubu].

### <a name="howto-staticschema"></a>Definování tabulek pomocí statické schéma

Můžete explicitně definovat sloupce, které chcete zveřejnit prostřednictvím webového rozhraní API. Aplikace azure mobile SDK pro Node.js automaticky přidá všechny sloupce navíc vyžadované pro synchronizaci offline dat v seznamu, který zadáte. Například klientské aplikace rychlý start vyžadovat tabulku se dvěma sloupci: `text` (řetězec) a `complete` (logická hodnota).  
V tabulce lze definovat v souboru jazyka JavaScript definice tabulky (umístěný ve `tables` adresáře) následujícím způsobem:

```javascript
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
```

Pokud definujete tabulky staticky, musíte také zavolat `tables.initialize()` metodu pro vytvoření schématu databáze při spuštění. `tables.initialize()` Metoda vrátí hodnotu [promise] tak, aby webová služba neobsluhuje požadavky před inicializací databáze.

### <a name="howto-sqlexpress-setup"></a>Použití SQL Server Express jako úložiště dat vývoj v místním počítači

Sady Mobile Apps Node.js SDK poskytuje tři možnosti pro poskytování dat mimo pole:

* Použití **paměti** ovladač poskytují příklad dočasné úložiště.
* Použití **mssql** ovladače k poskytnutí úložiště dat serveru SQL Server Express pro vývoj.
* Použití **mssql** ovladače k poskytování úložiště dat Azure SQL Database pro produkční prostředí.

Sady Mobile Apps Node.js SDK používá [balíček Node.js mssql] vytvořit a používat připojení k serveru SQL Server Express a SQL Database. Tento balíček vyžaduje, abyste povolili připojení TCP na instanci systému SQL Server Express.

> [!TIP]
> Ovladač paměti neposkytuje kompletní sadu zařízení pro účely testování. Pokud chcete otestovat back-endu místně, doporučujeme používat úložiště dat serveru SQL Server Express a ovladačů mssql.

1. Stáhněte a nainstalujte [Microsoft SQL Server 2014 Express]. Ujistěte se, že nainstalujete SQL Server 2014 Express Edition nástroje. Pokud požadujete explicitně 64-bit support, 32bitová verze využívá méně paměti při spuštění.
1. Spusťte Správce konfigurace systému SQL Server 2014:

   a. Rozbalte **síťová konfigurace systému SQL Server** uzel ve stromu nabídky.

   b. Vyberte **protokoly pro SQLEXPRESS**.

   c. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **povolit**. Vyberte **OK** v místním dialogovém.

   d. Klikněte pravým tlačítkem na **TCP/IP** a vyberte **vlastnosti**.

   e. Vyberte **IP adresy** kartu.

   f. Najít **IPAll** uzlu. V **TCP Port** zadejte **1433**.

      ![Konfigurace SQL Server Express pro protokol TCP/IP][3]

   g. Vyberte **OK**. Vyberte **OK** v místním dialogovém.

   h. Vyberte **služby SQL Server** v nabídce stromu.

   i. Klikněte pravým tlačítkem na **SQL serveru (SQLEXPRESS)** a vyberte **restartovat**.

   j. Zavřete Správce konfigurace systému SQL Server 2014.

1. Spusťte SQL Server 2014 Management Studio a připojte se k místní instanci systému SQL Server Express:

   1. Vaše instance v Průzkumníku objektů klikněte pravým tlačítkem a vyberte **vlastnosti**.
   1. Vyberte **zabezpečení** stránky.
   1. Ujistěte se, že **systému SQL Server a Windows Authentication mode** zaškrtnuto.
   1. Vyberte **OK**.

      ![Konfigurace ověřování systému SQL Server Express][4]
   1. Rozbalte **zabezpečení** > **přihlášení** v Průzkumníku objektů.
   1. Klikněte pravým tlačítkem na **přihlášení** a vyberte **nového přihlašovacího jména**.
   1. Zadejte přihlašovací jméno. Vyberte **Ověřování SQL Serveru**. Zadejte heslo a pak zadejte stejné heslo v **potvrzení hesla**. Heslo musí splňovat požadavky na složitost Windows.
   1. Vyberte **OK**.

      ![Přidání nového uživatele pro SQL Server Express][5]
   1. Klikněte pravým tlačítkem na nové přihlašovací údaje a vyberte **vlastnosti**.
   1. Vyberte **role serveru** stránky.
   1. Zaškrtněte políčko pro **dbcreator** role serveru.
   1. Vyberte **OK**.
   1. Zavřete SQL Server 2015 Management Studio.

Nezapomeňte si poznamenejte uživatelské jméno a heslo, které jste vybrali. Můžete potřebovat k přiřazení dalších rolí serveru nebo oprávnění, v závislosti na požadavcích vaší databáze.

Čtení aplikací Node.js `SQLCONNSTR_MS_TableConnectionString` proměnné prostředí pro připojovací řetězec pro tuto databázi. Tuto proměnnou lze nastavit ve vašem prostředí. Například můžete použít PowerShell k nastavení této proměnné prostředí:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Přístup k databázi přes připojení TCP/IP. Zadejte uživatelské jméno a heslo pro připojení.

### <a name="howto-config-localdev"></a>Konfigurace projektu pro místní vývoj

Mobile Apps přečte soubor JavaScriptu s názvem *azureMobile.js* z místního systému souborů. Tento soubor nepoužívejte konfigurace sady SDK mobilních aplikací v produkčním prostředí. Místo toho použijte **nastavení aplikace** v [Azure Portal].

Soubor azureMobile.js třeba exportovat objekt konfigurace. Většina běžných nastavení jsou:

* Nastavení databáze
* Nastavení protokolování diagnostiky
* Alternativní nastavení CORS

V tomto příkladu **azureMobile.js** souboru implementuje předchozí nastavení databáze:

```javascript
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
```

Doporučujeme, abyste přidali **azureMobile.js** do vaší **.gitignore** souboru (nebo jiné ignorovat souboru správy zdrojového kódu) aby hesla ukládat v cloudu. Vždy konfigurujte nastavení produkčního prostředí v **nastavení aplikace** v rámci [Azure Portal].

### <a name="howto-appsettings"></a>Konfigurace nastavení aplikace pro mobilní aplikace

Většinu nastavení v souboru azureMobile.js mají v nastavení odpovídající aplikace [Azure Portal]. Následující seznam použijte ke konfiguraci vaší aplikace v **nastavení aplikace**:

| Nastavení aplikace | nastavení azureMobile.js | Popis | Platné hodnoty |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |jméno |Název aplikace |řetězec |
| **MS_MobileLoggingLevel** |Logging.level |Minimální úroveň protokolování zpráv do protokolu |Chyba, upozornění, informace, verbose, debug, i |
| **MS_DebugMode** |ladit |Povolí nebo zakáže režim ladění |Hodnota TRUE, false |
| **MS_TableSchema** |data.schema |Výchozí název schématu pro tabulky SQL |String (výchozí: vlastník databáze) |
| **MS_DynamicSchema** |data.dynamicSchema |Povolí nebo zakáže režim ladění |Hodnota TRUE, false |
| **MS_DisableVersionHeader** |verze (nastavena na nedefinované) |Zakáže hlavičky X-záhlaví ZUMO-Server-Version |Hodnota TRUE, false |
| **MS_SkipVersionCheck** |skipversioncheck |Zakáže Kontrola verze rozhraní API klienta |Hodnota TRUE, false |

Chcete-li nastavit nastavení aplikace:

1. Přihlaste se k [Azure Portal].
1. Vyberte **všechny prostředky** nebo **App Services**a potom vyberte název vaší mobilní aplikace.
1. **Nastavení** ve výchozím nastavení se otevře podokno. Pokud tomu tak není, vyberte **nastavení**.
1. Na **Obecné** nabídce vyberte možnost **nastavení aplikace**.
1. Přejděte **nastavení aplikace** oddílu.
1. Pokud vaše aplikace, nastavení už existuje, vyberte hodnotu nastavení aplikace nastavte hodnotu upravit.
   Pokud vaše aplikace nastavení neexistuje, zadejte v nastavení aplikace **klíč** pole a hodnota v **hodnotu** pole.
1. Vyberte **Uložit**.

Změna nastavení většina aplikací vyžaduje restartování služby.

### <a name="howto-use-sqlazure"></a>Použití SQL Database jako produkční data ukládat

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Pomocí Azure SQL Database jako úložiště dat je identické napříč všemi typy aplikací Azure App Service. Pokud jste to ještě neudělali, postupujte podle těchto pokynů vytvořte back-end Mobile Apps:

1. Přihlaste se k [Azure Portal].
1. V levém horním rohu okna, vyberte **+ nová** tlačítko > **Web + mobilní zařízení** > **mobilní aplikace**a potom zadejte název pro back-endu Mobile Apps.
1. V **skupiny prostředků** zadejte stejný název jako vaše aplikace.
1. Je vybraný výchozí plán služby App Service. Pokud chcete změnit plán služby App Service:

   a. Vyberte **plán služby App Service** > **+ vytvořit novou**.

   b. Zadejte název nového plánu služby App Service a vyberte příslušné místo.

   c. Vyberte odpovídající cenové úrovně služby. Vyberte **zobrazit všechny** do zobrazení další cenové možnosti, jako například **Free** a **Shared**.

   d. Klikněte na tlačítko **vyberte** tlačítko.

   e. Zpátky **plán služby App Service** vyberte **OK**.
1. Vyberte **Vytvořit**.

Zřízení Mobile Apps back-endu může trvat několik minut. Po Mobile Apps zpět end zřizován, portál ho otevře **nastavení** podokno pro back-endu Mobile Apps.

Můžete nastavit připojení k back-endu Mobile Apps existující databázi SQL nebo vytvořte novou databázi SQL. V této části se nám vytvořit databázi SQL.

> [!NOTE]
> Pokud už máte databázi ve stejném umístění jako back-endu Mobile Apps, můžete místo toho vybrat **použít stávající databázi** a potom tuto databázi vybrat. Z důvodu vyšší latencí nedoporučujeme použití databáze do jiného umístění.

1. V nové mobilní aplikace back-endu, vyberte **nastavení** > **mobilní aplikace** > **Data** > **+ přidat**.
1. V **přidat datové připojení** vyberte **SQL Database – konfigurovat požadované nastavení** > **vytvořit novou databázi**. Zadejte název nové databáze v **název** pole.
1. Vyberte **Server**. V **nový server** podokně zadejte jedinečný název serveru v **název serveru** pole a zadejte vhodný server pro přihlašovací jméno správce a heslo. Ujistěte se, že **povolit službám azure přístup k serveru** zaškrtnuto. Vyberte **OK**.

   ![Vytvoření databáze SQL Azure][6]
1. V **novou databázi** vyberte **OK**.
1. Zpátky **přidat datové připojení** vyberte **připojovací řetězec**a zadejte přihlašovací jméno a heslo, které jste zadali při vytváření databáze. Pokud používáte existující databázi, zadejte přihlašovací údaje pro tuto databázi. Vyberte **OK**.
1. Zpátky **přidat datové připojení** podokně znovu, vyberte **OK** k vytvoření databáze.

<!--- END OF ALTERNATE INCLUDE -->

Vytvoření databáze může trvat několik minut. Použití **oznámení** oblast, kterou chcete monitorovat průběh nasazení. Nepostupuje až do databáze se úspěšně zavedena. Po nasazení databáze se vytvoří připojovací řetězec pro instanci služby SQL Database v nastavení aplikace back-end Mobile Apps. Zobrazí se toto nastavení aplikace v **nastavení** > **nastavení aplikace** > **připojovací řetězce**.

### <a name="howto-tables-auth"></a>Ověřování vyžadovat pro přístup do tabulek

Pokud chcete použít ověřování pomocí služby App Service s `tables` koncový bod, musíte nakonfigurovat ověřování pomocí služby App Service v [Azure Portal] první. Další informace najdete v tématu Průvodce konfigurací pro zprostředkovatele identity, které máte v úmyslu použít:

* [Konfigurovat ověřování Azure Active Directory]
* [Konfigurace ověřování sítě Facebook]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování Twitteru]

Každá tabulka má vlastnost přístup, můžete použít k řízení přístupu k tabulce. Následující příklad ukazuje staticky definovaných tabulek s je vyžadováno ověření.

```javascript
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
```

Vlastnost přístupu může trvat jednu ze tří hodnot:

* *Anonymní* označuje, že klientská aplikace může číst data bez ověřování.
* *ověření* označuje, že klientská aplikace musí posílat platný ověřovací token v požadavku.
* *zakázané* označuje, že tato tabulka je momentálně zakázané.

Pokud přístup vlastnosti není definován, je povolený neověřený přístup.

### <a name="howto-tables-getidentity"></a>Použití ověřování deklarací identity s tabulkami
Můžete nastavit různé deklarace identity, které jsou požadovány při ověřování je nastavena. Tyto deklarace nejsou obvykle dostupné `context.user` objektu. Nicméně je načíst s použitím `context.user.getIdentity()` metody. `getIdentity()` Metoda vrátí příslib, který se přeloží na objekt. Metoda ověřování s klíči objektu (`facebook`, `google`, `twitter`, `microsoftaccount`, nebo `aad`).

Například pokud nastavíte ověřování účtu Microsoft a žádosti, které deklarace identity e-mailové adresy, můžete přidat e-mailovou adresu do záznamu s tímto řadičem tabulky:

```javascript
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
```

Chcete-li zjistit, jaké deklarace identity jsou k dispozici, zobrazíte pomocí webového prohlížeče `/.auth/me` koncový bod vašeho webu.

### <a name="howto-tables-disabled"></a>Zakázat přístup k operacím konkrétní tabulky

Kromě uvedených v tabulce, vlastnost přístupu slouží k řízení jednotlivých operací. Existují čtyři operace:

* `read` operace RESTful GET v tabulce je.
* `insert` právě tato operace RESTful POST v tabulce.
* `update` právě tato operace RESTful oprava v tabulce.
* `delete` právě tato operace RESTful odstranění na tabulce.

Například můžete chtít poskytnout neověřené tabulka jen pro čtení:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Upravit dotaz, který se používá s operace s tabulkou

Běžné požadavky pro operace s tabulkou je kvůli omezeným zobrazením data. Můžete například zadat tabulku, která je označena jako ID ověřeného uživatele tak, aby lze pouze číst a aktualizovat své vlastní záznamy. Tuto funkci poskytuje následující definici tabulky:

```javascript
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
```

Operace, které se obvykle spouští dotaz mít zadanou vlastnost dotaz, který můžete upravit pomocí `where` klauzuli. Vlastnost dotazu je [QueryJS] objekt, který se používá k převodu dotaz OData na něco, že data back-endu může zpracovat. Pro jednoduché rovnosti případů (jako je ta předchozí) můžete použít mapu. Můžete také přidat konkrétní klauzulemi SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurace obnovitelné odstranění v tabulce

Obnovitelné odstranění nedojde k odstranění ve skutečnosti záznamy. Místo toho označí je jako odstraněné v rámci databáze tak, že nastavíte odstraněný sloupec na hodnotu true. Sady Mobile Apps SDK automaticky odebere záznamy obnovitelně odstraněný z výsledků, pokud sada SDK klienta mobilní zařízení používá `IncludeDeleted()`. Pokud chcete nakonfigurovat tabulku pro obnovitelné odstranění, nastavte `softDelete` vlastnost v souboru definice tabulky:

```javascript
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
```

Byste měli zavést mechanismus pro odstranění záznamů: klientská aplikace, webové úlohy, funkce Azure nebo vlastní rozhraní API.

### <a name="howto-tables-seeding"></a>Naplnit databázi daty

Při vytváření nové aplikace, můžete chtít počáteční hodnoty tabulku s daty. Provedete to v rámci souboru JavaScript definice tabulky následujícím způsobem:

```javascript
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
```

Předvyplnění dat dochází pouze v případě, že sady Mobile Apps SDK jste použili k vytvoření této tabulky. Pokud v databázi již existuje v tabulce, žádná data se vloží do tabulky. Pokud je zapnutá dynamické schéma, schéma je odvozen z dosazená data.

Doporučujeme vám, že explicitně volat `tables.initialize()` metody k vytvoření této tabulky při spuštění služby.

### <a name="Swagger"></a>Povolit podporu Swagger
Mobilní aplikace obsahuje integrované [Swagger] podporovat. Pokud chcete povolit podporu Swaggeru, nejdřív nainstalujte uživatelského rozhraní swagger jako závislost:

    npm install --save swagger-ui

Pak můžete povolit podporu Swaggeru v konstruktoru Mobile Apps:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Budete pravděpodobně pouze chcete povolit podporu Swaggeru v edicích vývoje. Můžete to provést pomocí `NODE_ENV` nastavení aplikace:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

`swagger` Koncový bod se nachází na http://*yoursite*.azurewebsites.net/swagger. Uživatelské rozhraní Swagger prostřednictvím můžete přistupovat `/swagger/ui` koncového bodu. Pokud budete chtít vyžadovat ověřování napříč celou aplikací, Swagger dojde k chybě. Nejlepších výsledků dosáhnete, zvolte možnost Povolit neověřené žádosti v nastavení ověřování/autorizace Azure App Service a poté řídit ověřování pomocí `table.access` vlastnost.

Pokud chcete pouze Swagger podpory pro vývoj místně, můžete přidat také možnost Swagger do souboru azureMobile.js.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Nabízená oznámení

Mobile Apps se integruje s Azure Notification Hubs, takže můžete odesílat cílená nabízená oznámení do milionů zařízení napříč nejpoužívanějšími platformami. Pomocí Notification Hubs posílat nabízená oznámení pro iOS, Android a Windows zařízení. Další informace o vše, co můžete dělat s Notification Hubs najdete v tématu [Přehled služby Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Odesílání nabízených oznámení

Následující kód ukazuje způsob použití `push` objekt se má poslat pouhé vysílání nabízených oznámení do zařízení s iOS registrované:

```javascript
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
```

Tím, že vytvoříte šablonu registrace nabízených oznámení z klienta, můžete místo toho odesílat nabízená zpráva šablony na zařízení na všech podporovaných platformách. Následující kód ukazuje, jak odeslat Šablona oznámení:

```javascript
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
```

### <a name="push-user"></a>Odesílání nabízených oznámení do ověřeného uživatele pomocí značek
Když ověřený uživatel zaregistruje pro nabízená oznámení, se automaticky přidá značku ID uživatele k registraci. Pomocí této značce je posílat nabízená oznámení pro všechna zařízení registrovaných konkrétního uživatele. Následující kód načte identifikátor SID uživatele, který se zadal žádost a odešle nabízené oznámení šablony k registraci všech zařízení pro tohoto uživatele:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Při registraci pro nabízená oznámení z ověřený klient Ujistěte se, že ověřování je dokončeno, než zkusíte registraci.

## <a name="CustomAPI"></a> Vlastní rozhraní API

### <a name="howto-customapi-basic"></a>Definování vlastního rozhraní API

Kromě rozhraní API služby Data Access prostřednictvím `/tables` koncový bod, Mobile Apps můžete zadat vlastní rozhraní API pokrytí. Vlastní rozhraní API jsou definovány v podobným způsobem jako definice tabulky a stejné můžou k zařízení, včetně ověřování.

Pokud chcete použít ověřování pomocí služby App Service s vlastním rozhraním API, je nutné nakonfigurovat ověřování pomocí služby App Service v [Azure Portal] první. Další informace najdete v tématu Průvodce konfigurací pro zprostředkovatele identity, které máte v úmyslu použít:

* [Konfigurovat ověřování Azure Active Directory]
* [Konfigurace ověřování sítě Facebook]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování Twitteru]

Vlastní rozhraní API jsou definovány stejným způsobem jako rozhraní API tabulky:

1. Vytvoření `api` adresáře.
1. Vytvořte soubor JavaScript definice rozhraní API v `api` adresáře.
1. Použijte metodu import pro import `api` adresáře.

Tady je prototyp definice rozhraní API založené na vzorku základní aplikaci, kterou jsme použili dříve:

```javascript
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
```

Vezměme si příklad rozhraní API, která vrací datum server s použitím `Date.now()` metody. Tady je api/date.js souboru:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Každý parametr je jedním z se standardními RESTful akcemi: získat, POST, PATCH nebo odstranění. Metoda je standard [ExpressJS middlewaru] funkce, která odesílá požadovaný výstup.

### <a name="howto-customapi-auth"></a>Ověřování vyžadovat pro přístup k vlastní rozhraní API

Sady Mobile Apps SDK implementuje ověřování stejně jako u obou `tables` koncový bod a vlastních rozhraní API. Chcete-li přidat ověřování do rozhraní API vyvinuté v předchozí části, přidejte `access` vlastnost:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Můžete také zadat ověřování na určité operace:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Stejný token, který se používá pro `tables` koncového bodu musí být použito pro vlastní rozhraní API, které vyžadují ověřování.

### <a name="howto-customapi-auth"></a>Zpracování nahrávání velkých souborů

Sady Mobile Apps SDK používá [analyzátor textu middleware](https://github.com/expressjs/body-parser) přijmout a dekódovat obsah textu ve své podání. Analyzátor textu potvrďte, že nahrání souborů větší umožňují předem nakonfigurovat:

```javascript
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
```

Soubor je před samotným přenosem kódování base-64. Toto kódování zvětší velikost skutečné nahrávání (a jejich velikost, který musíte vzít v úvahu pro).

### <a name="howto-customapi-sql"></a>Spustit vlastní příkazy SQL

Sady Mobile Apps SDK umožňuje přístup k celé kontextu prostřednictvím objekt žádosti. Snadno můžete spustit parametrizované příkazy SQL na poskytovatele dat definované:

```javascript
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
```

## <a name="Debugging"></a>Ladění, jednoduché tabulky a jednoduchá rozhraní API

### <a name="howto-diagnostic-logs"></a>Ladění, Diagnostika a řešení potíží s Mobile Apps

Azure App Service nabízí několik ladění a techniky řešení potíží pro aplikace Node.js.
Abyste mohli začít s řešením back-endu Node.js Mobile Apps, naleznete v následujících článcích:

* [Monitorování služby Azure App Service]
* [Povolit diagnostické protokolování ve službě Azure App Service]
* [Řešení potíží s Azure App Service v sadě Visual Studio]

Aplikace Node.js mají přístup k široké škále diagnostický protokol nástroje. Interně jsou sady Mobile Apps Node.js SDK používá [Winstona] protokolování diagnostiky. Když povolíte ladění automaticky povoleno protokolování režimu nebo sadu `MS_DebugMode` nastavení aplikace nastavte na hodnotu true [Azure Portal]. Vygenerovaný protokolů se objeví v diagnostických protokolů v [Azure Portal].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Práce s jednoduché tabulky na webu Azure Portal

Jednoduché tabulky můžete použít k vytváření a práci s tabulkami přímo na portálu. Můžete nahrát datové sady do jednoduchých tabulek ve formátu CSV. Všimněte si, že nemůžete použít názvy vlastností (ve vaší datové sadě sdíleného svazku clusteru), které jsou v konfliktu s názvy vlastností systému back-endu Mobile Apps. Názvy vlastností systému jsou:
* createdAt
* updatedAt
* odstraněna
* verze

Operace s tabulkou i můžete upravit pomocí editoru služby App Service. Když vyberete **jednoduché tabulky** v nastavení back-end serveru, můžete přidat, upravit nebo odstranit tabulku. Můžete také zobrazit data v tabulce.

![Práce s jednoduché tabulky](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Následující příkazy jsou k dispozici na panelu příkazů pro tabulku:

* **Změna oprávnění**: oprávnění pro čtení, vložení, aktualizace a odstranění operace v tabulce.
 Možnosti jsou k povolení anonymního přístupu, vyžadují ověřování nebo zakažte veškerý přístup k operaci.
* **Upravit skript**: otevření souboru skriptu k tabulce v App Service Editor.
* **Správa schématu**: Přidání nebo odstranění sloupců nebo změně indexu tabulky.
* **Vymazat tabulku**: existující tabulku zkrátit tak, že odstraníte všechny řádky dat, ale ponechání schéma beze změny.
* **Odstranit řádky**: odstranit jednotlivé řádky dat.
* **Zobrazit protokoly streamování**: připojení ke službě streamování protokolů pro váš web.

### <a name="work-easy-apis"></a>Práce s jednoduchá rozhraní API na webu Azure Portal

Jednoduchá rozhraní API slouží k vytváření a používání vlastních rozhraní API přímo na portálu. Upravit skripty rozhraní API s využitím App Service Editor.

Když vyberete **jednoduchá rozhraní API** v nastavení back-end serveru, můžete přidat, upravit nebo odstranit vlastní koncový bod rozhraní API.

![Práce s jednoduchá rozhraní API](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Na portálu můžete změnit oprávnění k přístupu pro akce HTTP, upravte soubor skriptu rozhraní API v App Service Editor nebo zobrazit protokoly datových proudů.

### <a name="online-editor"></a>Úpravy kódu v App Service Editor

Pomocí webu Azure portal, můžete upravit soubory skriptu back-end Node.js v aplikaci App Service Editor aniž byste museli stáhnout projektu do místního počítače. Chcete-li upravit soubory skriptů v online editoru:

1. V podokně pro Mobile Apps back-endu, vyberte **všechna nastavení** > buď **jednoduché tabulky** nebo **jednoduchá rozhraní API**. Vyberte tabulku nebo rozhraní API a pak vyberte **upravit skript**. Otevře se soubor skriptu v App Service Editor.

   ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. Proveďte požadované změny do souboru kódu v editoru online. Změny se uloží automaticky během psaní.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Rychlý start s androidem klienta]: app-service-mobile-android-get-started.md
[Rychlý start klientů Apache Cordova]: app-service-mobile-cordova-get-started.md
[Rychlý start klientů s Iosem]: app-service-mobile-ios-get-started.md
[Rychlé spuštění klienta Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Rychlý start klientů Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Rychlé spuštění klienta Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Rychlý start klientů Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[synchronizaci offline dat]: app-service-mobile-offline-data-sync.md
[Konfigurovat ověřování Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Konfigurace ověřování sítě Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Konfigurace ověřování Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Konfigurace ověřování společnosti Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Konfigurace ověřování Twitteru]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Průvodce nasazením služby Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Monitorování služby Azure App Service]: ../app-service/web-sites-monitor.md
[Povolit diagnostické protokolování ve službě Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Řešení potíží s Azure App Service v sadě Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[Zadejte verzi uzlu]: ../nodejs-specify-node-version-azure-apps.md
[použijte moduly Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure Portal]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp ukázka na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[TODO ukázka na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[adresáře ukázky na Githubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 pro sadu Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[balíček Node.js MSSQL]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middlewaru]: http://expressjs.com/guide/using-middleware.html
[Winstona]: https://github.com/winstonjs/winston

---
title: Práce se sadou SDK back-end serveru Node.js
description: Zjistěte, jak pracovat s sadou Node.js back-end Server SDK pro mobilní aplikace služby Azure App Service.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249942"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Použití sady SDK node.js pro mobilní aplikace

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Tento článek obsahuje podrobné informace a příklady, které ukazují, jak pracovat s Back-endu Node.js ve funkci Mobilní aplikace služby Azure App Service.

## <a name="introduction"></a><a name="Introduction"></a>Úvod

Mobilní aplikace poskytují možnost přidat webové rozhraní API pro přístup k datům optimalizované pro mobilní zařízení do webové aplikace. Sada SDK pro mobilní aplikace je k dispozici pro webové aplikace ASP.NET a Node.js. Sada SDK poskytuje následující operace:

* Operace tabulky (čtení, vkládání, aktualizace, odstraňování) pro přístup k datům
* Vlastní operace rozhraní API

Obě operace poskytují ověřování napříč všemi poskytovateli identit, které služba Azure App Service povoluje. Mezi tyto poskytovatele patří poskytovatelé sociální identity, jako je Facebook, Twitter, Google a Microsoft, a také Azure Active Directory pro podnikovou identitu.

Ukázky pro každý případ použití najdete v [adresáři ukázek na GitHubu].

## <a name="supported-platforms"></a>Podporované platformy

Sada SDK aplikace Mobile Apps Node.js podporuje aktuální vydání lts uzlu a novější. V současné době nejnovější verze LTS je Node v4.5.0. Jiné verze uzlu může fungovat, ale nejsou podporovány.

Sada SDK aplikace Mobile Apps Node.js podporuje dva ovladače databáze: 

* Ovladač node-mssql podporuje Azure SQL Database a místní instance SQL Serveru.  
* Sqlite3 ovladač podporuje Databáze SQLite pouze na jedné instanci.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Vytvoření základního back-endu Node.js pomocí příkazového řádku

Každý back-end Node.js aplikace pro mobilní aplikace se spustí jako aplikace ExpressJS. ExpressJS je nejoblíbenější rámec webových služeb, který je k dispozici pro soubor Node.js. Základní [expresní] aplikaci můžete vytvořit takto:

1. V okně příkazu nebo Prostředí PowerShell vytvořte adresář pro projekt:

        mkdir basicapp

1. Spustit `npm init` inicializovat strukturu balíčku:

        cd basicapp
        npm init

   Příkaz `npm init` požádá sadu otázek k inicializaci projektu. Podívejte se na ukázkový výstup:

   ![Výstup init npm][0]

1. Nainstalujte `express` `azure-mobile-apps` knihovny a z úložiště npm:

        npm install --save express azure-mobile-apps

1. Vytvořte soubor app.js pro implementaci základního mobilního serveru:

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

Tato aplikace vytvoří webové rozhraní API optimalizované pro`/tables/TodoItem`mobilní zařízení s jedním koncovým bodem ( ), které poskytuje neověřený přístup k podkladovému úložišti dat SQL pomocí dynamického schématu. Je vhodný pro následující rychlé starty klientské knihovny:

* [Rychlý start klienta Android]
* [Apache Cordova klient rychlý start]
* [Rychlý start klienta iOS]
* [Rychlý start klienta Windows Storu]
* [Rychlý start klienta Xamarin.iOS]
* [Xamarin.Android klient a rychlý start]
* [Rychlý start klienta Xamarin.Forms]

Kód pro tuto základní aplikaci najdete v [základní app ukázce na GitHubu].

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Vytvoření back-endu Node.js pomocí Visual Studia 2015

Visual Studio 2015 vyžaduje rozšíření pro vývoj aplikací Node.js v rámci ide. Chcete-li začít, nainstalujte [nástroje Node.js 1.1 pro visual studio]. Po dokončení instalace vytvořte aplikaci Express 4.x:

1. Otevřete dialogové okno **Nový projekt** (ze **souboru** > **nový** > **projekt).**
1. Rozbalte **šablony** > **JavaScript** > **Node.js**.
1. Vyberte **základní aplikaci Azure Node.js Express 4**.
1. Vyplňte název projektu. Vyberte **OK**.

   ![Visual Studio 2015 nový projekt][1]
1. Klepněte pravým tlačítkem myši na uzel **npm** a vyberte **možnost Instalovat nové balíčky npm**.
1. Po vytvoření první aplikace Node.js může být nutné aktualizovat katalog npm. V případě potřeby vyberte **Aktualizovat.**
1. Do vyhledávacího pole zadejte **azure-mobile-apps.** Vyberte balíček **azure-mobile-apps 2.0.0** a pak vyberte **Instalovat balíček**.

   ![Instalace nových balíčků npm][2]
1. Vyberte **Zavřít**.
1. Otevřete soubor app.js a přidejte podporu sady SDK pro mobilní aplikace. Na řádku 6 v dolní `require` části příkazů knihovny přidejte následující kód:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Na řádku 27 za `app.use` ostatní příkazy přidejte následující kód:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Uložte soubor.

1. Buď spusťte aplikaci místně (rozhraní API se obsluhuje ) `http://localhost:3000`nebo publikujte do Azure.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Vytvoření back-endu Node.js pomocí portálu Azure

Back-end mobilních aplikací můžete vytvořit přímo na [webu Azure Portal]. Můžete buď provést následující kroky, nebo vytvořit klienta a server společně podle vytvoření [kurzu vytvořit mobilní aplikace.](app-service-mobile-ios-get-started.md) Kurz obsahuje zjednodušenou verzi těchto pokynů a je nejvhodnější pro projekty proof-of-concept.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Zpět v podokně **Začínáme** v části **Vytvoření rozhraní API tabulky**zvolte **Node.js** jako back-endový jazyk.
Zaškrtněte **políčko Beru na vědomí, že to přepíše veškerý obsah webu**, a pak vyberte **vytvořit tabulku TodoItem**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>Stažení projektu kódu back-endu Node.js pomocí Gitu

Když vytvoříte back-end aplikací Node.js pomocí podokna **Rychlý start** portálu, vytvoří se pro vás projekt Node.js a nasadí se na váš web. Na portálu můžete přidávat tabulky a api a upravovat soubory kódu pro back-end Node.js. Můžete také použít různé nástroje pro nasazení ke stažení back-end projektu, takže můžete přidat nebo upravit tabulky a rozhraní API a potom znovu publikovat projekt. Další informace najdete v [průvodci nasazením služby Azure App Service].

Následující postup používá úložiště Git ke stažení kódu projektu rychlého startu:

1. Nainstalujte Git, pokud jste tak ještě neučinili. Kroky potřebné k instalaci Gitu se v jednotlivých operačních systémech liší. Informace o distribucích specifických pro operační systém a pokyny k instalaci naleznete [v tématu Instalace Gitu](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Viz [Příprava úložiště](../app-service/deploy-local-git.md#prepare-your-repository) pro povolení úložiště Git pro back-endový web. Poznamenejte si uživatelské jméno a heslo nasazení.
3. V podokně back-endu mobilních aplikací si poznamenejte nastavení **adresy URL klonování Gitu.**
4. Spusťte `git clone` příkaz pomocí adresy URL klonování Gitu. V případě potřeby zadejte heslo, jako v následujícím příkladu:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Přejděte do místního adresáře (v`/todolist` předchozím příkladu) a všimněte si, že byly staženy soubory projektu. Vyhledejte soubor todoitem.json `/tables` v adresáři. Tento soubor definuje oprávnění pro tabulku. Soubor todoitem.js najdete také ve stejném adresáři. Definuje skripty operace CRUD pro tabulku.
6. Po provedení změn v souborech projektu spusťte následující příkazy pro přidání, potvrzení a odeslání změn na web:

        $ git commit -m "updated the table script"
        $ git push origin master

   Když do projektu přidáte nové soubory, musíte `git add .` nejprve spustit příkaz.

Web je znovu publikován pokaždé, když je na web odsunuta nová sada revizí.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>Publikování back-endu node.js do Azure

Microsoft Azure poskytuje mnoho mechanismů pro publikování vašeho mobilního apps Node.js back-end u služby Azure. Tyto mechanismy zahrnují nástroje nasazení integrované do sady Visual Studio, nástroje příkazového řádku a možnosti průběžného nasazení založené na slučování zdrojového kódu. Další informace najdete v [průvodci nasazením služby Azure App Service].

Služba Azure App Service má konkrétní rady pro aplikace Node.js, které byste měli zkontrolovat před publikováním back-endu:

* Jak [zadat verzi uzlu]
* Jak [používat moduly uzlů]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Povolení domovské stránky aplikace

Mnoho aplikací je kombinací webových a mobilních aplikací. Můžete použít ExpressJS rozhraní kombinovat dvě omezující aspekty. Někdy však můžete chtít implementovat pouze mobilní rozhraní. Je užitečné poskytnout domovskou stránku, abyste zajistili, že je služba aplikace v provozu. Můžete zadat vlastní domovskou stránku nebo povolit dočasnou domovskou stránku. Chcete-li povolit dočasnou domovskou stránku, použijte k vytvoření instance mobilních aplikací následující kód:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Pokud chcete tuto možnost k dispozici jenom při místním vývoji, můžete toto nastavení přidat do souboru azureMobile.js.

## <a name="table-operations"></a><a name="TableOperations"></a>Operace s tabulkami

Sada Azure-mobile-apps Node.js Server SDK poskytuje mechanismy pro vystavení tabulek dat uložených v Azure SQL Database jako webové rozhraní API. Poskytuje pět operací:

| Operace | Popis |
| --- | --- |
| GET /tables/*název tabulky* |Získejte všechny záznamy v tabulce. |
| GET /tables/*název_tabulky*/:id |Získejte konkrétní záznam v tabulce. |
| POST /tables/*název tabulky* |Vytvořte záznam v tabulce. |
| PATCH /tables/*název_tabulky*/:id |Aktualizace záznamu v tabulce |
| DELETE /tables/*název_tabulky*/:id |Odstraňte záznam v tabulce. |

Toto webové rozhraní API podporuje [odata] a rozšiřuje schéma tabulky pro podporu [synchronizace dat offline].

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Definování tabulek pomocí dynamického schématu

Před použitím tabulky je nutné ji definovat. Tabulky můžete definovat pomocí statického schématu (kde definujete sloupce ve schématu) nebo dynamicky (kde sada SDK řídí schéma na základě příchozích požadavků). Kromě toho můžete řídit konkrétní aspekty webového rozhraní API přidáním kódu JavaScript u definice.

Jako osvědčený postup byste měli definovat každou tabulku v `tables` souboru JavaScriptu v adresáři a pak použít metodu `tables.import()` k importu tabulek. Rozšířením ukázky základní aplikace byste upravili soubor app.js:

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

Definujte tabulku v souboru ./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabulky ve výchozím nastavení používají dynamické schéma. Chcete-li dynamické schéma vypnout globálně, `MS_DynamicSchema` nastavte nastavení aplikace na false na webu Azure Portal.

Kompletní příklad najdete v ukázkové části [todo na GitHubu].

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Definování tabulek pomocí statického schématu

Můžete explicitně definovat sloupce, které chcete vystavit prostřednictvím webového rozhraní API. Sada Azure-mobile-apps Node.js SDK automaticky přidá všechny další sloupce potřebné pro synchronizaci offline dat do seznamu, který zadáte. Například klientské aplikace rychlého startu vyžadují `text` tabulku se dvěma sloupci: (řetězec) a `complete` (logická hodnota).  
Tabulku lze definovat v souboru JavaScript definice `tables` tabulky (umístěném v adresáři) následujícím způsobem:

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

Pokud definujete tabulky staticky, musíte `tables.initialize()` také volat metodu k vytvoření schématu databáze při spuštění. Metoda `tables.initialize()` vrátí [příslib] tak, aby webová služba neobsluhovala požadavky před inicializací databáze.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Použití sql serveru Express jako úložiště vývojových dat v místním počítači

Sada SDK aplikace Node.js nabízí tři možnosti pro doručování dat ipozahům:

* Pomocí ovladače **paměti** poskytují úložiště netrvalých příkladů.
* Ovladač **mssql** slouží k poskytování úložiště dat SQL Server Express pro vývoj.
* Ovladač **mssql** slouží k poskytování úložiště dat azure SQL database pro produkční prostředí.

Sada SDK aplikace Microsoft [MSW Node.js používá balíček mssql Node.js] k navázání a použití připojení k databázi SQL Server Express i SQL Database. Tento balíček vyžaduje povolení připojení TCP v instanci SQL Server Express.

> [!TIP]
> Ovladač paměti neposkytuje úplnou sadu zařízení pro testování. Pokud chcete otestovat back-end místně, doporučujeme použít úložiště dat SQL Server Express a ovladač msSQL.

1. Stáhněte a nainstalujte [microsoft SQL Server 2014 Express]. Ujistěte se, že nainstalujete sql server 2014 Express s nástroji vydání. Pokud explicitně nepožadujete 64bitovou podporu, 32bitová verze spotřebovává při spuštění méně paměti.
1. Spusťte Správce konfigurace serveru SQL Server 2014:

   a. Rozbalte uzel **konfigurace sítě serveru SQL Server** v nabídce stromu.

   b. Vyberte **protokoly pro sqlexpress**.

   c. Klepněte pravým tlačítkem myši na **protokol TCP/IP** a vyberte **možnost Povolit**. V rozbalovacím dialogovém okně vyberte **OK.**

   d. Klepněte pravým tlačítkem myši na **protokol TCP/IP** a vyberte příkaz **Vlastnosti**.

   e. Vyberte kartu **IP adresy.**

   f. Najít uzel **IPAll.** Do pole **Port TCP** zadejte **hodnotu 1433**.

      ![Konfigurace serveru SQL Server Express pro protokol TCP/IP][3]

   g. Vyberte **OK**. V rozbalovacím dialogovém okně vyberte **OK.**

   h. Ve stromové nabídce vyberte **služby SQL Server Services.**

   i. Klepněte pravým tlačítkem myši na **sql server (SQLEXPRESS)** a vyberte **možnost Restartovat**.

   j. Zavřete Správce konfigurace serveru SQL Server 2014.

1. Spusťte sql server 2014 Management Studio a připojte se k místní instanci SQL Server Express:

   1. Klepněte pravým tlačítkem myši na instanci v Průzkumníkovi objektů a vyberte **vlastnosti**.
   1. Vyberte stránku **Zabezpečení.**
   1. Ujistěte se, že je vybrán **režim ověřování sql serveru a systému Windows.**
   1. Vyberte **OK**.

      ![Konfigurace ověřování serveru SQL Server Express][4]
   1. Rozbalte**přihlášení** **zabezpečení** > v Průzkumníkovi objektů.
   1. Klepněte pravým tlačítkem myši na **položku Přihlášení** a vyberte možnost **Nové přihlášení**.
   1. Zadejte přihlašovací jméno. Vyberte **Ověřování SQL Serveru**. Zadejte heslo a potom stejné heslo v **potvrdíte heslo**. Heslo musí splňovat požadavky na složitost systému Windows.
   1. Vyberte **OK**.

      ![Přidání nového uživatele do služby SQL Server Express][5]
   1. Klikněte pravým tlačítkem myši na nové přihlášení a vyberte **vlastnosti**.
   1. Vyberte stránku **Role serveru.**
   1. Zaškrtněte políčko pro roli serveru **dbcreator.**
   1. Vyberte **OK**.
   1. Zavřete sql server 2015 Management Studio.

Nezapomeňte zaznamenat uživatelské jméno a heslo, které jste vybrali. V závislosti na požadavcích na databázi může být nutné přiřadit další role serveru nebo oprávnění.

Aplikace Node.js přečte proměnnou `SQLCONNSTR_MS_TableConnectionString` prostředí pro připojovací řetězec pro tuto databázi. Tuto proměnnou můžete nastavit ve vašem prostředí. Například můžete použít Prostředí PowerShell k nastavení této proměnné prostředí:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Přístup k databázi prostřednictvím připojení TCP/IP. Zadejte uživatelské jméno a heslo pro připojení.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>Konfigurace projektu pro místní vývoj

Mobilní aplikace přečte soubor JavaScript uznaný *azureMobile.js* z místního systému souborů. Nepoužívejte tento soubor ke konfiguraci sady SDK mobilních aplikací v produkčním prostředí. Místo toho použijte **nastavení aplikací** na webu [Azure Portal].

Soubor azureMobile.js by měl exportovat konfigurační objekt. Nejběžnější nastavení jsou:

* Nastavení databáze
* Nastavení protokolování diagnostiky
* Alternativní nastavení CORS

Tento příklad **souboru azureMobile.js** implementuje předchozí nastavení databáze:

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

Doporučujeme přidat **azureMobile.js** do souboru **.gitignore** (nebo jiný soubor ignorovat ovládací prvek zdrojového kódu), aby se zabránilo hesla uložených v cloudu. Nastavení produkčního prostředí vždy nakonfigurujte v **nastavení aplikací** na webu [Azure Portal].

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Konfigurace nastavení aplikace pro mobilní aplikaci

Většina nastavení v souboru azureMobile.js má ekvivalentní nastavení aplikace na [webu Azure Portal]. Pomocí následujícího seznamu nakonfigurujte aplikaci v **nastavení aplikace**:

| Nastavení aplikace | nastavení azureMobile.js | Popis | Platné hodnoty |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |jméno |Název aplikace |řetězec |
| **MS_MobileLoggingLevel** |protokolování.úroveň |Minimální úroveň protokolu zpráv k protokolu |chyba, varování, info, verbose, ladění, hloupé |
| **MS_DebugMode** |ladit |Povolí nebo zakáže režim ladění. |pravda, nepravdivé |
| **MS_TableSchema** |data.schéma |Výchozí název schématu pro tabulky SQL |řetězec (výchozí: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Povolí nebo zakáže režim ladění. |pravda, nepravdivé |
| **MS_DisableVersionHeader** |verze (nastavena na nedefinovanou) |Zakáže hlavičku X-ZUMO-Server-Version. |pravda, nepravdivé |
| **MS_SkipVersionCheck** |skipversioncheck |Zakáže kontrolu verze rozhraní API klienta. |pravda, nepravdivé |

Nastavení aplikace:

1. Přihlaste se k [portálu Azure].
1. Vyberte **Všechny prostředky** nebo **Služby aplikací**a pak vyberte název mobilní aplikace.
1. Ve výchozím nastavení se otevře podokno **Nastavení.** Pokud tomu tak není, vyberte **Nastavení**.
1. V nabídce **OBECNÉ** vyberte **nastavení aplikace**.
1. Přejděte do části **Nastavení aplikace.**
1. Pokud nastavení aplikace již existuje, vyberte hodnotu nastavení aplikace a upravte ji.
   Pokud nastavení aplikace neexistuje, zadejte nastavení aplikace do pole **Klíč** a hodnotu do pole **Hodnota.**
1. Vyberte **Uložit**.

Změna většiny nastavení aplikace vyžaduje restartování služby.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Použití databáze SQL jako úložiště produkčních dat

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Použití Azure SQL Database jako úložiště dat je identické ve všech typech aplikací Služby Azure App Service. Pokud jste tak ještě neučinili, vytvořte back-end mobilních aplikací takto:

1. Přihlaste se k [portálu Azure].
1. V levém horním rohu okna vyberte tlačítko **+NEW** > **Web + Mobilní** > **mobilní aplikace**a pak zadejte název back-endu mobilních aplikací.
1. Do pole **Skupina prostředků** zadejte stejný název jako aplikace.
1. Je vybrán výchozí plán služby App Service. Pokud chcete změnit plán služby App Service:

   a. Vyberte **Plán služby App Service** > **+Vytvořit nový**.

   b. Zadejte název nového plánu služby App Service a vyberte vhodné umístění.

   c. Vyberte příslušnou cenovou úroveň pro službu. Výběrem **možnosti Zobrazit vše** zobrazíte další cenové možnosti, například **Volné** a **Sdílené**.

   d. Klepněte na tlačítko **Vybrat.**

   e. Zpět v podokně **plán služby App Service** vyberte **OK**.
1. Vyberte **Vytvořit**.

Zřizování back-endu mobilních aplikací může trvat několik minut. Po zřízení back-endu mobilních aplikací otevře portál podokno **Nastavení** pro back-end mobilních aplikací.

Můžete buď připojit existující databázi SQL k back-endu mobilních aplikací, nebo vytvořit novou databázi SQL. V této části vytvoříme databázi SQL.

> [!NOTE]
> Pokud již máte databázi ve stejném umístění jako back-end mobilních aplikací, můžete místo toho vybrat **Použít existující databázi** a pak vybrat tuto databázi. Nedoporučujeme použití databáze v jiném umístění z důvodu vyšší latence.

1. V novém back-endu mobilních aplikací vyberte **Nastavení** > **dat** > **mobilní aplikace** > **+Přidat**.
1. V podokně **Přidat datové připojení** vyberte možnost Databáze SQL – Konfigurace **požadovaných nastavení** > **Vytvoření nové databáze**. Do pole **Název** zadejte název nové databáze.
1. Vyberte **možnost Server**. V podokně **Nový server** zadejte do pole **Název serveru** jedinečný název serveru a zadejte vhodné přihlašovací jméno a heslo správce serveru. Ujistěte se, že je vybraná možnost **Povolit služby Azure přístup k serveru.** Vyberte **OK**.

   ![Vytvoření databáze Azure SQL][6]
1. V podokně **Nová databáze** vyberte **ok**.
1. V podokně **Přidat datové připojení** vyberte **připojovací řetězec**a zadejte přihlašovací jméno a heslo, které jste zadali při vytváření databáze. Pokud používáte existující databázi, zadejte přihlašovací údaje pro tuto databázi. Vyberte **OK**.
1. V podokně **Přidat datové připojení** znovu vyberte **OK** a vytvořte databázi.

<!--- END OF ALTERNATE INCLUDE -->

Vytvoření databáze může trvat několik minut. Oblast **Oznámení** slouží ke sledování průběhu nasazení. Neprogresí, dokud databáze úspěšně nasazena. Po nasazení databáze se vytvoří připojovací řetězec pro instanci databáze SQL v nastavení back-endaplikace pro mobilní aplikace. Toto nastavení aplikace najdete v **nastavení** > nastavení nastavení**připojovacích****Application settings** > řetězců .

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Vyžadovat ověření pro přístup k tabulkám

Pokud chcete používat ověřování služby `tables` App Service s koncovým bodem, musíte nejprve nakonfigurovat ověřování služby App Service na [webu Azure Portal.] Další informace naleznete v průvodci konfigurací pro zprostředkovatele identity, který chcete použít:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování na Facebooku]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování na Twitteru]

Každá tabulka má vlastnost přístupu, kterou můžete použít k řízení přístupu k tabulce. Následující ukázka ukazuje staticky definovanou tabulku s požadovaným ověřováním.

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

Vlastnost access může mít jednu ze tří hodnot:

* *anonym* označuje, že klientská aplikace může číst data bez ověření.
* *ověřeno* označuje, že klientská aplikace musí s požadavkem odeslat platný ověřovací token.
* *zakázáno* označuje, že tato tabulka je nyní zakázána.

Pokud vlastnost access není definována, je povolen neověřený přístup.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Použití deklarací identity ověřování u tabulek
Můžete nastavit různé deklarace identity, které jsou požadovány při nastavení ověřování. Tyto deklarace identity `context.user` nejsou obvykle k dispozici prostřednictvím objektu. Můžete je však načíst `context.user.getIdentity()` pomocí metody. Metoda `getIdentity()` vrátí příslib, který se překládá na objekt. Objekt je zařazován`facebook` `google`metodou `twitter` `microsoftaccount`ověřování `aad`( , , , , nebo ).

Pokud například nastavíte ověřování účtu Microsoft a požádáte o nárok na e-mailové adresy, můžete e-mailovou adresu přidat k záznamu pomocí následujícího řadiče tabulky:

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

Chcete-li zjistit, jaké deklarace jsou `/.auth/me` k dispozici, zobrazte koncový bod webu pomocí webového prohlížeče.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Zakázání přístupu k určitým operacím tabulky

Kromě zobrazení v tabulce lze vlastnost access použít k řízení jednotlivých operací. Existují čtyři operace:

* `read`je operace RESTful GET na stole.
* `insert`je operace RESTful POST na stole.
* `update`je operace RESTful PATCH na stole.
* `delete`je operace RESTful DELETE v tabulce.

Můžete například zadat neověřenou tabulku jen pro čtení:

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

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>Úprava dotazu, který se používá při operacích tabulky

Běžným požadavkem pro operace tabulky je poskytnout omezené zobrazení dat. Můžete například zadat tabulku, která je označena ověřeným ID uživatele, takže můžete číst nebo aktualizovat pouze vlastní záznamy. Následující definice tabulky poskytuje tuto funkci:

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

Operace, které obvykle spouštějí dotaz, mají vlastnost `where` dotazu, kterou můžete upravit pomocí klauzule. Vlastnost query je [queryJS] objekt, který se používá k převodu dotazu OData na něco, co může zpracovat back-end dat. Pro jednoduché případy rovnosti (jako předchozí) můžete použít mapu. Můžete také přidat konkrétní klauzule SQL:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Konfigurace obnovitelného odstranění v tabulce

Obnovitelné odstranění ve skutečnosti neodstraní záznamy. Místo toho je označí jako odstraněné v databázi nastavením odstraněný sloupec na true. Sada SDK pro mobilní aplikace automaticky odebere z výsledků soubory `IncludeDeleted()`SDK s montovnama, pokud ji nepoužívá sada SDK mobilního klienta . Chcete-li nakonfigurovat tabulku pro `softDelete` obnovitelné odstranění, nastavte vlastnost v definičním souboru tabulky:

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

Měli byste vytvořit mechanismus pro odstranění záznamů: klientská aplikace, WebJob, funkce Azure nebo vlastní rozhraní API.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Osivo databáze s daty

Při vytváření nové aplikace můžete chtít osivo tabulku s daty. Můžete to provést v rámci souboru JavaScript definice tabulky takto:

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

K osivu dat dochází pouze v případě, že jste k vytvoření tabulky použili sadu SDK pro mobilní aplikace. Pokud tabulka již v databázi existuje, nejsou do ní vložena žádná data. Pokud je zapnuté dynamické schéma, schéma je odvozeno z osiva dat.

Doporučujeme explicitně volat `tables.initialize()` metodu k vytvoření tabulky při spuštění služby.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Podpora pro Enable Swagger
Mobilní aplikace jsou dodávány s integrovanou podporou [Swagger.] Chcete-li povolit podporu Swagger, nejprve nainstalujte swagger-ui jako závislost:

    npm install --save swagger-ui

Poté můžete povolit podporu Swagger v konstruktoru mobilních aplikací:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Pravděpodobně chcete povolit podporu Swagger ve vývojových edicích. Můžete to udělat pomocí `NODE_ENV` nastavení aplikace:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Koncový `swagger` bod je umístěn na http://*webu*.azurewebsites.net/swagger. Můžete přistupovat k uj.swagger prostřednictvím koncového `/swagger/ui` bodu. Pokud se rozhodnete vyžadovat ověření v celé aplikaci, Swagger vytvoří chybu. Nejlepších výsledků dosáhnete, jestli chcete povolit neověřené požadavky v nastavení ověřování a `table.access` autorizace služby Azure App Service a pak řídit ověřování pomocí vlastnosti.

Můžete také přidat možnost Swagger do souboru azureMobile.js, pokud chcete pouze podporu Swagger pro místní vývoj.

## <a name="push-notifications"></a><a name="push"/>Nabízená oznámení

Mobilní aplikace se integrují s Azure Notification Hubs, takže můžete odesílat cílená nabízená oznámení milionům zařízení na všech hlavních platformách. Pomocí center oznámení můžete odesílat nabízená oznámení na zařízení se systémem iOS, Android a Windows. Další informace o všem, co můžete dělat s centry oznámení, najdete v tématu [Přehled centra oznámení](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push-notifications"></a><a name="send-push"></a>Odesílat nabízená oznámení

Následující kód ukazuje, jak `push` použít objekt k odeslání nabízeného vysílání oznámení registrovaným zařízením iOS:

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

Vytvořením push registrace šablony z klienta můžete místo toho odeslat nabízenou zprávu šablony zařízením na všech podporovaných platformách. Následující kód ukazuje, jak odeslat oznámení šablony:

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

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Odesílání nabízených oznámení ověřenému uživateli pomocí značek
Když se ověřený uživatel zaregistruje pro nabízená oznámení, značka ID uživatele se automaticky přidá do registrace. Pomocí této značky můžete odesílat nabízená oznámení do všech zařízení registrovaných konkrétním uživatelem. Následující kód získá SID uživatele, který je podání žádosti a odešle šablony nabízené oznámení pro každou registraci zařízení pro tohoto uživatele:

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

Při registraci nabízených oznámení od ověřeného klienta se ujistěte, že ověřování je dokončeno před pokusem o registraci.

## <a name="custom-apis"></a><a name="CustomAPI"></a>Vlastní api

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Definování vlastního rozhraní API

Kromě rozhraní API pro přístup `/tables` k datům prostřednictvím koncového bodu můžou mobilní aplikace poskytovat vlastní pokrytí rozhraní API. Vlastní api jsou definovány podobným způsobem jako definice tabulek a mají přístup ke všem stejným zařízením, včetně ověřování.

Pokud chcete používat ověřování služby App Service s vlastním rozhraním API, musíte nejprve nakonfigurovat ověřování služby App Service na [webu Azure Portal.] Další informace naleznete v průvodci konfigurací pro zprostředkovatele identity, který chcete použít:

* [Konfigurace ověřování Azure Active Directory]
* [Konfigurace ověřování na Facebooku]
* [Konfigurace ověřování Google]
* [Konfigurace ověřování společnosti Microsoft]
* [Konfigurace ověřování na Twitteru]

Vlastní rozhraní API jsou definována v podstatě stejným způsobem jako rozhraní API tabulky:

1. Vytvořte `api` adresář.
1. Vytvořte v adresáři soubor `api` JavaScript s definicí rozhraní API.
1. K importu adresáře `api` použijte metodu importu.

Tady je definice rozhraní API prototypu založená na ukázce základní aplikace, kterou jsme použili dříve:

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

Vezměme si příklad rozhraní API, které `Date.now()` vrací datum serveru pomocí metody. Zde je soubor api/date.js:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Každý parametr je jedním ze standardních restful sloves: GET, POST, PATCH nebo DELETE. Metoda je standardní [expressjs middleware] funkce, která odešle požadovaný výstup.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Vyžadovat ověření pro přístup k vlastnímu rozhraní API

Sada SDK pro mobilní aplikace implementuje `tables` ověřování stejným způsobem pro koncový bod i vlastní řešení API. Chcete-li přidat ověřování do rozhraní API vyvinutého v předchozí části, přidejte `access` vlastnost:

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

Můžete také určit ověřování u konkrétních operací:

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

Stejný token, který se `tables` používá pro koncový bod, musí být použit pro vlastní řešení API, která vyžadují ověření.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Zpracování velkých nahrávek souborů

Sada Mobile Apps SDK používá [middleware analyzátoru těla](https://github.com/expressjs/body-parser) k přijímání a dekódování obsahu těla ve vašem podání. Můžete předem nakonfigurovat tělo-analyzátor přijímat větší soubory nahrávání:

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

Soubor je zakódován před přenosem. Toto kódování zvětšuje velikost skutečného nahrávání (a velikost, kterou musíte zohlednit).

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Spuštění vlastních příkazů SQL

Sada SDK pro mobilní aplikace umožňuje přístup k celému kontextu prostřednictvím objektu požadavku. Parametrizované příkazy SQL můžete snadno spouštět definovanému poskytovateli dat:

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

## <a name="debugging"></a><a name="Debugging"></a>Ladění

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Ladění, diagnostika a řešení potíží s mobilními aplikacemi

Služba Azure App Service poskytuje několik technik ladění a řešení potíží pro aplikace Node.js.
Pokud chcete začít s odstraňováním problémů s back-endem aplikací Node.js Mobile Apps, přečtěte si následující články:

* [Monitorování služby Azure App Service]
* [Povolení protokolování diagnostiky ve službě Azure App Service]
* [Poradce při potížích s azure app service ve Visual Studiu]

Aplikace Node.js mají přístup k široké škále nástrojů diagnostického protokolu. Interně sada SDK aplikace Node.js používá [winston] pro diagnostické protokolování. Protokolování se automaticky aktivuje, když povolíte režim ladění nebo nastavíte nastavení `MS_DebugMode` aplikace na hodnotu true na webu Azure [Portal]. Generované protokoly se zobrazí v diagnostických protokolech na [webu Azure Portal].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Rychlý start klienta Android]: app-service-mobile-android-get-started.md
[Apache Cordova Klient rychlý start]: app-service-mobile-cordova-get-started.md
[Rychlý start klienta iOS]: app-service-mobile-ios-get-started.md
[Rychlý start klienta Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android Klient rychlý start]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms Rychlý start klienta]: app-service-mobile-xamarin-forms-get-started.md
[Rychlý start klienta Windows Storu]: app-service-mobile-windows-store-dotnet-get-started.md
[synchronizace dat offline]: app-service-mobile-offline-data-sync.md
[Konfigurace ověřování Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Konfigurace ověřování na Facebooku]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurace ověřování Google]: ../app-service/configure-authentication-provider-google.md
[Konfigurace ověřování společnosti Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurace ověřování na Twitteru]: ../app-service/configure-authentication-provider-twitter.md
[Průvodce nasazením služby Azure App Service]: ../app-service/deploy-local-git.md
[Monitorování služby Azure App Service]: ../app-service/web-sites-monitor.md
[Povolení protokolování diagnostiky ve službě Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Poradce při potížích s azure app service ve Visual Studiu]: ../app-service/troubleshoot-dotnet-visual-studio.md
[určení verze uzlu]: ../nodejs-specify-node-version-azure-apps.md
[použití modulů uzlů]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Portál Azure]: https://portal.azure.com/
[OData]: https://www.odata.org
[Slib]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[ukázka aplikace basicapp na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[ukázka todo na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[ukázkový adresář na GitHubu]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[Dotazjs]: https://github.com/Azure/queryjs
[Nástroje Node.js 1.1 pro Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[Balíček mssql Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

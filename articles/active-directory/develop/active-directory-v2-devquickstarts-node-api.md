---
title: Zabezpečení Azure Active Directory v2.0 webového rozhraní API pomocí Node.js | Microsoft Docs
description: Naučte se vytvářet webové aplikace Node.js API, které přijímá tokeny z osobního účtu Microsoft a z pracovní nebo školní účty.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0b572fc1-2aaf-4cb6-82de-63010fb1941d
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 05/13/2017
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 19ad25c7b08ff073097cacf3be359772ca0a327f
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158366"
---
# <a name="secure-a-web-api-by-using-nodejs"></a>Zabezpečení webového rozhraní API pomocí Node.js
> [!NOTE]
> Ne všechny funkce a scénáře Azure Active Directory fungovat s koncovým bodem v2.0. Pokud chcete zjistit, zda by měl používat koncového bodu v2.0 nebo koncový bod verze 1.0, přečtěte si informace o [v2.0 omezení](active-directory-v2-limitations.md).
> 
> 

Pokud používáte koncového bodu v2.0 Azure Active Directory (Azure AD), můžete použít [OAuth 2.0](active-directory-v2-protocols.md) přístup tokeny k ochraně vašeho webového rozhraní API. S OAuth 2.0 tokeny přístupu, uživatelé, kteří mají osobní účet Microsoft i pracovní nebo školní účty mít bezpečný přístup k vašemu webovému rozhraní API.

*Passport* je ověřovací middleware pro Node.js. Flexibilní a modulární, můžete do jakéhokoli nenápadně vyřadit Passport využívající Express nebo restify webové aplikace. Komplexní sada strategií Passport, podporují ověřování pomocí uživatelského jména a hesla, Facebook, Twitter a další možnosti. Vyvinuli jsme strategii pro Azure AD. V tomto článku jsme ukazují, jak nainstalovat modul a poté přidejte Azure AD `passport-azure-ad` modulu plug-in.

## <a name="download"></a>Ke stažení
Kód k tomuto kurzu je udržovaný [na GitHubu](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs). Chcete-li postupovat v kurzu, můžete [stáhnout kostru aplikace jako soubor ZIP](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/skeleton.zip), nebo tuto kostru klonovat:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Také můžete získat hotová aplikace na konci tohoto kurzu.

## <a name="1-register-an-app"></a>1: registrace aplikace
Vytvoření nové aplikace v [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), nebo postupujte podle [tyto podrobné kroky](active-directory-v2-app-registration.md) k registraci aplikace. Ověřte, že je:

* Kopírování **Id aplikace** přiřazené vaší aplikaci. Budete ho potřebovat pro účely tohoto kurzu.
* Přidat **Mobile** platformu pro vaši aplikaci.
* Kopírování **identifikátor URI pro přesměrování** z portálu. Musíte použít výchozí hodnotu identifikátoru URI `urn:ietf:wg:oauth:2.0:oob`.

## <a name="2-install-nodejs"></a>2: Instalace softwaru Node.js
Ukázku použít pro tento kurz, musíte [instalace softwaru Node.js](http://nodejs.org).

## <a name="3-install-mongodb"></a>3: instalace MongoDB
Pro úspěšné fungování této ukázky musíte [nainstalujte MongoDB](http://www.mongodb.org). V této ukázce použijete k zajištění trvalosti REST API trvalé napříč instancemi serveru MongoDB.

> [!NOTE]
> V tomto článku předpokládáme, že používáte výchozí instalaci a server koncové body pro MongoDB: mongodb://localhost.
> 
> 

## <a name="4-install-the-restify-modules-in-your-web-api"></a>4: Instalace modulů restify ve webovém rozhraní API
Používáme Resitfy k sestavení našem REST API. Restify je minimalistické a flexibilní Node.js aplikace rozhraní, které je odvozené z Express. Restify obsahuje robustní sadu funkcí, které můžete použít k sestavení REST API postavených na protokolu Connect.

### <a name="install-restify"></a>Instalace restify
1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

    Pokud **azuread** adresář neexistuje, vytvořte ho:

    `mkdir azuread`

2.  Instalace restify:

    `npm install restify`

    Výstup tohoto příkazu by měl vypadat takto:

    ```
    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0(mv@0.0.5)
    ```

#### <a name="did-you-get-an-error"></a>Obdrželi jste chybu?
V některých operačních systémech se při použití `npm` příkaz, může se zobrazit tato zpráva: `Error: EPERM, chmod '/usr/local/bin/..'`. Chyba následuje žádost zkuste účet Spustit jako správce. Pokud k tomu dojde, použijte příkaz `sudo` ke spuštění `npm` na vyšší úrovni oprávnění.

#### <a name="did-you-get-an-error-related-to-dtrace"></a>Obdrželi jste chybu související s DTrace?
Při instalaci restify, může se zobrazit tato zpráva:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: two
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify má efektivní mechanismus pro trasování volání REST pomocí DTrace. DTrace však není k dispozici v operačních systémech. Tato chybová zpráva můžete bezpečně ignorovat.


## <a name="5-install-passportjs-in-your-web-api"></a>5: instalace Passport.js ve vašem webovém rozhraní API
1.  Na příkazovém řádku změňte adresář na **azuread**.

2.  Nainstalujte Passport.js:

    `npm install passport`

    Výstup příkazu by měl vypadat takto:

    ```
     passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3
    ```

## <a name="6-add-passport-azure-ad-to-your-web-api"></a>6: Přidání passport-azure-ad do webového rozhraní API
Dál přidejte strategii OAuth pomocí passport-azuread. `passport-azuread` je sada strategií, které propojují Azure AD s Passport. Používáme tuto strategii pro nosné tokeny v této ukázce REST API.

> [!NOTE]
> I když OAuth 2.0 poskytuje rozhraní, ve kterém můžou být vystavené všechny známé typy tokenů, se běžně používají určitým typům tokenů. Nosné tokeny se běžně používají k ochraně koncových bodů. Nosné tokeny jsou nejčastěji vydávaným typem tokenů v OAuth 2.0. Mnoho implementací OAuth 2.0 předpokládá, že jsou nosné tokeny jediným typem vydávaných tokenů.
> 
> 

1.  Na příkazovém řádku změňte adresář na **azuread**.

    `cd azuread`

2.  Nainstalujte Passport.js `passport-azure-ad` modul:

    `npm install passport-azure-ad`

    Výstup příkazu by měl vypadat takto:

    ```
    passport-azure-ad@1.0.0 node_modules/passport-azure-ad
    ├── xtend@4.0.0
    ├── xmldom@0.1.19
    ├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
    ├── underscore@1.8.3
    ├── async@1.3.0
    ├── jsonwebtoken@5.0.2
    ├── xml-crypto@0.5.27 (xpath.js@1.0.6)
    ├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
    ├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
    ├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    └── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
    ```

## <a name="7-add-mongodb-modules-to-your-web-api"></a>7: přidání modulů MongoDB do webového rozhraní API
V této ukázce používáme jako naše úložiště dat MongoDB. 

1.  Nainstalujte Mongoose, často používaný modul plug-in pro správu modelů a schémat: 

    `npm install mongoose`

2.  Nainstalujte ovladač databáze pro MongoDB, které je také nazývaný MongoDB:

    `npm install mongodb`

## <a name="8-install-additional-modules"></a>8: instalace dalších modulů
Nainstalujte zbývající požadované moduly.

1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  Zadejte následující příkazy. Příkazy instalace následující modulů ve vašem adresáři node_modules:

    *   `npm install crypto`
    *   `npm install assert-plus`
    *   `npm install posix-getopt`
    *   `npm install util`
    *   `npm install path`
    *   `npm install connect`
    *   `npm install xml-crypto`
    *   `npm install xml2js`
    *   `npm install xmldom`
    *   `npm install async`
    *   `npm install request`
    *   `npm install underscore`
    *   `npm install grunt-contrib-jshint@0.1.1`
    *   `npm install grunt-contrib-nodeunit@0.1.2`
    *   `npm install grunt-contrib-watch@0.2.0`
    *   `npm install grunt@0.4.1`
    *   `npm install xtend@2.0.3`
    *   `npm install bunyan`
    *   `npm update`

## <a name="9-create-a-serverjs-file-for-your-dependencies"></a>9: vytvoření souboru Server.js pro svoje závislosti
Soubor Server.js obsahuje většinu funkcí webového rozhraní API serveru. Do tohoto souboru přidáte většinu kódu. Pro produkční účely můžete můžete funkčnost rozdělit do menších souborů, jako je pro samostatné trasy a ovladače. V tomto článku používáme Server.js pro tento účel.

1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  Pomocí zvoleného editoru, vytvořte soubor Server.js. Do souboru přidejte následující informace:

    ```Javascript
    'use strict';
    /**
    * Module dependencies.
    */
    var util = require('util');
    var assert = require('assert-plus');
    var mongoose = require('mongoose/');
    var bunyan = require('bunyan');
    var restify = require('restify');
    var config = require('./config');
    var passport = require('passport');
    var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
    ```

3.  Uložte soubor. Vrátíte se k němu za chvíli.

## <a name="10-create-a-config-file-to-store-your-azure-ad-settings"></a>10: Vytvořte konfigurační soubor pro uložení nastavení Azure AD
Tento soubor s kódem předává parametry konfigurace z portálu Azure AD Passport.js. Tyto hodnoty konfigurace jste vytvořili, když jste přidali webové rozhraní API na portálu na začátku článku. Po zkopírování kód vysvětlíme, co zadat jako hodnoty těchto parametrů.

1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  V editoru vytvoření souboru Config.js. Přidejte následující informace:

    ```Javascript
    // Don't commit this file to your public repos. This config is for first-run.
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your Mongo auth URI goes here.
    issuer: 'https://sts.windows.net/**<your application id>**/',
    audience: '<your redirect URI>',
    identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For Microsoft, you should never need to change this.
    };

    ```



### <a name="required-values"></a>Požadované hodnoty

*   **IdentityMetadata**: to je, kdy `passport-azure-ad` vyhledá konfigurační data pro zprostředkovatele identity (IDP) a klíče k ověření webových tokenů JSON (Jwt). Pokud používáte Azure AD, pravděpodobně nechcete toto nastavení změnit.

*   **Cílová skupina**: váš identifikátor URI přesměrování z portálu.

> [!NOTE]
> Vrátit klíče v pravidelných intervalech. Ujistěte se, že jste vždy stáhněte z adresy URL pro "openid_keys" a aplikaci můžete získat přístup k Internetu.
> 
> 

## <a name="11-add-the-configuration-to-your-serverjs-file"></a>11: přidejte konfiguraci do souboru Server.js
Aplikace musí čtení hodnoty z konfigurační soubor, který jste právě vytvořili. Přidejte soubor .config jako požadovaný prostředek vaší aplikace. Nastavte globální proměnné na ty, které jsou v souboru Config.js.

1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  V editoru otevřete Server.js. Přidejte následující informace:

    ```Javascript
    var config = require('./config');
    ```

3.  Přidejte novou část Server.js:

    ```Javascript
    // Pass these options in to the ODICBearerStrategy.
    var options = {
    // The URL of the metadata document for your app. Put the keys for token validation from the URL found in the jwks_uri tag in the metadata.
    identityMetadata: config.creds.identityMetadata,
    issuer: config.creds.issuer,
    audience: config.creds.audience
    };
    // Array to hold signed-in users and the current signed-in user (owner).
    var users = [];
    var owner = null;
    // Your logger
    var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
    });
    ```

## <a name="12-add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>12: přidejte informace modelu a schématu MongoDB pomocí Mongoose
V dalším kroku připojte tyto tři soubory ve službě REST API.

V tomto článku používáme k uložení naše úlohy MongoDB. To probereme *krok 4*.

V souboru Config.js jste vytvořili v kroku 11, se nazývá databáze *tasklist*. Který byl uveďte na konci adresy URL mongoose_auth_local připojení. Tuto databázi nemusíte předem vytvářet v MongoDB. Databáze se vytvoří během prvního spuštění vaší serverové aplikace (za předpokladu, že databáze ještě neexistuje).

Jste sdělili serveru, jaké databázi MongoDB má použít. Potom budete muset napsat další kód pro vytvoření modelu a schématu pro váš server úloh.

### <a name="the-model"></a>Model
Model schématu je velmi jednoduché. Ho můžete rozšířit, pokud je potřeba. 

Schéma modelu má tyto hodnoty:

*   **NÁZEV**. Osoba, která úlohu. Toto je **řetězec** hodnotu.
*   **ÚLOHA**. Název úlohy. Toto je **řetězec** hodnotu.
*   **DATUM**. Datum, tato úloha je kvůli. Toto je **data a času** hodnotu.
*   **DOKONČIT**. Zda je úloha dokončena. Toto je **Boolean** hodnotu.

### <a name="create-the-schema-in-the-code"></a>Vytvoření schématu v kódu
1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  V editoru otevřete Server.js. Pod položku konfigurace přidejte následující informace:

    ```Javascript
    // MongoDB setup.
    // Set up some configuration.
    var serverPort = process.env.PORT || 8080;
    var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
    // Connect to MongoDB.
    global.db = mongoose.connect(serverURI);
    var Schema = mongoose.Schema;
    log.info('MongoDB Schema loaded');
    ```

Tento kód se připojí k serveru MongoDB. Vrátí také objekt schématu.

#### <a name="using-the-schema-create-your-model-in-the-code"></a>Pomocí schématu, vytvoření modelu v kódu
Pod předchozí kód přidejte následující kód:

```Javascript
// Create a basic schema to store your tasks and users.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model.
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```

Jak se dá zjistit z kódu, nejprve vytvoříte schéma. V dalším kroku vytvoříte objekt modelu. Použít objekt modelu k ukládání dat napříč kódem, když definujete vaše **trasy**.

## <a name="13-add-your-routes-for-your-task-rest-api-server"></a>13: Přidat trasy pro serveru úloh REST API
Teď, když máte model databáze můžete pracovat, přidejte trasy, které budete používat pro svůj server REST API.

### <a name="about-routes-in-restify"></a>O trasách v restify
Trasy v restify pracovní úplně stejně, jako při použití balíku Express. Trasy se definují pomocí identifikátoru URI, který by měly volat klientské aplikace.  Trasy se obvykle definovat v samostatném souboru. V tomto kurzu jsme uveďte naše trasy v Server.js. Pro použití v provozním prostředí doporučujeme, abyste je do svých vlastních souboru zvážit trasy.

Typický vzor trasy restify je:

```Javascript
function createObject(req, res, next) {
// Do work on object.
_object.name = req.params.object; // Passed value is in req.params under object.
///...
return next(); // Keep the server going.
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```


Toto je vzor na nejzákladnější úrovni. Restify (a Express) poskytují mnohem hlubší funkčnost, jako je schopnost definovat typy aplikací a komplexního trasování napříč různými koncovými body.

#### <a name="add-default-routes-to-your-server"></a>Přidání výchozích tras na server
Přidat do základní trasy CRUD: **vytvořit**, **načíst**, **aktualizace**, a **odstranit**.

1.  Na příkazovém řádku změňte adresář na **azuread**:

    `cd azuread`

2.  V editoru otevřete Server.js. Níže položky databáze, které jste provedli dříve, přidejte následující informace:

    ```Javascript
    /**
    *
    * APIs for your REST task server
    */
    // Create a task.
    function createTask(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow you to use MongoDB Server as your response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    // Create a new task model, fill it, and save it to MongoDB.
    var _task = new Task();
    if (!req.params.task) {
    req.log.warn({
    params: p
    }, 'createTodo: missing task');
    next(new MissingTaskError());
    return;
    }
    _task.owner = owner;
    _task.task = req.params.task;
    _task.date = new Date();
    _task.save(function(err) {
    if (err) {
    req.log.warn(err, 'createTask: unable to save');
    next(err);
    } else {
    res.send(201, _task);
    }
    });
    return next();
    }
    // Delete a task by name.
    function removeTask(req, res, next) {
    Task.remove({
    task: req.params.task,
    owner: owner
    }, function(err) {
    if (err) {
    req.log.warn(err,
    'removeTask: unable to delete %s',
    req.params.task);
    next(err);
    } else {
    log.info('Deleted task:', req.params.task);
    res.send(204);
    next();
    }
    });
    }
    // Delete all tasks.
    function removeAll(req, res, next) {
    Task.remove();
    res.send(204);
    return next();
    }
    // Get a specific task based on name.
    function getTask(req, res, next) {
    log.info('getTask was called for: ', owner);
    Task.find({
    owner: owner
    }, function(err, data) {
    if (err) {
    req.log.warn(err, 'get: unable to read %s', owner);
    next(err);
    return;
    }
    res.json(data);
    });
    return next();
    }
    /// Returns the list of TODOs that were loaded.
    function listTasks(req, res, next) {
    // Resitify currently has a bug that doesn't allow you to set default headers.
    // These headers comply with CORS, and allow us to use MongoDB Server as our response to any origin.
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");
    log.info("listTasks was called for: ", owner);
    Task.find({
    owner: owner
    }).limit(20).sort('date').exec(function(err, data) {
    if (err)
    return next(err);
    if (data.length > 0) {
    log.info(data);
    }
    if (!data.length) {
    log.warn(err, "There are no tasks in the database. Add one!");
    }
    if (!owner) {
    log.warn(err, "You did not pass an owner when listing tasks.");
    } else {
    res.json(data);
    }
    });
    return next();
    }
    ```

### <a name="add-error-handling-for-the-routes"></a>Přidání zpracování chyb pro trasy
Přidáte nějaké zpracování chyb pro komunikaci zpět do klienta o problému, který jste se setkali.

Přidejte následující kód pod kód, který jste již zapsány:

```Javascript
///--- Errors for communicating something more information back to the client.
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="14-create-your-server"></a>14: vytvoření serveru
Poslední věcí, kterou chcete je přidání vaší instance serveru. Instance serveru spravuje vaše volání.

Restify (a Express) mají přímý přizpůsobení, které můžete použít s server REST API. V tomto kurzu používáme nejzákladnější nastavení.

```Javascript
/**
* Your server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that you don't drop data on uploads.
server.pre(restify.pre.pause());
// Clean up imprecise paths like //todo//////1//.
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl).
server.pre(restify.pre.userAgentConnection());
// Set a per-request Bunyan logger (with requestid filled in).
server.use(restify.requestLogger());
// Allow 5 requests/second by IP address, and burst to 10.
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use common commands, such as:
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## <a name="15-add-the-routes-without-authentication-for-now"></a>15: přidání tras (bez ověřování, teď)
```Javascript
/// Use CRUD to add the real handlers.
/**
/*
/* Each of these handlers is protected by your Open ID Connect Bearer strategy. Invoke 'oidc-bearer'
/* in the pasport.authenticate() method. Because REST is stateless, set 'session: false'. You 
/* don't need to maintain session state. You can experiment with removing API protection.
/* To do this, remove the passport.authenticate() method:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## <a name="16-run-the-server"></a>16: Spusťte serveru
Je vhodné před přidáním ověřování otestovat svůj server.

Nejjednodušší způsob, jak otestovat svůj server je pomocí curl na příkazovém řádku. Chcete-li to provést, musíte jednoduchý nástroj, který můžete použít k analýze výstup jako JSON. 

1.  Nainstalujte nástroj JSON, který používáme v následujících příkladech:

    `$npm install -g jsontool`

    Tím se globálně nainstaluje nástroj JSON.

2.  Ujistěte se, že je spuštěna instance MongoDB.

    `$sudo mongod`

3.  Změňte adresář na **azuread**, a poté spusťte curl:

    `$ cd azuread`
    `$ node server.js`

    `$ curl -isS http://127.0.0.1:8080 | json`

    ```Shell
    HTTP/1.1 2.0OK
    Connection: close
    Content-Type: application/json
    Content-Length: 171
    Date: Tue, 14 Jul 2015 05:43:38 GMT
    [
    "GET /",
    "POST /tasks/:owner/:task",
    "POST /tasks (for JSON body)",
    "GET /tasks",
    "PUT /tasks/:owner",
    "GET /tasks/:owner",
    "DELETE /tasks/:owner/:task"
    ]
    ```

4.  Chcete-li přidat úloha:

    `$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

    Odpověď by měla být:

    ```Shell
    HTTP/1.1 201 Created
    Connection: close
    Access-Control-Allow-Origin: *
    Access-Control-Allow-Headers: X-Requested-With
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 5
    Date: Tue, 04 Feb 2014 01:02:26 GMT
    Hello
    ```

5.  Seznam úloh pro Brandon:

    `$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Pokud tyto příkazy spustí bez chyb, jste připraveni pro přidání OAuth na server REST API.

*Nyní máte server REST API s MongoDB!*

## <a name="17-add-authentication-to-your-rest-api-server"></a>17: přidání ověřování na server REST API
Teď, když máte spuštěný rozhraní REST API, nastavte tak, aby jeho použití s Azure AD.

Na příkazovém řádku změňte adresář na **azuread**:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-thats-included-with-passport-azure-ad"></a>Použití oidcbearerstrategy, která je součástí passport-azure-ad
Zatím jste vytvořili typický server REST se seznamem úkolů bez jakéhokoli druhu ověřování. Nyní přidáte ověřování.

Nejprve znamenat, že chcete použít Passport. Po konfiguraci serveru starší PUT tato práva:

```Javascript
// Start using Passport.js.

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [!TIP]
> Při psaní rozhraní API, je vhodné vždy měli propojit data s něčím jedinečným z tokenu, který uživatel nemůže zfalšovat. Pokud tento server ukládá položky úkolů, uloží je na základě předplatného ID uživatele v tokenu (zavolaném prostřednictvím token.sub). Do pole "vlastník" Vložit token.sub. To zajišťuje, že pouze tento uživatel přístup TODOs uživatele. Nikdo jiný přístup k TODOs, které jste zadali. Neexistuje vystavení v rozhraní API pro "vlastník". Externí uživatel může požádat o TODOs jiní uživatelé, i když jsou ověřeni.
> 
> 

Pak pomocí Open ID Connect nosnou strategii, která se dodává s `passport-azure-ad`. To uvedli po jste vložili dříve:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users.
/*
/* Because of the Passport pattern, you need to manage users and info tokens
/* with a FindorCreate() method. The method must be provided by the implementor.
/* In the following code, you autoregister any user and implement a FindById().
/* It's a good idea to do something more advanced.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically, because they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport používá podobný Princip pro všechny svoje strategie (Twitteru, Facebooku a tak dále). Řídí všichni autoři strategií se vzorem. Předat strategie `function()` token, který používá a `done` jako parametry. Strategie se vrátí po dělá svou práci. Uložení uživatele a skrytí tokenu, takže je nebudete muset požadovat znovu.

> [!IMPORTANT]
> Předchozí kód přijme všechny uživatele, který může ověřit na váš server. To se označuje jako Automatická registrace. Na provozním serveru byste neměli chtít vpouštět každý, kdo bez toho, aby předtím prošli registračním procesem, který zvolíte. To je obvykle vzor, který můžete vidět u uživatelských aplikací. Aplikace může umožňují registraci pomocí Facebooku, ale následně požádá, můžete k zadání dalších informací. Pokud v tomto kurzu nebyly pomocí příkazového řádku programu, může extrahovat e-mail z vráceného objektu tokenu. Potom může požádat uživatele k zadání dalších informací. Protože se jedná o testovací server, je třeba přidat uživatele přímo k databázi v paměti.
> 
> 

### <a name="protect-endpoints"></a>Ochrana koncových bodů
Ochrana koncových bodů tak, že zadáte **passport.authenticate()** volání s protokol, který chcete použít.

Můžete upravit trasu v kódu serveru pro pokročilejší možnosti:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## <a name="18-run-your-server-application-again"></a>18: Spusťte serverovou aplikaci znovu
Znovu použijte curl a zkontrolujte, jestli máte OAuth 2.0 ochrany koncové body. To udělejte předtím, než spustíte všechny klientské sady SDK proti tomuto koncovému bodu. Vrátí hlavičky by mělo být uvedeno, zda ověřování pracuje správně.

1.  Ujistěte se, zda je spuštěna vaše isntance MongoDB:

    `$sudo mongod`

2.  Změnit na **azuread** adresář a potom pomocí curl:

    `$ cd azuread`

    `$ node server.js`

3.  Zkuste základní požadavek POST:

    `$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

    ```Shell
    HTTP/1.1 401 Unauthorized
    Connection: close
    WWW-Authenticate: Bearer realm="Users"
    Date: Tue, 14 Jul 2015 05:45:03 GMT
    Transfer-Encoding: chunked
    ```

401 odpovědi vyplývá, že se vrstva Passportu pokouší přesměrovat na koncový bod authorize, který je právě co chcete použít.

*Nyní máte službu REST API, která používá OAuth 2.0!*

Jste došli nejdál, co můžete bez použití klienta OAuth 2.0 kompatibilní s tímto serverem. Pro tento musíte zkontrolovat další kurzu.

## <a name="next-steps"></a>Další postup
Pro srovnání je hotová ukázka (bez vašich hodnot nastavení) k dispozici jako [soubor .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs/archive/complete.zip). Vám může ho také klonovat z Githubu:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-nodejs.git```

Teď můžete přesunout pokročilejší témata. Můžete chtít zkuste [zabezpečení webové aplikace Node.js pomocí koncového bodu v2.0](active-directory-v2-devquickstarts-node-web.md).

Zde jsou některé další zdroje informací:

* [Příručka vývojáře v2.0 služby Azure AD](active-directory-appmodel-v2-overview.md)
* [Značka "azure-active-directory" přetečení zásobníku](http://stackoverflow.com/questions/tagged/azure-active-directory)

### <a name="get-security-updates-for-our-products"></a>Získejte bezpečnostní aktualizace našich produktů
Doporučujeme registrace oznámení o bezpečnostních incidentech. Na [technické oznámení zabezpečení Microsoft](https://technet.microsoft.com/security/dd252948) stránky, odběr výstrah zpravodaje zabezpečení.


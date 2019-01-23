---
title: Zabezpečení webového rozhraní API s použitím prostředí Node.js v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak vytvořit webové aplikace Node.js API, které přijímá tokeny z klienta B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4016a81cd743149d846d425f1e2f7d5774a9e1fb
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449074"
---
# <a name="secure-a-web-api-by-using-nodejs-in-azure-active-directory-b2c"></a>Zabezpečení webového rozhraní API s použitím prostředí Node.js v Azure Active Directory B2C

S Azure Active Directory (Azure AD) B2C můžete zabezpečit webové rozhraní API pomocí přístupových tokenů OAuth 2.0. Tyto tokeny umožňují ověření přístupu klientských aplikací, které používají Azure AD B2C, do rozhraní API. Tento článek ukazuje, jak vytvořit rozhraní API „seznam úkolů“, které umožňuje uživatelům přidávat úkoly a vypisovat jejich seznam. Webové rozhraní API je zabezpečeno pomocí Azure AD B2C a umožňuje pouze ověřeným uživatelům spravovat své seznamy úkolů.

> [!NOTE]
> Tato ukázka byla napsána pro připojení pomocí naší [ukázkové aplikace iOS B2C](active-directory-b2c-devquickstarts-ios.md). Nejprve pokračujte podle aktuálního návodu a poté postupujte podle příslušné ukázky.
>
>

**Passport** je ověřovací middleware pro Node.js. Passport je flexibilní a modulární a lze ho snadno nainstalovat v jakékoli webové aplikaci využívající Express nebo Restify. Komplexní sada strategií podporuje ověřování pomocí uživatelského jména a hesla, Facebooku, Twitteru a dalších. Vyvinuli jsme strategii pro Azure Active Directory (Azure AD). Nainstalujte tento modul a poté přidejte modul plug-in Azure AD `passport-azure-ad`.

Chcete-li postupovat podle této ukázky, budete muset:

1. Zaregistrovat aplikaci s Azure AD.
2. Nastavit aplikaci pro používání modulu plug-in `passport-azure-ad` Passportu.
3. Nakonfigurovat klientskou aplikaci, aby volala webové rozhraní API „seznam úkolů“.

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C
Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta.  Adresář je kontejner pro všechny uživatele, aplikace, skupiny a další.  Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace
Dále musíte ve svém adresáři B2C vytvořit aplikaci, která poskytne Azure AD informace potřebné k bezpečné komunikaci s vaší aplikací. V tomto případě mají klientská aplikace i webové rozhraní API stejné **ID aplikace**, protože společně tvoří jednu logickou aplikaci. Chcete-li vytvořit aplikaci, postupujte podle [těchto pokynů](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Jste do aplikace zahrnuli **webovou aplikaci nebo webové rozhraní API**.
* Jste do pole **Adresa URL odpovědi** vyplnili `http://localhost/TodoListService`. To je výchozí URL pro tento příklad.
* Vytvořte pro aplikaci **tajný klíč aplikace** a poznamenejte si ho. Tato data budete potřebovat později. Před tím, než tuto hodnotu použijete, musí být [uvozena v XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape).
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Tato data budete potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady
V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato aplikace obsahuje dvě možnosti pro identitu: Zaregistrujte se a přihlaste se. Je potřeba vytvořit jednu zásadu každého typu.  Při vytváření zásady nezapomeňte na následující:

* Zvolit **Zobrazovaný název** a další atributy registrace ve svojí registrační zásadě.
* Zvolit deklarace identity aplikace **Zobrazovaný název** a **ID objektu** v každé zásadě.  Můžete zvolit i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Měl by mít předponu `b2c_1_`.  Tyto názvy zásad budete potřebovat později.

Po vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-code"></a>Stáhněte si kód
Kód k tomuto kurzu [je udržovaný na GitHubu](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS). Chcete-li během čtení tohoto návodu rovnou sestavit ukázku, můžete si [stáhnout kostru projektu v souboru ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip). Kostru můžete také klonovat:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

Dokončená aplikace je také [k dispozici jako soubor ZIP](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip) nebo ve větvi `complete` stejného úložiště.

## <a name="download-nodejs-for-your-platform"></a>Stažení Node.js pro vaši platformu
Pro úspěšné fungování této ukázky je třeba mít funkční instalaci Node.js.

Nainstalujte si Node.js z [nodejs.org](https://nodejs.org).

## <a name="install-mongodb-for-your-platform"></a>Instalace MongoDB pro vaši platformu
Pro úspěšné fungování této ukázky je třeba mít funkční instalaci MongoDB. MongoDB budeme používat k zajištění trvalosti REST API napříč instancemi serveru.

Nainstalujte MongoDB z [mongodb.org](https://www.mongodb.org).

> [!NOTE]
> Tento návod předpokládá, že používáte výchozí instalaci a koncové body serveru pro MongoDB, což je v době psaní tohoto návodu `mongodb://localhost`.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Instalace modulů Restify ve webovém rozhraní API
Restify použijeme k sestavení REST API. Restify je minimalistické a flexibilní rozhraní Node.js odvozené z Express. Obsahuje robustní sadu funkcí pro sestavování rozhraní REST API postavených na protokolu Connect.

### <a name="install-restify"></a>Instalace Restify
V příkazovém řádku přejděte do adresáře `azuread`. Pokud adresář `azuread` neexistuje, vytvořte ho.

`cd azuread` nebo `mkdir azuread;`

Zadejte následující příkaz:

`npm install restify`

Tento příkaz nainstaluje Restify.

#### <a name="did-you-get-an-error"></a>Obdrželi jste chybu?
Při použití příkazu `npm` můžete v některých operačních systémech obdržet chybu `Error: EPERM, chmod '/usr/local/bin/..'` s žádostí, abyste spustili účet jako správce. Pokud se vyskytne tento problém, použijte příkaz `sudo` ke spuštění příkazu `npm` na vyšší úrovni oprávnění.

#### <a name="did-you-get-a-dtrace-error"></a>Obdrželi jste chybu DTrace?
Při instalaci Restify se může zobrazit podobný text:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
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

Restify poskytuje výkonný mechanismus pro trasování volání REST pomocí DTrace. Nicméně, na mnoha operačních systémech není DTrace k dispozici. Tyto chyby můžete bezpečně ignorovat.

Výstup příkazu by měl vypadat podobně jako tento text:

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
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Instalace Passportu ve vašem webovém rozhraní API
V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste.

Nainstalujte Passport pomocí následujícího příkazu:

`npm install passport`

Výstup příkazu by měl se měl podobat tomuto textu:

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Přidání passport-azuread do webového rozhraní API
Nyní přidejte strategii OAuth pomocí `passport-azuread` – sady strategií, které propojují Azure AD s Passportem. Použijte tuto strategii pro nosné tokeny v ukázce REST API.

> [!NOTE]
> Přestože OAuth2 poskytuje rozhraní, ve kterém mohou být vydávané všechny známé typy tokenů, rozšířeného využití se dostalo pouze určitým typům tokenů. Tokeny pro ochranu koncových bodů jsou nosné tokeny. Tyto typy tokenů jsou v OAuth2 vydávány nejčastěji. Mnoho implementací předpokládá, že jsou nosné tokeny jediným typem vydávaných tokenů.
>
>

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste.

Nainstalujte modulu `passport-azure-ad` Passport pomocí následujícího příkazu:

`npm install passport-azure-ad`

Výstup příkazu by měl se měl podobat tomuto textu:

``
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
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Přidání modulů MongoDB do webového rozhraní API
V této ukázce se jako úložiště dat používá MongoDB. Pro tento účel nainstalujte Mongoose, což je běžně používaný modul plug-in pro správu modelů a schémat.

* `npm install mongoose`

## <a name="install-additional-modules"></a>Instalace dalších modulů
Dále nainstalujte zbývající požadované moduly.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

Nainstalujte moduly v adresáři `node_modules`:

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>Vytvoření souboru server.js se závislostmi
Soubor `server.js` poskytuje většinu funkčnosti vašeho serveru webového rozhraní API.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

V editoru vytvořte soubor `server.js`. Přidejte následující informace:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Uložte soubor. Vrátíte se k němu později.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Vytvoření souboru config.js pro ukládání nastavení Azure AD
Tento soubor s kódem předává parametry konfigurace z portálu Azure AD do souboru `Passport.js`. Tyto hodnoty konfigurace jste vytvořili, když jste přidali webové rozhraní API do portálu v první části tohoto návodu.  Vysvětlíme, co zadat jako hodnoty těchto parametrů, až zkopírujete kód.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

V editoru vytvořte soubor `config.js`. Přidejte následující informace:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>Požadované hodnoty
`clientID`: ID klienta aplikace webového rozhraní API.

`IdentityMetadata`: Tady `passport-azure-ad` hledá konfigurační data pro zprostředkovatele identity. Také zde hledá klíče pro ověření webových tokenů JSON.

`audience`: Identifikátor URI (URI) z portálu, který identifikuje vaši volající aplikaci.

`tenantName`: Název vašeho klienta (například **contoso.onmicrosoft.com**).

`policyName`: Zásady, které chcete k ověřování tokenů přicházejících na váš server. Tato zásada by měla být stejná jako ta, kterou používáte pro přihlašování na klientské aplikaci.

> [!NOTE]
> Použijte stejné zásady v nastavení klienta i serveru. Pokud jste již dokončili návod a tyto zásady jste už vytvořili, nemusíte to dělat znovu. Vzhledem k tomu, že jste návod dokončili, nemělo by být třeba nastavovat nové zásady u návodů pro klienty na této stránce.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>Přidání konfigurace do souboru server.js
Chcete-li načíst hodnoty z vytvořeného souboru `config.js` přidejte soubor `.config` jako požadovaný prostředek ve vaší aplikaci a pak nastavte globální proměnné na hodnoty v dokumentu `config.js`.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

V editoru otevřete soubor `server.js`. Přidejte následující informace:

```Javascript
var config = require('./config');
```
Do souboru `server.js` přidejte nový oddíl s následujícím kódem:

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

V dalším kroku přidáme několik zástupných symbolů pro uživatele obdržené z volajících aplikací.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

Nyní pokračujme a vytvořme i protokolovací nástroj.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Přidání informací o modelu a schématu MongoDB pomocí Mongoose
Předchozí příprava se vyplatí, protože tyto tři soubory spojíte dohromady ve službu REST API.

Pro tento návod použijte k ukládání úkolů MongoDB, jak je uvedeno výše.

V souboru `config.js` jste databázi nazvali **tasklist**. Tento název jste také vložili na konec adresy URL připojení `mongoose_auth_local`. Tuto databázi nemusíte předem vytvářet v MongoDB. Databáze se vytvoří během prvního spuštění vaší serverové aplikace.

Poté, co sdělíte serveru, kterou databázi MongoDB má použít, budete muset napsat další kód pro vytvoření modelu a schématu pro serverové úlohy.

### <a name="expand-the-model"></a>Rozšíření modelu
Tento model schématu je jednoduchý. Podle potřeby ho můžete rozšířit.

`owner`: Kdo je přiřazen k úloze. Tento objekt je typu **string**.  

`Text`: Vlastní úloha. Tento objekt je typu **string**.

`date`: Datum, které je třeba provést úlohy. Tento objekt je typu **datetime**.

`completed`: Pokud je úloha dokončena. Tento objekt je typu **Boolean**.

### <a name="create-the-schema-in-the-code"></a>Vytvoření schématu v kódu
V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

V editoru otevřete soubor `server.js`. Pod položku konfigurace přidejte následující informace:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Nejprve vytvoříte schéma a poté vytvoříte objekt modelu, který použijete k ukládání dat napříč kódem, když definujete svoje **trasy**.

## <a name="add-routes-for-your-rest-api-task-server"></a>Přidání tras pro serveru úloh REST API
Teď, když máte model databáze, se kterým můžete pracovat, přidejte trasy, které budete používat pro svůj server REST API.

### <a name="about-routes-in-restify"></a>O trasách v Restify
Trasy v Restify fungují stejně jako při použití balíku Express. Trasy se definují pomocí identifikátoru URI, který by měly volat klientské aplikace. 

Typický vzor trasy Restify je:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Restify a Express poskytují mnohem hlubší funkčnost, jako například definování typů aplikací a provádění komplexního trasování napříč různými koncovými body. Pro účely tohoto kurzu ponecháme tyto trasy co nejjednodušší.

#### <a name="add-default-routes-to-your-server"></a>Přidání výchozích tras na server
Nyní pro příslušné rozhraní REST API přidejte trasy základních operací CRUD **vytvoření** a **vypsání seznamu**. Další trasy najdete ve větvi `complete` ukázky.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

V editoru otevřete soubor `server.js`. Pod položky databáze, které jste vytvořili dříve, přidejte následující informace:

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
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
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

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
            log.warn(err, "There is no tasks in the database. Add one!");
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


#### <a name="add-error-handling-for-the-routes"></a>Přidání zpracování chyb pro trasy
Přidejte postupy zpracování chyb, abyste mohli srozumitelně sdělit klientu informace o jakýchkoli problémech, na které narazíte.

Přidejte následující kód:

```Javascript
///--- Errors for communicating something interesting back to the client
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


## <a name="create-your-server"></a>Vytvoření serveru
Nyní jste definovali databázi a přidali trasy. Poslední věcí, kterou musíte udělat, je přidání instance serveru, která spravuje vaše volání.

Restify a Express poskytují široké možnosti přizpůsobení serveru REST API, ale zde použijeme to nejzákladnější nastavení.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directory TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>Přidání tras na server (bez ověřování)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>Přidání ověřování na server REST API
Když teď máte fungující server REST API, můžete ho využít s Azure AD.

V příkazovém řádku přejděte do adresáře `azuread`, pokud v něm ještě nejste:

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>Použití OIDCBearerStrategy, která je součástí passport-azure-ad
> [!TIP]
> Při psaní rozhraní API byste vždy měli propojit data s něčím jedinečným z tokenu, co uživatel nemůže zfalšovat. Server ukládá položky úkolů na základě hodnoty **oid** uživatele v tokenu (zavolaném prostřednictvím token.oid) a ten se ukládá do pole „vlastník“. Tato hodnota zajišťuje, že pouze tento uživatel bude mít přístup ke svým vlastním položkám ToDo. V rozhraní API se „vlastník“ nijak neprojevuje, takže externí uživatelé nemohou přistupovat k položkám úkolů jiných uživatelů ani když jsou ověřeni.
>
>

Dále použijte nosnou strategii, která je součástí `passport-azure-ad`.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
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
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport používá stejný vzor pro všechny své strategie. Předáváte jí `function()`, která jako parametry přijímá `token` a `done`. Po dokončení veškeré práce se k vám strategie vrátí. Poté byste měli uložit uživatele a token, abyste ho nemuseli znovu vyžadovat.

> [!IMPORTANT]
> Výše uvedený kód přijímá jakéhokoli uživatele, který se na vašem serveru ověří. Tento proces se nazývá automatická registrace. Na produkčních serverech neumožňujte žádným uživatelům přístup k rozhraní API bez toho, aby předtím prošli registračním procesem. Tento proces představujte obvyklý vzor, který můžete vidět u uživatelských aplikací, které vám umožňují zaregistrovat se pomocí Facebooku, ale poté vás požádají o vyplnění dodatečných informací. Pokud by se nejednalo o program v příkazovém řádku, mohli bychom extrahovat e-mail z vráceného objektu tokenu a poté požádat uživatele o vyplnění dodatečných informací. Jelikož toto je ukázka, přidáme je do databáze v paměti.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>Spuštění serverové aplikace kvůli ověření, že vás odmítne
Můžete použít příkaz `curl`, abyste zjistili, zda jsou již koncové body chráněné pomocí OAuth2. Vrácené hlavičky by vám měly dostatečně napovědět, že jste na správné cestě.

Ujistěte se, že je spuštěna instance MongoDB.

    $sudo mongodb

Přejděte do adresáře a spusťte server:

    $ cd azuread
    $ node server.js

V novém okně terminálu spusťte příkaz `curl`

Zkuste základní požadavek POST:

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Chyba 401 je odpověď, kterou si přejete. Označuje, že se vrstva Passportu pokouší přesměrovat na koncový bod ověření.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>Nyní máte službu REST API, která používá OAuth2
Implementovali jste rozhraní REST API s použitím Restify a OAuth! Nyní máte dostatek kódu pro pokračování ve vývoji vlastní služby a navázání na tento příklad. S tímto serverem jste došli nejdál, co to jde bez použití klienta kompatibilního s OAuth2. Pro tento další krok použijte další podrobný postup, například náš návod [Připojení k webovému rozhraní API pomocí iOS s B2C](active-directory-b2c-devquickstarts-ios.md).

## <a name="next-steps"></a>Další postup
Nyní se můžete přesunout k pokročilejším tématům, jako například:

[Připojení k webovému rozhraní API pomocí iOS s B2C](active-directory-b2c-devquickstarts-ios.md)

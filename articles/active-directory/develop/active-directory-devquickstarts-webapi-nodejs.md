---
title: Azure AD Node.js webového rozhraní API Začínáme | Microsoft Docs
description: Jak sestavit webové Node.js REST API, které se integruje se službou Azure AD pro ověřování.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 24591c46858970724fbd1fe36336f7f2b0b2fefd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD Node.js webového rozhraní API Začínáme

Tento článek ukazuje, jak zabezpečit [Restify](http://restify.com/) koncový bod rozhraní API s [Passport](http://passportjs.org/) pomocí [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modulu pro zpracování komunikace se službou Azure Active Directory (AAD). 

Tomto kurzu se vztahuje si nemuseli dělat starosti strategii zabezpečení koncových bodů rozhraní API. Otázky přihlášení a zachování tokeny ověřování nejsou implementované sem a zodpovídají klientské aplikace. Podrobnosti, které obaluje implementace klienta, přečtěte si [webové aplikace Node.js přihlášení a odhlášení s Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md).

Úplný příklad spojené s tímto článkem je k dispozici na [Githubu](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Vytvoření ukázkového projektu
Tato aplikace server vyžaduje několik závislosti balíčků na podporu Restify a Passport, stejně jako účet informace, který je předán do AAD.

Pokud chcete začít, přidejte následující kód do souboru s názvem `package.json`:

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

Jednou `package.json` je vytvořen, spusťte `npm install` v příkazovém řádku pro instalaci závislostí balíčků. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurace projektu pro použití služby Active Directory

Abyste mohli začít, konfigurace aplikace, jsou několik hodnot specifické pro účet, který můžete získat z příkazového řádku Azure. Nejjednodušší způsob, jak začít pracovat s rozhraní příkazového řádku je použití prostředí cloudové služby Azure.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Zadejte následující příkaz v prostředí cloudu: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create) pro `create` příkazu zahrnout:

| Argument  | Popis |
|---------|---------|
|`display-name` | Popisný název registrace |
|`homepage` | Adresa URL, kde uživatelé mohou přihlásit a používat vaši aplikaci |
|`identifier-uris` | Místo oddělené jedinečné identifikátory URI, který můžete použít Azure AD pro tuto aplikaci |

Před připojením ke službě Azure Active Directory, je třeba následující informace:

| Název  | Popis | Název proměnné v konfiguračním souboru |
| ------------- | ------------- | ------------- |
| Název klienta  | [Název klienta](active-directory-howto-tenant.md) chcete použít pro ověřování | `tenantName`  |
| ID klienta  | ID klienta je název OAuth použitý pro AAD _ID aplikace_. |  `clientID`  |

Z odpovědi registrace v prostředí cloudu Azure, zkopírujte `appId` hodnotu a vytvořte nový soubor s názvem `config.js`. V dalším kroku přidejte následující kód a nahraďte hodnoty v závorkách tokenů:

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
Další informace týkající se nastavení konfigurace jednotlivých, zkontrolujte [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dokumentaci modulu.

## <a name="implement-the-server"></a>Implementace serveru
[Passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dva strategií ověření funkce modulu: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) a [nosiče](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategie. Server implementované v tomto článku používá nosnou strategii k zabezpečení koncový bod rozhraní API.

### <a name="step-1-import-dependencies"></a>Krok 1: Import závislosti
Vytvořte nový soubor s názvem `app.js` a vložte následující text:

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

V této části kódu:

- `restify` a `restify-plugins` moduly se odkazuje, aby bylo možné nastavit Restify server.

- `passport` a `passport-azure-ad` moduly jsou zodpovědní za komunikaci s AAD.

- `config` Proměnné je inicializovat pomocí hodnoty z `config.js` soubor vytvořený v předchozím kroku.

- Pole se vytvoří pro `authenticatedUserTokens` k uložení tokeny uživatele, jako jsou předávány do zabezpečené koncové body.

- `serverPort` Je definována z prostředí procesu portu nebo z konfiguračního souboru.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Vytvoření instance strategie ověření
Jak zabezpečit koncový bod, je nutné zadat strategie zodpovědná za určení, zda aktuální požadavek pochází z ověřeného uživatele. Zde `authenticatonStrategy` proměnné je instance `passport-azure-ad` `BearerStrategy` – třída. Přidejte následující kód po `require` příkazy.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
Tato implementace používá automatické registrace přidáním tokeny ověřování do `authenticatedUserTokens` pole, pokud dosud neexistují.

Jakmile je vytvořena nová instance strategie, je nutné předat do služby Passport prostřednictvím `use` metoda. Přidejte následující kód, který `app.js` pro použití strategie v Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfigurace serveru
S strategii ověřování definované teď můžete nastavit server Restify se některé základní nastavení a nastavení pro použití služby Passport pro zabezpečení.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Tento server je inicializován a nakonfigurovat tak, aby analyzovat hlavičky ověření a nastavte pro použití služby Passport.


### <a name="step-4-define-routes"></a>Krok 4: Definování tras
Teď můžete definovat trasy a rozhodnout, které chcete zabezpečit pomocí AAD. Tento projekt obsahuje dvě trasy, kde je otevřený úrovni kořenového adresáře a `/api` trasy je nastavit, aby vyžadovaly ověřování.

V `app.js` přidejte následující kód pro tuto trasu kořenové úrovně:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Kořenové trasy, která umožňuje všechny požadavky prostřednictvím trasy a vrátí zprávu, která obsahuje příkaz k testování `/api` trasy. Naopak `/api` trasy je uzamčené pomocí [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Přidejte následující kód po kořenové trasy.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Tato konfigurace umožňuje pouze ověřené žádosti, které zahrnují přístup k tokenu nosiče k `/api`. Možnost `session: false` slouží k zakázání relace tak, aby vyžadovala, že token je předán spolu s každou žádostí do rozhraní API.

Nakonec serveru je nastavena tak, aby naslouchala na konfigurovaném portu voláním `listen` metoda.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Spuštění ukázky

Teď, když se implementuje na server, můžete spustit server tak, že otevřete příkazový řádek a zadejte:

```Shell
npm start
```

Pomocí serveru se službou můžete odeslat požadavek na server k otestování výsledky. K předvedení odpověď z kořenové trasy, otevřete prostředí bash a zadejte následující kód:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Pokud váš server jste správně nakonfigurovali, odpověď by měla vypadat podobně jako:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Potom můžete otestovat trasy, která vyžaduje ověření zadáním následujícího příkazu do své prostředí bash:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Pokud jste správně nakonfigurovali server, pak server by měl odpovídat se stavem `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Teď, když jste vytvořili zabezpečené rozhraní API, můžete implementovat klienta, který se bude moct předat tokeny ověřování rozhraní API.

## <a name="next-steps"></a>Další postup
Jak jsme uvedli v úvodu, je nutné implementovat příslušného klienta pro připojení k serveru, který zpracovává přihlášení, odhlášení a správě tokeny. Příklady založené na kódu, můžete použít odkaz na klientské aplikace v [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) a [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Podrobný kurz najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Webové aplikace Node.js přihlášení a odhlášení s Azure AD](active-directory-devquickstarts-openidconnect-nodejs.md)
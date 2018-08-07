---
title: Azure AD Node.js web API Začínáme | Dokumentace Microsoftu
description: Postup pro sestavení Node.js REST webového rozhraní API, která se integruje s Azure AD pro ověřování.
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
ms.openlocfilehash: 3b203e5be82c01e7d586c90bae454aca23ebd630
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581197"
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD Node.js web API Začínáme

Tento článek ukazuje, jak zabezpečit [Restify](http://restify.com/) koncový bod rozhraní API s [Passport](http://passportjs.org/) pomocí [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) modul pro zpracování komunikace se službou Azure Active Directory (AAD). 

Rozsah v tomto kurzu zahrnuje obavy týkající se zabezpečení koncových bodů rozhraní API. Otázky přihlášení a zachování ověřovacích tokenů nejsou implementované tady a jsou na starost klientská aplikace. Podrobnosti o okolní implementace klienta, najdete v tématu [webové aplikace v Node.js přihlašování a odhlašování pomocí Azure AD](quickstart-v1-openid-connect-code.md).

Ukázka úplného kódu přidružené k tomuto článku je k dispozici na [Githubu](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="create-the-sample-project"></a>Vytvoření ukázkového projektu
Tento server aplikace vyžaduje několik závislosti balíčků pro Restify a Passport taky informace, který je předán AAD účtu.

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

Jednou `package.json` vytvoření, spuštění `npm install` v příkazovém řádku pro instalaci závislosti balíčků. 

### <a name="configure-the-project-to-use-active-directory"></a>Konfigurace projektu pro použití služby Active Directory

Abyste mohli začít, konfigurace aplikace, jsou několik hodnot specifické pro účet, který můžete získat z příkazového řádku Azure. Nejjednodušší způsob, jak začít pracovat s rozhraní příkazového řádku je chcete použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ve službě cloud shell zadejte následující příkaz: 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) pro `create` příkazu zahrnout:

| Argument  | Popis |
|---------|---------|
|`display-name` | Popisný název registrace |
|`homepage` | Adresa URL, kde uživatelé mohou přihlásit a používat vaši aplikaci |
|`identifier-uris` | Místo oddělený jedinečné identifikátory URI, který Azure AD můžete použít pro tuto aplikaci |

Před připojením ke službě Azure Active Directory, budete potřebovat následující informace:

| Název  | Popis | Název proměnné v konfiguračním souboru |
| ------------- | ------------- | ------------- |
| Název tenanta  | [Název tenanta](quickstart-create-new-tenant.md) chcete použít pro ověřování | `tenantName`  |
| ID klienta  | ID klienta je termín OAuth pro AAD _ID aplikace_. |  `clientID`  |

Z odpovědi registrace ve službě Azure Cloud Shell, zkopírujte `appId` hodnoty a vytvořte nový soubor s názvem `config.js`. V dalším kroku přidejte následující kód a nahraďte hodnoty v závorkách tokeny:

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
Další informace týkající se jednotlivých konfigurační nastavení, najdete v tématu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dokumentaci modulu.

## <a name="implement-the-server"></a>Implementace serveru
[Passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dvou strategií ověření funkce modulu: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) a [nosiče](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategie. Server implementované v tomto článku používá nosnou strategii zabezpečení koncového bodu rozhraní API.

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

- `restify` a `restify-plugins` moduly jsou odkazovány Pokud chcete nastavit Restify server.

- `passport` a `passport-azure-ad` moduly jsou zodpovědné za komunikaci s AAD.

- `config` Proměnná je inicializována s hodnotami z `config.js` soubor vytvořený v předchozím kroku.

- Je vytvořeno pole pro `authenticatedUserTokens` ukládat tokeny uživatelů, jako jsou předávány do zabezpečené koncové body.

- `serverPort` Je definován v prostředí procesu portu nebo z konfiguračního souboru.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Vytvoření instance strategie ověření
Jak zabezpečit koncový bod, je nutné zadat strategii odpovědností, zda aktuální požadavek pochází od ověřeného uživatele. Tady `authenticatonStrategy` proměnná je instance `passport-azure-ad` `BearerStrategy` třídy. Přidejte následující kód za `require` příkazy.

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
Tato implementace používá automatické registrace serveru tak, že přidáte do ověřovacích tokenů `authenticatedUserTokens` array – Pokud ještě neexistují.

Jakmile se vytvoří novou instanci třídy strategie, musíte jí předat do služby Passport prostřednictvím `use` metody. Přidejte následující kód, který `app.js` strategie používané k účtu služby Passport.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfigurace serveru
S metodu ověřování definované teď můžete nastavit server Restify s některá základní nastavení a nastavení pro používání pro zabezpečení.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Tento server je inicializován a nakonfigurovat tak, aby analyzovat hlavičky ověření a nastavte pro použití služby Passport.


### <a name="step-4-define-routes"></a>Krok 4: Definování tras
Teď můžete definovat trasy a rozhodnout, které k zabezpečení aad. Tento projekt obsahuje dvě trasy, pokud je otevřený kořenové úrovni a `/api` trasy je nastaven tak, aby vyžadovala ověřování.

V `app.js` přidejte následující kód pro tuto trasu kořenové úrovně:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Kořenové trasy umožňuje všechny požadavky prostřednictvím trasy a vrátí zprávu, která obsahuje příkaz k testování `/api` trasy. Oproti tomu `/api` trasy je uzamčené pomocí [ `passport.authenticate` ](http://passportjs.org/docs/authenticate). Přidejte následující kód za kořenové trasy.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Tato konfigurace umožňuje pouze ověřených požadavků, které zahrnují nosný token přístupu k k `/api`. Možnost `session: false` slouží k zakázání relace tak, aby vyžadovala, že token je předán s každou žádostí do rozhraní API.

Nakonec server je nastaven tak, aby naslouchala na konfigurovaném portu voláním `listen` metody.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Spuštění ukázky

Teď, když se implementuje na server, můžete spustit na serveru tak, že otevřete příkazový řádek a zadejte:

```Shell
npm start
```

Se spuštěným serverem můžete odeslat požadavek na server výsledky testů. Abychom si předvedli odpovědi z kořenového trasy, otevřete prostředí bash a zadejte následující kód:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

Pokud váš server správně nakonfigurujete, by měla vypadat podobně jako odpovědi:

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

V dalším kroku můžete otestovat trasy, která vyžaduje ověření zadáním následujícího příkazu do vašeho prostředí bash:

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

Pokud jste nakonfigurovali na serveru správně, pak server by měl odpovídat se stavem `Unauthorized`.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
Teď, když jste vytvořili zabezpečené rozhraní API, můžete implementovat klienta, který je schopen předání ověřovacích tokenů do rozhraní API.

## <a name="next-steps"></a>Další postup
Jak jsme uvedli v úvodu, je nutné implementovat protějšek klienta pro připojení k serveru, který se stará o přihlášení, odhlášení a Správa tokenů. Příklady založený na kódu, mohou odkazovat na klientské aplikace v [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) a [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android). Podrobný návod najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Webové aplikace v Node.js přihlašování a odhlašování pomocí Azure AD](quickstart-v1-openid-connect-code.md)
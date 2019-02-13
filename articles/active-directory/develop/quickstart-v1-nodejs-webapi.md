---
title: Zabezpečení webového rozhraní API službou Azure AD ! Microsoft Docs
description: Přečtěte si, jak vytvořit webové rozhraní REST API platformy Node.js, které integruje ověřování prostřednictvím Azure AD.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed159decb51d71e8c0beddb285f6c01ae264ed2
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206663"
---
# <a name="quickstart-secure-a-web-api-with-azure-active-directory"></a>Rychlý start: Zabezpečení webového rozhraní API pomocí Azure Active Directory

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

V tomto rychlém startu se naučíte zabezpečit koncový bod rozhraní API [Restify](http://restify.com/) middlewarem [Passport](http://passportjs.org/). Použijete k tomu modul [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad), který řeší komunikaci s Azure Active Directory (Azure AD).

Tento rychlý start se zabývá jenom zabezpečením koncových bodů rozhraní API. Problematika přihlašování a uchovávání ověřovacích tokenů zde není implementovaná, protože za ni odpovídá klientská aplikace. Podrobné informace o implementaci klienta najdete v článku o [přihlášení k webové aplikaci Node.js nebo odhlášení službou Azure AD](quickstart-v1-openid-connect-code.md).

Veškerý ukázkový kód, který je v tomto článku, je k dispozici na [GitHubu](https://github.com/Azure-Samples/active-directory-node-webapi-basic).

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky.

### <a name="create-the-sample-project"></a>Vytvoření ukázkového projektu

Serverová aplikace vyžaduje několik souvisejících balíčků, které podporují Restify a Passport, a také informace o účtu předávané do Azure AD.

Začněte tím, že do souboru `package.json` přidáte následující kód:

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

Jakmile vytvoříte soubor `package.json`, spusťte z příkazového řádku příkaz `npm install`, který nainstaluje související balíčky. 

#### <a name="configure-the-project-to-use-active-directory"></a>Konfigurace projektu na použití Active Directory

Nejprve nakonfigurujte aplikaci. Některé hodnoty, které se týkají účtu, můžete získat z Azure CLI. Nejjednodušší způsob jak začít s CLI je použít Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

V Cloud Shellu zadejte následující příkaz:

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

[Argumenty](/cli/azure/ad/app?view=azure-cli-latest#az-ad-app-create) příkazu `create`:

| Argument  | Popis |
|---------|---------|
|`display-name` | Popisný název registrace. |
|`homepage` | Adresa URL, kde se uživatelé přihlašují a používají aplikaci. |
|`identifier-uris` | Jedinečné identifikátory URI oddělené mezerami, které používá Azure AD pro tuto aplikaci. |

Abyste se mohli připojit k Azure Active Directory, potřebujete následující informace:

| Název  | Popis | Název proměnné v konfiguračním souboru |
| ------------- | ------------- | ------------- |
| Název tenanta  | [Název tenanta](quickstart-create-new-tenant.md), kterého použijete k ověřování. | `tenantName`  |
| ID klienta  | ID klienta je výraz OAuth používaný jako _ID aplikace_ v AAD. |  `clientID`  |

Z odpovědi na registraci v Azure Cloud Shellu zkopírujte hodnotu `appId` a vytvořte nový soubor s názvem `config.js`. Přidejte do něj následující kód. Tokeny v lomených závorkách nahraďte svými hodnotami:

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

Další informace o individuálním nastavení konfigurace najdete v dokumentaci k modulu [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage).

### <a name="implement-the-server"></a>Implementace serveru

[Passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) dvou strategií ověření funkce modulu: [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) a [nosiče](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy) strategie. Server, který implementujeme v tomto článku, používá k zabezpečení koncového bodu rozhraní API strategii nosičů.

### <a name="step-1-import-dependencies"></a>Krok 1: Importujte závislosti

Vytvořte nový soubor s názvem `app.js` a vložte do něj následující text:

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

- Odkazy na moduly `restify` a `restify-plugins` slouží k nastavení serveru Restify.
- Moduly `passport` a `passport-azure-ad` odpovídají za komunikaci s Azure AD.
- Proměnnou `config` inicializují hodnoty ze souboru `config.js` vytvořeného v předchozím kroku.
- Vytvoří se pole, do kterého se budou ukládat uživatelské tokeny `authenticatedUserTokens` předávané zabezpečeným koncovým bodům.
- Hodnota `serverPort` je buď definovaná portem prostředí daného procesu, nebo se vezme z konfiguračního souboru.

### <a name="step-2-instantiate-an-authentication-strategy"></a>Krok 2: Vytvoření instance strategie ověření

Při zabezpečení koncového bodu musíte zadat strategii, která zodpovídá za zjištění, jestli aktuální požadavek pochází od ověřeného uživatele. Proměnná `authenticatonStrategy` je instancí třídy `passport-azure-ad` `BearerStrategy`. Za výraz `require` přidejte následující kód.

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

Tato implementace používá automatickou registraci. Do pole `authenticatedUserTokens` přidá ověřovací tokeny, pokud tam ještě nejsou.

Jakmile vytvoříte novou instanci strategie, musíte ji předat Passportu metodou `use`. Pokud chcete v Passportu použít strategii, přidejte do souboru `app.js` následující kód.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>Krok 3: Konfigurace serveru

Jakmile máte definovanou ověřovací strategii, můžete provést základní nastavení serveru Restify a nastavit používání bezpečnostního middlewaru Passport.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directory with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
Server je inicializovaný a nakonfigurovaný na parsování autorizačních hlaviček a pak nastavený na používání Passportu.

### <a name="step-4-define-routes"></a>Krok 4: Definování tras

Teď můžete definovat trasy a rozhodnout se, jaké z nich zabezpečíte službou Azure AD. V tomto projektu jsou dvě trasy, které jsou otevřené na kořenové úrovni. Trasa `/api` je nastavená tak, aby vyžadovala ověřování.

Do souboru `app.js` přidejte následující kód trasy na úrovni kořene:

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

Kořenová trasa propustí všechny požadavky a vrátí zprávu, ve které je příkaz k testování trasy `/api`. Naopak trasa `/api` je zamčená příkazem [`passport.authenticate`](http://passportjs.org/docs/authenticate). Za kořenovou trasu přidejte následující kód.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

Tato konfigurace povolí jenom ověřené požadavky, které zahrnují přístup k `/api` pomocí nosného tokenu. Volba `session: false` slouží k zakázání relací. S každým požadavkem do rozhraní API se vyžaduje předání tokenu.

Nakonec voláním metody `listen` nastavíme server, aby naslouchal na nakonfigurovaném portu.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>Spuštění ukázky

Implementovaný server můžete spustit otevřením příkazového řádku, na kterém zadáte:

```shell
npm start
```

Pokud je server spuštěný, odešlete mu požadavek, aby otestoval výsledky. Pokud chcete předvést odpověď kořenové trasy, otevřete prostředí Bash a zadejte následující kód:

```shell
curl -isS -X GET http://127.0.0.1:3000/
```

Pokud jste server nakonfigurovali správně, měla by odpověď vypadat podobně jako v následující ukázce:

```shell
HTTP/1.1 200 OK
Server: Azure Active Directory with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

Teď můžete otestovat trasu, která vyžaduje ověření. V prostředí Bash zadejte následující příkaz:

```shell
curl -isS -X GET http://127.0.0.1:3000/api
```

Pokud jste server nakonfigurovali správně, měl by odpovědět stavem `Unauthorized`.

```shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directory with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```

Vytvořili jste zabezpečené rozhraní API. Teď můžete implementovat klienta, který ověřovací tokeny předává rozhraní API.

## <a name="next-steps"></a>Další postup

* Musíte implementovat klientský protějšek, který se připojuje k serveru. Tento server zajišťuje přihlášení, odhlášení a správu tokenů. Příklady kódu najdete v klientských aplikacích pro [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) a [Androidu](https://github.com/MSOpenTech/azure-activedirectory-library-for-android).
* Podrobný kurz najdete v článku o [přihlášení k webové aplikaci Node.js a odhlášení z ní službou Azure AD](quickstart-v1-openid-connect-code.md).

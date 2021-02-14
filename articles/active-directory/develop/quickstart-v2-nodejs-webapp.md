---
title: 'Rychlý Start: přidání přihlášení uživatele do webové aplikace Node.js | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak implementovat ověřování ve webové aplikaci Node.js pomocí nástroje OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, devx-track-js
ms.openlocfilehash: f7f14b91dc69eeba4ac06f6608f6151634dc38d3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103494"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Rychlý Start: přidání přihlášení pomocí OpenID připojení k webové aplikaci Node.js

V tomto rychlém startu si stáhnete a spustíte ukázku kódu, která ukazuje, jak nastavit ověřování OpenID Connect ve webové aplikaci sestavené pomocí Node.js pomocí Express. Ukázka je navržená tak, aby běžela na jakékoli platformě.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/).

## <a name="register-your-application"></a>Registrace aplikace

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace, například `MyWebApp` . Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v libovolném organizačním adresáři a v osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)**.

    Pokud je k dispozici více identifikátorů URI přesměrování, přidejte je na kartě **ověřování** později po úspěšném vytvoření aplikace.

1. Pokud chcete vytvořit aplikaci, vyberte **zaregistrovat** .
1. Na stránce **Přehled** aplikace vyhledejte hodnotu **ID aplikace (klienta)** a zaznamenejte ji pro pozdější použití. Tuto hodnotu budete potřebovat ke konfiguraci aplikace později v tomto projektu.
1. V části **Spravovat** vyberte **ověřování**.
1. Vyberte **Přidat**  >  **Web** platformy. 
1. V části **identifikátory URI pro přesměrování** zadejte `http://localhost:3000/auth/openid/return` .
1. Zadejte **adresu URL pro odhlášení front-Channel** `https://localhost:3000` .
1. V části **implicitní udělení a hybridní toky** vyberte možnost **tokeny ID** , protože tato ukázka vyžaduje, aby byl [tok implicitního udělení](./v2-oauth2-implicit-grant-flow.md) povolen pro přihlášení uživatele.
1. Vyberte **Konfigurovat**.
1. V části **Spravovat** vyberte **certifikáty & tajných klíčů**  >  **nový tajný klíč klienta**.
1. Zadejte popis klíče (např. tajný klíč aplikace).
1. Vyberte dobu trvání klíče buď **v 1 roce, 2 roky,** nebo **nikdy nevyprší**.
1. Vyberte **Přidat**. Hodnota klíče se zobrazí. Zkopírujte hodnotu klíče a uložte ji v bezpečném umístění pro pozdější použití.


## <a name="download-the-sample-application-and-modules"></a>Stažení ukázkové aplikace a modulů

Dále naklonujte ukázkové úložiště a nainstalujte moduly NPM.

Z prostředí nebo příkazového řádku:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

nebo

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Z kořenového adresáře projektu spusťte příkaz:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurace aplikace

Zadejte parametry v `exports.creds` v config.js podle pokynů.

* Aktualizujte `<tenant_name>` `exports.identityMetadata` se pomocí názvu TENANTA Azure AD ve formátu \* . onmicrosoft.com.
* Aktualizujte `exports.clientID` ID aplikace, kterou jste si poznamenali v registraci aplikace.
* Aktualizujte `exports.clientSecret` pomocí tajného klíče aplikace zjištěného při registraci aplikace.
* Aktualizujte `exports.redirectUrl` identifikátor URI přesměrování, který jste si poznamenali v registraci aplikace.

**Volitelná konfigurace pro produkční aplikace:**

* `exports.destroySessionUrl`Pokud chcete použít jiný, aktualizujte v config.js `post_logout_redirect_uri` .

* `exports.useMongoDBSessionStore`Pokud chcete používat [MongoDB](https://www.mongodb.com) nebo jiná [kompatibilní úložiště relací](https://github.com/expressjs/session#compatible-session-stores), nastavte v config.js na hodnotu true.
Výchozí úložiště relací v této ukázce je `express-session` . Výchozí úložiště relací není vhodné pro produkční prostředí.

* Aktualizujte `exports.databaseUri` , pokud chcete použít úložiště relací MongoDB a jiný identifikátor URI databáze.

* Aktualizace `exports.mongoDBSessionMaxAge` . Tady můžete určit, jak dlouho chcete uchovat relaci v mongoDB. Jednotka má sekundu (y).

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Spusťte službu mongoDB. Pokud v této aplikaci používáte úložiště relací mongoDB, musíte [nainstalovat MongoDB](http://www.mongodb.org/) a nejdřív službu spustit. Pokud používáte výchozí úložiště relací, můžete tento krok přeskočit.

Spusťte aplikaci pomocí následujícího příkazu z příkazového řádku.

```
$ node app.js
```

**Je pro vás výstup serveru obtížné?:** `bunyan` K přihlášení v této ukázce používáme. Konzola nebude velmi smyslná, pokud nenainstalujete Bunyan a spustíte server, jak je uvedeno výše, ale přesměrujte ho prostřednictvím binárního souboru Bunyan:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>A je to hotové!

Budete mít úspěšné spuštění serveru v systému `http://localhost:3000` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o scénáři webové aplikace, kterou podporuje platforma Microsoft Identity Platform:
> [!div class="nextstepaction"]
> [Webová aplikace, která se podepisuje ve scénáři uživatelů](scenario-web-app-sign-user-overview.md)
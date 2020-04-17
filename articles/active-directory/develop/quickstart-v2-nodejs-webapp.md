---
title: Přidání přihlášení OIDC do webové aplikace Node.js – platforma identit microsoftu | Azure
description: Zjistěte, jak implementovat ověřování ve webové aplikaci Node.js pomocí OpenID Connect.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 1ff92b8a9477800477ebb2d79145ddaa78831f30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536059"
---
# <a name="quickstart-add-sign-in-using-openid-connect-to-a-nodejs-web-app"></a>Úvodní příručka: Přidání přihlášení pomocí OpenID Connect do webové aplikace Node.js

V tomto rychlém startu se dozvíte, jak nastavit ověřování OpenID Connect ve webové aplikaci vytvořené pomocí souboru Node.js s expressem. Ukázka je určena ke spuštění na libovolné platformě.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit tuto ukázku, budete potřebovat:

* Instalace souboru Node.js zhttp://nodejs.org/

* Účet [Microsoft](https://www.outlook.com) nebo [vývojářská aplikace Office 365](/office/developer-program/office-365-developer-program)

## <a name="register-your-application"></a>Registrace vaší aplikace
1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud je váš účet k dispozici ve více než jednom tenantovi Azure AD:
    - Vyberte svůj profil z nabídky v pravém horním rohu stránky a potom **přepněte adresář**.
    - Změňte svou relaci na klienta Azure AD, kde chcete vytvořit aplikaci.

1. Chcete-li aplikaci zaregistrovat, přejděte na [registraci > aplikací služby Azure Active Directory.](https://go.microsoft.com/fwlink/?linkid=2083908)

1. Vyberte **Nová registrace.**

1. Po zobrazení stránky **Registrace aplikace** zadejte registrační údaje aplikace:
    - V části **Název** zadejte smysluplný název, který se uživatelům aplikace zobrazí. Například: MyWebApp
    - V části **Podporované typy účtů** vyberte Účty v **libovolném organizačním adresáři a osobních účtech Microsoft (např. skype, xbox, Outlook.com).**

    Pokud existuje více než jeden přesměrování IDENTIFIKÁTORŮ URI, budete muset přidat z karty **Ověřování** později po aplikaci byla úspěšně vytvořena.

1. Chcete-li aplikaci vytvořit, vyberte **Registrovat.**

1. Na stránce **Přehled** aplikace najděte hodnotu **ID aplikace (klienta)** a zaznamenejte ji na později. Tuto hodnotu budete potřebovat ke konfiguraci aplikace dále v tomto projektu.

1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
    - V části **Přesměrování identifikátorů URI** vyberte v poli se seznamem **web** a zadejte následující identifikátor URI přesměrování:`http://localhost:3000/auth/openid/return`
    - V části **Upřesnit nastavení** nastavte adresu `http://localhost:3000`URL **odhlášení** na adresu .
    - V části **Upřesnit nastavení > implicitní udělení** zkontrolujte **tokeny ID,** protože tato ukázka vyžaduje, aby byl povolen [implicitní tok grantu](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-implicit-grant-flow) pro přihlášení uživatele.

1. Vyberte **Uložit**.

1. Na stránce **Certifikáty & tajných kódů** v části **Tajné klíče klienta** zvolte **Nový tajný klíč klienta**.
    - Zadejte popis klíče (například tajný klíč aplikace).
    - Vyberte dobu trvání klíče **buď v 1 rok, za 2 roky** nebo nikdy **nevyprší**.
    - Po klepnutí na tlačítko **Přidat** se zobrazí hodnota klíče. Zkopírujte hodnotu klíče a uložte ji na bezpečném místě.

    Tento klíč budete později potřebovat ke konfiguraci aplikace. Tato hodnota klíče se znovu nezobrazí ani nelze ji získat žádným jiným způsobem, takže ji zaznamenejte, jakmile je viditelná z portálu Azure.

## <a name="download-the-sample-application-and-modules"></a>Stažení ukázkové aplikace a modulů

Dále naklonujte ukázkové repo a nainstalujte moduly NPM.

Z prostředí nebo příkazového řádku:

`$ git clone git@github.com:AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

– nebo –

`$ git clone https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git`

Z kořenového adresáře projektu spusťte příkaz:

`$ npm install`

## <a name="configure-the-application"></a>Konfigurace aplikace

Zadejte parametry `exports.creds` v konstru.js podle pokynů.

* `<tenant_name>` Aktualizujte `exports.identityMetadata` v s názvem klienta \*Azure AD formátu .onmicrosoft.com.
* Aktualizujte `exports.clientID` id aplikace, které bylo zaznamenáno z registrace aplikace.
* Aktualizujte `exports.clientSecret` tajným tajemstvím aplikace, který je zaznamenán při registraci aplikace.
* Aktualizujte `exports.redirectUrl` identifikátorem URI přesměrování, který byl zaznamenán z registrace aplikace.

**Volitelná konfigurace pro produkční aplikace:**

* Aktualizace `exports.destroySessionUrl` v config.js, pokud chcete `post_logout_redirect_uri`použít jiný .

* Nastavte `exports.useMongoDBSessionStore` v config.js na true, pokud chcete použít [mongoDB](https://www.mongodb.com) nebo jiné [kompatibilní úložiště relací](https://github.com/expressjs/session#compatible-session-stores).
Výchozí úložiště relací `express-session`v této ukázce je . Výchozí úložiště relací není vhodné pro výrobu.

* Aktualizace `exports.databaseUri`, pokud chcete použít úložiště relací mongoDB a jinou databázi URI.

* Aktualizovat `exports.mongoDBSessionMaxAge`. Zde můžete určit, jak dlouho chcete zachovat relaci v mongoDB. Jednotka je druhá (jednotky).

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

Spusťte službu mongoDB. Pokud používáte úložiště relací mongoDB v této aplikaci, musíte [nainstalovat mongoDB](http://www.mongodb.org/) a nejprve spustit službu. Pokud používáte výchozí úložiště relací, můžete tento krok přeskočit.

Spusťte aplikaci pomocí následujícího příkazu z příkazového řádku.

```
$ node app.js
```

**Je výstup serveru těžko pochopitelný?:** Používáme `bunyan` pro přihlášení v této ukázce. Konzole nebude mít velký smysl pro vás, pokud jste také nainstalovat bunyan a spustit server jako výše, ale potrubí přes bunyan binární:

```
$ npm install -g bunyan

$ node app.js | bunyan
```

### <a name="youre-done"></a>A je to hotové!

Server bude úspěšně spuštěn na `http://localhost:3000`.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Další kroky
Další informace o scénáři webové aplikace, který platforma identit Microsoftu podporuje:
> [!div class="nextstepaction"]
> [Webová aplikace, která se přikresluje ve scénáři uživatelů](scenario-web-app-sign-user-overview.md)

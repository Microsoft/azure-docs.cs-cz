---
title: Migrace jednostránkové aplikace v JavaScriptu z implicitního udělení na tok autorizačního kódu | Azure
titleSuffix: Microsoft identity platform
description: Postup aktualizace zabezpečeného hesla typu JavaScript pomocí MSAL.js 1. x a implicitního toku udělení pro MSAL.js 2. x a toku autorizačního kódu s podporou PKCE a CORS.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 2c03e6940d392e3e2c03bf6508de41a7a19aef3b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063769"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrace jednostránkové aplikace v JavaScriptu z implicitního udělení na tok kódu ověřování

Knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) v 2.0 přináší podporu toku autorizačního kódu s PKCE a CORS pro jednostránkové aplikace na platformě Microsoft identity. Postupujte podle kroků v následujících částech a migrujte svou aplikaci MSAL.js 1. x pomocí implicitního udělení MSAL.js 2.0 + (dále *2. x*) a toku kódu ověřování.

MSAL.js 2. x vylepšuje MSAL.js 1. x tím, že podporuje tok autorizačního kódu v prohlížeči místo implicitního toku udělení. MSAL.js 2. **x nepodporuje implicitní** tok.

## <a name="migration-steps"></a>Kroky migrace

Chcete-li aktualizovat aplikaci na MSAL.js 2. x a tok kódu ověřování, existují tři hlavní kroky:

1. Přepněte identifikátor URI přesměrování [Registrace aplikace](#switch-redirect-uris-to-spa-platform) z **webové** platformy na **jednoduchou aplikační** platformu.
1. Aktualizujte [kód](#switch-redirect-uris-to-spa-platform) z MSAL.js 1. x na **2. x**.
1. Zakáže [implicitní udělení](#disable-implicit-grant-settings) v registraci vaší aplikace, když jsou všechny aplikace sdílející registraci aktualizované tak, aby MSAL.js 2. x a tok kódu ověřování.

V následujících částech jsou podrobněji popsány jednotlivé kroky.

## <a name="switch-redirect-uris-to-spa-platform"></a>Přepnout identifikátory URI přesměrování na platformu SPA

Pokud chcete pro své aplikace nadále používat stávající registraci aplikace, použijte Azure Portal k aktualizaci identifikátorů URI přesměrování registrace na platformu SPA. Díky tomu je možné tok autorizačního kódu s PKCE a podporou CORS pro aplikace, které používají registraci (stále potřebujete aktualizovat kód vaší aplikace na MSAL.js v2. x).

Pro registrace aplikací, které jsou aktuálně nakonfigurované pomocí identifikátorů URI přesměrování **webové** platformy, použijte následující postup:

1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a> a vyberte svého tenanta **Azure Active Directory** .
1. V **Registrace aplikací** vyberte svou aplikaci a pak **ověřování**.
1. Na dlaždici **Webová** platforma v části **identifikátory URI pro přesměrování** vyberte informační zpráva s oznámením, že byste měli migrovat identifikátory URI.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Informační zpráva k implicitnímu toku na dlaždici webové aplikace v Azure Portal":::
1. Vyberte *jenom* ty identifikátory URI přesměrování, jejichž aplikace budou používat MSAL.js 2. x, a pak vyberte **Konfigurovat**.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="V podokně hesla vyberte podokno identifikátor URI přesměrování v Azure Portal":::

Tyto identifikátory URI pro přesměrování by se teď měly zobrazit na dlaždici aplikační platforma s **jednou stránkou** , která ukazuje, že pro tyto identifikátory URI je povolená podpora CORS s tokem autorizačního kódu a PKCE.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Dlaždice jednostránkové aplikace v registraci aplikace v Azure Portal":::

Místo aktualizace identifikátorů URI přesměrování v existující registraci můžete také [vytvořit novou registraci aplikace](scenario-spa-app-registration.md) .

## <a name="update-your-code-to-msaljs-2x"></a>Aktualizujte kód na MSAL.js 2. x

V MSAL 1. x jste vytvořili instanci aplikace inicializací [UserAgentApplication][msal-js-useragentapplication] následujícím způsobem:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

V MSAL 2. x inicializujte místo toho [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Úplný návod k přidání MSAL 2. x do vaší aplikace najdete v tématu [kurz: přihlášení uživatelů a volání rozhraní API Microsoft Graph z jednostránkové aplikace v JavaScriptu (Spa) pomocí toku kódu ověřování](tutorial-v2-javascript-auth-code.md).

Další změny, které budete muset provést v kódu, najdete v [Průvodci migrací](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) na GitHubu.

## <a name="disable-implicit-grant-settings"></a>Zakázat nastavení implicitního udělení

Po aktualizaci všech produkčních aplikací, které používají tuto registraci aplikace, a jejího ID klienta, aby MSAL 2. x a tok autorizačního kódu, byste měli v registraci aplikace zrušit kontrolu nastavení implicitního udělení.

Když zrušíte kontrolu nastavení implicitního udělení v registraci aplikace, implicitní tok se zakáže pro všechny aplikace, které používají registraci a její ID klienta.

**Nepovolujte** tok implicitního udělení před aktualizací všech aplikací na MSAL.js 2. x a [PublicClientApplication][msal-js-publicclientapplication].

## <a name="next-steps"></a>Další kroky

Další informace o toku autorizačního kódu, včetně rozdílů mezi toky implicitního a ověřovacího kódu, najdete v části [Microsoft Identity Platform a tok autorizačního kódu OAuth 2,0](v2-oauth2-auth-code-flow.md).

Pokud byste chtěli podrobně hlubší vývoj aplikací v JavaScriptu na platformě Microsoft Identity Platform, scénář s více částmi: řada článků s [jednou stránkou](scenario-spa-overview.md) vám může pomáhat začít.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html

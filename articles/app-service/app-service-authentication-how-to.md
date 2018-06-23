---
title: Přizpůsobení ověřování a autorizace ve službě Azure App Service | Microsoft Docs
description: Ukazuje, jak přizpůsobit ověřování a autorizace ve službě App Service a získat deklarace identity uživatele a jiné tokeny.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: 688ea090384755b9a6d60a4968d958678edc27ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337853"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Přizpůsobení ověřování a autorizace ve službě Azure App Service

Tento článek ukazuje, jak přizpůsobit [ověřování a autorizace ve službě App Service](app-service-authentication-overview.md)a jak spravovat identitu z vaší aplikace. 

Abyste mohli rychle začít, najdete v jednom z následujících kurzů:

* [Kurz: Ověřování a autorizaci uživatelů klient server v Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Kurz: Ověřování a autorizaci uživatelů klient server v Azure App Service pro Linux](containers/tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](app-service-mobile-how-to-configure-google-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Konfigurace více možností přihlášení

Konfigurace portálu nenabízí klíč způsob, jak k dispozici více možností přihlášení pro vaše uživatele (například Facebook nebo Twitter). Však není pro přidání funkce do vaší webové aplikace. Kroky jsou uvedeny následujícím způsobem:

V první **ověřování / autorizace** na portálu Azure, nakonfigurujte všechny zprostředkovatele identity, které chcete povolit.

V **akci provést, když požadavek nebude ověřený**, vyberte **povolit anonymní žádosti (žádná akce)**.

V přihlašovací stránky, nebo na navigačním panelu nebo v jiném umístění vaší webové aplikace, přidejte přihlašovací odkaz ke každému zprostředkovatele jste povolili (`/.auth/login/<provider>`). Příklad:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře se stránka příslušných přihlášení pro přihlášení uživatele.

## <a name="access-user-claims"></a>Deklarace identity uživatele přístup

Služby App Service předá deklarace identity uživatelů do vaší aplikace pomocí speciálními záhlavími. Externí požadavky nejsou povoleny nastavit tyto hlavičky, takže jsou k dispozici pouze v případě nastavení službou App Service. Některé příklad hlavičky zahrnují:

* X-MS-KLIENTA HLAVNÍ NÁZEV
* X-MS-CLIENT-HLAVNÍ ID

Kód, který je napsán v libovolném jazyce nebo rozhraní můžete získat informace, které se musí z těchto hlavičky. Pro aplikace ASP.NET 4.6 **ClaimsPrincipal** bude automaticky nastavena s příslušnými hodnotami.

Aplikace můžete také získat další informace o ověřený uživatel voláním `/.auth/me`. Server Mobile Apps sady SDK poskytují pomocné metody pro práci s těmito daty. Další informace najdete v tématu [jak používat Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), a [pracovat s .NET back-end serveru SDK pro Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Načíst tokeny v kódu aplikace

Z vašeho kódu serveru specifický pro zprostředkovatele tokeny jsou vloženy do hlavička požadavku tak lze snadno přistupovat. Následující tabulka uvádí možné tokenu hlavičky názvy:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token služby Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Účet Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

V kódu klienta (například mobilní aplikace nebo JavaScript v prohlížeči), odeslat HTTP `GET` žádost o `/.auth/me`. Vrácený JSON obsahuje tokenů specifický pro zprostředkovatele.

> [!NOTE]
> Tokeny přístupu jsou pro přístup k prostředkům poskytovatele, takže jsou k dispozici pouze v případě, že nakonfigurujete zprostředkovatele s tajný klíč klienta. Jak získat tokeny obnovení najdete v sekci [aktualizace přístupové tokeny](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Aktualizujte přístupové tokeny

Když vyprší platnost přístupového tokenu svého poskytovatele, musíte k novému ověření uživatele. Vypršení platnosti tokenu se můžete vyhnout tím, že `GET` volat na `/.auth/refresh` koncový bod vaší aplikace. Při volání, služby App Service automaticky aktualizuje přístupových tokenů v úložišti tokenu pro ověřené uživatele. Odeslání dalších žádostí o tokeny pomocí kódu aplikace získat aktualizovat tokeny. Ale tokenu obnovení fungovat, musí obsahovat úložiště tokenů [obnovovacích tokenů](https://auth0.com/learn/refresh-tokens/) pro poskytovatele. Způsob, jak získat tokeny obnovení popsané každého poskytovatele, ale v následujícím seznamu je uveden stručný přehled:

- **Google**: připojení `access_type=offline` parametr řetězce k dotazu vaše `/.auth/login/google` volání rozhraní API. Pokud používáte sadu SDK Mobile Apps, můžete přidat parametr do jednoho z `LogicAsync` přetížení (najdete v části [Google aktualizovat tokeny](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: neposkytuje obnovovacích tokenů. Dlouhodobé tokeny vyprší za 60 dnů (najdete v části [vypršení platnosti Facebook a rozšíření přístupové tokeny](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: přístupové tokeny nevyprší (najdete v části [Twitter – nejčastější dotazy OAuth](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq)).
- **Microsoft Account**: když [konfiguraci nastavení ověřování účtu Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), vyberte `wl.offline_access` oboru.
- **Azure Active Directory**: V [ https://resources.azure.com ](https://resources.azure.com), proveďte následující kroky:
    1. V horní části stránky, vyberte **pro čtení a zápis**.
    1. In the left browser, navigate to **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 
    1. Klikněte na **Upravit**.
    1. Upravte následující vlastnosti. Nahraďte  _\<aplikace\_id >_ s ID aplikace Azure Active Directory, služby, které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Klikněte na tlačítko **Put**. 

Jakmile poskytovatel nakonfigurovaný, můžete [najít tokenu obnovení a dobu vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenu. 

Pokud chcete v kdykoli aktualizovat přístupový token, stačí zavolat `/.auth/refresh` v libovolném jazyce. Následující fragment kódu používá jQuery aktualizovat vaše přístupové tokeny z klienta JavaScript.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Pokud uživatel Odvolá oprávnění udělená aplikaci volání `/.auth/me` může selhat s `403 Forbidden` odpovědi. Při diagnostice chyb, zkontrolujte podrobnosti v protokolech vaší aplikace.

## <a name="extend-session-expiration-grace-period"></a>Období odkladu vypršení platnosti relace

Po vypršení platnosti ověřená relace je ve výchozím nastavení období odkladu 72 hodin. Během tohoto období odkladu máte oprávnění k aktualizaci souboru cookie relace nebo token relace službou App Service bez opětovném uživatele. Právě můžete volat `/.auth/refresh` při souboru cookie relace nebo token relace stává neplatným a vy nemusíte sledovat vypršení platnosti tokenu sami. Po 72 hodin poskytnutá lhůta je dojde k chybě, uživatel musí přihlásit znovu získat platný soubor cookie nebo token relace.

Pokud 72 hodin není dostatek času pro vás, můžete rozšířit toto okno vypršení platnosti. Rozšíření vypršení platnosti po dlouhou dobu, může mít vliv na důležité zabezpečení (například pokud je ověřovací token úniku nebo odcizení). Proto by měla necháte nastavenou výchozí 72 hodin nebo nastavte období rozšíření na nejmenší hodnota.

Pokud chcete rozšířit okno Výchozí vypršení platnosti, spusťte následující příkaz [cloudové prostředí](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Období odkladu se vztahuje pouze k ověření služby App Service relaci, není tokeny od zprostředkovatele identity. Neexistuje žádné období odkladu pro tokeny vypršela platnost zprostředkovatele. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény účtů přihlášení

Account Microsoft a Azure Active Directory umožňuje přihlášení z několika domén. Umožňuje například Account Microsoft _outlook.com_, _live.com_, a _hotmail.com_ účty. Azure Active Directory umožňuje libovolný počet vlastních domén pro účty přihlášení. Toto chování může být žádoucí pro interní aplikace, které nechcete, aby každý, kdo má _outlook.com_ účet přístup. Chcete-li omezit názvu domény účtů přihlásit, postupujte takto.

In [https://resources.azure.com](https://resources.azure.com), navigate to **subscriptions** > **_\<subscription\_name_** > **resourceGroups** > _**\<resource\_group\_name>**_ > **providers** > **Microsoft.Web** > **sites** > _**\<app\_name>**_ > **config** > **authsettings**. 

Klikněte na tlačítko **upravit**, upravte vlastnost následující a pak klikněte na tlačítko **Put**. Nezapomeňte nahradit  _\<domény\_name >_ s doménou, které chcete.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Ověřování a autorizaci uživatelů začátku do konce (Windows)](app-service-web-tutorial-auth-aad.md)
> [kurz: ověřování a autorizaci uživatelů začátku do konce (Linux)](containers/tutorial-auth-aad.md)
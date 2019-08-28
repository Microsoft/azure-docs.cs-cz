---
title: Pokročilé použití ověřování a autorizace – Azure App Service | Microsoft Docs
description: Ukazuje, jak přizpůsobit ověřování a autorizaci v App Service a získat deklarace identity uživatelů a různé tokeny.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ee8d8c54bd618780e00d9975f2fc6950cd795d44
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098549"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Rozšířené použití ověřování a autorizace v Azure App Service

V tomto článku se dozvíte, jak přizpůsobit integrované [ověřování a autorizaci v App Service](overview-authentication-authorization.md)a spravovat identitu z vaší aplikace. 

Pokud chcete rychle začít, přečtěte si jedno z následujících kurzů:

* [Kurz: Ověřování a autorizace uživatelů na konci Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Kurz: Ověřování a autorizace uživatelů na konci Azure App Service pro Linux](containers/tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](configure-authentication-provider-aad.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](configure-authentication-provider-facebook.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](configure-authentication-provider-google.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](configure-authentication-provider-microsoft.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Použití více poskytovatelů přihlašování

Konfigurace portálu nenabízí způsob, jak pro uživatele prezentovat více poskytovatelů přihlášení (například Facebook i Twitter). Do vaší aplikace ale není obtížné přidávat funkce. Postup je popsaný následujícím způsobem:

Nejprve na stránce **ověřování/autorizace** v Azure Portal nakonfigurujte každého poskytovatele identity, kterého chcete povolit.

V **akci, která se má provést, když se žádost neověřuje**, vyberte možnost **povoluje anonymní žádosti (bez akce)** .

Na přihlašovací stránce nebo v navigačním panelu nebo jakémkoli jiném umístění aplikace přidejte odkaz pro přihlášení ke každému poskytovateli, který jste povolili (`/.auth/login/<provider>`). Příklad:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře se příslušná přihlašovací stránka pro přihlášení uživatele.

Pokud chcete přesměrovat uživatele po přihlášení na vlastní adresu URL, použijte `post_login_redirect_url` parametr řetězce dotazu (Nezaměňujte ho pomocí identifikátoru URI přesměrování v konfiguraci zprostředkovatele identity). Chcete-li například procházet uživatele `/Home/Index` po přihlášení, použijte následující kód HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Ověřit tokeny od zprostředkovatelů

V přihlašování klienta se aplikace přihlásí k poskytovateli ručně a poté odešle ověřovací token k App Service k ověření (viz [tok ověřování](overview-authentication-authorization.md#authentication-flow)). Toto ověření sama o sobě neuděluje přístup k požadovaným prostředkům aplikace, ale úspěšné ověření vám poskytne token relace, který můžete použít pro přístup k prostředkům aplikace. 

Pokud chcete ověřit token poskytovatele, App Service aplikace musí být nejdřív nakonfigurované s požadovaným poskytovatelem. Po načtení tokenu ověřování od poskytovatele za běhu vystavte token `/.auth/login/<provider>` pro ověření. Příklad: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Formát tokenu se mírně liší v závislosti na poskytovateli. Podrobnosti najdete v následující tabulce:

| Hodnota poskytovatele | Vyžadováno v textu žádosti | Komentáře |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` Vlastnost je nepovinná. <br/>Při žádosti o token ze služby Live Services vždy vyžádat `wl.basic` rozsah. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` Vlastnost je nepovinná. Je-li tento parametr zadán, může být také případně doplněn `redirect_uri` vlastností. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Použijte platný [přístupový token uživatele](https://developers.facebook.com/docs/facebook-login/access-tokens) z Facebooku. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

V případě úspěšného ověření tokenu poskytovatele vrátí `authenticationToken` rozhraní API text v odpovědi, který je vaším tokenem relace. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Po vytvoření tohoto tokenu relace můžete získat přístup k prostředkům chráněných aplikací přidáním `X-ZUMO-AUTH` hlavičky do požadavků HTTP. Příklad: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Odhlásit se z relace

Uživatelé můžou zahájit odhlášení odesláním `GET` žádosti do `/.auth/logout` koncového bodu aplikace. `GET` Požadavek provede následující akce:

- Vymaže soubory cookie ověřování z aktuální relace.
- Odstraní tokeny aktuálního uživatele z úložiště tokenů.
- Pro Azure Active Directory a Google provede na poskytovateli identity přihlášení na straně serveru.

Tady je jednoduchý odkaz na odhlášení na webové stránce:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Ve výchozím nastavení se při úspěšném odhlášení přesměruje klient na adresu URL `/.auth/logout/done`. Stránku přesměrování po odhlášení můžete změnit přidáním `post_logout_redirect_uri` parametru dotazu. Příklad:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Doporučuje se [kódovat](https://wikipedia.org/wiki/Percent-encoding) hodnotu `post_logout_redirect_uri`.

Při použití plně kvalifikovaných adres URL musí být adresa URL buď hostovaná ve stejné doméně, nebo nakonfigurovaná jako povolená externí adresa URL pro přesměrování vaší aplikace. V následujícím příkladu pro přesměrování na `https://myexternalurl.com` , který není hostovaný ve stejné doméně:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

V [Azure Cloud Shell](../cloud-shell/quickstart.md)musíte spustit následující příkaz:

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachovat fragmenty adresy URL

Až se uživatelé přihlásí do aplikace, obvykle chtějí být přesměrováni na stejnou část stejné stránky, `/wiki/Main_Page#SectionZ`jako je například. Protože se ale [Fragmenty adresy URL](https://wikipedia.org/wiki/Fragment_identifier) (například `#SectionZ`) nikdy neodesílají na server, po dokončení přihlášení OAuth a přesměrování zpátky do vaší aplikace se ve výchozím nastavení nezachovají. Uživatelé pak dostanou optimální prostředí, když potřebují znovu přejít k požadovanému kotvu. Toto omezení se vztahuje na všechna řešení ověřování na straně serveru.

V App Service ověřování můžete zachovat fragmenty adresy URL v rámci přihlášení OAuth. To provedete tak, že nastavíte `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` nastavení `true`aplikace s názvem na. Můžete to provést v [Azure Portal](https://portal.azure.com)nebo stačí spustit následující příkaz v [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Přístup k deklaracím uživatelů

App Service předá do vaší aplikace deklarace identity uživatelů pomocí speciálních hlaviček. Externí požadavky nemají povolené nastavení těchto hlaviček, takže jsou k dispozici pouze v případě, že jsou nastaveny pomocí App Service. Mezi příklady hlaviček patří:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kód, který je napsán v libovolném jazyce nebo v rozhraní, může získat informace, které potřebuje z těchto hlaviček. Pro aplikace ASP.NET 4,6 se **ClaimsPrincipal** automaticky nastaví s příslušnými hodnotami.

Vaše aplikace může také získat další podrobnosti o ověřeném uživateli voláním `/.auth/me`. Sady SDK Mobile Apps serveru poskytují pomocné metody pro práci s těmito daty. Další informace najdete v tématu [Jak používat sadu azure Mobile Apps Node. js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)a [pracovat s back-end serverem .net pro Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Načtení tokenů v kódu aplikace

Z kódu serveru jsou tokeny specifické pro poskytovatele vloženy do hlavičky žádosti, takže k nim můžete snadno přistupovat. V následující tabulce jsou uvedeny možné názvy hlaviček tokenů:

| Poskytovatel | Názvy hlaviček |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token Facebooku | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Účet Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z kódu klienta (například mobilní aplikace nebo JavaScript v prohlížeči) odešle požadavek HTTP `GET` na. `/.auth/me` Vrácený kód JSON má tokeny specifické pro poskytovatele.

> [!NOTE]
> Přístupové tokeny slouží k přístupu k prostředkům poskytovatele, takže jsou k dispozici pouze v případě, že poskytovatele nakonfigurujete pomocí tajného klíče klienta. Informace o tom, jak získat aktualizační tokeny, najdete v tématu aktualizace přístupových tokenů.

## <a name="refresh-identity-provider-tokens"></a>Aktualizovat tokeny zprostředkovatele identity

Když vyprší platnost přístupového tokenu poskytovatele (ne [tokenu relace](#extend-session-token-expiration-grace-period)), budete muset uživatele před opětovným použitím tohoto tokenu znovu ověřit. Vypršení platnosti tokenu se můžete vyhnout `GET` provedením volání `/.auth/refresh` koncového bodu aplikace. Při volání App Service automaticky aktualizuje přístupové tokeny v úložišti tokenů pro ověřeného uživatele. Následné žádosti o tokeny pomocí kódu vaší aplikace získají aktualizované tokeny. Aby ale aktualizace tokenu fungovala, musí úložiště tokenů obsahovat [aktualizační tokeny](https://auth0.com/learn/refresh-tokens/) pro vašeho poskytovatele. Způsob získání aktualizačních tokenů je popsán u každého poskytovatele, ale následující seznam je stručným shrnutím:

- **Google**: Přidejte parametr řetězce `/.auth/login/google` dotazu do volání rozhraní API. `access_type=offline` Pokud používáte sadu Mobile Apps SDK, můžete do jednoho z `LogicAsync` přetížení přidat parametr (viz [aktualizace tokenů Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Neposkytuje aktualizační tokeny. Do vypršení platnosti tokenů po dobu 60 dnů (viz [doba ukončení a rozšíření přístupových tokenů Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: Platnost přístupových tokenů nevyprší (viz [Nejčastější dotazy k Twitteru OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Účet Microsoft**: Při [konfiguraci nastavení ověřování účtu Microsoft](configure-authentication-provider-microsoft.md)vyberte `wl.offline_access` obor.
- **Azure Active Directory**: V [https://resources.azure.com](https://resources.azure.com)nástroji proveďte následující kroky:
    1. V horní části stránky vyberte možnost **čtení/zápis**.
    2. V levém prohlížeči přejděte na předplatná >  **_\<název\__**  > předplatného**resourceGroups** >  **_Skupinaprostředků\_.\< název\_>_** **poskytovatelé** **Microsoft. Web** **sites** **_název aplikace>\_config\<_**  >  >  >   >  >   >  **authsettings**. 
    3. Klikněte na **Upravit**.
    4. Upravte následující vlastnost. Nahraďte  _\<\_ID aplikace >_ kódem Azure Active Directory ID aplikace služby, ke které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klikněte na tlačítko **Vložit**. 

Po nakonfigurování zprostředkovatele můžete [Najít obnovovací token a čas vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenů. 

Chcete-li kdykoli aktualizovat přístupový token, stačí volat `/.auth/refresh` v libovolném jazyce. Následující fragment kódu používá jQuery k aktualizaci přístupových tokenů z JavaScriptového klienta.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Pokud uživatel Odvolá oprávnění udělená vaší aplikaci, volání `/.auth/me` může selhat `403 Forbidden` s odpovědí. Chcete-li diagnostikovat chyby, zkontrolujte podrobnosti v protokolech aplikace.

## <a name="extend-session-token-expiration-grace-period"></a>Doba odkladu pro prodloužení platnosti tokenu relace

Platnost ověřené relace vyprší po 8 hodinách. Po vypršení platnosti ověřené relace je ve výchozím nastavení k dispozici období odkladu 72 hodin. V rámci této lhůty odkladu je dovoleno aktualizovat token relace pomocí App Service bez opětovného ověření uživatele. Můžete zavolat `/.auth/refresh` pouze v případě, že je token relace neplatný a nemusíte sledovat vypršení platnosti tokenu sami. Po uplynutí doby 72 hodin se uživatel musí znovu přihlásit, aby získal platný token relace.

Pokud pro vás není dostatek času 72 hodin, můžete toto okno vypršení platnosti zvětšit. Prodloužení platnosti po dlouhou dobu může mít významný vliv na zabezpečení (například při nevracení nebo odcizení ověřovacího tokenu). Proto byste měli ponechat výchozí 72 hodiny nebo nastavit dobu rozšíření na nejmenší hodnotu.

Chcete-li zvětšit výchozí okno vypršení platnosti, spusťte následující příkaz v [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Doba odkladu se vztahuje pouze na App Service ověřená relace, nikoli na tokeny od zprostředkovatelů identity. Pro tokeny poskytovatele s vypršenou platností se neplatí žádné období odkladu. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény přihlašovacích účtů

Účet Microsoft i Azure Active Directory vám umožňují přihlašovat se z více domén. Například účet Microsoft umožňuje účty _Outlook.com_, _Live.com_a _hotmail.com_ . Azure Active Directory umožňuje pro přihlašovací účty libovolný počet vlastních domén. Toto chování může být nežádoucí pro interní aplikaci, u které nechcete, aby k nim měl nikdo účet _Outlook.com_ . Chcete-li omezit název domény přihlašovacích účtů, postupujte podle těchto kroků.

V [https://resources.azure.com](https://resources.azure.com)přejděte na předplatná > název předplatného resourceGroups **_\_\<_** prostředek > . >  **_\<\_ název\_ skupiny >_** **poskytovatelé** **Microsoft. Web**sites >  **_název aplikace\_ >\<_**  >  >  >  >  **Konfigurace**  >  **authsettings**. 

Klikněte na tlačítko **Upravit**, upravte následující vlastnost a pak klikněte na tlačítko **Vložit**. Nezapomeňte nahradit  _\<\_název domény >_ doménou, kterou chcete.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Kurz pro ověřování a autorizaci uživatelů pro koncové uživatele (Windows)](app-service-web-tutorial-auth-aad.md):
> [ Kompletní ověřování a autorizace uživatelů (Linux)](containers/tutorial-auth-aad.md)

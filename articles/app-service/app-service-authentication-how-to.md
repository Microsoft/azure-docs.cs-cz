---
title: Rozšířené využití ověřování a autorizace ve službě Azure App Service | Dokumentace Microsoftu
description: Ukazuje, jak přizpůsobit ověřování a autorizace ve službě App Service a získat deklarace identity uživatelů a různé tokeny.
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
ms.date: 11/08/2018
ms.author: cephalin
ms.openlocfilehash: e1109ec8cc98c7e5fc72d7f56ade19968b0056cc
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51685323"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Rozšířené využití ověřování a autorizace ve službě Azure App Service

V tomto článku se dozvíte, jak přizpůsobit předdefinované [ověřování a autorizace ve službě App Service](app-service-authentication-overview.md)a jak spravovat identitu z vaší aplikace. 

Abyste mohli rychle začít, najdete v jednom z následujících kurzů:

* [Kurz: Ověřování a autorizace uživatelů začátku do konce ve službě Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Kurz: Ověřování a autorizace uživatelů začátku do konce ve službě Azure App Service pro Linux](containers/tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](app-service-mobile-how-to-configure-google-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="use-multiple-sign-in-providers"></a>Použití více poskytovatelů přihlášení

Konfigurace portálu nenabízí klíč způsob, jak k dispozici více zprostředkovatelů přihlašování pro vaše uživatele (například Facebook a Twitter). Však není pro přidání funkce do vaší webové aplikace. Kroky jsou uvedeny následovně:

V první **ověřování / autorizace** stránce na webu Azure Portal, nakonfigurujte všechny zprostředkovatele identity, které chcete povolit.

V **akce má být provedena, když požadavek nebude ověřený**vyberte **povolit anonymní požadavky (NIC)**.

V přihlašovací stránku, nebo na navigačním panelu nebo jiného umístění vaší webové aplikace, přidejte odkaz přihlásit ke každému zprostředkovatele jste povolili (`/.auth/login/<provider>`). Příklad:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře příslušné přihlašovací stránku pro přihlášení uživatele.

Chcete-li přesměrovat uživatele po-přihlášení se změnami na vlastní adresu URL, použijte `post_login_redirect_url` parametr (ne by se zaměňovat s identifikátorem URI pro přesměrování v konfiguraci zprostředkovatele identity) řetězce dotazu. Například přesměrovat uživatele na `/Home/Index` po přihlášení, použijte následující kód HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Ověřovat tokeny od poskytovatelů

V u klienta přesměruje přihlášení, aplikace přihlásí uživatele ke zprostředkovateli ručně a poté ho předá ověřovací token do služby App Service pro ověřování (viz [tok ověřování](app-service-authentication-overview.md#authentication-flow)). Toto ověření samotný není ve skutečnosti udělit přístup k prostředkům požadovanou aplikaci, ale úspěšné ověření získáte token relace, který můžete použít pro přístup k prostředkům aplikace. 

Ověřit token poskytovatele, aplikaci služby App Service musíte nejdřív nakonfigurovat požadovaného poskytovatele. Za běhu a po získání ověřovacího tokenu od poskytovatele odeslat token, který má `/.auth/login/<provider>` pro ověření. Příklad: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Formát tokenu se může mírně lišit podle poskytovatele. Podrobnosti najdete v následující tabulce:

| Hodnota zprostředkovatele | Vyžaduje v textu požadavku | Komentáře |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in` Vlastnost je volitelná. <br/>Při požadování tokenu z Live services, vždy požadavku `wl.basic` oboru. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code` Vlastnost je volitelná. -Li zadána, ji můžou také v případě potřeby doprovázet `redirect_uri` vlastnost. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Použijte platný [přístupový token uživatele](https://developers.facebook.com/docs/facebook-login/access-tokens) ze sítě Facebook. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

V případě poskytovatele tokenu ověření úspěšně, rozhraní API vrátí `authenticationToken` v těle odpovědi, což je tokenu relace. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Jakmile budete mít tento token relace, můžete přístup k prostředkům chráněné aplikace, tak, že přidáte `X-ZUMO-AUTH` záhlaví na požadavky HTTP. Příklad: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Odhlaste se z relace

Uživatelé mohou spustit odesláním odhlášení `GET` požadavek na aplikaci `/.auth/logout` koncového bodu. `GET` Požadavek provede následující akce:

- Vymaže soubory cookie pro ověřování z aktuální relace.
- Odstraní aktuální uživatel tokeny z úložiště tokenů.
- Pro Azure Active Directory a Google provede serverové odhlašování zprostředkovatele identity.

Tady je jednoduchý odhlašování odkaz na webové stránce:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Ve výchozím nastavení úspěšném odhlášení přesměruje klienta na adresu URL `/.auth/logout/done`. Na stránce post-sign-out přesměrování můžete změnit tak, že přidáte `post_logout_redirect_uri` parametr dotazu. Příklad:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Doporučujeme vám [kódování](https://wikipedia.org/wiki/Percent-encoding) hodnotu `post_logout_redirect_uri`.

Při používání plně kvalifikovaných adres URL, adresa URL musí být hostované ve stejné doméně nebo nakonfigurovat jako adresu URL povolené externí přesměrování pro aplikaci. V následujícím příkladu, a přesměrujte `https://myexternalurl.com` , který není hostované ve stejné doméně:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Spuštěním následujícího příkazu [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachovat fragmenty adresy URL

Po přihlášení k aplikaci, obvykle chtějí přesměrovat do stejné části na stejné stránce, jako například `/wiki/Main_Page#SectionZ`. Ale protože [URL fragmenty](https://wikipedia.org/wiki/Fragment_identifier) (například `#SectionZ`) se nikdy neodesílá do serveru, nejsou zachovány ve výchozím nastavení po přihlášení OAuth dokončí a přesměruje zpátky do vaší aplikace. Uživatelé pak získat neoptimální prostředí, když je budou potřebovat znovu přejděte do požadovaného ukotvení. Toto omezení se vztahuje na všechna řešení pro ověřování na straně serveru.

Při ověřování služby App Service můžete zachovat URL fragmenty napříč přihlášení OAuth. K tomu, nastavit aplikaci názvem `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` k `true`. Můžete to provést [webu Azure portal](https://portal.azure.com), nebo jednoduše spuštěním následujícího příkazu [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Deklarace identity uživatele přístup

App Service předává pomocí speciálními záhlavími deklarace identity uživatelů do vaší aplikace. Požadavky na externí nejsou povoleny tyto hlavičky nastavit tak, aby byly k dispozici pouze v případě nastavení ve službě App Service. Některé hlavičky příkladu patří:

* X-MS-KLIENTA PRINCIPAL-NAME
* X-MS-CLIENT-INSTANČNÍ OBJEKT ID

Kód, který je napsán v libovolném jazyce nebo rozhraní můžete získat informace, které je nutné z těchto záhlaví. Pro aplikace ASP.NET 4.6 **ClaimsPrincipal** se automaticky nastaví příslušnými hodnotami.

Aplikace můžete také získat další informace o ověřeném uživateli voláním `/.auth/me`. Sadami SDK serveru Mobile Apps poskytuje pomocné metody pro práci s těmito daty. Další informace najdete v tématu [jak používat Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), a [pracovat s back-end .NET server SDK pro Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Načítání tokenů v kódu aplikace

Z kódu serveru jsou specifické pro zprostředkovatele tokenů vloženy do hlavičky požadavku tak budete mít snadný přístup. Následující tabulka uvádí možné tokenu hlavičky názvy:

| Poskytovatel | Názvy záhlaví |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Token služby Facebook | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Účet Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z klientského kódu (například mobilní aplikace nebo jazyka JavaScript v prohlížeči), odeslat HTTP `GET` žádat o `/.auth/me`. Vrácený JSON obsahuje tokeny specifickým pro zprostředkovatele.

> [!NOTE]
> Přístupové tokeny jsou určené pro přístup k prostředkům zprostředkovatele, takže jsou k dispozici pouze v případě, že nakonfigurujete poskytovatele s tajným klíčem klienta. Jak získat tokeny obnovení najdete v tématu [aktualizaci přístupových tokenů](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Obnovit přístupové tokeny

Když vyprší platnost přístupového tokenu poskytovatele, musíte uživatele donutit k. Vypršení platnosti tokenu se můžete vyhnout tím, že `GET` volání `/.auth/refresh` koncový bod aplikace. Při volání služby App Service automaticky aktualizuje přístupové tokeny v úložišti tokenů pro ověřeného uživatele. Odeslání dalších žádostí o tokeny pomocí kódu vaší aplikace získáte aktualizovat tokeny. Ale token aby aktualizace fungovala, musí obsahovat úložiště tokenů [obnovovacích tokenů](https://auth0.com/learn/refresh-tokens/) pro vašeho poskytovatele. Způsob, jak získat tokeny obnovení jsou popsány od každého poskytovatele, ale v následujícím seznamu je uveden stručný přehled:

- **Google**: připojení `access_type=offline` parametr řetězce do dotazu vaše `/.auth/login/google` volání rozhraní API. Pokud pomocí sady SDK služby Mobile Apps, můžete přidat parametr do jednoho z `LogicAsync` přetížení (naleznete v tématu [Google aktualizovat tokeny](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: neposkytuje obnovovací tokeny. Dlouhodobé tokeny vyprší za 60 dní (naleznete v tématu [Facebook vypršení platnosti a rozšíření přístupové tokeny](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: přístupové tokeny nevyprší (naleznete v tématu [nejčastější dotazy k Twitteru OAuth](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq)).
- **Microsoft Account**: při [nastavení ověřování účtu Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md), vyberte `wl.offline_access` oboru.
- **Azure Active Directory**: V [ https://resources.azure.com ](https://resources.azure.com), proveďte následující kroky:
    1. V horní části stránky vyberte **r/w**.
    1. V levém prohlížeč, přejděte na **předplatná** > **_\<předplatné\_název_**   >  **resourceGroups** > _**\<prostředků\_skupiny\_name >**_   >  **poskytovatelé** > **Microsoft.Web** > **lokality** > _**\<aplikace \_name >**_ > **config** > **authsettings**. 
    1. Klikněte na **Upravit**.
    1. Upravte následující vlastnosti. Nahraďte  _\<aplikace\_id >_ s ID aplikace Azure Active Directory, služby, které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Klikněte na tlačítko **umístit**. 

Po nakonfigurování poskytovatele můžete [najít tokenu obnovení a dobu vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenů. 

Kdykoli aktualizovat váš přístupový token, stačí zavolat `/.auth/refresh` v libovolném jazyce. Následující fragment kódu používá jQuery aktualizovat vaše přístupové tokeny z klienta jazyka JavaScript.

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

Pokud uživatel Odvolá oprávnění udělená aplikaci volání `/.auth/me` může selhat s `403 Forbidden` odpovědi. Chcete-li diagnostikovat chyby, najdete v protokolech aplikace podrobnosti.

## <a name="extend-session-expiration-grace-period"></a>Rozšíření období odkladu vypršení platnosti relace

Po vypršení platnosti ověřená relace, je ve výchozím nastavení období odkladu 72 hodin. Během tohoto období odkladu povolená aktualizace souboru cookie relace nebo tokenu relace se službou App Service bez opětovném uživatele. Můžete ho prostě zavoláte `/.auth/refresh` při souboru cookie relace nebo tokenu relace stává neplatným a není nutné ke sledování vypršení platnosti tokenu sami. Po období odkladu 72 hodin zavřeli, uživatel musí přihlásit znovu a získat platný soubor cookie nebo tokenu relace.

Pokud 72 hodin není dostatečně dlouho, můžete rozšířit toto okno vypršení platnosti. Rozšíření vypršení platnosti po dlouhou dobu, může mít vliv na důležité zabezpečení (například když ověřovací token úniku nebo vám ho někdo ukradne). Proto by měl ponechat výchozí 72 hodin nebo nastavte období na nejmenší hodnotu.

Rozšířit okno Výchozí vypršení platnosti, spusťte následující příkaz [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Období odkladu platí jenom pro relace ověření služby App Service, ne tokeny od poskytovatelů identit. Neexistuje žádné období odkladu pro vypršela platnost poskytovatele tokenů. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény účty přihlášení

Account Microsoft a Azure Active Directory umožňuje přihlášení z několika domén. Například Microsoft Account umožňuje _outlook.com_, _live.com_, a _hotmail.com_ účty. Azure Active Directory umožňuje libovolný počet vlastních domén pro účty přihlášení. Toto chování může způsobit nežádoucí pro interní aplikace, které nechcete, aby každý, kdo má _outlook.com_ účet pro přístup. Pokud chcete omezit název domény účtů přihlásit, postupujte takto.

V [ https://resources.azure.com ](https://resources.azure.com), přejděte na **předplatná** > **_\<předplatné\_název_**   >  **resourceGroups** > _**\<prostředků\_skupiny\_name >**_   >  **poskytovatelé** > **Microsoft.Web** > **lokality**  >    _**\<aplikace\_name >**_ > **config** > **authsettings**. 

Klikněte na tlačítko **upravit**upravit následující vlastnosti a pak klikněte na tlačítko **umístit**. Nezapomeňte nahradit  _\<domény\_name >_ s doménou chcete.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Ověřování a autorizace uživatelů začátku do konce (Windows)](app-service-web-tutorial-auth-aad.md)
> [kurz: ověřování a autorizaci uživatelů začátku do konce (Linux)](containers/tutorial-auth-aad.md)
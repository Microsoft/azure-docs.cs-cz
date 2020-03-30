---
title: Pokročilé použití AuthN/AuthO
description: Naučte se přizpůsobit funkci ověřování a autorizace ve službě App Service pro různé scénáře a získat deklarace identity uživatelů a různé tokeny.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280830"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Pokročilé využití ověřování a autorizace ve službě Azure App Service

Tento článek ukazuje, jak přizpůsobit integrované [ověřování a autorizaci ve službě App Service](overview-authentication-authorization.md)a spravovat identitu z vaší aplikace. 

Chcete-li začít rychle, podívejte se na jeden z následujících kurzů:

* [Kurz: Ověření a autorizace uživatelů od konce ve službě Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Kurz: Ověření a autorizace uživatelů od konce ve službě Azure App Service pro Linux](containers/tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](configure-authentication-provider-aad.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](configure-authentication-provider-facebook.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](configure-authentication-provider-google.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](configure-authentication-provider-microsoft.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Použití více poskytovatelů přihlášení

Konfigurace portálu nenabízí způsob, jak na klíč představit uživatelům více poskytovatelů přihlášení (například Facebook a Twitter). Není však obtížné přidat funkce do aplikace. Postup je popsán takto:

Nejprve na stránce **Ověřování / Autorizace** na webu Azure Portal nakonfigurujte každého zprostředkovatele identity, kterého chcete povolit.

V **akci, která má být v případě, že požadavek není ověřen**, vyberte **povolit anonymní požadavky (žádná akce).**

Na přihlašovací stránce, na navigačním panelu nebo v jakémkoli jiném umístění aplikace přidejte přihlašovací odkaz`/.auth/login/<provider>`ke každému z poskytovatelů, které jste povolili ( . Například:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře se příslušná přihlašovací stránka pro přihlášení uživatele.

Chcete-li uživatele po přihlášení přesměrovat na vlastní `post_login_redirect_url` adresu URL, použijte parametr řetězce dotazu (nezaměňovat s identifikátorem URI přesměrování v konfiguraci poskytovatele identity). Chcete-li například přejít uživatele po `/Home/Index` přihlášení, použijte následující kód HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Ověřit tokeny od poskytovatelů

V přihlášení řízeném klientem aplikace přihlásí uživatele k poskytovateli ručně a potom odešle ověřovací token službě App Service pro ověření (viz [Tok ověřování](overview-authentication-authorization.md#authentication-flow)). Toto ověření samo o sobě ve skutečnosti neuděluje přístup k požadovaným prostředkům aplikace, ale úspěšné ověření vám poskytne token relace, který můžete použít pro přístup k prostředkům aplikace. 

Chcete-li ověřit token zprostředkovatele, aplikace App Service musí být nejprve nakonfigurována s požadovaným zprostředkovatelem. Za běhu po načtení ověřovacího tokenu od poskytovatele `/.auth/login/<provider>` zaúčtujte token k ověření. Například: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Formát tokenu se mírně liší podle zprostředkovatele. Podrobnosti naleznete v následující tabulce:

| Hodnota zprostředkovatele | Požadováno v textu požadavku | Komentáře |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Ubytovací `expires_in` zařízení je volitelné. <br/>Při vyžádání tokenu ze služeb Live `wl.basic` vždy požádejte o obor. |
| `google` | `{"id_token":"<id_token>"}` | Ubytovací `authorization_code` zařízení je volitelné. Pokud je uvedeno, může být také `redirect_uri` volitelně doprovázeno ubytováním. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Použijte platný [přístupový token uživatele](https://developers.facebook.com/docs/facebook-login/access-tokens) z Facebooku. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Pokud je token zprostředkovatele úspěšně ověřen, rozhraní `authenticationToken` API vrátí s v těle odpovědi, což je token relace. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Jakmile budete mít tento token relace, můžete získat `X-ZUMO-AUTH` přístup k prostředkům chráněných aplikací přidáním záhlaví do vašich požadavků HTTP. Například: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Odhlášení z relace

Uživatelé mohou zahájit odhlášení `GET` odesláním požadavku `/.auth/logout` do koncového bodu aplikace. Požadavek `GET` provádí následující:

- Vymaže ověřovací soubory cookie z aktuální relace.
- Odstraní tokeny aktuálního uživatele z úložiště tokenů.
- Pro Azure Active Directory a Google provádí odhlášení na straně serveru na zprostředkovatele identity.

Tady je jednoduchý odkaz na odhlášení z webové stránky:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Ve výchozím nastavení úspěšné odhlášení přesměruje klienta na adresu URL `/.auth/logout/done`. Stránku přesměrování po odhlášení můžete změnit přidáním parametru dotazu. `post_logout_redirect_uri` Například:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Doporučujeme [zakódovat](https://wikipedia.org/wiki/Percent-encoding) hodnotu aplikace `post_logout_redirect_uri`.

Při použití plně kvalifikovaných adres URL musí být adresa URL hostována ve stejné doméně nebo nakonfigurovaná jako povolená adresa URL externího přesměrování pro vaši aplikaci. V následujícím příkladu přesměrovat `https://myexternalurl.com` na to není hostované ve stejné doméně:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Spusťte následující příkaz v [prostředí Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachovat fragmenty adresy URL

Jakmile se uživatelé přihlásí k vaší aplikaci, obvykle chtějí být přesměrováni do stejné části stejné stránky, například `/wiki/Main_Page#SectionZ`. Protože však [fragmenty adresy](https://wikipedia.org/wiki/Fragment_identifier) `#SectionZ`URL (například ) nejsou nikdy odesílány na server, nejsou ve výchozím nastavení zachovány po dokončení přihlášení OAuth a přesměruje zpět do aplikace. Uživatelé pak získat neoptimální prostředí, když potřebují přejít na požadovanou kotvu znovu. Toto omezení platí pro všechna řešení ověřování na straně serveru.

Při ověřování služby App Service můžete zachovat fragmenty adres URL v rámci přihlášení OAuth. Chcete-li to provést, `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` nastavte `true`nastavení aplikace volané na . Můžete to udělat na [webu Azure Portal](https://portal.azure.com), nebo jednoduše spustit následující příkaz v Prostředí Azure [Cloud:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Přístup k deklaracím identity uživatelů

Služba App Service předává deklarace identity uživatelů vaší aplikaci pomocí speciálních záhlaví. Externí požadavky nejsou povoleny k nastavení těchto záhlaví, takže jsou k dispozici pouze v případě, že nastavit službou App Service. Některé příklady záhlaví patří:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kód, který je napsán v libovolném jazyce nebo rozhraní frameworku můžete získat informace, které potřebuje z těchto záhlaví. Pro aplikace ASP.NET 4.6 **claimsprincipal** je automaticky nastavena s příslušnými hodnotami. ASP.NET Core však neposkytuje ověřovací middleware, který se integruje s deklaracemi uživatelů služby App Service. Řešení naleznete v [tématu MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Aplikace může také získat další podrobnosti o `/.auth/me`ověřeném uživateli voláním . Sady SDK serveru Mobile Apps poskytují pomocné metody pro práci s těmito daty. Další informace najdete [v tématu Jak používat Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)a [práce s .NET back-end server SDK pro Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Načtení tokenů v kódu aplikace

Z kódu serveru jsou tokeny specifické pro zprostředkovatele vloženy do hlavičky požadavku, takže k nim můžete snadno přistupovat. V následující tabulce jsou uvedeny možné názvy záhlaví tokenu:

| Poskytovatel | Názvy záhlaví |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Účet Microsoft | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Z klientského kódu (například mobilní aplikace nebo javascriptu `GET` v `/.auth/me`prohlížeči) odešlete požadavek HTTP na adresu . Vrácené JSON má tokeny specifické pro zprostředkovatele.

> [!NOTE]
> Přístupové tokeny jsou určeny pro přístup k prostředkům zprostředkovatele, takže jsou k dispozici pouze v případě, že nakonfigurujete zprostředkovatele s tajným klíčem klienta. Postup získání aktualizačních tokenů najdete v tématu Aktualizovat přístupové tokeny.

## <a name="refresh-identity-provider-tokens"></a>Aktualizovat tokeny zprostředkovatele identity

Když vyprší platnost přístupového tokenu vašeho poskytovatele (nikoli [tokenu relace),](#extend-session-token-expiration-grace-period)je třeba před opětovným použitím tohoto tokenu uživatele znovu ověřit. Vypršení platnosti tokenu `GET` můžete vyhnout `/.auth/refresh` voláním koncového bodu vaší aplikace. Při volání služba App Service automaticky aktualizuje přístupové tokeny v úložišti tokenů pro ověřeného uživatele. Následné požadavky na tokeny podle kódu aplikace získají obnovené tokeny. Aby však aktualizace tokenů fungovala, musí úložiště tokenů obsahovat [obnovovací tokeny](https://auth0.com/learn/refresh-tokens/) pro vašeho poskytovatele. Způsob, jak získat tokeny aktualizace jsou dokumentovány každý zprostředkovatel, ale následující seznam je stručný souhrn:

- **Google**: Přidejte parametr řetězce dotazu `access_type=offline` k volání `/.auth/login/google` rozhraní API. Pokud používáte sadu SDK pro mobilní aplikace, `LogicAsync` můžete přidat parametr k jednomu z přetížení (viz [Tokeny aktualizace Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Neposkytuje obnovovací tokeny. Platnost dlouhodobých tokenů vyprší za 60 dní (viz [Vypršení platnosti a rozšíření přístupových tokenů na Facebooku).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: Platnost přístupových tokenů nevyprší (viz [Nejčastější dotazy k Twitteru OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Účet Microsoft**: Při [konfiguraci nastavení ověřování účtu Microsoft](configure-authentication-provider-microsoft.md)vyberte `wl.offline_access` obor.
- **Azure Active**Directory [https://resources.azure.com](https://resources.azure.com): V , postupujte takto:
    1. V horní části stránky vyberte **Číst/zapisovat**.
    2. V levém prohlížeči přejděte na název**_\<\_předplatného_** > **resourceGroups** > **_\<název\_\_skupiny _** **subscriptions** > zdrojů> >  **zprostředkovatelů** > **Microsoft.Web** > **sites** > **_\<název aplikace\_>_**  >  **config** > **authsettings**. 
    3. Klikněte na **Upravit**.
    4. Upravte následující vlastnost. Nahraďte _ \<id aplikace\_>_ ID aplikace Azure Active Directory služby, ke které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klepněte na tlačítko **Umístit**. 

Jakmile je váš poskytovatel nakonfigurován, můžete [najít obnovovací token a čas vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenů. 

Chcete-li přístupový token kdykoli `/.auth/refresh` aktualizovat, stačí zavolat v libovolném jazyce. Následující fragment příkazu používá jQuery k aktualizaci přístupových tokenů z klienta JavaScriptu.

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

Pokud uživatel odvolá oprávnění udělená vaší aplikaci, `/.auth/me` může `403 Forbidden` volání selhat s odpovědí. Chcete-li diagnostikovat chyby, zkontrolujte protokoly aplikací podrobnosti.

## <a name="extend-session-token-expiration-grace-period"></a>Prodloužit dobu vypršení platnosti tokenu relace

Platnost ověřené relace vyprší po 8 hodinách. Po vypršení platnosti ověřené relace je ve výchozím nastavení 72hodinová lhůta odkladu. Během této období odkladu můžete aktualizovat token relace pomocí služby App Service bez opětovného ověření uživatele. Můžete jen `/.auth/refresh` zavolat, když váš token relace stane se neplatným a není nutné sledovat vypršení platnosti tokenu sami. Po uplynutí 72hodinové lhůty se uživatel musí znovu přihlásit, aby získal platný token relace.

Pokud pro vás není dostatek času 72 hodin, můžete toto okno vypršení platnosti prodloužit. Prodloužení vypršení platnosti po dlouhou dobu může mít významné důsledky pro zabezpečení (například při úniku nebo odcizení ověřovacího tokenu). Takže byste měli nechat na výchozí 72 hodin nebo nastavit prodloužení období na nejmenší hodnotu.

Chcete-li prodloužit výchozí okno vypršení platnosti, spusťte následující příkaz v [prostředí Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Období odkladu platí pouze pro ověřenou relaci služby App Service, nikoli tokeny od poskytovatelů identity. Neexistuje žádná lhůta pro tokeny zprostředkovatele vypršela. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény přihlašovacích účtů

Účet Microsoft i služba Azure Active Directory umožňují přihlášení z více domén. Účet Microsoft například umožňuje _účty outlook.com_, _live.com_a _hotmail.com._ Azure AD umožňuje libovolný počet vlastních domén pro přihlašovací účty. Můžete však chtít urychlit uživatele přímo na vlastní přihlašovací stránku Azure AD značky (například). `contoso.com` Chcete-li navrhnout název domény přihlašovacích účtů, postupujte takto.

V [https://resources.azure.com](https://resources.azure.com) **aplikaci** >  > **_\<\_ _** > **sites**  >  >  >   >  > **_\<\_\_ _** **config** > **_\<\__** přejděte na název předplatného resourcegroups název**skupiny**zdrojů>**zprostředkovatelů****Microsoft.Web**sites název aplikace>config**authsettings**. 

Klepněte na tlačítko **Upravit**, upravte následující vlastnost a potom klepněte na tlačítko **Put**. Nezapomeňte nahradit _ \<název\_domény>_ doménou, kterou chcete.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Toto nastavení `domain_hint` připojí parametr řetězce dotazu k adrese URL přesměrování přihlášení. 

> [!IMPORTANT]
> Je možné, že klient odebrat `domain_hint` parametr po obdržení adresy URL přesměrování a potom se přihlásit s jinou doménou. Takže i když je tato funkce pohodlná, není to bezpečnostní funkce.
>

## <a name="authorize-or-deny-users"></a>Autorizace nebo odepření uživatelů

Zatímco služba App Service se postará o nejjednodušší případ autorizace (tj. odmítnout neověřené požadavky), vaše aplikace může vyžadovat podrobnější autorizační chování, jako je například omezení přístupu pouze na určitou skupinu uživatelů. V některých případech je třeba napsat vlastní kód aplikace, který umožní nebo odepře přístup přihlášeného uživatele. V ostatních případech může být služba App Service nebo váš poskytovatel identity schopen pomoci bez nutnosti změny kódu.

- [Úroveň serveru](#server-level-windows-apps-only)
- [Úroveň zprostředkovatele identity](#identity-provider-level)
- [Aplikační úroveň](#application-level)

### <a name="server-level-windows-apps-only"></a>Úroveň serveru (pouze aplikace pro Windows)

Pro libovolnou aplikaci pro Windows můžete definovat autorizační chování webového serveru služby IIS úpravou souboru *Web.config.* Linuxové aplikace nepoužívají službu IIS a nelze je konfigurovat pomocí *souboru Web.config*.

1. Přejít na`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. V průzkumníku prohlížeče souborů služby App Service přejděte na *web/wwwroot*. Pokud *web.config* neexistuje, vytvořte ji výběrem **+**  >  **možnosti Nový soubor**. 

1. Vyberte tužku pro *web.config,* kterou chcete upravit. Přidejte následující konfigurační kód a klepněte na tlačítko **Uložit**. Pokud *Web.config* již existuje, `<authorization>` stačí přidat prvek se vším, co v něm. Přidejte účty, které chcete `<allow>` povolit v prvku.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Úroveň zprostředkovatele identity

Poskytovatel identity může poskytnout určitou autorizaci na klíč. Například:

- Pro [Azure App Service](configure-authentication-provider-aad.md)můžete spravovat přístup na podnikové [úrovni](../active-directory/manage-apps/what-is-access-management.md) přímo ve službě Azure AD. Pokyny naleznete [v tématu Jak odebrat uživateli přístup k aplikaci](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Pro [Google](configure-authentication-provider-google.md)lze projekty rozhraní Google API, které patří do [organizace,](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) nakonfigurovat tak, aby umožňovaly přístup pouze uživatelům ve vaší organizaci (viz [stránka podpory **OAuth 2.0** ](https://support.google.com/cloud/answer/6158849?hl=en)společnosti Google ).

### <a name="application-level"></a>Aplikační úroveň

Pokud některá z ostatních úrovní neposkytuje potřebnou autorizaci nebo pokud vaše platforma nebo poskytovatel identity není podporován, musíte napsat vlastní kód pro autorizaci uživatelů na základě [deklarací identity uživatele](#access-user-claims).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Ověření a autorizace uživatelů end-to-end (Windows)](app-service-web-tutorial-auth-aad.md)
> [Výuka: Ověření a autorizace uživatelů end-to-end (Linux)](containers/tutorial-auth-aad.md)

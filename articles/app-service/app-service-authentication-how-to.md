---
title: Pokročilé použití AuthN/AuthO
description: Naučte se přizpůsobit funkci ověřování a autorizace v App Service pro různé scénáře a získat deklarace identity uživatelů a různé tokeny.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280830"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Rozšířené použití ověřování a autorizace v Azure App Service

V tomto článku se dozvíte, jak přizpůsobit integrované [ověřování a autorizaci v App Service](overview-authentication-authorization.md)a spravovat identitu z vaší aplikace. 

Pokud chcete rychle začít, přečtěte si jedno z následujících kurzů:

* [Kurz: ověřování a autorizace uživatelů na konci Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Kurz: ověřování a autorizace uživatelů na konci Azure App Service pro Linux](containers/tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](configure-authentication-provider-aad.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](configure-authentication-provider-facebook.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](configure-authentication-provider-google.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](configure-authentication-provider-microsoft.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Použití více poskytovatelů přihlašování

Konfigurace portálu nenabízí způsob, jak pro uživatele prezentovat více poskytovatelů přihlášení (například Facebook i Twitter). Do vaší aplikace ale není obtížné přidávat funkce. Postup je popsaný následujícím způsobem:

Nejprve na stránce **ověřování/autorizace** v Azure Portal nakonfigurujte každého poskytovatele identity, kterého chcete povolit.

V **akci, která se má provést, když se žádost neověřuje**, vyberte možnost **povoluje anonymní žádosti (bez akce)** .

Na přihlašovací stránce nebo v navigačním panelu nebo v jakémkoli jiném umístění aplikace přidejte odkaz na přihlášení ke každému poskytovateli, který jste povolili (`/.auth/login/<provider>`). Příklad:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře se příslušná přihlašovací stránka pro přihlášení uživatele.

Pokud chcete přesměrovat uživatele po přihlášení na vlastní adresu URL, použijte parametr řetězce dotazu `post_login_redirect_url` (nepleťte si s identifikátorem URI přesměrování v konfiguraci zprostředkovatele identity). Chcete-li například přejít uživatele na `/Home/Index` po přihlášení, použijte následující kód HTML:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Ověřit tokeny od zprostředkovatelů

V přihlašování klienta se aplikace přihlásí k poskytovateli ručně a poté odešle ověřovací token k App Service k ověření (viz [tok ověřování](overview-authentication-authorization.md#authentication-flow)). Toto ověření sama o sobě neuděluje přístup k požadovaným prostředkům aplikace, ale úspěšné ověření vám poskytne token relace, který můžete použít pro přístup k prostředkům aplikace. 

Pokud chcete ověřit token poskytovatele, App Service aplikace musí být nejdřív nakonfigurované s požadovaným poskytovatelem. Po načtení tokenu ověřování od poskytovatele za běhu vystavte token pro `/.auth/login/<provider>` k ověření. Příklad: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Formát tokenu se mírně liší v závislosti na poskytovateli. Podrobnosti najdete v následující tabulce:

| Hodnota poskytovatele | Vyžadováno v textu žádosti | Komentáře |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | Vlastnost `expires_in` je volitelná. <br/>Při žádosti o token ze služby Live Services vždy vyžádejte `wl.basic` rozsah. |
| `google` | `{"id_token":"<id_token>"}` | Vlastnost `authorization_code` je volitelná. Je-li tento parametr zadán, může být také případně doprovázen vlastností `redirect_uri`. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Použijte platný [přístupový token uživatele](https://developers.facebook.com/docs/facebook-login/access-tokens) z Facebooku. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

V případě úspěšného ověření tokenu poskytovatele vrátí rozhraní API `authenticationToken` v těle odpovědi, což je váš token relace. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Jakmile budete mít tento token relace, budete mít přístup k prostředkům chráněných aplikací tak, že do požadavků HTTP přidáte hlavičku `X-ZUMO-AUTH`. Příklad: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Odhlásit se z relace

Uživatelé mohou zahájit odhlášení odesláním žádosti o `GET` do koncového bodu aplikace `/.auth/logout`. `GET` požadavek provede následující akce:

- Vymaže soubory cookie ověřování z aktuální relace.
- Odstraní tokeny aktuálního uživatele z úložiště tokenů.
- Pro Azure Active Directory a Google provede na poskytovateli identity přihlášení na straně serveru.

Tady je jednoduchý odkaz na odhlášení na webové stránce:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Ve výchozím nastavení přesměruje úspěšné odhlášení klienta na adresu URL `/.auth/logout/done`. Stránku přesměrování po odhlášení můžete změnit přidáním parametru `post_logout_redirect_uri` dotazu. Příklad:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Doporučuje se [kódovat](https://wikipedia.org/wiki/Percent-encoding) hodnotu `post_logout_redirect_uri`.

Při použití plně kvalifikovaných adres URL musí být adresa URL buď hostovaná ve stejné doméně, nebo nakonfigurovaná jako povolená externí adresa URL pro přesměrování vaší aplikace. V následujícím příkladu můžete přesměrovat na `https://myexternalurl.com`, která není hostovaná ve stejné doméně:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Spusťte následující příkaz v [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachovat fragmenty adresy URL

Až se uživatelé přihlásí do aplikace, obvykle chtějí být přesměrováni na stejnou část stejné stránky, například `/wiki/Main_Page#SectionZ`. Protože se ale [Fragmenty adresy URL](https://wikipedia.org/wiki/Fragment_identifier) (například `#SectionZ`) nikdy neodesílají na server, po dokončení přihlášení OAuth a přesměrování zpátky do aplikace se ve výchozím nastavení nezachovají. Uživatelé pak dostanou optimální prostředí, když potřebují znovu přejít k požadovanému kotvu. Toto omezení se vztahuje na všechna řešení ověřování na straně serveru.

V App Service ověřování můžete zachovat fragmenty adresy URL v rámci přihlášení OAuth. Provedete to tak, že nastavíte nastavení aplikace s názvem `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` na `true`. Můžete to provést v [Azure Portal](https://portal.azure.com)nebo stačí spustit následující příkaz v [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Přístup k deklaracím uživatelů

App Service předá do vaší aplikace deklarace identity uživatelů pomocí speciálních hlaviček. Externí požadavky nemají povolené nastavení těchto hlaviček, takže jsou k dispozici pouze v případě, že jsou nastaveny pomocí App Service. Mezi příklady hlaviček patří:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kód, který je napsán v libovolném jazyce nebo v rozhraní, může získat informace, které potřebuje z těchto hlaviček. Pro aplikace ASP.NET 4,6 se **ClaimsPrincipal** automaticky nastaví s příslušnými hodnotami. ASP.NET Core ale neposkytuje middleware ověřování, který se integruje s deklaracemi identity uživatelů App Service. Alternativní řešení najdete v tématu [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

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

Z kódu klienta (například mobilní aplikace nebo JavaScript v prohlížeči) odešlete požadavek HTTP `GET`, který se má `/.auth/me`. Vrácený kód JSON má tokeny specifické pro poskytovatele.

> [!NOTE]
> Přístupové tokeny slouží k přístupu k prostředkům poskytovatele, takže jsou k dispozici pouze v případě, že poskytovatele nakonfigurujete pomocí tajného klíče klienta. Informace o tom, jak získat aktualizační tokeny, najdete v tématu aktualizace přístupových tokenů.

## <a name="refresh-identity-provider-tokens"></a>Aktualizovat tokeny zprostředkovatele identity

Když vyprší platnost přístupového tokenu poskytovatele (ne [tokenu relace](#extend-session-token-expiration-grace-period)), budete muset uživatele před opětovným použitím tohoto tokenu znovu ověřit. Vypršení platnosti tokenu se můžete vyhnout provedením `GET` volání `/.auth/refresh`ho koncového bodu vaší aplikace. Při volání App Service automaticky aktualizuje přístupové tokeny v úložišti tokenů pro ověřeného uživatele. Následné žádosti o tokeny pomocí kódu vaší aplikace získají aktualizované tokeny. Aby ale aktualizace tokenu fungovala, musí úložiště tokenů obsahovat [aktualizační tokeny](https://auth0.com/learn/refresh-tokens/) pro vašeho poskytovatele. Způsob získání aktualizačních tokenů je popsán u každého poskytovatele, ale následující seznam je stručným shrnutím:

- **Google**: připojí parametr řetězce dotazu `access_type=offline` k volání rozhraní API `/.auth/login/google`. Pokud používáte sadu Mobile Apps SDK, můžete přidat parametr do jednoho z `LogicAsync` přetížení (viz [aktualizace Google tokeny](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: neposkytuje aktualizační tokeny. Do vypršení platnosti tokenů po dobu 60 dnů (viz [doba ukončení a rozšíření přístupových tokenů Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: přístupové tokeny neprošly (viz [Nejčastější dotazy k Twitteru OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Účet Microsoft**: při [konfiguraci nastavení ověřování účtu microsoft](configure-authentication-provider-microsoft.md)vyberte obor `wl.offline_access`.
- **Azure Active Directory**: v [https://resources.azure.com](https://resources.azure.com)proveďte následující kroky:
    1. V horní části stránky vyberte možnost **čtení/zápis**.
    2. V levém prohlížeči přejděte na **odběry** >  **_\<předplatné\_název_**  > **resourceGroups** >  **_\<Resource\_group\_název >_**  > **poskytovatelé** > **Microsoft. Web** > **weby** >  **_\<aplikace\_název >_**  > **config** > **authsettings**. 
    3. Klikněte na tlačítko**Upravit**.
    4. Upravte následující vlastnost. _\_id > aplikace\<_ nahraďte Azure Active Directory ID aplikace služby, ke které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klikněte na tlačítko **Vložit**. 

Po nakonfigurování zprostředkovatele můžete [Najít obnovovací token a čas vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenů. 

Pokud chcete přístupový token kdykoli aktualizovat, stačí volat `/.auth/refresh` v libovolném jazyce. Následující fragment kódu používá jQuery k aktualizaci přístupových tokenů z JavaScriptového klienta.

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

Pokud uživatel Odvolá oprávnění udělená vaší aplikaci, volání `/.auth/me` může selhat s `403 Forbidden` reakci. Chcete-li diagnostikovat chyby, zkontrolujte podrobnosti v protokolech aplikace.

## <a name="extend-session-token-expiration-grace-period"></a>Doba odkladu pro prodloužení platnosti tokenu relace

Platnost ověřené relace vyprší po 8 hodinách. Po vypršení platnosti ověřené relace je ve výchozím nastavení k dispozici období odkladu 72 hodin. V rámci této lhůty odkladu je dovoleno aktualizovat token relace pomocí App Service bez opětovného ověření uživatele. Můžete volat `/.auth/refresh` jenom v případě, že váš token relace je neplatný a nemusíte sledovat vypršení platnosti tokenu sami. Po uplynutí doby 72 hodin se uživatel musí znovu přihlásit, aby získal platný token relace.

Pokud pro vás není dostatek času 72 hodin, můžete toto okno vypršení platnosti zvětšit. Prodloužení platnosti po dlouhou dobu může mít významný vliv na zabezpečení (například při nevracení nebo odcizení ověřovacího tokenu). Proto byste měli ponechat výchozí 72 hodiny nebo nastavit dobu rozšíření na nejmenší hodnotu.

Chcete-li zvětšit výchozí okno vypršení platnosti, spusťte následující příkaz v [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Doba odkladu se vztahuje pouze na App Service ověřená relace, nikoli na tokeny od zprostředkovatelů identity. Pro tokeny poskytovatele s vypršenou platností se neplatí žádné období odkladu. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény přihlašovacích účtů

Účet Microsoft i Azure Active Directory vám umožňují přihlašovat se z více domén. Například účet Microsoft umožňuje účty _Outlook.com_, _Live.com_a _hotmail.com_ . Azure AD povoluje pro přihlašovací účty libovolný počet vlastních domén. Můžete ale chtít zrychlit uživatele přímo na přihlašovací stránku služby Azure AD, která je označená značkou (například `contoso.com`). Chcete-li navrhnout název domény přihlašovacích účtů, postupujte podle těchto kroků.

V [https://resources.azure.com](https://resources.azure.com)přejděte na **odběry** >  **_\<předplatné\_název_**  > **resourceGroups** >  **_\<_** \_\_skupiny > > název >  > **poskytovatel** > **Microsoft. Web**\<**weby**\_ **_> aplikace > název > _** **config** **authsettings**. 

Klikněte na tlačítko **Upravit**, upravte následující vlastnost a pak klikněte na tlačítko **Vložit**. Nezapomeňte nahradit _\<\_název domény >_ požadovanou doménou.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Toto nastavení připojí parametr řetězce dotazu `domain_hint` k adrese URL pro přesměrování přihlášení. 

> [!IMPORTANT]
> Klient může po přijetí adresy URL pro přesměrování odebrat parametr `domain_hint` a pak se přihlásit k jiné doméně. Takže když je tato funkce užitečná, není to funkce zabezpečení.
>

## <a name="authorize-or-deny-users"></a>Autorizovat nebo Odepřít uživatele

I když App Service postará o nejjednodušší případ autorizace (tj. odmítnout neověřené žádosti), vaše aplikace může vyžadovat přesnější chování autorizace, jako je omezení přístupu jenom na konkrétní skupinu uživatelů. V některých případech je nutné napsat vlastní kód aplikace, aby bylo možné povolit nebo odepřít přístup přihlášenému uživateli. V jiných případech App Service nebo váš poskytovatel identity může pomáhat bez nutnosti změny kódu.

- [Úroveň serveru](#server-level-windows-apps-only)
- [Úroveň poskytovatele identity](#identity-provider-level)
- [Úroveň aplikace](#application-level)

### <a name="server-level-windows-apps-only"></a>Úroveň serveru (jenom aplikace pro Windows)

U jakékoli aplikace pro Windows můžete definovat chování ověřování webového serveru IIS úpravou souboru *Web. config* . Aplikace pro Linux nepoužívají službu IIS a nelze je konfigurovat pomocí *souboru Web. config*.

1. Přejít na `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. V Průzkumníkovi prohlížeče souborů App Service přejděte na *lokalitu/wwwroot*. Pokud soubor *Web. config* neexistuje, vytvořte jej výběrem **+**  > **nový soubor**. 

1. Vyberte tužku pro *Web. config* a upravte ji. Přidejte následující konfigurační kód a klikněte na **Uložit**. Pokud soubor *Web. config* již existuje, stačí přidat prvek `<authorization>` se všemi. Přidejte účty, které chcete v prvku `<allow>` povolené.

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

### <a name="identity-provider-level"></a>Úroveň poskytovatele identity

Poskytovatel identity může poskytovat určitou autorizaci autorizace klíče. Příklad:

- Pro [Azure App Service](configure-authentication-provider-aad.md)můžete [spravovat přístup na podnikové úrovni](../active-directory/manage-apps/what-is-access-management.md) přímo ve službě Azure AD. Pokyny najdete v tématu [Postup odebrání přístupu uživatele k aplikaci](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Pro [Google](configure-authentication-provider-google.md)jsou projekty Google API, které patří do [organizace](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) , možné nakonfigurovat tak, aby povolovaly přístup jenom uživatelům ve vaší organizaci (viz [Stránka podpory **OAuth 2,0 s nastavením** Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Úroveň aplikace

Pokud žádná z ostatních úrovní neposkytne autorizaci, kterou potřebujete, nebo pokud vaše platforma nebo zprostředkovatel identity není podporována, musíte napsat vlastní kód, který autorizuje uživatele na základě [deklarací identity uživatele](#access-user-claims).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: ověření a autorizace uživatelů po konci až do konce (Windows)](app-service-web-tutorial-auth-aad.md)
> [kurzu: ověřování a autorizace uživatelů koncová (Linux)](containers/tutorial-auth-aad.md)

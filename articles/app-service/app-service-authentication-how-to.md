---
title: Rozšířené použití AuthN/AuthZ
description: Naučte se přizpůsobit funkci ověřování a autorizace v App Service pro různé scénáře a získat deklarace identity uživatelů a různé tokeny.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: 93c697162bfcb51b77c2e6f48b5824b81070bf51
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91816414"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>Rozšířené použití ověřování a autorizace v Azure App Service

V tomto článku se dozvíte, jak přizpůsobit integrované [ověřování a autorizaci v App Service](overview-authentication-authorization.md)a spravovat identitu z vaší aplikace. 

Pokud chcete rychle začít, přečtěte si jedno z následujících kurzů:

* [Kurz: Komplexní ověřování a autorizace uživatelů v Azure App Service](tutorial-auth-aad.md)
* [Konfigurace aplikace pro použití přihlášení Azure Active Directory](configure-authentication-provider-aad.md)
* [Konfigurace aplikace pro použití přihlášení k Facebooku](configure-authentication-provider-facebook.md)
* [Konfigurace aplikace pro použití přihlášení ke Googlu](configure-authentication-provider-google.md)
* [Konfigurace aplikace pro použití přihlášení k účtu Microsoft](configure-authentication-provider-microsoft.md)
* [Konfigurace aplikace pro použití přihlášení k Twitteru](configure-authentication-provider-twitter.md)
* [Jak nakonfigurovat aplikaci pro přihlášení pomocí poskytovatele OpenID Connect (Preview)](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>Použití více poskytovatelů přihlašování

Konfigurace portálu nenabízí způsob, jak pro uživatele prezentovat více poskytovatelů přihlášení (například Facebook i Twitter). Do vaší aplikace ale není obtížné přidávat funkce. Postup je popsaný následujícím způsobem:

Nejprve na stránce **ověřování/autorizace** v Azure Portal nakonfigurujte každého poskytovatele identity, kterého chcete povolit.

V **akci, která se má provést, když se žádost neověřuje**, vyberte možnost **povoluje anonymní žádosti (bez akce)**.

Na přihlašovací stránce nebo v navigačním panelu nebo jakémkoli jiném umístění aplikace přidejte odkaz pro přihlášení ke každému poskytovateli, který jste povolili ( `/.auth/login/<provider>` ). Například:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Když uživatel klikne na jeden z odkazů, otevře se příslušná přihlašovací stránka pro přihlášení uživatele.

Pokud chcete přesměrovat uživatele po přihlášení na vlastní adresu URL, použijte `post_login_redirect_url` parametr řetězce dotazu (Nezaměňujte ho pomocí identifikátoru URI přesměrování v konfiguraci zprostředkovatele identity). Chcete-li například procházet uživatele `/Home/Index` po přihlášení, použijte následující kód HTML:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Ověřit tokeny od zprostředkovatelů

V přihlašování klienta se aplikace přihlásí k poskytovateli ručně a poté odešle ověřovací token k App Service k ověření (viz [tok ověřování](overview-authentication-authorization.md#authentication-flow)). Toto ověření sama o sobě neuděluje přístup k požadovaným prostředkům aplikace, ale úspěšné ověření vám poskytne token relace, který můžete použít pro přístup k prostředkům aplikace. 

Pokud chcete ověřit token poskytovatele, App Service aplikace musí být nejdřív nakonfigurované s požadovaným poskytovatelem. Po načtení tokenu ověřování od poskytovatele za běhu vystavte token `/.auth/login/<provider>` pro ověření. Například: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

Formát tokenu se mírně liší v závislosti na poskytovateli. Podrobnosti najdete v následující tabulce:

| Hodnota poskytovatele | Vyžadováno v textu žádosti | Komentáře |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | `expires_in`Vlastnost je nepovinná. <br/>Při žádosti o token ze služby Live Services vždy vyžádat `wl.basic` rozsah. |
| `google` | `{"id_token":"<id_token>"}` | `authorization_code`Vlastnost je nepovinná. Je-li tento parametr zadán, může být také případně doplněn `redirect_uri` vlastností. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Použijte platný [přístupový token uživatele](https://developers.facebook.com/docs/facebook-login/access-tokens) z Facebooku. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

V případě úspěšného ověření tokenu poskytovatele vrátí rozhraní API `authenticationToken` text v odpovědi, který je vaším tokenem relace. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Po vytvoření tohoto tokenu relace můžete získat přístup k prostředkům chráněných aplikací přidáním `X-ZUMO-AUTH` hlavičky do požadavků HTTP. Například: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Odhlásit se z relace

Uživatelé můžou zahájit odhlášení odesláním `GET` žádosti do `/.auth/logout` koncového bodu aplikace. `GET`Požadavek provede následující akce:

- Vymaže soubory cookie ověřování z aktuální relace.
- Odstraní tokeny aktuálního uživatele z úložiště tokenů.
- Pro Azure Active Directory a Google provede na poskytovateli identity přihlášení na straně serveru.

Tady je jednoduchý odkaz na odhlášení z webové stránky:

```html
<a href="/.auth/logout">Sign out</a>
```

Ve výchozím nastavení se při úspěšném odhlášení přesměruje klient na adresu URL `/.auth/logout/done` . Stránku přesměrování po odhlášení můžete změnit přidáním `post_logout_redirect_uri` parametru dotazu. Například:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Doporučuje se [kódovat](https://wikipedia.org/wiki/Percent-encoding) hodnotu `post_logout_redirect_uri` .

Při použití plně kvalifikovaných adres URL musí být adresa URL buď hostovaná ve stejné doméně, nebo nakonfigurovaná jako povolená externí adresa URL pro přesměrování vaší aplikace. V následujícím příkladu pro přesměrování na `https://myexternalurl.com` , který není hostovaný ve stejné doméně:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Spusťte následující příkaz v [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>Zachovat fragmenty adresy URL

Až se uživatelé přihlásí do aplikace, obvykle chtějí být přesměrováni na stejnou část stejné stránky, jako je například `/wiki/Main_Page#SectionZ` . Protože se ale [Fragmenty adresy URL](https://wikipedia.org/wiki/Fragment_identifier) (například `#SectionZ` ) nikdy neodesílají na server, po dokončení přihlášení OAuth a přesměrování zpátky do vaší aplikace se ve výchozím nastavení nezachovají. Uživatelé pak dostanou optimální prostředí, když potřebují znovu přejít k požadovanému kotvu. Toto omezení se vztahuje na všechna řešení ověřování na straně serveru.

V App Service ověřování můžete zachovat fragmenty adresy URL v rámci přihlášení OAuth. To provedete tak, že nastavíte nastavení aplikace `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` s názvem na `true` . Můžete to provést v [Azure Portal](https://portal.azure.com)nebo stačí spustit následující příkaz v [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Přístup k deklaracím uživatelů

App Service předá do vaší aplikace deklarace identity uživatelů pomocí speciálních hlaviček. Externí požadavky nemají povolené nastavení těchto hlaviček, takže jsou k dispozici pouze v případě, že jsou nastaveny pomocí App Service. Mezi příklady hlaviček patří:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Kód, který je napsán v libovolném jazyce nebo v rozhraní, může získat informace, které potřebuje z těchto hlaviček. Pro aplikace ASP.NET 4,6 se **ClaimsPrincipal** automaticky nastaví s příslušnými hodnotami. ASP.NET Core ale neposkytuje middleware ověřování, který se integruje s deklaracemi identity uživatelů App Service. Alternativní řešení najdete v tématu [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Pokud je [úložiště tokenů](overview-authentication-authorization.md#token-store) pro vaši aplikaci povoleno, můžete také získat další podrobnosti o ověřeném uživateli voláním `/.auth/me` . Sady SDK Mobile Apps serveru poskytují pomocné metody pro práci s těmito daty. Další informace najdete v tématu [Jak používat sadu azure Mobile Apps Node.js SDK a jak](/previous-versions/azure/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk#howto-tables-getidentity) [pracovat se serverem back-end .NET sdk pro Azure Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk#user-info).

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

Z kódu klienta (jako je mobilní aplikace nebo JavaScript v prohlížeči) odešle `GET` požadavek HTTP `/.auth/me` (musí být povolený[úložiště tokenů](overview-authentication-authorization.md#token-store) ). Vrácený kód JSON má tokeny specifické pro poskytovatele.

> [!NOTE]
> Přístupové tokeny slouží k přístupu k prostředkům poskytovatele, takže jsou k dispozici pouze v případě, že poskytovatele nakonfigurujete pomocí tajného klíče klienta. Informace o tom, jak získat aktualizační tokeny, najdete v tématu aktualizace přístupových tokenů.

## <a name="refresh-identity-provider-tokens"></a>Aktualizovat tokeny zprostředkovatele identity

Když vyprší platnost přístupového tokenu poskytovatele (ne [tokenu relace](#extend-session-token-expiration-grace-period)), budete muset uživatele před opětovným použitím tohoto tokenu znovu ověřit. Vypršení platnosti tokenu se můžete vyhnout provedením `GET` volání `/.auth/refresh` koncového bodu aplikace. Při volání App Service automaticky aktualizuje přístupové tokeny v [úložišti tokenů](overview-authentication-authorization.md#token-store) pro ověřeného uživatele. Následné žádosti o tokeny pomocí kódu vaší aplikace získají aktualizované tokeny. Aby ale aktualizace tokenu fungovala, musí úložiště tokenů obsahovat [aktualizační tokeny](https://auth0.com/learn/refresh-tokens/) pro vašeho poskytovatele. Způsob získání aktualizačních tokenů je popsán u každého poskytovatele, ale následující seznam je stručným shrnutím:

- **Google**: připojí `access_type=offline` k `/.auth/login/google` volání rozhraní API parametr řetězce dotazu. Pokud používáte sadu Mobile Apps SDK, můžete do jednoho z přetížení přidat parametr `LogicAsync` (viz [aktualizace tokenů Google](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: neposkytuje aktualizační tokeny. Do vypršení platnosti tokenů po dobu 60 dnů (viz [doba ukončení a rozšíření přístupových tokenů Facebook](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: přístupové tokeny neprošly (viz [Nejčastější dotazy k Twitteru OAuth](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Účet Microsoft**: při [konfiguraci nastavení ověřování účtu Microsoft](configure-authentication-provider-microsoft.md)vyberte `wl.offline_access` obor.
- **Azure Active Directory**: v nástroji [https://resources.azure.com](https://resources.azure.com) proveďte následující kroky:
    1. V horní části stránky vyberte možnost **čtení/zápis**.
    2. V levém prohlížeči přejděte na **odběry** > * *_ \<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **poskytovatelé**  >  **společnosti Microsoft.**  >  **weby** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 
    3. Klikněte na **Upravit**.
    4. Upravte následující vlastnost. Nahraďte _\<app\_id>_ Azure Active Directory ID aplikace služby, ke které chcete získat přístup.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Klikněte na tlačítko **Vložit**. 

Po nakonfigurování zprostředkovatele můžete [Najít obnovovací token a čas vypršení platnosti přístupového tokenu](#retrieve-tokens-in-app-code) v úložišti tokenů. 

Pokud chcete přístupový token kdykoli aktualizovat, stačí volat `/.auth/refresh` v libovolném jazyce. Následující fragment kódu používá jQuery k aktualizaci přístupových tokenů z JavaScriptového klienta.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Pokud uživatel Odvolá oprávnění udělená vaší aplikaci, volání `/.auth/me` může selhat s `403 Forbidden` odpovědí. Chcete-li diagnostikovat chyby, zkontrolujte podrobnosti v protokolech aplikace.

## <a name="extend-session-token-expiration-grace-period"></a>Doba odkladu pro prodloužení platnosti tokenu relace

Platnost ověřené relace vyprší po 8 hodinách. Po vypršení platnosti ověřené relace je ve výchozím nastavení k dispozici období odkladu 72 hodin. V rámci této lhůty odkladu je dovoleno aktualizovat token relace pomocí App Service bez opětovného ověření uživatele. Můžete zavolat pouze `/.auth/refresh` v případě, že je token relace neplatný a nemusíte sledovat vypršení platnosti tokenu sami. Po uplynutí doby 72 hodin se uživatel musí znovu přihlásit, aby získal platný token relace.

Pokud pro vás není dostatek času 72 hodin, můžete toto okno vypršení platnosti zvětšit. Prodloužení platnosti po dlouhou dobu může mít významný vliv na zabezpečení (například při nevracení nebo odcizení ověřovacího tokenu). Proto byste měli ponechat výchozí 72 hodiny nebo nastavit dobu rozšíření na nejmenší hodnotu.

Chcete-li zvětšit výchozí okno vypršení platnosti, spusťte následující příkaz v [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> Doba odkladu se vztahuje pouze na App Service ověřená relace, nikoli na tokeny od zprostředkovatelů identity. Pro tokeny poskytovatele s vypršenou platností se neplatí žádné období odkladu. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Omezení domény přihlašovacích účtů

Účet Microsoft i Azure Active Directory vám umožňují přihlašovat se z více domén. Například účet Microsoft umožňuje účty _Outlook.com_, _Live.com_a _hotmail.com_ . Azure AD povoluje pro přihlašovací účty libovolný počet vlastních domén. Můžete ale chtít zrychlit uživatele přímo na přihlašovací stránku služby Azure AD, která je označená značkou (například `contoso.com` ). Chcete-li navrhnout název domény přihlašovacích účtů, postupujte podle těchto kroků.

V [https://resources.azure.com](https://resources.azure.com) přejděte na **odběry** > * *_ \<subscription\_name_** > **resourceGroups** > * *_* \<resource\_group\_name> _* > **poskytovatelé**  >  **společnosti Microsoft.**  >  **weby** > * *_ \<app\_name> _ * * > **config**  >  **authsettings**. 

Klikněte na tlačítko **Upravit**, upravte následující vlastnost a pak klikněte na tlačítko **Vložit**. Nezapomeňte nahradit _\<domain\_name>_ doménou, kterou chcete.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Toto nastavení připojí `domain_hint` parametr řetězce dotazu k adrese URL pro přesměrování přihlášení. 

> [!IMPORTANT]
> Klient může `domain_hint` po přijetí adresy URL pro přesměrování odebrat parametr a pak se přihlásit k jiné doméně. Takže když je tato funkce užitečná, není to funkce zabezpečení.
>

## <a name="authorize-or-deny-users"></a>Autorizovat nebo Odepřít uživatele

I když App Service postará o nejjednodušší případ autorizace (tj. odmítnout neověřené žádosti), vaše aplikace může vyžadovat přesnější chování autorizace, jako je omezení přístupu jenom na konkrétní skupinu uživatelů. V některých případech je nutné napsat vlastní kód aplikace, aby bylo možné povolit nebo odepřít přístup přihlášenému uživateli. V jiných případech App Service nebo váš poskytovatel identity může pomáhat bez nutnosti změny kódu.

- [Úroveň serveru](#server-level-windows-apps-only)
- [Úroveň poskytovatele identity](#identity-provider-level)
- [Úroveň aplikace](#application-level)

### <a name="server-level-windows-apps-only"></a>Úroveň serveru (jenom aplikace pro Windows)

V případě jakékoli aplikace pro Windows můžete definovat chování webového serveru služby IIS, a to úpravou souboru *Web.config* . Aplikace pro Linux nepoužívají službu IIS a nelze je konfigurovat prostřednictvím *Web.config*.

1. Přejděte na adresu `https://<app-name>.scm.azurewebsites.net/DebugConsole`.

1. V Průzkumníkovi prohlížeče souborů App Service přejděte na *lokalitu/wwwroot*. Pokud *Web.config* neexistuje, vytvořte ho výběrem možnosti **+**  >  **nový soubor**. 

1. Vyberte tužku pro *Web.config* a upravte ji. Přidejte následující konfigurační kód a klikněte na **Uložit**. Pokud *Web.config* již existuje, stačí přidat `<authorization>` prvek se všemi. Přidejte účty, které chcete v `<allow>` elementu použít.

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

Poskytovatel identity může poskytovat určitou autorizaci autorizace klíče. Například:

- Pro [Azure App Service](configure-authentication-provider-aad.md)můžete [spravovat přístup na podnikové úrovni](../active-directory/manage-apps/what-is-access-management.md) přímo ve službě Azure AD. Pokyny najdete v tématu [Postup odebrání přístupu uživatele k aplikaci](../active-directory/manage-apps/methods-for-removing-user-access.md).
- Pro [Google](configure-authentication-provider-google.md)jsou projekty Google API, které patří do [organizace](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) , možné nakonfigurovat tak, aby povolovaly přístup jenom uživatelům ve vaší organizaci (viz [Stránka podpory **OAuth 2,0 s nastavením** Google](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Úroveň aplikace

Pokud žádná z ostatních úrovní neposkytne autorizaci, kterou potřebujete, nebo pokud vaše platforma nebo zprostředkovatel identity není podporována, musíte napsat vlastní kód, který autorizuje uživatele na základě [deklarací identity uživatele](#access-user-claims).

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurace pomocí souboru (Preview)

Nastavení ověřování lze volitelně nakonfigurovat prostřednictvím souboru, který je poskytován vaším nasazením. To může být vyžadováno některými možnostmi verze Preview App Service ověřování/autorizace.

> [!IMPORTANT]
> Mějte na paměti, že datová část aplikace, a proto se tento soubor může přesouvat mezi prostředími jako s [sloty](./deploy-staging-slots.md). Je možné, že budete chtít, aby se k jednotlivým slotům připnula jiná registrace aplikace, a v těchto případech byste místo použití konfiguračního souboru měli nadále používat standardní metodu konfigurace.

### <a name="enabling-file-based-configuration"></a>Povolení konfigurace na základě souborů

> [!CAUTION]
> Během období Preview se povolením konfigurace na základě souborů zakáže Správa App Service funkce ověřování/autorizace pro vaši aplikaci prostřednictvím některých klientů, jako je například Azure Portal, Azure CLI a Azure PowerShell.

1. Vytvořte nový soubor JSON pro vaši konfiguraci v kořenovém adresáři projektu (nasazený do D:\home\site\wwwroot ve vaší webové nebo funkční aplikaci). Vyplňte požadovanou konfiguraci podle [odkazu na konfiguraci na základě souborů](#configuration-file-reference). Pokud upravujete existující konfiguraci Azure Resource Manager, nezapomeňte přeložit vlastnosti zachycené v `authsettings` kolekci do konfiguračního souboru.

2. Upravte existující konfiguraci, která je zachycena v rozhraních API [Azure Resource Manager](../azure-resource-manager/management/overview.md) v části `Microsoft.Web/sites/<siteName>/config/authsettings` . Pokud to chcete změnit, můžete použít [šablonu Azure Resource Manager](../azure-resource-manager/templates/overview.md) nebo nástroj jako [Azure Resource Explorer](https://resources.azure.com/). V rámci kolekce authsettings budete muset nastavit tři vlastnosti (a můžou jiné odebrat):

    1.  Nastavit `enabled` na hodnotu "true"
    2.  Nastavit `isAuthFromFile` na hodnotu "true"
    3.  Nastavte `authFilePath` na název souboru (například "auth.json").

> [!NOTE]
> Formát pro `authFilePath` různé platformy se liší. V systému Windows jsou podporovány relativní i absolutní cesty. Doporučuje se relativní. Pro Linux se v současné době podporují jenom absolutní cesty, takže hodnota nastavení by měla být "/Home/site/wwwroot/auth.json" nebo podobná.

Po provedení této aktualizace konfigurace bude obsah souboru použit k definování chování App Service ověřování/autorizace pro danou lokalitu. Pokud se někdy chcete vrátit ke konfiguraci Azure Resource Manager, můžete to udělat tak, že nastavíte `isAuthFromFile` zpět na false (NEPRAVDA).

### <a name="configuration-file-reference"></a>Odkaz na konfigurační soubor

Všechny tajné kódy, které budou odkazovány z konfiguračního souboru, musí být uloženy jako [nastavení aplikace](./configure-common.md#configure-app-settings). Nastavení můžete pojmenovat cokoli, co si přejete. Stačí se ujistit, že odkazy z konfiguračního souboru používají stejné klíče.

Následující vyčerpání možných možností konfigurace v souboru:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUri": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityProviderDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scope": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Připnutí aplikace ke konkrétní verzi modulu runtime ověřování

Pokud povolíte ověřování/autorizaci, middleware platformy se vloží do kanálu požadavků HTTP, jak je popsáno v [přehledu funkcí](overview-authentication-authorization.md#how-it-works). Tento middleware platformy se pravidelně aktualizuje novými funkcemi a vylepšeními v rámci rutiny rutinní aktualizace platforem. Ve výchozím nastavení se webová nebo aplikace Function App spustí na nejnovější verzi tohoto middlewaru platformy. Tyto automatické aktualizace jsou vždycky zpětně kompatibilní. Nicméně v vzácných událostech, které tato funkce Automatické aktualizace představuje pro vaši webovou aplikaci nebo aplikaci Function App problém, můžete se dočasně vrátit k předchozí verzi middlewaru. Tento článek vysvětluje, jak dočasně připnout aplikaci na konkrétní verzi middleware pro ověřování.

### <a name="automatic-and-manual-version-updates"></a>Automatické a ruční aktualizace verze 

Aplikaci můžete připnout do konkrétní verze middlewaru platformy nastavením `runtimeVersion` nastavení pro aplikaci. Vaše aplikace se vždycky spouští na nejnovější verzi, pokud se nerozhodnete ji explicitně připnout zpátky na konkrétní verzi. V tuto chvíli se podporuje několik verzí. Pokud připnete připnutí na neplatnou verzi, která už není podporovaná, bude místo toho použita nejnovější verze aplikace. Pokud chcete vždycky spustit nejnovější verzi, nastavte `runtimeVersion` na ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Zobrazení a aktualizace aktuální verze modulu runtime

Můžete změnit verzi modulu runtime, kterou používá vaše aplikace. Nová verze modulu runtime by se měla projevit po restartování aplikace. 

#### <a name="view-the-current-runtime-version"></a>Zobrazit aktuální verzi modulu runtime

Aktuální verzi middlewaru pro ověření platformy můžete zobrazit buď pomocí Azure CLI, nebo přes jeden z koncových bodů HTTP verze built0 ve vaší aplikaci.

##### <a name="from-the-azure-cli"></a>Z Azure CLI

Pomocí Azure CLI zobrazte aktuální verzi middlewaru pomocí příkazu [AZ WebApp auth show](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-show) .

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

V tomto kódu nahraďte `<my_app_name>` názvem vaší aplikace. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace.

Zobrazí se `runtimeVersion` pole ve výstupu rozhraní příkazového řádku. Bude vypadat podobně jako v následujícím příkladu výstupu, který byl zkrácen pro přehlednost: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Z koncového bodu verze

K zobrazení aktuální verze middlewaru, na které je aplikace spuštěná, můžete také v aplikaci stisknout/.auth/Version koncový bod. Bude vypadat podobně jako v následujícím příkladu výstupu:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aktualizace aktuální verze modulu runtime

Pomocí Azure CLI můžete `runtimeVersion` v aplikaci aktualizovat nastavení pomocí příkazu [AZ WebApp auth Update](/cli/azure/webapp/auth?view=azure-cli-latest&preserve-view=true#az-webapp-auth-update) .

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Nahraďte `<my_app_name>` názvem vaší aplikace. Nahraďte také `<my_resource_group>` názvem skupiny prostředků vaší aplikace. Nahraďte také `<version>` platnou verzí modulu runtime 1. x nebo `~1` nejnovější verzí. Poznámky k verzi najdete v různých verzích modulu runtime [zde] (aby bylo možné https://github.com/Azure/app-service-announcements) určit verzi, ke které se má připnout).

Tento příkaz můžete spustit z [Azure Cloud Shell](../cloud-shell/overview.md) výběrem možnosti **vyzkoušet** v předchozím příkladu kódu. Pomocí rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) můžete také spustit tento příkaz po provedení příkazu [AZ Login](/cli/azure/reference-index#az-login) pro přihlášení.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Komplexní ověřování a autorizace uživatelů](tutorial-auth-aad.md)
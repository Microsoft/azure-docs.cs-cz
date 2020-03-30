---
title: Správa relací – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: zmírnění hrozeb vystavených v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 5d9dc1595e3cc812ba060d958b6e981867500ae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73161506"
---
# <a name="security-frame-session-management"></a>Rámec zabezpečení: Správa relací
| Produkt/služba | Článek |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementace správného odhlášení pomocí metod ADAL při použití Služby Azure AD](#logout-adal)</li></ul> |
| Zařízení IoT | <ul><li>[Použití konečných životností pro generované tokeny SaS](#finite-tokens)</li></ul> |
| **Databáze dokumentů Azure** | <ul><li>[Použít minimální životnost tokenů pro generované tokeny prostředků](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementace správného odhlášení pomocí metod WsFederation při použití služby ADFS](#wsfederation-logout)</li></ul> |
| **Server identit** | <ul><li>[Implementace správného odhlášení při použití serveru Identity Server](#proper-logout)</li></ul> |
| **Webová aplikace** | <ul><li>[Aplikace dostupné přes protokol HTTPS musí používat zabezpečené soubory cookie](#https-secure-cookies)</li><li>[Všechny aplikace založené na http by měly specifikovat http pouze pro definici souboru cookie](#cookie-definition)</li><li>[Zmírnit útoky na ASP.NET webových stránkách ASP.NET](#csrf-asp)</li><li>[Nastavit relaci pro životnost nečinnosti](#inactivity-lifetime)</li><li>[Implementace správného odhlášení z aplikace](#proper-app-logout)</li></ul> |
| **Web API** | <ul><li>[Zmírnit útoky na ASP.NET webová api napříč weby (CSRF)](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementace správného odhlášení pomocí metod ADAL při použití Služby Azure AD

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Pokud aplikace závisí na přístupový token vydaný Azure AD, měla by být volána obslužná rutina události odhlášení. |

### <a name="example"></a>Příklad
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Příklad
Měl by také zničit relaci uživatele voláním Session.Abandon() metoda. Následující metoda ukazuje bezpečnou implementaci odhlášení uživatele:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Použití konečných životností pro generované tokeny SaS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Tokeny SaS generované pro ověřování do služby Azure IoT Hub by měly mít omezenou dobu vypršení platnosti. Udržujte životnost tokenu SaS na minimum, abyste omezili dobu, po kterou mohou být přehrány v případě, že jsou tokeny ohroženy.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Použít minimální životnost tokenů pro generované tokeny prostředků

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze dokumentů Azure | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Snižte časový rozsah tokenu prostředku na požadovanou minimální hodnotu. Tokeny prostředků mají výchozí platný časový rozsah 1 hodinu.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementace správného odhlášení pomocí metod WsFederation při použití služby ADFS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | ADFS | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Pokud aplikace závisí na tokenu STS vydaném službou ADFS, měla by obslužná rutina události odhlášení volat metodu WSFederationAuthenticationModule.FederatedSignOut() pro odhlášení uživatele. Také aktuální relace by měla být zničena a hodnota tokenu relace by měla být resetována a zrušena.|

### <a name="example"></a>Příklad
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementace správného odhlášení při použití serveru Identity Server

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Odhlásit se pomocí serveru IdentityServer3](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroky** | IdentityServer podporuje schopnost federate s externími zprostředkovateli identity. Když se uživatel odhlásí od poskytovatele identity upstream, v závislosti na použitém protokolu, může být možné přijímat oznámení, když se uživatel odhlásí. Umožňuje IdentityServer upozornit své klienty, aby mohli také odhlásit uživatele. Podrobnosti implementace naleznete v dokumentaci v části reference.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Aplikace dostupné přes protokol HTTPS musí používat zabezpečené soubory cookie

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | [httpCookies Element (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [httpcookie.secure vlastnost](https://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroky** | Soubory cookie jsou obvykle přístupné pouze do oblasti, pro kterou byly vymezeny. Definice "domény" bohužel neobsahuje protokol, takže soubory cookie vytvořené přes protokol HTTPS jsou přístupné přes protokol HTTP. Atribut "secure" označuje prohlížeči, že soubor cookie by měl být k dispozici pouze prostřednictvím protokolu HTTPS. Ujistěte se, že všechny soubory cookie nastavené přes protokol HTTPS používají **zabezpečený** atribut. Požadavek lze vynutit v souboru web.config nastavením atributu requireSSL na true. Jedná se o upřednostňovaný přístup, protože bude vynucovat **zabezpečený** atribut pro všechny aktuální a budoucí soubory cookie bez nutnosti provádět další změny kódu.|

### <a name="example"></a>Příklad
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Nastavení je vynuceno i v případě, že protokol HTTP slouží k přístupu k aplikaci. Pokud se pro přístup k aplikaci používá protokol HTTP, nastavení přeruší aplikaci, protože soubory cookie jsou nastaveny pomocí zabezpečeného atributu a prohlížeč je neodešle zpět do aplikace.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Pokud je webová aplikace předávající stranou a IdP je server ADFS, lze zabezpečený atribut tokenu FedAuth nakonfigurovat nastavením requireSSL na True v `system.identityModel.services` části web.config:|

### <a name="example"></a>Příklad
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Všechny aplikace založené na http by měly specifikovat http pouze pro definici souboru cookie

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Atribut zabezpečeného souboru cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroky** | Chcete-li zmírnit riziko zveřejnění informací pomocí útoku skriptování mezi webovými servery (XSS), byl do souborů cookie zaveden nový atribut httpOnly a je podporován všemi hlavními prohlížeči. Atribut určuje, že soubor cookie není přístupný prostřednictvím skriptu. Pomocí souborů cookie HttpOnly webová aplikace snižuje možnost, že citlivé informace obsažené v souboru cookie mohou být ukradeny prostřednictvím skriptu a odeslány na webové stránky útočníka. |

### <a name="example"></a>Příklad
Všechny aplikace založené na protokolu HTTP, které používají soubory cookie, by měly v definici souboru cookie určit hodnotu HttpOnly implementující následující konfiguraci v souboru web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | webové formuláře |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Vlastnost FormsAuthentication.requiressl](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroky** | Hodnota vlastnosti RequireSSL je nastavena v konfiguračním souboru pro ASP.NET aplikaci pomocí atributu requireSSL konfiguračního prvku. V souboru Web.config pro vaši ASP.NET aplikaci můžete určit, zda je k vrácení souboru cookie ověřování formulářů na server vyžadováno protokol SSL (SSL) nastavením atributu requireSSL.|

### <a name="example"></a>Příklad 
Následující příklad kódu nastaví atribut requireSSSL v souboru Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5 |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | [Konfigurace Windows Identity Foundation (WIF) – část II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroky** | Chcete-li nastavit httpOnly atribut pro Soubory cookie FedAuth, hideFromCsript hodnota atributu by měla být nastavena na True. |

### <a name="example"></a>Příklad
Následující konfigurace ukazuje správnou konfiguraci:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Zmírnit útoky na ASP.NET webových stránkách ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Padělání požadavků mezi weby (CSRF nebo XSRF) je typ útoku, při kterém může útočník provádět akce v kontextu zabezpečení relace vytvořené jiným uživatelem na webu. Cílem je upravit nebo odstranit obsah, pokud se cílená webová stránka spoléhá výhradně na soubory cookie relace k ověření přijaté žádosti. Útočník by mohl tuto chybu zabezpečení zneužít tak, že by prohlížeč jiného uživatele načetl adresu URL pomocí příkazu z ohroženého webu, ke kterému je uživatel již přihlášen. Existuje mnoho způsobů, jak to útočník může udělat, například hostováním jiného webu, který načte prostředek z ohroženého serveru, nebo přiměje težkou, aby uživatel klikl na odkaz. Útoku lze zabránit, pokud server odešle klientovi další token, vyžaduje, aby klient zahrnul tento token do všech budoucích požadavků, a ověří, zda všechny budoucí požadavky obsahují token, který se vztahuje k aktuální relaci, například pomocí ASP.NET AntiForgeryToken nebo ViewState. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Prevence XSRF/CSRF v ASP.NET MVC a na webových stránkách](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroky** | Anti-CSRF a ASP.NET MVC `AntiForgeryToken` formuláře - Použijte pomocnou metodu na zobrazení; dát `Html.AntiForgeryToken()` do formy, například,|

### <a name="example"></a>Příklad
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Příklad
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad
Současně Html.AntiForgeryToken() dává návštěvníkovi soubor cookie s názvem __RequestVerificationToken, se stejnou hodnotou jako náhodná skrytá hodnota uvedená výše. Dále chcete-li ověřit příchozí formulář post, přidejte filtr [ValidateAntiForgeryToken] do metody cílové akce. Například:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorizační filtr, který kontroluje, že:
* Příchozí požadavek má soubor cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položku s názvem __RequestVerificationToken
* Tyto soubory `Request.Form` cookie a hodnoty se shodují Za předpokladu, že vše je v pořádku, požadavek prochází jako obvykle. Ale pokud ne, pak selhání autorizace se zprávou "Požadovaný token proti padělání nebyl zadán nebo byl neplatný". 

### <a name="example"></a>Příklad
Anti-CSRF a AJAX: Token formuláře může být problém pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, nikoli data formuláře HTML. Jedním z řešení je odeslání tokenů ve vlastní hlavičce HTTP. Následující kód používá syntaxi Razor ke generování tokenů a potom přidá tokeny do požadavku AJAX. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Příklad
Při zpracování požadavku extrahujte tokeny z hlavičky požadavku. Potom volání AntiForgery.Validate metoda k ověření tokeny. Metoda Validate vyvolá výjimku, pokud tokeny nejsou platné.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | webové formuláře |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Využijte výhod ASP.NET vestavěných funkcí k odrazení webových útoků](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroky** | CsRF útoky v aplikacích založených na webových formulářích lze zmírnit nastavením ViewStateUserKey na náhodný řetězec, který se liší pro každého uživatele - ID uživatele nebo ještě lépe ID relace. Z mnoha technických a sociálních důvodů je ID relace mnohem vhodnější, protože ID relace je nepředvídatelné, časový interval a liší se na základě jednotlivých uživatelů.|

### <a name="example"></a>Příklad
Zde je kód, který musíte mít na všech svých stránkách:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Nastavit relaci pro životnost nečinnosti

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Vlastnost HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroky** | Časový výtek relace představuje událost, ke které dochází, když uživatel neprovede žádnou akci na webu během intervalu (definovaného webovým serverem). Událost na straně serveru změní stav uživatelské relace na "neplatný" (například "již není použita") a instruuje webový server, aby ji zničil (odstranění všech dat, která jsou v ní obsažena). Následující příklad kódu nastaví atribut relace časového opovězení na 15 minut v souboru Web.config.|

### <a name="example"></a>Příklad
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Povolení zjišťování hrozeb v Azure SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | webové formuláře |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Element formuláře pro ověřování (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Kroky** | Nastavení časového času lístku lístku ověřování formulářů na 15 minut|

### <a name="example"></a>Příklad
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroky** | Pokud je webová aplikace předávající stranou a ADFS je STS, životnost ověřovacích souborů cookie - tokeny FedAuth - lze nastavit následující konfigurací v web.config:|

### <a name="example"></a>Příklad
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Příklad
Také adfs vydané SAML token deklarace by měla být nastavena na 15 minut, spuštěním následující příkaz powershell na serveru ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementace správného odhlášení z aplikace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Proveďte správné odhlášení z aplikace, když uživatel stiskne tlačítko odhlásit. Při odhlášení by aplikace měla zničit relaci uživatele a také obnovit a zrušit hodnotu souboru cookie relace spolu s resetováním a zrušením hodnoty ověřovacího souboru cookie. Také pokud více relací jsou vázány na identitu jednoho uživatele, musí být společně ukončena na straně serveru v časovém odhlášení nebo odhlášení. Nakonec se ujistěte, že funkce odhlášení je k dispozici na každé stránce. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Zmírnit útoky na ASP.NET webová api napříč weby (CSRF)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Padělání požadavků mezi weby (CSRF nebo XSRF) je typ útoku, při kterém může útočník provádět akce v kontextu zabezpečení relace vytvořené jiným uživatelem na webu. Cílem je upravit nebo odstranit obsah, pokud se cílená webová stránka spoléhá výhradně na soubory cookie relace k ověření přijaté žádosti. Útočník by mohl tuto chybu zabezpečení zneužít tak, že by prohlížeč jiného uživatele načetl adresu URL pomocí příkazu z ohroženého webu, ke kterému je uživatel již přihlášen. Existuje mnoho způsobů, jak to útočník může udělat, například hostováním jiného webu, který načte prostředek z ohroženého serveru, nebo přiměje težkou, aby uživatel klikl na odkaz. Útoku lze zabránit, pokud server odešle klientovi další token, vyžaduje, aby klient zahrnul tento token do všech budoucích požadavků, a ověří, zda všechny budoucí požadavky obsahují token, který se vztahuje k aktuální relaci, například pomocí ASP.NET AntiForgeryToken nebo ViewState. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zabránění útokům na základě požadavků na příčné weby (CSRF) v ASP.NET webovérozhraní API](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroky** | Anti-CSRF a AJAX: Token formuláře může být problém pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, nikoli data formuláře HTML. Jedním z řešení je odeslání tokenů ve vlastní hlavičce HTTP. Následující kód používá syntaxi Razor ke generování tokenů a potom přidá tokeny do požadavku AJAX. |

### <a name="example"></a>Příklad
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Příklad
Při zpracování požadavku extrahujte tokeny z hlavičky požadavku. Potom volání AntiForgery.Validate metoda k ověření tokeny. Metoda Validate vyvolá výjimku, pokud tokeny nejsou platné.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Příklad
Anti-CSRF a ASP.NET MVC formuláře - Použijte antiforgeryToken pomocné metody na zobrazení; dát Html.AntiForgeryToken() do formy, například,
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Příklad
Výše uvedený příklad bude výstup něco jako následující:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad
Současně Html.AntiForgeryToken() dává návštěvníkovi soubor cookie s názvem __RequestVerificationToken, se stejnou hodnotou jako náhodná skrytá hodnota uvedená výše. Dále chcete-li ověřit příchozí formulář post, přidejte filtr [ValidateAntiForgeryToken] do metody cílové akce. Například:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Autorizační filtr, který kontroluje, že:
* Příchozí požadavek má soubor cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položku s názvem __RequestVerificationToken
* Tyto soubory `Request.Form` cookie a hodnoty se shodují Za předpokladu, že vše je v pořádku, požadavek prochází jako obvykle. Ale pokud ne, pak selhání autorizace se zprávou "Požadovaný token proti padělání nebyl zadán nebo byl neplatný".

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Zprostředkovatel identity – ADFS, Zprostředkovatel identity – Azure AD |
| **Odkazy**              | [Zabezpečení webového rozhraní API pomocí individuálních účtů a místního přihlášení v ASP.NET webové rozhraní API 2.2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroky** | Pokud je webové rozhraní API zabezpečeno pomocí OAuth 2.0, očekává v hlavičce žádosti o autorizaci nosný token a udělí přístup k požadavku pouze v případě, že je token platný. Na rozdíl od ověřování založeného na souborech cookie prohlížeče nepřipojují tokeny nosiče k požadavkům. Žádající klient musí explicitně připojit token nosiče v hlavičce požadavku. Proto pro ASP.NET webová api chráněná pomocí OAuth 2.0, tokeny nosiče jsou považovány za obranu proti útokům CSRF. Vezměte prosím na vědomí, že pokud část Aplikace MVC používá ověřování pomocí formulářů (tj. používá soubory cookie), musí webová aplikace MVC používat tokeny proti padělání. |

### <a name="example"></a>Příklad
Webové rozhraní API musí být informováno, aby se spoléhalo pouze na nosné tokeny a ne na soubory cookie. To lze provést pomocí následující `WebApiConfig.Register` konfigurace v metodě:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda SuppressDefaultHostAuthentication říká webovému rozhraní API, aby ignorovalo jakékoli ověřování, ke kterému dojde před tím, než požadavek dosáhne kanálu webového rozhraní API, a to buď službou IIS, nebo middlewarem OWIN. Tímto způsobem můžeme omezit webové rozhraní API k ověření pouze pomocí nosné tokeny.

---
title: Správa relací – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o zmírnění správy relací pro hrozby vystavené v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518097"
---
# <a name="security-frame-session-management"></a>Rámec zabezpečení: Správa relací
| Produkt/služba | Článek |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementace správného odhlášení pomocí metod ADAL při použití Azure AD](#logout-adal)</li></ul> |
| **Zařízení IoT** | <ul><li>[Použití konečných životností pro vygenerované tokeny SaS](#finite-tokens)</li></ul> |
| **Azure Document DB** | <ul><li>[Použití minimální životnosti tokenů pro vygenerované tokeny prostředků](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementace správného odhlášení pomocí metod WsFederation při použití ADFS](#wsfederation-logout)</li></ul> |
| **Server identit** | <ul><li>[Implementace správného odhlášení při použití serveru identity](#proper-logout)</li></ul> |
| **Webová aplikace** | <ul><li>[Aplikace dostupné přes HTTPS musí používat zabezpečené soubory cookie.](#https-secure-cookies)</li><li>[Všechny aplikace založené na protokolu HTTP by měly určovat pouze http pro definici souboru cookie.](#cookie-definition)</li><li>[Zmírnění útoků proti útokům přes CSRF (site-to Request) na webových stránkách ASP.NET](#csrf-asp)</li><li>[Nastavte relaci pro dobu neaktivity.](#inactivity-lifetime)</li><li>[Implementace správného odhlášení z aplikace](#proper-app-logout)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Zmírnění útoků na webové rozhraní API v ASP.NET proti útokům přes lokalitu (CSRF)](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementace správného odhlášení pomocí metod ADAL při použití Azure AD

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Pokud aplikace spoléhá na přístupový token vydaný službou Azure AD, měla by obslužná rutina události odhlášení zavolat. |

### <a name="example"></a>Příklad
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Příklad
Měla by také zničit relaci uživatele voláním metody Session. Abandon (). Následující metoda ukazuje zabezpečenou implementaci odhlášení uživatele:
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

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Použití konečných životností pro vygenerované tokeny SaS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Tokeny SaS generované pro ověřování v Azure IoT Hub by měly mít omezenou dobu vypršení platnosti. Ponechte životnost tokenů SaS minimální, aby se omezila doba, kterou je možné znovu přehrát v případě ohrožení bezpečnosti tokenů.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Použití minimální životnosti tokenů pro vygenerované tokeny prostředků

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Document DB | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Snižte časový rozsah tokenu prostředku na minimální požadovanou hodnotu. Tokeny prostředků mají výchozí časový interval pro 1 hodinu.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Implementace správného odhlášení pomocí metod WsFederation při použití ADFS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | ADFS | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Pokud aplikace spoléhá na token STS vydaný službou AD FS, obslužná rutina události odhlášení by měla zavolat metodu WSFederationAuthenticationModule. FederatedSignOut (), která uživatele odhlásí. Aktuální relace by měla být také zničena a hodnota tokenu relace by měla být resetována a nullified.|

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

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Implementace správného odhlášení při použití serveru identity

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Server identit | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [IdentityServer3 – federované odhlašování](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroky** | IdentityServer podporuje možnost federovat s externími zprostředkovateli identity. Když se uživatel odhlásí od nadřazeného poskytovatele identity, může se v závislosti na použitém protokolu zobrazit oznámení, když se uživatel odhlásí. Umožňuje IdentityServer upozorňování svých klientů, aby mohli uživatele také odhlásit. Podrobnosti o implementaci najdete v dokumentaci v části odkazy.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Aplikace dostupné přes HTTPS musí používat zabezpečené soubory cookie.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – OnPrem |
| **Reference**              | [httpCookies – element (schéma nastavení ASP.NET)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100)), [vlastnost HttpCookie. Secure](/dotnet/api/system.web.httpcookie.secure) |
| **Kroky** | Soubory cookie jsou obvykle přístupné pouze pro doménu, pro kterou byly vymezeny. Definice "doména" ale nezahrnuje protokol, takže soubory cookie, které jsou vytvořené přes protokol HTTPS, jsou přístupné přes HTTP. Atribut "Secure" označuje prohlížeč, že by měl být soubor cookie zpřístupněn pouze přes protokol HTTPS. Zajistěte, aby všechny soubory cookie nastavené přes protokol HTTPS používaly **zabezpečený** atribut. Požadavek lze vyhovět v souboru web.config nastavením atributu vlastnost requireSSL na hodnotu true. Je to preferovaný přístup, protože vygeneruje **zabezpečený** atribut pro všechny aktuální a budoucí soubory cookie, aniž by bylo potřeba provádět další změny kódu.|

### <a name="example"></a>Příklad
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Nastavení se vynutilo i v případě, že se pro přístup k aplikaci používá protokol HTTP. Pokud se pro přístup k aplikaci používá protokol HTTP, nastavení aplikaci přeruší, protože soubory cookie jsou nastaveny pomocí atributu Secure a prohlížeč je nepošle zpět do aplikace.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | EnvironmentType – OnPrem |
| **Reference**              | –  |
| **Kroky** | Když je webová aplikace předávající stranou a IdP je server ADFS, zabezpečený atribut tokenu FedAuth lze nakonfigurovat nastavením vlastnost requireSSL na hodnotu true v `system.identityModel.services` části web.config:|

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

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Všechny aplikace založené na protokolu HTTP by měly určovat pouze http pro definici souboru cookie.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Atribut zabezpečeného souboru cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroky** | Aby bylo možné zmírnit riziko odhalení informací pomocí útoku skriptování mezi weby (XSS), byl do souborů cookie zaveden nový atribut-httpOnly-, který je podporovaný všemi hlavními prohlížeči. Atribut určuje, že soubor cookie není přístupný prostřednictvím skriptu. Díky použití souborů cookie HttpOnly může webová aplikace omezit možnost odcizení citlivých informací obsažených v souboru cookie prostřednictvím skriptu a odeslání na web útočníka. |

### <a name="example"></a>Příklad
Všechny aplikace založené na protokolu HTTP, které používají soubory cookie, by měly v definici souboru cookie určovat HttpOnly implementací následující konfigurace v web.config:
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
| **Atributy**              | –  |
| **Reference**              | [FormsAuthentication. vlastnost RequireSSL – vlastnost](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **Kroky** | Hodnota vlastnosti vlastnost RequireSSL je nastavena v konfiguračním souboru pro aplikaci ASP.NET pomocí atributu vlastnost requireSSL konfiguračního elementu. Můžete zadat v souboru Web.config pro vaši aplikaci ASP.NET, jestli je pro vrácení souboru cookie s ověřováním pomocí formulářů SSL (Secure Sockets Layer) (vlastnost requireSSL) na serveru potřeba, aby se na server vrátil soubor cookie s ověřováním pomocí formulářů, a to tak, že nastaví atribut.|

### <a name="example"></a>Příklad 
Následující příklad kódu nastaví atribut vlastnost requireSSL v souboru Web.config.
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
| **Atributy**              | EnvironmentType – OnPrem |
| **Reference**              | [Konfigurace Windows Identity Foundation (WIF) – část II](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) |
| **Kroky** | Chcete-li nastavit atribut httpOnly pro soubory cookie FedAuth, měla by být hodnota atributu hideFromCsript nastavena na hodnotu true. |

### <a name="example"></a>Příklad
Následující konfigurace zobrazuje správnou konfiguraci:
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

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Zmírnění útoků proti útokům přes CSRF (site-to Request) na webových stránkách ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Padělání žádostí mezi weby (CSRF nebo XSRF) je typ útoku, ve kterém může útočník provést akce v kontextu zabezpečení navázané relace jiného uživatele na webu. Cílem je upravit nebo odstranit obsah, pokud cílový web spoléhá na přijatý požadavek výhradně na soubory cookie relace. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získá jiný uživatel, který načte adresu URL pomocí příkazu z zranitelné lokality, ve které je uživatel již přihlášen. Existuje mnoho způsobů, jak útočník to udělat, například hostováním jiného webu, který načte prostředek z ohroženého serveru, nebo když uživatel klikne na odkaz. Útok může být znemožněn, pokud server odešle klientovi další token, vyžaduje, aby klient tento token zahrnul do všech budoucích požadavků a ověří, že všechny budoucí požadavky obsahují token, který se vztahuje k aktuální relaci, například pomocí ASP.NET AntiForgeryToken nebo ViewState. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | –  |
| **Reference**              | [Prevence XSRF/CSRF v ASP.NET MVC a na webových stránkách](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroky** | CSRF a ASP.NET MVC Forms – použijte `AntiForgeryToken` pomocnou metodu pro zobrazení; vložte `Html.AntiForgeryToken()` do formuláře, například|

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
Ve stejnou dobu HTML. AntiForgeryToken () přiřadí návštěvníkovi soubor cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodná Skrytá hodnota uvedená výše. Dále pro ověření příchozího příspěvku formuláře přidejte filtr [ValidateAntiForgeryToken] do metody cíle akce. Například:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, který kontroluje:
* Příchozí požadavek má soubor cookie s názvem __RequestVerificationToken
* Příchozí požadavek obsahuje položku s `Request.Form` názvem __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` hodnoty odpovídají za předpokladu, že požadavek prochází běžným způsobem. Ale v takovém případě nedošlo k chybě autorizace se zprávou "vyžadovaný token proti padělání se nezadal nebo byl neplatný". 

### <a name="example"></a>Příklad
Anti-CSRF a AJAX: token formuláře může být problémem pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, nikoli data formuláře HTML. Jedním z řešení je odeslat tokeny ve vlastní hlavičce protokolu HTTP. Následující kód používá syntaxe Razor k vygenerování tokenů a následně přidá tokeny do požadavku AJAX. 
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
Při zpracování žádosti extrahujte tokeny z hlavičky žádosti. Pak zavoláním metody. Validate ověřte tokeny. Metoda Validate vyvolá výjimku, pokud tokeny nejsou platné.
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
| **Atributy**              | –  |
| **Reference**              | [Využijte výhod integrovaných funkcí ASP.NET k Fendí webových útoků.](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **Kroky** | Útoky CSRF v aplikacích založených na webformách je možné zmírnit nastavením ViewStateUserKey na náhodný řetězec, který se u každého uživatele změní na ID uživatele nebo je ještě lepší, ID relace. Z řady technických a sociálních důvodů je ID relace mnohem lepší, protože ID relace je nepředvídatelné, má časový limit a liší se podle jednotlivých uživatelů.|

### <a name="example"></a>Příklad
Tady je kód, který musíte mít na všech stránkách:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Nastavte relaci pro dobu neaktivity.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [HttpSessionState. Timeout – vlastnost](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **Kroky** | Časový limit relace představuje událost, když uživatel neprovede žádnou akci na webu během intervalu (definovaného webovým serverem). Událost na straně serveru mění stav uživatelské relace na neplatnou (například již nepoužito) a vydá pokyn webovému serveru, aby ho zničil (odstraní všechna data, která jsou v něm obsažená). Následující příklad kódu nastaví atribut relace Timeout na 15 minut v souboru Web.config.|

### <a name="example"></a>Příklad
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Povolení detekce hrozeb v Azure SQL

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | webové formuláře |
| **Atributy**              | –  |
| **Reference**              | [Element Forms pro ověřování (schéma nastavení ASP.NET)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **Kroky** | Nastavte časový limit souboru cookie lístku pro ověřování formulářů na 15 minut.|

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
| **Atributy**              | EnvironmentType – OnPrem |
| **Reference**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroky** | Když je webová aplikace předávající stranou a služba AD FS je STS, doba života souborů cookie ověřování – FedAuth tokeny-lze nastavit pomocí následující konfigurace v web.config:|

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
Životnost tokenu deklarací SAML vydaných službou ADFS by měla být také nastavená na 15 minut, a to spuštěním následujícího příkazu PowerShellu na serveru ADFS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementace správného odhlášení z aplikace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Když uživatel stiskne tlačítko Odhlásit se, provede se správným odhlášením z aplikace. Po odhlášení aplikace by měla zničit uživatelskou relaci a také resetovat a nezruší hodnotu cookie relace, společně s resetováním a hodnotou souboru cookie pro ověřování nullifying. Pokud je více relací svázáno s identitou jednoho uživatele, musí být souhrnně ukončeny na straně serveru v časovém limitu nebo odhlášení. Nakonec zajistěte, aby byly funkce odhlášení k dispozici na každé stránce. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Zmírnění útoků na webové rozhraní API v ASP.NET proti útokům přes lokalitu (CSRF)

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Padělání žádostí mezi weby (CSRF nebo XSRF) je typ útoku, ve kterém může útočník provést akce v kontextu zabezpečení navázané relace jiného uživatele na webu. Cílem je upravit nebo odstranit obsah, pokud cílový web spoléhá na přijatý požadavek výhradně na soubory cookie relace. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získá jiný uživatel, který načte adresu URL pomocí příkazu z zranitelné lokality, ve které je uživatel již přihlášen. Existuje mnoho způsobů, jak útočník to udělat, například hostováním jiného webu, který načte prostředek z ohroženého serveru, nebo když uživatel klikne na odkaz. Útok může být znemožněn, pokud server odešle klientovi další token, vyžaduje, aby klient tento token zahrnul do všech budoucích požadavků a ověří, že všechny budoucí požadavky obsahují token, který se vztahuje k aktuální relaci, například pomocí ASP.NET AntiForgeryToken nebo ViewState. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | –  |
| **Reference**              | [Prevence útoků na CSRF (site-to-site Request) ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroky** | Anti-CSRF a AJAX: token formuláře může být problémem pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, nikoli data formuláře HTML. Jedním z řešení je odeslat tokeny ve vlastní hlavičce protokolu HTTP. Následující kód používá syntaxe Razor k vygenerování tokenů a následně přidá tokeny do požadavku AJAX. |

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
Při zpracování žádosti extrahujte tokeny z hlavičky žádosti. Pak zavoláním metody. Validate ověřte tokeny. Metoda Validate vyvolá výjimku, pokud tokeny nejsou platné.
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
Anti-CSRF a ASP.NET MVC Forms – použijte pomocnou metodu AntiForgeryToken v zobrazeních; Vložte do formuláře kód HTML. AntiForgeryToken (), například
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Příklad
V předchozím příkladu bude výstup vypadat přibližně takto:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad
Ve stejnou dobu HTML. AntiForgeryToken () přiřadí návštěvníkovi soubor cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodná Skrytá hodnota uvedená výše. Dále pro ověření příchozího příspěvku formuláře přidejte filtr [ValidateAntiForgeryToken] do metody cíle akce. Například:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, který kontroluje:
* Příchozí požadavek má soubor cookie s názvem __RequestVerificationToken
* Příchozí požadavek obsahuje položku s `Request.Form` názvem __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` hodnoty odpovídají za předpokladu, že požadavek prochází běžným způsobem. Ale v takovém případě nedošlo k chybě autorizace se zprávou "vyžadovaný token proti padělání se nezadal nebo byl neplatný".

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC5, MVC6 |
| **Atributy**              | Zprostředkovatel identity – ADFS, zprostředkovatel identity – Azure AD |
| **Reference**              | [Zabezpečení webového rozhraní API pomocí individuálních účtů a místního přihlášení v ASP.NET Web API 2,2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroky** | Pokud je webové rozhraní API zabezpečené pomocí OAuth 2,0, očekává se token nosiče v hlavičce autorizační žádosti a udělí přístup k žádosti jenom v případě, že je token platný. Na rozdíl od ověřování na základě souborů cookie nepřipojují tokeny nosičům požadavky. Žádající klient musí explicitně připojit nosný token v hlavičce požadavku. Proto pro ASP.NET webová rozhraní API chráněná pomocí OAuth 2,0 jsou nosné tokeny považovány za obranu před útoky CSRF. Upozorňujeme, že pokud v části MVC aplikace používáte ověřování pomocí formulářů (tj. používá soubory cookie), musí webové aplikace MVC používat tokeny pro ochranu proti padělání. |

### <a name="example"></a>Příklad
Webové rozhraní API je potřeba informovat, aby se mohlo spoléhat jenom na tokeny nosiče a ne na soubory cookie. To lze provést pomocí následující konfigurace v `WebApiConfig.Register` metodě:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda SuppressDefaultHostAuthentication oznamuje webovému rozhraní API, že bude ignorovat jakékoli ověřování, ke kterému dojde předtím, než požadavek dosáhne kanálu webového rozhraní API, a to buď prostřednictvím služby IIS, nebo middleware OWIN. Tímto způsobem můžeme omezit webové rozhraní API tak, aby se ověřilo jenom pomocí nosných tokenů.
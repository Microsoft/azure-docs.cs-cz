---
title: Relace správy – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 97953779f1132d89c7ad07abdb4e08c0f476f4b9
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841809"
---
# <a name="security-frame-session-management"></a>Zabezpečení rámce: Správa relací
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementace správné odhlašování pomocí knihovny ADAL metod při použití služby Azure AD](#logout-adal)</li></ul> |
| Zařízení IoT | <ul><li>[Použití omezeného životnost generovaných tokenů SaS](#finite-tokens)</li></ul> |
| **Azure Documentdb** | <ul><li>[Pomocí minimální životností tokenů pro generovaných tokenů prostředku](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementace správné odhlašování pomocí metod WsFederation při použití služby AD FS](#wsfederation-logout)</li></ul> |
| **Serveru identit** | <ul><li>[Implementace správné odhlášení při používání serveru identit](#proper-logout)</li></ul> |
| **Webové aplikace** | <ul><li>[Aplikace je k dispozici prostřednictvím protokolu HTTPS, musíte použít bezpečné soubory cookie](#https-secure-cookies)</li><li>[Všechny aplikace na základě protokolu http měla určit pouze pro definici souboru cookie http](#cookie-definition)</li><li>[Pomáhá zmírnit dopady útoků proti padělání žádosti mezi weby (CSRF) na rozhraní ASP.NET web pages](#csrf-asp)</li><li>[Nastavení relace po dobu platnosti nečinnosti](#inactivity-lifetime)</li><li>[Implementace správné odhlášení z aplikace](#proper-app-logout)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Pomáhá zmírnit dopady útoků proti padělání žádosti mezi weby (CSRF) na rozhraní ASP.NET Web API](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementace správné odhlašování pomocí knihovny ADAL metod při použití služby Azure AD

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud aplikace závisí na přístupu u tokenu vydaného službou Azure AD, by měly volat obslužná rutina události odhlášení |

### <a name="example"></a>Příklad:
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Příklad:
Zavoláním metody Session.Abandon() ji by měl také zničit uživatelské relace. Tuto metodu ukazuje zabezpečenou implementaci odhlášení uživatele:
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

## <a id="finite-tokens"></a>Použití omezeného životnost generovaných tokenů SaS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Tokeny SaS vygenerovaný pro ověřování pro službu Azure IoT Hub by měl mít omezené vypršení platnosti období. Životnost tokenů SaS zachovejte na minimum Chcete-li omezit množství času, které mohou být přehrály v případě, že dojde k ohrožení tokeny.|

## <a id="resource-tokens"></a>Pomocí minimální životností tokenů pro generovaných tokenů prostředku

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Minimální hodnota povolená snížit časový interval tokenu prostředku. Tokeny prostředků mají výchozí platný časový interval 1 hodina.|

## <a id="wsfederation-logout"></a>Implementace správné odhlašování pomocí metod WsFederation při použití služby AD FS

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | ADFS | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud aplikace závisí na službu tokenů zabezpečení u tokenu vydaného službou ADFS, by měla obslužná rutina události odhlášení voláním metody WSFederationAuthenticationModule.FederatedSignOut() Odhlásit uživatele. Také by měl zničeny v aktuální relaci, a hodnota tokenu relace by měl obnovit a zrušeny.|

### <a name="example"></a>Příklad:
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

## <a id="proper-logout"></a>Implementace správné odhlášení při používání serveru identit

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Federované IdentityServer3 Odhlásit se.](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroky** | IdentityServer podporuje možnost pro vytvoření federace s externích zprostředkovatelů identity. Když se uživatel odhlásí od zprostředkovatele upstream identity, v závislosti na protokol použitý, je možné pro příjem oznámení při odhlášení uživatele. To umožňuje IdentityServer tak můžou zaregistrovat také uživatele upozornit svým klientům. V části odkazy na podrobnosti implementace naleznete v dokumentaci.|

## <a id="https-secure-cookies"></a>Aplikace je k dispozici prostřednictvím protokolu HTTPS, musíte použít bezpečné soubory cookie

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | EnvironmentType - místního prostředí |
| **Odkazy**              | [Element (schéma nastavení technologie ASP.NET) httpCookies](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure vlastnost](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroky** | Soubory cookie jsou obvykle přístupné pouze do domény, pro kterou byly obor. Definice "domény" bohužel neobsahuje protokol, tak, aby soubory cookie, které jsou vytvořeny pomocí protokolu HTTPS byly přístupné přes protokol HTTP. Atribut "zabezpečení" označuje do prohlížeče, že soubor cookie by měl být vytvořena pouze k dispozici přes protokol HTTPS. Ujistěte se, že všechny soubory cookie nastavené přes HTTPS pomocí **zabezpečené** atribut. Požadavek lze vynutit v souboru web.config nastavením atributu requireSSL na hodnotu true. Je oblíbený přístup, protože bude vynucovat **zabezpečené** atribut pro všechny aktuální a budoucí soubory cookie bez nutnosti provádět žádné další změny kódu.|

### <a name="example"></a>Příklad:
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Nastavení se nevynucuje i v případě protokolu HTTP se používá pro přístup k aplikaci. Pokud HTTP slouží k přístupu k aplikaci, přeruší nastavení aplikace, protože soubory cookie jsou nastavené s atributem zabezpečené a prohlížeč nebude odeslat zpět do aplikace.

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře, MVC5 |
| **Atributy**              | EnvironmentType - místního prostředí |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud je webová aplikace předávající strany a zprostředkovatele identity je server služby AD FS, zabezpečené atribut tokenu FedAuth je možné nakonfigurovat tak, že nastavíte vlastnost requireSSL na hodnotu True v `system.identityModel.services` části souboru Web.config:|

### <a name="example"></a>Příklad:
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a id="cookie-definition"></a>Všechny aplikace na základě protokolu http měla určit pouze pro definici souboru cookie http

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Atribut zabezpečení souboru Cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroky** | Pokud chcete omezit riziko zpřístupnění informací s s útoky skriptování napříč weby (XSS), nový atribut – httpOnly - byl zaveden souborů cookie pomocí nastavení a podporuje všechny hlavní prohlížeče. Atribut určuje, že soubor cookie není přístupné prostřednictvím skriptu. Webové aplikace pomocí souborů cookie HttpOnly snižuje možnost, že můžete citlivé informace obsažené v souboru cookie, který vám ho někdo ukradne prostřednictvím skriptů a odeslány útočníka Web. |

### <a name="example"></a>Příklad:
Všechny aplikace založené na protokolu HTTP, které používají soubory cookie by měl v definici souboru cookie zadejte HttpOnly díky implementaci následující konfigurace v souboru web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vlastnost FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroky** | Hodnota vlastnosti RequireSSL je nastavena v konfiguračním souboru pro aplikaci ASP.NET pomocí atributu requireSSL konfiguračního elementu. Můžete použít v souboru Web.config pro vaši aplikaci ASP.NET, zda protokol SSL (Secure Sockets Layer) je nutné pro navrácení souboru cookie ověřování založené na formulářích na server tak, že nastavíte vlastnost requireSSL atribut.|

### <a name="example"></a>Příklad: 
Následující příklad kódu nastaví atribut requireSSL v souboru Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5 |
| **Atributy**              | EnvironmentType - místního prostředí |
| **Odkazy**              | [Windows Identity Foundation (WIF) konfigurace – část II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroky** | Pokud chcete nastavit atribut httpOnly soubory cookie FedAuth, by měl být hideFromCsript hodnotu atributu nastavit na hodnotu True. |

### <a name="example"></a>Příklad:
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

## <a id="csrf-asp"></a>Pomáhá zmírnit dopady útoků proti padělání žádosti mezi weby (CSRF) na rozhraní ASP.NET web pages

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Proti padělání požadavků mezi weby (CSRF nebo XSRF) je typ útoku, ve kterém může útočník provádět akce v kontextu zabezpečení navázanou relaci jiného uživatele na webovém serveru. Cílem je upravit nebo odstranit obsah, pokud cílové webové stránky spoléhá výhradně na soubory cookie relace k ověření přijatý požadavek. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získáme prohlížeče jiný uživatel se načíst adresu URL pomocí příkazu z ohrožených lokality, na kterém je již přihlášeni. Existuje mnoho způsobů pro útočníka, stačí, třeba hostování na jiný web, který načítá prostředku z ohrožených serveru nebo získávání uživatel kliknutím na odkaz. Útok lze zabránit, pokud je server odešle klientovi další token, vyžaduje klient mají být zahrnuty všechny budoucí žádosti o token a ověřuje, že všechny budoucí požadavky zahrnují token, který se týká aktuální relace, například s použitím technologie ASP.NET AntiForgeryToken nebo vlastnosti ViewState. |

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Prevence XSRF/CSRF v ASP.NET MVC a na webových stránkách](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroky** | Anti-útokům CSRF a formulářů rozhraní ASP.NET MVC – použijte `AntiForgeryToken` pomocnou metodu pro zobrazení; put `Html.AntiForgeryToken()` do formuláře, například|

### <a name="example"></a>Příklad:
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Příklad:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad:
Ve stejnou dobu poskytuje Html.AntiForgeryToken() návštěvníka souboru cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodnou hodnotu skryté uvedené výše. Dále ověřit příchozí odeslaného formuláře, přidejte filtr [ValidateAntiForgeryToken] cílové metody akce. Příklad:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, která kontroluje, zda:
* Příchozí požadavek má souboru cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položka __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` shody hodnoty za předpokladu, že všechny je dobře, požadavek prochází normálním způsobem. Ale pokud ne, pak chybě autorizace se zprávou "požadovaný token proti padělání nebyl zadán nebo byl neplatný". 

### <a name="example"></a>Příklad:
Anti-útokům CSRF a AJAX: tokenu formuláře může být problém pro odesílání požadavků AJAX, protože požadavek AJAX může odesílat data JSON, ne data formuláře HTML. Jedním z řešení je odesílat tokeny ve vlastní hlavičku HTTP. Následující kód používá syntaxi Razor ke generování tokenů a pak přidá tokenů pro požadavek AJAX. 
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

### <a name="example"></a>Příklad:
Při zpracování požadavku extrahujte tokeny v hlavičce požadavku. Poté zavolejte metodu AntiForgery.Validate k ověřování tokenů. Metodu Validate vyvolá výjimku, pokud tokeny nejsou platné.
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

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Využijte výhod ASP.NET integrované funkce, které jim webovými útoky](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroky** | Útokům CSRF v webového formuláře na základě aplikace můžete minimalizovat tak, že nastavíte ViewStateUserKey je náhodný řetězec, který se liší pro jednotlivé uživatele – ID uživatele nebo ještě lépe, ID relace. Pro řadu technických a sociální důvodů ID relace je mnohem lépe vyhovovat, protože vyprší časový limit relace, které nepředvídatelné, ID a se liší pro jednotlivé uživatele zvlášť.|

### <a name="example"></a>Příklad:
Tady je kód, který je potřeba mít na všech stránkách:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Nastavení relace po dobu platnosti nečinnosti

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vlastnost HttpSessionState.Timeout](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroky** | Časový limit relace představuje událost, ke kterým dochází, když uživatel neprovede žádnou akci na webové stránce během intervalu (definované webový server). Událost na straně serveru a změnit stav relace uživatele na "neplatný" (například "nepoužívá už") a dáte pokyn, aby webový server ke zničení ho (odstranění všech dat obsažených do něj). Následující příklad kódu nastaví atribut časového limitu relace na 15 minut v souboru Web.config.|

### <a name="example"></a>Příklad:
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a id="threat-detection"></a>Povolit detekci hrozeb v Azure SQL
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms |
| **Attributes**              | N/A  |
| **References**              | [forms Element for authentication (ASP.NET Settings Schema)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Steps** | Set the Forms Authentication Ticket cookie timeout to 15 minutes|

### Example
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře, MVC5 |
| **Atributy**              | EnvironmentType - místního prostředí |
| **Odkazy**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroky** | Když je služba tokenů zabezpečení AD FS, předávající strana je webová aplikace dobu života souboru cookie ověřování - FedAuth tokeny – můžete nastavit následující konfigurace v souboru web.config:|

### <a name="example"></a>Příklad:
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

### <a name="example"></a>Příklad:
Také deklarací služby AD FS vydala SAML životnost tokenu musí být nastavena na 15 minut, spuštěním následujícího příkazu powershellu na serveru služby AD FS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementace správné odhlášení z aplikace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Proveďte řádné odhlásit z aplikace, když uživatel stiskne odhlásit tlačítko. Při odhlašování aplikace by měla zničit uživatelské relace a také resetovat a nezruší se tím hodnota souboru cookie relace, spolu s resetováním a anulovány hodnoty souboru cookie ověřování. Také když více relací jsou svázány s jednou identitou uživatele, se musí být souhrnně ukončen na straně serveru na časový limit nebo se odhlaste. A konečně Ujistěte se, že funkce odhlášení jsou k dispozici na každé stránce. |

## <a id="csrf-api"></a>Pomáhá zmírnit dopady útoků proti padělání žádosti mezi weby (CSRF) na rozhraní ASP.NET Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Proti padělání požadavků mezi weby (CSRF nebo XSRF) je typ útoku, ve kterém může útočník provádět akce v kontextu zabezpečení navázanou relaci jiného uživatele na webovém serveru. Cílem je upravit nebo odstranit obsah, pokud cílové webové stránky spoléhá výhradně na soubory cookie relace k ověření přijatý požadavek. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získáme prohlížeče jiný uživatel se načíst adresu URL pomocí příkazu z ohrožených lokality, na kterém je již přihlášeni. Existuje mnoho způsobů pro útočníka, stačí, třeba hostování na jiný web, který načítá prostředku z ohrožených serveru nebo získávání uživatel kliknutím na odkaz. Útok lze zabránit, pokud je server odešle klientovi další token, vyžaduje klient mají být zahrnuty všechny budoucí žádosti o token a ověřuje, že všechny budoucí požadavky zahrnují token, který se týká aktuální relace, například s použitím technologie ASP.NET AntiForgeryToken nebo vlastnosti ViewState. |

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Prevence útoků proti padělání (CSRF) žádosti více webů v ASP.NET Web API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroky** | Anti-útokům CSRF a AJAX: tokenu formuláře může být problém pro odesílání požadavků AJAX, protože požadavek AJAX může odesílat data JSON, ne data formuláře HTML. Jedním z řešení je odesílat tokeny ve vlastní hlavičku HTTP. Následující kód používá syntaxi Razor ke generování tokenů a pak přidá tokenů pro požadavek AJAX. |

### <a name="example"></a>Příklad:
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

### <a name="example"></a>Příklad:
Při zpracování požadavku extrahujte tokeny v hlavičce požadavku. Poté zavolejte metodu AntiForgery.Validate k ověřování tokenů. Metodu Validate vyvolá výjimku, pokud tokeny nejsou platné.
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

### <a name="example"></a>Příklad:
Anti-útokům CSRF a formulářů rozhraní ASP.NET MVC – využít pomocnou metodu AntiForgeryToken na zobrazení Vložte Html.AntiForgeryToken() do formuláře, například
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Příklad:
Výše uvedený příklad bude výstup přibližně takto:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad:
Ve stejnou dobu poskytuje Html.AntiForgeryToken() návštěvníka souboru cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodnou hodnotu skryté uvedené výše. Dále ověřit příchozí odeslaného formuláře, přidejte filtr [ValidateAntiForgeryToken] cílové metody akce. Příklad:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, která kontroluje, zda:
* Příchozí požadavek má souboru cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položka __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` shody hodnoty za předpokladu, že všechny je dobře, požadavek prochází normálním způsobem. Ale pokud ne, pak chybě autorizace se zprávou "požadovaný token proti padělání nebyl zadán nebo byl neplatný".

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC5, MVC6 |
| **Atributy**              | Poskytovatel Identity poskytovatele – služby AD FS, identity – Azure AD |
| **Odkazy**              | [Zabezpečení webového rozhraní API s jednotlivých účtů a místní přihlášení v rozhraní ASP.NET Web API 2.2](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroky** | Pokud je webové rozhraní API zabezpečené pomocí OAuth 2.0, potom očekává, že nosného tokenu v autorizační hlavičce požadavku a udělí přístup k požadavku, pouze pokud je token platný. Na rozdíl od ověřování na základě souborů cookie prohlížeče nepřipojujte nosné tokeny požadavků. Klienta, který je potřeba explicitně připojte nosný token v hlavičce požadavku. Pro ASP.NET webová rozhraní API chráněné pomocí OAuth 2.0, proto jsou nosné tokeny za obrana proti útokům CSRF. Všimněte si, že pokud se část aplikace MVC používá ověřování pomocí formulářů (tj, soubory cookie používá), tokenů proti padělání muset používat webové aplikace MVC. |

### <a name="example"></a>Příklad:
Webové rozhraní API má být informováni spoléhat jenom na nosné tokeny a nikoli na soubory cookie. To lze provést následující konfiguraci v `WebApiConfig.Register` metody:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda SuppressDefaultHostAuthentication říká webového rozhraní API pro ignorování ověřování, který se stane, než požadavek dosáhne kanál rozhraní Web API, služby IIS nebo OWIN middleware. Tímto způsobem můžeme omezit webové rozhraní API ověřování jenom pomocí nosných tokenů.

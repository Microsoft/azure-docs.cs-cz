---
title: Relace správy – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: způsoby zmírnění hrozeb, které jsou zveřejněné v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24bd0e8eff616920dba0eb5353f983444e3161cd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019955"
---
# <a name="security-frame-session-management--articles"></a>Rámce zabezpečení: Správa relací | Články 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementace správné odhlášení pomocí ADAL metod při používání služby Azure AD](#logout-adal)</li></ul> |
| Zařízení IoT | <ul><li>[Použití omezené životnost generovaných tokenů SaS](#finite-tokens)</li></ul> |
| **Azure Documentdb** | <ul><li>[Pomocí minimální životnosti tokenu pro generovaných tokenů prostředků](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Implementace správné odhlášení metodami WsFederation při použití služby AD FS](#wsfederation-logout)</li></ul> |
| **Serveru identit** | <ul><li>[Implementace správné odhlášení při použití serveru identit](#proper-logout)</li></ul> |
| **Webové aplikace** | <ul><li>[Aplikace dostupné přes protokol HTTPS, musíte použít bezpečné soubory cookie](#https-secure-cookies)</li><li>[Všechny aplikace http, na základě by měl určovat pouze pro definici souboru cookie http](#cookie-definition)</li><li>[Zmírnění před útoky webů požadavku padělání (proti útokům CSRF) na webových stránkách ASP.NET](#csrf-asp)</li><li>[Nastavení relace dobu jeho existence nečinnosti](#inactivity-lifetime)</li><li>[Implementace správné odhlášení z aplikace](#proper-app-logout)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Zmírnění před útoky webů požadavku padělání (proti útokům CSRF) na rozhraní ASP.NET Web API](#csrf-api)</li></ul> |

## <a id="logout-adal"></a>Implementace správné odhlášení pomocí ADAL metod při používání služby Azure AD

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure AD | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud aplikace využívá přístupový token vydán Azure AD, by měly volat obslužné rutiny události odhlášení |

### <a name="example"></a>Příklad:
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Příklad:
Relace uživatele ho měli zrušte také voláním metody Session.Abandon(). Následující metoda ukazuje zabezpečené implementací odhlášení uživatele:
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

## <a id="finite-tokens"></a>Použití omezené životnost generovaných tokenů SaS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Vygeneruje ověřování pro službu Azure IoT Hub tokeny SaS by měl mít dobou vypršení platnosti. Zachovat životnosti tokenu SaS s minimální omezit množství času, který může být přehrány v případě, že dojde k ohrožení tokenů.|

## <a id="resource-tokens"></a>Pomocí minimální životnosti tokenu pro generovaných tokenů prostředků

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Documentdb | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Snižte časový interval tokenu prostředků na minimální požadovanou hodnotu. Prostředek tokeny mají platný časový interval výchozí 1 hodina.|

## <a id="wsfederation-logout"></a>Implementace správné odhlášení metodami WsFederation při použití služby AD FS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | ADFS | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud aplikace spoléhá na službu tokenů zabezpečení tokenem vydaným pomocí služby AD FS, obslužné rutiny události odhlášení by měly volat metodu WSFederationAuthenticationModule.FederatedSignOut() pro odhlášení uživatele. Také musí být zničený aktuální relace a hodnota tokenu relace by měl resetování a zrušeny.|

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

## <a id="proper-logout"></a>Implementace správné odhlášení při použití serveru identit

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Serveru identit | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Federované IdentityServer3 odhlášení](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroky** | IdentityServer podporuje možnost vytvořit federaci s poskytovateli externí identity. Když uživatel odhlásí od zprostředkovatele identity nadřazeného, v závislosti na použitém protokolu, může být obdržet oznámení, když se uživatel odhlásí. To umožňuje IdentityServer k upozornění klientů, jeho, takže se můžete také odhlášení uživatele. Podívejte se do dokumentace v části odkazy podrobnosti implementace.|

## <a id="https-secure-cookies"></a>Aplikace dostupné přes protokol HTTPS, musíte použít bezpečné soubory cookie

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | [Element (schéma nastavení ASP.NET) httpCookies](http://msdn.microsoft.com/library/ms228262(v=vs.100).aspx), [HttpCookie.Secure vlastnost](http://msdn.microsoft.com/library/system.web.httpcookie.secure.aspx) |
| **Kroky** | Soubory cookie jsou obvykle přístupné pouze k doméně, pro kterou byly obor. Bohužel definici "domény" nezahrnuje protokol, soubory cookie, které jsou vytvořené pomocí protokolu HTTPS jsou přístupné přes protokol HTTP. Atribut "zabezpečení" označuje do prohlížeče, soubor cookie měl pouze být k dispozici prostřednictvím protokolu HTTPS. Zkontrolujte, že všechny soubory cookie nastavená přes HTTPS pomocí **zabezpečené** atribut. Požadavek na lze vynutit v souboru web.config nastavením requireSSL atributu na hodnotu true. Je žádoucí, protože se vynutit **zabezpečené** atribut pro všechny soubory cookie aktuálních a budoucích bez nutnosti žádné změny další kód.|

### <a name="example"></a>Příklad:
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Nastavení se nevynucuje i v případě, že HTTP se používá pro přístup k aplikaci. Pokud HTTP se používá pro přístup k aplikaci, dělí nastavení aplikace, protože soubory cookie se nastavují s atribut zabezpečení a prohlížeče nebude přidán do aplikace.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Web Forms, MVC5 |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Pokud webová aplikace je předávající strana a rozšíření IdP je ADFS server, nastavením requireSSL na hodnotu True v je možné nakonfigurovat zabezpečené atribut tokenu FedAuth `system.identityModel.services` části souboru Web.config:|

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

## <a id="cookie-definition"></a>Všechny aplikace http, na základě by měl určovat pouze pro definici souboru cookie http

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zabezpečený soubor Cookie atribut](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroky** | Chcete-li zmírnit riziko zpřístupnění informací s útoku skriptování (XSS), nový atribut - httpOnly - zavedl se soubory cookie a podporuje všechny hlavní prohlížeče. Atribut určuje, že soubor cookie není přístupná prostřednictvím skriptu. Webovou aplikaci pomocí souborů cookie HttpOnly snižuje možnost, můžete důvěrné informace obsažené v souboru cookie odcizení pomocí skriptu a posílá webu útočníka. |

### <a name="example"></a>Příklad:
Všechny aplikace založené na protokolu HTTP, které používají soubory cookie by měl v definici souboru cookie zadejte HttpOnly implementací následující konfigurace v souboru web.config:
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
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Webové formuláře |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Vlastnost FormsAuthentication.RequireSSL](https://msdn.microsoft.com/library/system.web.security.formsauthentication.requiressl.aspx) |
| **Kroky** | Hodnota vlastnosti RequireSSL je nastavena v konfiguračním souboru aplikace ASP.NET pomocí atributu requireSSL prvku konfigurace. Zadaný v souboru Web.config vaší aplikace zda vrátit souboru cookie ověřování založené na formulářích na server nastavením atributu requireSSL je požadován protokol SSL (Secure Sockets Layer).|

### <a name="example"></a>Příklad: 
Následující příklad kódu nastaví atribut requireSSL v souboru Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5 |
| **Atributy**              | EnvironmentType - OnPrem |
| **Odkazy**              | [Windows Identity Foundation (WIF) konfigurace – část II](https://blogs.msdn.microsoft.com/alikl/2011/02/01/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler/) |
| **Kroky** | Pokud chcete nastavit atribut httpOnly pro soubory cookie FedAuth, hideFromCsript atribut je třeba nastavit na hodnotu True. |

### <a name="example"></a>Příklad:
Následující konfigurace zobrazuje správné konfiguraci:
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

## <a id="csrf-asp"></a>Zmírnění před útoky webů požadavku padělání (proti útokům CSRF) na webových stránkách ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Padělání požadavku posílaného mezi weby (proti útokům CSRF nebo XSRF) je typ útoku, ve kterém může útočník provádět akce v kontextu zabezpečení navázanou relaci jiného uživatele na webovém serveru. Cílem je upravit nebo odstranit obsah, pokud cílové webu závisí výhradně na soubory cookie relace k ověření přijatý požadavek. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získáme prohlížeče jiný uživatel se načíst adresu URL pomocí příkazu z citlivé lokality, na kterém je již přihlášen. Existuje mnoho způsobů pro útočníka na to, jako třeba hostování na jiný web, který načítá prostředku ze serveru citlivé nebo získávání uživatele klikněte na odkaz. Pokud server odešle klientovi další token, vyžaduje, aby klient mají být zahrnuty všechny budoucí požadavky tento token a ověřuje, že všechny budoucí požadavky patří token, který se týká aktuální relaci, například pomocí technologie ASP.NET se dá zabránit útokům AntiForgeryToken nebo stavu zobrazení. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Prevence XSRF/proti útokům CSRF v architektuře ASP.NET MVC a webových stránek](http://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroky** | Anti-proti útokům CSRF a formulářů rozhraní ASP.NET MVC – použít `AntiForgeryToken` pomocnou metodu pro zobrazení; put `Html.AntiForgeryToken()` do formátu, například|

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
Ve stejnou dobu Html.AntiForgeryToken() dává návštěvníka souboru cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodné hodnoty hidden uvedené výše. V dalším kroku k ověření příchozí post formuláře, přidejte filtr [ValidateAntiForgeryToken] do cílové metody akce. Příklad:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, který kontroluje, zda:
* Příchozí požadavek má souboru cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položka názvem __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` za předpokladu, že všechny shody hodnoty je dobře, žádost prochází jako normální. Ale pokud ne, pak chybu autorizace se zprávou "požadovaný token proti padělání nebyl zadán nebo byl neplatný". 

### <a name="example"></a>Příklad:
Ochrana proti útokům CSRF a AJAX: tokenu formuláře může být problém pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, ne data formuláře HTML. Jedno řešení je odeslat tokenů v vlastní hlavičku HTTP. Následující kód používá syntaxi Razor ke generování tokenů a potom přidá tokenů na požadavek AJAX. 
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
Při zpracovávání požadavku extrahuje tokeny z hlaviček požadavku. Potom zavolejte metodu AntiForgery.Validate k ověřování tokenů. Metodu Validate vyvolá výjimku, pokud tokeny nejsou platné.
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
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Webové formuláře |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Využití výhod funkcí ASP.NET integrované do jim útoky webové](https://msdn.microsoft.com/library/ms972969.aspx#securitybarriers_topic2) |
| **Kroky** | Útoky proti útokům CSRF v aplikacích webový formulář na základě omezeny nastavení ViewStateUserKey náhodný řetězec, který se liší pro jednotlivé uživatele – ID uživatele, nebo ještě lepší ID relace. Pro počtu technická a sociálních důvodů ID relace je mnohem lepší umístit, protože relace na ID nepředvídatelným, časový limit a liší se na jednotlivé uživatele.|

### <a name="example"></a>Příklad:
Tady je kód, který je potřeba mít v všechny stránky:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a id="inactivity-lifetime"></a>Nastavení relace dobu jeho existence nečinnosti

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [HttpSessionState.Timeout Property](https://msdn.microsoft.com/library/system.web.sessionstate.httpsessionstate.timeout(v=vs.110).aspx) |
| **Kroky** | Časový limit relace představuje událostí, ke kterým dochází, když uživatel neprovede žádnou akci na webovém serveru během intervalu (definovanou webový server). Událost na straně serveru, změňte stav relace uživatele na "neplatný. (například" nepoužívá už") a na server WWW zrušení jeho (odstranění všech dat obsažených do ní). Následující příklad kódu nastaví atribut časový limit relace na 15 minut v souboru Web.config.|

### <a name="example"></a>Příklad:
```XML code <configuration> <system.web> <sessionState mode="InProc" cookieless="true" timeout="15" /> </system.web> </configuration>
```

## <a id="threat-detection"></a>Enable Threat detection on Azure SQL
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Webové formuláře |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [forms Element pro ověřování (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/1d3t3c61(v=vs.100).aspx) |
| **Kroky** | Nastavit časový limit souboru cookie lístek pro ověřování pomocí formulářů na 15 minut|

### <a name="example"></a>Příklad:
"" Kód XML<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Title                   | Details      |
| ----------------------- | ------------ |
| **Component**               | Web Application | 
| **SDL Phase**               | Build |  
| **Applicable Technologies** | Web Forms, MVC5 |
| **Attributes**              | EnvironmentType - OnPrem |
| **References**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Steps** | When the web application is Relying Party and ADFS is the STS, the lifetime of the authentication cookies - FedAuth tokens - can be set by the following configuration in web.config:|

### Example
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
Také služby AD FS vydán SAML deklarace identity, že doba platnosti tokenu musí být nastavena na 15 minut, a to spuštěním následujícího příkazu powershellu na serveru služby AD FS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName “<RelyingPartyWebApp>” -ClaimsProviderName @(“Active Directory”) -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a id="proper-app-logout"></a>Implementace správné odhlášení z aplikace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Proveďte správné odhlásit z aplikace, pokud uživatele stisknutí odhlášení tlačítko. Po odhlášení aplikace by měl destroy uživatelské relace a také resetování a nezruší se tím hodnota souboru cookie relace, spolu s obnovením a anulovány hodnota souboru cookie ověřování. Navíc pokud více relací, jsou svázané s identitou jednoho uživatele, se musí být souhrnně ukončena na straně serveru při vypršení časového limitu nebo odhlášení. Nakonec zkontrolujte, že odhlášení funkce je k dispozici na každé stránce. |

## <a id="csrf-api"></a>Zmírnění před útoky webů požadavku padělání (proti útokům CSRF) na rozhraní ASP.NET Web API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Padělání požadavku posílaného mezi weby (proti útokům CSRF nebo XSRF) je typ útoku, ve kterém může útočník provádět akce v kontextu zabezpečení navázanou relaci jiného uživatele na webovém serveru. Cílem je upravit nebo odstranit obsah, pokud cílové webu závisí výhradně na soubory cookie relace k ověření přijatý požadavek. Útočník by mohl zneužít tuto chybu zabezpečení tím, že získáme prohlížeče jiný uživatel se načíst adresu URL pomocí příkazu z citlivé lokality, na kterém je již přihlášen. Existuje mnoho způsobů pro útočníka na to, jako třeba hostování na jiný web, který načítá prostředku ze serveru citlivé nebo získávání uživatele klikněte na odkaz. Pokud server odešle klientovi další token, vyžaduje, aby klient mají být zahrnuty všechny budoucí požadavky tento token a ověřuje, že všechny budoucí požadavky patří token, který se týká aktuální relaci, například pomocí technologie ASP.NET se dá zabránit útokům AntiForgeryToken nebo stavu zobrazení. |

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Prevence útoků (proti útokům CSRF) padělání požadavku posílaného mezi weby v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroky** | Ochrana proti útokům CSRF a AJAX: tokenu formuláře může být problém pro požadavky AJAX, protože požadavek AJAX může odesílat data JSON, ne data formuláře HTML. Jedno řešení je odeslat tokenů v vlastní hlavičku HTTP. Následující kód používá syntaxi Razor ke generování tokenů a potom přidá tokenů na požadavek AJAX. |

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
Při zpracovávání požadavku extrahuje tokeny z hlaviček požadavku. Potom zavolejte metodu AntiForgery.Validate k ověřování tokenů. Metodu Validate vyvolá výjimku, pokud tokeny nejsou platné.
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
Anti-proti útokům CSRF a formulářů rozhraní ASP.NET MVC – využít pomocnou metodu AntiForgeryToken na zobrazení; Uveďte Html.AntiForgeryToken() do formuláře, například
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Příklad:
V předchozím příkladu bude výstup přibližně takto:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Příklad:
Ve stejnou dobu Html.AntiForgeryToken() dává návštěvníka souboru cookie s názvem __RequestVerificationToken se stejnou hodnotou jako náhodné hodnoty hidden uvedené výše. V dalším kroku k ověření příchozí post formuláře, přidejte filtr [ValidateAntiForgeryToken] do cílové metody akce. Příklad:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autorizace, který kontroluje, zda:
* Příchozí požadavek má souboru cookie s názvem __RequestVerificationToken
* Příchozí požadavek má `Request.Form` položka názvem __RequestVerificationToken
* Tyto soubory cookie a `Request.Form` za předpokladu, že všechny shody hodnoty je dobře, žádost prochází jako normální. Ale pokud ne, pak chybu autorizace se zprávou "požadovaný token proti padělání nebyl zadán nebo byl neplatný".

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC5, MVC6 |
| **Atributy**              | Poskytovatel Identity identity zprostředkovatel – AD FS, – Azure AD |
| **Odkazy**              | [Zabezpečení webového rozhraní API s jednotlivých účtů a místní přihlášení v rozhraní ASP.NET Web API 2.2](http://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroky** | Pokud webového rozhraní API je zabezpečené pomocí OAuth 2.0, pak se očekává nosný token v hlavičce autorizace požadavku a udělí přístup k požadavek pouze v případě, že token je platný. Na rozdíl od ověřování na základě souborů cookie prohlížeče nepřipojujte nosné tokeny požadavků. Klienta, který je potřeba explicitně připojte nosný token v hlavičce žádosti. Pro rozhraní ASP.NET Web API chráněné pomocí OAuth 2.0, proto jsou nosné tokeny za obrana proti útokům proti útokům CSRF. Upozorňujeme, že pokud část aplikace MVC používá ověřování pomocí formulářů (tj, soubory cookie používá), tokeny proti zfalšování musí být použity ve webové aplikaci MVC. |

### <a name="example"></a>Příklad:
Rozhraní Web API musí být informováni spolehnout jenom na nosné tokeny a ne v souborech cookie. Je možné ji provést následující konfigurace v `WebApiConfig.Register` metoda: '''C ostré kód konfigurace. SuppressDefaultHostAuthentication(); konfigurace. Filters.Add (nové HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```
The SuppressDefaultHostAuthentication method tells Web API to ignore any authentication that happens before the request reaches the Web API pipeline, either by IIS or by OWIN middleware. That way, we can restrict Web API to authenticate only using bearer tokens.
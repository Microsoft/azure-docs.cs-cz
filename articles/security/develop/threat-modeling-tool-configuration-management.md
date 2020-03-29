---
title: Správa konfigurace nástroje Microsoft Threat Modeling Tool
titleSuffix: Azure
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
ms.openlocfilehash: 3c89fae09583c96cf8139885fe2554cf6784b4e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78269828"
---
# <a name="security-frame-configuration-management--mitigations"></a>Rámec zabezpečení: Správa konfigurace | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Implementovat zásady zabezpečení obsahu (CSP) a zakázat vsazený javascript](#csp-js)</li><li>[Povolení filtru XSS prohlížeče](#xss-filter)</li><li>[ASP.NET aplikace musí před nasazením zakázat trasování a ladění.](#trace-deploy)</li><li>[Přístup k javaskriptům třetích stran pouze z důvěryhodných zdrojů](#js-trusted)</li><li>[Ujistěte se, že ověřené ASP.NET stránky obsahují obranu proti nápravě nebo přezvedání kliknutí.](#ui-defenses)</li><li>[Ujistěte se, že jsou povoleny pouze důvěryhodné počátky, pokud je v ASP.NET webových aplikacích povolen a je povolen](#cors-aspnet)</li><li>[Povolit atribut ValidateRequest na stránkách ASP.NET](#validate-aspnet)</li><li>[Použití místně hostovaných nejnovějších verzí knihoven JavaScript](#local-js)</li><li>[Zakázat automatické čichání MIME](#mime-sniff)</li><li>[Odebrání standardních záhlaví serverů na webech Windows Azure, abyste zabránili snímání otisků prstů](#standard-finger)</li></ul> |
| **Databáze** | <ul><li>[Konfigurace brány Windows Firewall pro přístup k databázovému stroji](#firewall-db)</li></ul> |
| **Web API** | <ul><li>[Ujistěte se, že jsou povoleny pouze důvěryhodné počátky, pokud je v ASP.NET webovém rozhraní API povolen a korzet](#cors-api)</li><li>[Šifrování částí konfiguračních souborů webového rozhraní API, které obsahují citlivá data](#config-sensitive)</li></ul> |
| **Zařízení IoT** | <ul><li>[Ujistěte se, že všechna rozhraní pro správu jsou zabezpečena silnými přihlašovacími údaji](#admin-strong)</li><li>[Ujistěte se, že neznámý kód nelze spustit na zařízeních](#unknown-exe)</li><li>[Šifrování operačního systému a dalšíoddíly zařízení IoT pomocí bitové skříňky](#partition-iot)</li><li>[Ujistěte se, že jsou na zařízeních povoleny pouze minimální služby/funkce.](#min-enable)</li></ul> |
| **Brána ioT pole** | <ul><li>[Šifrování operačního systému a dalšíoddíly brány IoT Field Gateway pomocí bitové skříňky](#field-bit-locker)</li><li>[Ujistěte se, že se během instalace změní výchozí přihlašovací údaje brány pole.](#default-change)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Ujistěte se, že cloudová brána implementuje proces, který udržuje firmware připojených zařízení v aktuálním stavu.](#cloud-firmware)</li></ul> |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncového bodu nakonfigurované podle zásad organizace](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zajištění zabezpečené správy přístupových klíčů úložiště Azure](#secure-keys)</li><li>[Ujistěte se, že jsou povoleny pouze důvěryhodné původy, pokud je cors povolena v úložišti Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Povolit funkci omezení služeb WCF](#throttling)</li><li>[WCF-Information disclosure prostřednictvím metadat](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementovat zásady zabezpečení obsahu (CSP) a zakázat vsazený javascript

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Úvod k zásadám zabezpečení obsahu](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [odkaz na zásady zabezpečení obsahu](https://content-security-policy.com/), funkce [zabezpečení](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), úvod do zásad [zabezpečení obsahu](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), lze [použít csp?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroky** | <p>Zásady zabezpečení obsahu (CSP) je mechanismus zabezpečení hloubkové ochrany, standard W3C, který umožňuje vlastníkům webových aplikací mít kontrolu nad obsahem vloženým na jejich webu. CSP je přidán jako hlavička odpovědi HTTP na webovém serveru a je vynucenna straně klienta prohlížeči. Jedná se o zásadu založenou na whitelistu - webová stránka může deklarovat sadu důvěryhodných domén, ze kterých lze načíst aktivní obsah, jako je JavaScript.</p><p>CSP poskytuje následující výhody zabezpečení:</p><ul><li>**Ochrana proti XSS:** Pokud je stránka ohrožena xss, útočník ji může zneužít 2 způsoby:<ul><li>Aplikujte `<script>malicious code</script>`. Toto zneužití nebude fungovat z důvodu Základní omezení CSP-1</li><li>Aplikujte `<script src="http://attacker.com/maliciousCode.js"/>`. Toto zneužití nebude fungovat, protože útočníci řízená doména nebude na seznamu povolených domén csp</li></ul></li><li>**Kontrola exfiltrace dat:** Pokud se škodlivý obsah na webové stránce pokusí připojit k externímu webu a ukrást data, bude připojení zprostředkovatelem zprostředkovatele zprostředkovatele zprostředkovatele zabezpečení přerušeno. Důvodem je, že cílová doména nebude v seznamu povolených csp</li><li>**Obrana proti click-jacking:** click-jacking je útočná technika, pomocí které může protivník zarámovat originální webové stránky a přinutit uživatele, aby klikli na prvky uživatelského rozhraní. V současné době je obrana proti click-jacking dosaženo konfigurací hlavičky odezvy- X-Frame-Options. Ne všechny prohlížeče respektují toto záhlaví a do budoucna CSP bude standardní způsob, jak se bránit proti click-jacking</li><li>**Hlášení o útoku v reálném čase:** Pokud dojde k injektážnímu útoku na webu s povolenou csp, prohlížeče automaticky spustí oznámení koncovému bodu nakonfigurovanému na webovém serveru. Tímto způsobem csp slouží jako systém varování v reálném čase.</li></ul> |

### <a name="example"></a>Příklad
Příklad zásad: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Tato zásada umožňuje, aby se skripty načítalo pouze ze serveru webové aplikace a serveru Google Analytics. Skripty načtené z jiného webu budou odmítnuty. Pokud je na webu povolen a) je povolen a které mají následující funkce, jsou automaticky zakázány, aby se zmírnily útoky XSS. 

### <a name="example"></a>Příklad
Vázací skripty se nespustí. Následují příklady válčných skriptů 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Příklad
Řetězce nebudou vyhodnoceny jako kód. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Povolení filtru XSS prohlížeče

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Filtr ochrany XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Kroky** | <p>Konfigurace hlavičky odezvy X-XSS-Protection řídí filtr skriptů mezi webovými stránkami prohlížeče. Tato hlavička odpovědi může mít následující hodnoty:</p><ul><li>`0:`Tím se zakáže filtr</li><li>`1: Filter enabled`Pokud je zjištěn skriptovací útok mezi weby, prohlížeč stránku dezinfikuje, aby útok zastavil.</li><li>`1: mode=block : Filter enabled`. Spíše než dezinfikovat stránku, když je detekován útok XSS, prohlížeč zabrání vykreslování stránky</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Prohlížeč stránku dezinfikuje a nahlásí porušení.</li></ul><p>Jedná se o funkci Chromium využívající zprávy o porušení csp k odeslání podrobností uri podle vašeho výběru. Poslední 2 možnosti jsou považovány za bezpečné hodnoty.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET aplikace musí před nasazením zakázat trasování a ladění.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [ASP.NET Přehled ladění](https://msdn.microsoft.com/library/ms227556.aspx), [přehled trasování ASP.NET](https://msdn.microsoft.com/library/bb386420.aspx), [Postup: Povolit trasování pro ASP.NET aplikaci](https://msdn.microsoft.com/library/0x5wc973.aspx), [Jak: Povolit ladění pro ASP.NET aplikace](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroky** | Je-li pro stránku povoleno trasování, získá každý prohlížeč, který ji požaduje, také informace o trasování, které obsahují data o stavu interního serveru a pracovním postupu. Ta informace by mohla být citlivá na bezpečnost. Pokud je pro stránku povoleno ladění, chyby, ke nimž dochází na serveru, mají za následek úplná data trasování zásobníku prezentovaná prohlížeči. Tato data mohou vystavit citlivé informace o pracovním postupu serveru citlivé na zabezpečení. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Přístup k javaskriptům třetích stran pouze z důvěryhodných zdrojů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | JavaScripty třetích stran by měly být odkazovány pouze z důvěryhodných zdrojů. Referenční koncové body by měly být vždy na SSL. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Ujistěte se, že ověřené ASP.NET stránky obsahují obranu proti nápravě nebo přezvedání kliknutí.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [OWASP click-jacking obrana Cheat Sheet](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals - Boj proti click-jacking s X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroky** | <p>click-jacking, také známý jako "útok na nápravu uživatelského rozhraní", je, když útočník používá více průhledných nebo neprůhledných vrstev, aby oklamal uživatele, aby klikl na tlačítko nebo odkaz na jiné stránce, když měl v úmyslu kliknout na stránku nejvyšší úrovně.</p><p>Toto vrstvení je dosaženo vytvořením škodlivé stránky s iframe, který načte stránku oběti. Útočník tedy "zneucovává" kliknutí určená pro jejich stránku a jejich směrování na jinou stránku, pravděpodobně vlastněnou jinou aplikací, doménou nebo oběma. Chcete-li zabránit útokům na klepnutí, nastavte správné hlavičky odpovědí HTTP s x-frame-options, které prohlížeči dávají pokyn, aby nepovoloval rámování z jiných domén.</p>|

### <a name="example"></a>Příklad
Hlavičku X-FRAME-OPTIONS lze nastavit pomocí web.config služby IIS. Fragment kódu Web.config pro weby, které by nikdy neměly být zarámovány: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Příklad
Web.config kód pro weby, které by měly být orámovány pouze stránkami ve stejné doméně: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Ujistěte se, že jsou povoleny pouze důvěryhodné počátky, pokud je v ASP.NET webových aplikacích povolen a je povolen

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásady stejného původu a zabraňuje škodlivému webu ve čtení citlivých dat z jiného webu. Někdy však může být nutné bezpečně vystavit api, která jiné weby mohou využívat. Cross Origin Resource Sharing (CORS) je standard W3C, který umožňuje serveru uvolnit zásady stejného původu. Pomocí CORS server může explicitně povolit některé požadavky napříč původy při odmítnutí jiných.</p><p>CORS je bezpečnější a flexibilnější než předchozí techniky, jako je JSONP. Ve své podstatě, povolení CORS překládá k přidání několika hlavičky odpovědi HTTP (Access-Control-*) do webové aplikace, a to lze provést několika způsoby.</p>|

### <a name="example"></a>Příklad
Pokud je k dispozici přístup k web.config, pak CORS lze přidat pomocí následujícího kódu: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Příklad
Pokud přístup k web.config není k dispozici, pak CORS lze nakonfigurovat přidáním následujícího kódu CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Vezměte prosím na vědomí, že je důležité zajistit, aby seznam počátků v atributu "Access-Control-Allow-Origin" byl nastaven na omezenou a důvěryhodnou sadu počátků. Pokud to nenakonfigurujete nevhodně (např. nastavení hodnoty jako '*') umožní škodlivým webům spouštět požadavky na příčný původ do webové aplikace >bez jakýchkoli omezení, čímž se aplikace stane zranitelnou vůči útokům CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Povolit atribut ValidateRequest na stránkách ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Ověření požadavku – obrana před skriptovými útoky](https://www.asp.net/whitepapers/request-validation) |
| **Kroky** | <p>Ověření požadavku, funkce ASP.NET od verze 1.1, brání serveru v přijímání obsahu obsahujícího nekódovaný kód HTML. Tato funkce je navržena tak, aby pomohla zabránit některým útokům vkládání skriptů, při kterých může být kód klientského skriptu nebo html nevědomky odeslán na server, uložen a poté předložen ostatním uživatelům. Stále důrazně doporučujeme ověřit všechna vstupní data a html je v případě potřeby zakódovat.</p><p>Ověření požadavku se provádí porovnáním všech vstupních dat se seznamem potenciálně nebezpečných hodnot. Pokud dojde ke shodě, `HttpRequestValidationException`ASP.NET vyvolá . Ve výchozím nastavení je funkce Ověření požadavku povolena.</p>|

### <a name="example"></a>Příklad
Tuto funkci však lze zakázat na úrovni stránky: 
```XML
<%@ Page validateRequest="false" %> 
```
nebo na aplikační úrovni 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Upozorňujeme, že funkce ověření žádosti není podporována a není součástí kanálu MVC6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Použití místně hostovaných nejnovějších verzí knihoven JavaScript

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Vývojáři používající standardní javascriptové knihovny, jako je JQuery, musí používat schválené verze běžných javascriptových knihoven, které neobsahují známé bezpečnostní chyby. Osvědčeným postupem je použití nejnovější verze knihoven, protože obsahují opravy zabezpečení známých chyb zabezpečení ve starších verzích.</p><p>Pokud nejnovější verzi nelze použít z důvodů kompatibility, měly by být použity níže uvedené minimální verze.</p><p>Přijatelné minimální verze:</p><ul><li>**Jquery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Ověřit 1,9</li><li>JQuery Mobile 1.0.1</li><li>Cyklus JQuery 2,99</li><li>JQuery Datové tabulky 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Sada řídicích nástrojů Ajax 40412</li></ul></li><li>**ASP.NET webové formuláře a Ajax**<ul><li>Webové formuláře ASP.NET a Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nikdy nenačítat žádnou knihovnu JavaScriptu z externích webů, jako jsou veřejné sítě CDN</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Zakázat automatické čichání MIME

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [IE8 Bezpečnostní část V: Komplexní ochrana](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [typ MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Kroky** | Hlavička X-Content-Type-Options je hlavička HTTP, která vývojářům umožňuje určit, že jejich obsah by neměl být mime-sniffed. Tato hlavička je určena ke zmírnění útoků MIME-sniffing. Pro každou stránku, která by mohla obsahovat uživatelsky kontrolovatelný obsah, musíte použít hlavičku HTTP X-Content-Type-Options:nosniff. Chcete-li povolit požadované záhlaví globálně pro všechny stránky v aplikaci, můžete provést jednu z následujících akcí|

### <a name="example"></a>Příklad
Pokud je aplikace hostována službou InternetOvou informační službou (IIS) 7, přidejte záhlaví do souboru web.config. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Příklad
Přidání záhlaví prostřednictvím\_globálního požadavku BeginRequest aplikace 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Příklad
Implementace vlastního modulu HTTP 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Příklad
Požadované záhlaví můžete povolit pouze pro určité stránky přidáním jednotlivých odpovědí: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Odebrání standardních záhlaví serverů na webech Windows Azure, abyste zabránili snímání otisků prstů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Odebrání standardních záhlaví serverů na webech Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroky** | Záhlaví jako Server, X-Powered-By, X-AspNet-Version odhalují informace o serveru a základních technologiích. Doporučuje se potlačit tyto hlavičky, čímž se zabrání snímání otisků prstů aplikace |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurace brány Windows Firewall pro přístup k databázovému stroji

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | Není k mna, VERZE SQL - V12 |
| **Odkazy**              | [Konfigurace brány firewall databáze Azure SQL](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), Konfigurace brány Windows Firewall pro přístup k [databázovému stroji](https://msdn.microsoft.com/library/ms175043) |
| **Kroky** | Firewallové systémy zabraňují neoprávněnému přístupu k počítačovým prostředkům. Chcete-li získat přístup k instanci databázového stroje serveru SQL Server prostřednictvím brány firewall, je nutné nakonfigurovat bránu firewall v počítači se systémem SQL Server, aby byl přístup povolen |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Ujistěte se, že jsou povoleny pouze důvěryhodné počátky, pokud je v ASP.NET webovém rozhraní API povolen a korzet

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Povolení požadavků na příčné počátky v rozhraní ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), ASP.NET web API – podpora [CORS v ASP.NET web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásady stejného původu a zabraňuje škodlivému webu ve čtení citlivých dat z jiného webu. Někdy však může být nutné bezpečně vystavit api, která jiné weby mohou využívat. Cross Origin Resource Sharing (CORS) je standard W3C, který umožňuje serveru uvolnit zásady stejného původu.</p><p>Pomocí CORS server může explicitně povolit některé požadavky napříč původy při odmítnutí jiných. CORS je bezpečnější a flexibilnější než předchozí techniky, jako je JSONP.</p>|

### <a name="example"></a>Příklad
V souboru App_Start/WebApiConfig.cs přidejte do metody WebApiConfig.Register následující kód 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Příklad
Atribut EnableCors lze použít na metody akce v kontroleru následujícím způsobem: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Vezměte prosím na vědomí, že je důležité zajistit, aby seznam počátků v EnableCors atribut je nastaven na omezenou a důvěryhodnou sadu počátků. Pokud to nenakonfigurujete nevhodně (např. nastavení hodnoty jako *), umožní škodlivé weby aktivovat požadavky na příčný původ do rozhraní API bez jakýchkoli omezení, >čímž se rozhraní API stane zranitelným vůči útokům CSRF. EnableCors lze dekorovat na úrovni řadiče. 

### <a name="example"></a>Příklad
Chcete-li zakázat CORS na konkrétní metodu ve třídě, DisableCors atribut lze použít, jak je znázorněno níže: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Povolení požadavků na příčný původ (CORS) v ASP.NET jádra 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroky** | <p>V ASP.NET Core 1.0, CORS může být povolena buď pomocí middleware nebo pomocí MVC. Při použití MVC povolit CORS stejné cors služby jsou používány, ale cors middleware není.</p>|

**Přiblížení-1** Povolení CORS s middleware: Chcete-li povolit CORS pro celou aplikaci, přidejte middleware CORS do kanálu požadavků pomocí metody rozšíření UseCors. Zásady křížového původu lze zadat při přidávání middleware CORS pomocí Třídy CorsPolicyBuilder. Toto lze provést dvěma způsoby:

### <a name="example"></a>Příklad
První je zavolat UseCors s lambdou. Lambda trvá CorsPolicyBuilder objekt: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Příklad
Druhým je definovat jednu nebo více pojmenovaných zásad CORS a potom vyberte zásadu podle názvu za běhu. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Přístup-2** Povolení CORS v MVC: Vývojáři mohou alternativně použít MVC použít konkrétní CORS na akci, na řadič, nebo globálně pro všechny řadiče.

### <a name="example"></a>Příklad
Pro akci: Chcete-li zadat zásadu CORS pro konkrétní akci, přidejte atribut [EnableCors] do akce. Zadejte název zásady. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Příklad
Na regulátor: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Příklad
Globálně: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Vezměte prosím na vědomí, že je důležité zajistit, aby seznam počátků v EnableCors atribut je nastaven na omezenou a důvěryhodnou sadu počátků. Pokud to nenakonfigurujete nevhodně (např. nastavení hodnoty jako *), umožní škodlivé weby aktivovat požadavky na příčný původ do rozhraní API bez jakýchkoli omezení, >čímž se rozhraní API stane zranitelným vůči útokům CSRF. 

### <a name="example"></a>Příklad
Chcete-li zakázat cors pro řadič nebo akci, použijte atribut [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Šifrování částí konfiguračních souborů webového rozhraní API, které obsahují citlivá data

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Postup: Šifrování oddílů konfigurace v ASP.NET 2.0 pomocí dpapi](https://msdn.microsoft.com/library/ff647398.aspx), [určení zprostředkovatele chráněné konfigurace](https://msdn.microsoft.com/library/68ze1hb2.aspx), použití [trezoru klíčů Azure k ochraně tajných kódů aplikací](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Konfigurační soubory, jako je například web.config, appsettings.json se často používají k uložení citlivých informací, včetně uživatelských jmen, hesel, připojovacích řetězců databáze a šifrovacích klíčů. Pokud tyto informace nezaštítíte, je aplikace ohrožena útočníky nebo uživateli se zlými úmysly, kteří zpřístupňují citlivé informace, jako jsou uživatelská jména a hesla účtu, názvy databází a názvy serverů. Na základě typu nasazení (azure/on-prem) zašifrujte citlivé části konfiguračních souborů pomocí DPAPI nebo služeb, jako je Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Ujistěte se, že všechna rozhraní pro správu jsou zabezpečena silnými přihlašovacími údaji

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Všechna rozhraní pro správu, která zařízení nebo brána pole zveřejňuje by měla být zabezpečena pomocí silných pověření. Také jakékoli jiné exponované rozhraní, jako je WiFi, SSH, sdílení souborů, FTP by měly být zabezpečeny silnými pověřeními. Výchozí slabá hesla by neměla být používána. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Ujistěte se, že neznámý kód nelze spustit na zařízeních

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Povolení zabezpečeného spouštění a šifrování zařízení s bitovou skříňkou ve Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Kroky** | Zabezpečené spuštění uEFI omezuje systém tak, aby umožňoval pouze provádění binárních souborů podepsaných zadanou autoritou. Tato funkce zabraňuje spuštění neznámého kódu na platformě a potenciálně oslabuje jeho stav zabezpečení. Povolte zabezpečené spouštění UEFI a omezte seznam certifikačních autorit, které jsou důvěryhodné pro podepisování kódu. Podepište veškerý kód nasazený v zařízení pomocí jednoho z důvěryhodných autorit. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Šifrování operačního systému a dalšíoddíly zařízení IoT pomocí bitové skříňky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Windows 10 IoT Core implementuje odlehčenou verzi bit-locker device encryption, která má silnou závislost na přítomnosti čipu TPM na platformě, včetně nezbytného protokolu preOS v UEFI, který provádí potřebná měření. Tato měření preOS zajišťují, že operační systém má později konečný záznam o tom, jak byl operační systém spuštěn. Šifrujte oddíly operačního systému pomocí bit-locker a další oddíly i v případě, že ukládají citlivá data. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Ujistěte se, že jsou na zařízeních povoleny pouze minimální služby/funkce.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Nepovolujte ani nevypínejte žádné funkce nebo služby v os, které nejsou vyžadovány pro fungování řešení. Pokud například zařízení nevyžaduje nasazení ui, nainstalujte windows IoT Core v bezhlavém režimu. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Šifrování operačního systému a dalšíoddíly brány IoT Field Gateway pomocí bitové skříňky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Windows 10 IoT Core implementuje odlehčenou verzi bit-locker device encryption, která má silnou závislost na přítomnosti čipu TPM na platformě, včetně nezbytného protokolu preOS v UEFI, který provádí potřebná měření. Tato měření preOS zajišťují, že operační systém má později konečný záznam o tom, jak byl operační systém spuštěn. Šifrujte oddíly operačního systému pomocí bit-locker a další oddíly i v případě, že ukládají citlivá data. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Ujistěte se, že se během instalace změní výchozí přihlašovací údaje brány pole.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána ioT pole | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že se během instalace změní výchozí přihlašovací údaje brány pole. |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Ujistěte se, že cloudová brána implementuje proces, který udržuje firmware připojených zařízení v aktuálním stavu.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Odkazy**              | [Přehled správy zařízení služby IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [Jak aktualizovat firmware zařízení](../../iot-hub/tutorial-firmware-update.md) |
| **Kroky** | LWM2M je protokol od Open Mobile Alliance pro správu ioT zařízení. Správa zařízení Azure IoT umožňuje interakci s fyzickými zařízeními pomocí úloh zařízení. Ujistěte se, že cloudová brána implementuje proces, který běžně udržuje data zařízení a další konfigurace aktuální pomocí správy zařízení Azure IoT Hub. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncového bodu nakonfigurované podle zásad organizace

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncového bodu, jako je bitová skříňka pro šifrování na úrovni disku, antivirová ochrana s aktualizovanými podpisy, brána firewall založená na hostiteli, upgrady operačního systému, zásady skupiny atd. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Zajištění zabezpečené správy přístupových klíčů úložiště Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Průvodce zabezpečením úložiště Azure – správa klíčů účtu úložiště](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroky** | <p>Úložiště klíčů: Doporučujeme uložit přístupové klíče úložiště Azure v Azure Key Vault jako tajný klíč a mít aplikace načíst klíč z trezoru klíčů. To se doporučuje z následujících důvodů:</p><ul><li>Aplikace nikdy nebude mít klíč úložiště pevně zakódovaný v konfiguračním souboru, který odstraní, že avenue někdo získat přístup ke klíčům bez konkrétního oprávnění</li><li>Přístup ke klíčům lze řídit pomocí služby Azure Active Directory. To znamená, že vlastník účtu může udělit přístup k hrstce aplikací, které potřebují načíst klíče z Azure Key Vault. Jiné aplikace nebudou mít přístup ke klíčům bez udělení oprávnění konkrétně</li><li>Regenerace klíčů: Doporučujeme mít zavedený proces pro obnovení přístupových klíčů úložiště Azure z bezpečnostních důvodů. Podrobnosti o tom, proč a jak plánovat regeneraci klíčů jsou popsány v referenčním článku průvodce zabezpečením úložiště Azure</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Ujistěte se, že jsou povoleny pouze důvěryhodné původy, pokud je cors povolena v úložišti Azure

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Podpora CORS pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroky** | Azure Storage umožňuje povolit CORS – Cross Origin sdílení prostředků. Pro každý účet úložiště můžete zadat domény, které mají přístup k prostředkům v tomto účtu úložiště. Ve výchozím nastavení je cors zakázán ve všech službách. Cors můžete povolit pomocí rozhraní REST API nebo knihovny klienta úložiště volat jednu z metod pro nastavení zásad služby. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Povolit funkci omezení služeb WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com) |
| **Kroky** | <p>Neomezení použití systémových prostředků by mohlo vést k vyčerpání prostředků a nakonec k odmítnutí služby.</p><ul><li>**VYSVĚTLENÍ:** Windows Communication Foundation (WCF) nabízí možnost omezení požadavků na služby. Povolení příliš mnoha požadavků klientů může zaplavit systém a vyčerpat jeho prostředky. Na druhou stranu povolení pouze malého počtu požadavků na službu může bránit oprávněným uživatelům v používání služby. Každá služba by měla být individuálně naladěna a nakonfigurována tak, aby umožňovala odpovídající množství prostředků.</li><li>**DOPORUČENÍ** Povolte funkci omezení služby WCF a nastavte limity vhodné pro vaši aplikaci.</li></ul>|

### <a name="example"></a>Příklad
Následuje příklad konfigurace s povoleným omezením:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-Information disclosure prostřednictvím metadat

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Rozhraní .NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com) |
| **Kroky** | Metadata mohou útočníkům pomoci seznámit se se systémem a naplánovat formu útoku. Služby WCF lze nakonfigurovat tak, aby zpřístupňovaly metadata. Metadata poskytují podrobné informace o popisu služby a neměla by být vysílána v produkčním prostředí. Vlastnosti `HttpGetEnabled`  /  `HttpsGetEnabled` třídy ServiceMetaData definují, zda služba zpřístupní metadata | 

### <a name="example"></a>Příklad
Níže uvedený kód instruuje WCF k vysílání metadat služby
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Nevysílat metadata služby v produkčním prostředí. Nastavte vlastnosti HttpGetEnabled / HttpsGetEnabled třídy ServiceMetaData na hodnotu false. 

### <a name="example"></a>Příklad
Níže uvedený kód instruuje WCF nevysílat metadata služby. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

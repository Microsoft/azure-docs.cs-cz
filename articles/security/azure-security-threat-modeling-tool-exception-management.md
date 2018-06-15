---
title: Výjimka Management - nástroj Microsoft Threat modelování – Azure | Microsoft Docs
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
ms.openlocfilehash: 9a8e0154faccca356c7fb8ce93e43ce67cc0aae2
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019581"
---
# <a name="security-frame-exception-management--mitigations"></a>Zabezpečení rámce: Správa výjimek | Způsoby zmírnění rizik 
| Produktům a službám | Článek |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – nezahrnujte serviceDebug uzlu v konfiguračním souboru](#servicedebug)</li><li>[WCF – nezahrnujte serviceMetadata uzlu v konfiguračním souboru](#servicemetadata)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Zajistit, že je v rozhraní ASP.NET Web API správné zpracování výjimek](#exception)</li></ul> |
| **Webové aplikace** | <ul><li>[Podrobné informace o zabezpečení v chybových zprávách, neuvádějí](#messages)</li><li>[Implementace výchozí chyba zpracování stránky](#default)</li><li>[Nastavte metodu nasazení prodejní ve službě IIS](#deployment)</li><li>[Výjimky má bezpečně selhat.](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF – nezahrnujte serviceDebug uzlu v konfiguračním souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné, NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | Ke zveřejnění informace o ladění lze nakonfigurovat komunikaci Framework služby Windows (WCF). Ladění informace nesmí použít v produkčním prostředí. `<serviceDebug>` Značky definuje, zda je povolena funkce informace o ladění služby WCF. Pokud třídu includeExceptionDetailInFaults atributu je nastavena na hodnotu true, informace o výjimce z aplikace, bude vrácen klientům. Útočníci můžou využít další informace, které získají z ladění výstupu připojit útoků, které cílí na rozhraní, databázi nebo jiným prostředkům, které používá aplikace. |

### <a name="example"></a>Příklad:
Zahrnuje následující konfigurační soubor `<serviceDebug>` značky: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Zakažte ladicí informace ve službě. Toho lze dosáhnout odebráním `<serviceDebug>` značky z konfiguračního souboru aplikace. 

## <a id="servicemetadata"></a>WCF – nezahrnujte serviceMetadata uzlu v konfiguračním souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | Obecné, NET Framework 3 |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroky** | Útočníkům cenné přehled o tom, jak mohou využívat služby můžete poskytnout veřejně odhalení informací o služby. `<serviceMetadata>` Značky povolí funkci publikování metadat. Metadata služby můžou obsahovat citlivé informace, které by neměl být veřejně přístupná. Minimálně povolíte jenom důvěryhodným uživatelům přístup k metadatům a ujistěte se, že nebude vystavena, víc informací. Ještě lepší zcela zakážete možnost publikování metadat. Nebude obsahovat bezpečné konfigurace WCF `<serviceMetadata>` značky. |

## <a id="exception"></a>Zajistit, že je v rozhraní ASP.NET Web API správné zpracování výjimek

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | MVC 5, MVC 6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zpracování výjimek v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling), [modelu ověřování v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Ve výchozím nastavení jsou nejvíce nezachycená výjimek v rozhraní ASP.NET Web API přeložit na odpovědi HTTP se stavovým kódem`500, Internal Server Error`|

### <a name="example"></a>Příklad:
K řízení vrátil rozhraní API, kód stavu `HttpResponseException` je možné, jak je uvedeno níže: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Příklad:
Pro další ovládací prvek v odpovědi výjimka `HttpResponseMessage` třídu lze použít, jak je uvedeno níže: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
K zachycení neošetřených výjimek, které nejsou typu `HttpResponseException`, lze použít filtry výjimek. Filtry výjimek implementovat `System.Web.Http.Filters.IExceptionFilter` rozhraní. Nejjednodušší způsob, jak zápis filtru výjimek je odvozena od `System.Web.Http.Filters.ExceptionFilterAttribute` třídy a přepsat metodu OnException. 

### <a name="example"></a>Příklad:
Tady je filtr, který převádí `NotImplementedException` výjimky do stavového kódu protokolu HTTP `501, Not Implemented`: 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Existuje několik způsobů, jak zaregistrovat filtr výjimek webového rozhraní API:
- Akce
- Adaptérem
- Globálně

### <a name="example"></a>Příklad:
Pokud chcete použít filtr na určité akce, přidejte filtr jako atribut na akci: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Příklad:
Filtr pro všechny akce, které použijete na `controller`, přidejte filtr jako atributu `controller` – třída: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Příklad:
Pokud chcete použít filtr globálně na všechny řadiče webového rozhraní API, přidá instanci filtr, který má `GlobalConfiguration.Configuration.Filters` kolekce. Filtry výjimek v této kolekci platí pro všechny akce kontroleru webového rozhraní API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Příklad:
Pro ověření modelu stav modelu, který lze předat CreateErrorResponse metoda, jak je uvedeno níže: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Zkontrolujte na odkazy v části odkazy na další podrobnosti o výjimečných zpracování a ověření modelu v rozhraní ASP.Net Web API 

## <a id="messages"></a>Podrobné informace o zabezpečení v chybových zprávách, neuvádějí

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Obecné chybové zprávy jsou poskytovány přímo na uživatele bez včetně dat citlivé aplikaci. Příklady citlivých dat:</p><ul><li>Názvy serverů</li><li>Připojovací řetězce</li><li>Uživatelská jména</li><li>Hesla</li><li>Postupy SQL</li><li>Podrobnosti o selhání dynamické SQL</li><li>Trasování zásobníku a řádky kódu</li><li>Proměnné uložené v paměti</li><li>Umístění jednotky a složky</li><li>Body instalace aplikací</li><li>Nastavení konfigurace hostitele</li><li>Další podrobnosti o interní aplikace</li></ul><p>Zachycení všech chyb v aplikaci a poskytuje obecné chybové zprávy, jakož i povolení vlastní chyby v rámci služby IIS vám pomůže zabránili odhalení informací. Databáze systému SQL Server a .NET zpracování výjimek, mezi jiné chybě zpracování architektury, jsou obzvláště podrobné a uživatel se zlými úmysly profilace aplikace velmi užitečné. Ne přímo zobrazit obsah třídy odvozené od třídy výjimky .NET a zajistěte, abyste měli správné zpracování výjimek, aby neočekávanou výjimku není vyvolána nechtěně přímo na uživatele.</p><ul><li>Zadejte obecné chybové zprávy přímo na uživatele, který abstraktní tokeny konkrétní podrobnosti najít přímo v výjimky nebo chybové zprávě</li><li>Nezobrazovat obsah třídy výjimky .NET přímo na uživatele</li><li>Depeše všechny chybové zprávy a podle potřeby informovat uživatele na základě obecnou chybovou zprávu odeslat klientovi aplikace</li><li>Nevystavujte obsah třídy výjimek, přímo na uživatele, zejména vrácená hodnota z `.ToString()`, nebo hodnoty vlastností zpráv nebo trasování zásobníku. Bezpečně protokolu tyto informace a zobrazit více neškodné zprávu pro uživatele</li></ul>|

## <a id="default"></a>Implementace výchozí chyba zpracování stránky

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Upravit dialogové okno nastavení stránky ASP.NET chyby](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroky** | <p>Pokud aplikace ASP.NET nezdaří a dojde HTTP/1.x 500 Vnitřní chyba serveru nebo konfigurace funkcí (například filtrování požadavků) zabraňuje zobrazení stránky, se budou generovat chybovou zprávu. Můžou správci rozhodnout, zda aplikace by měla pro klienta, podrobných chybových zpráv klientovi nebo podrobné chybové zprávy na localhost jenom zobrazit přátelskou zprávou. <customErrors> v souboru web.config má tři režimy:</p><ul><li>**Na:** Určuje, že jsou povoleny vlastní chyby. Pokud není zadán žádný atribut defaultRedirect, uživatelé uvidí Obecná chyba. Vlastní chyby jsou zobrazeny ke vzdáleným klientům a na místního hostitele</li><li>**Vypnutí:** Určuje, že vlastní chyby jsou vypnuté. Podrobné chyby technologie ASP.NET se zobrazí ke vzdáleným klientům a místního hostitele</li><li>**RemoteOnly:** Určuje, že vlastní chyby jsou zobrazeny pouze ke vzdáleným klientům, a že ASP.NET chyby jsou zobrazeny na místního hostitele. Toto je výchozí hodnota</li></ul><p>Otevřete `web.config` souboru aplikace nebo webu a zkontrolujte, zda má značky buď `<customErrors mode="RemoteOnly" />` nebo `<customErrors mode="On" />` definované.</p>|

## <a id="deployment"></a>Nastavte metodu nasazení prodejní ve službě IIS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Nasazení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [nasazení – Element (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroky** | <p>`<deployment retail>` Přepínač je určená pro produkční servery služby IIS. Tento přepínač se používá k pomoci se spouští s nejmenšími možnými úniků informace o zabezpečení a nejlepší možný výkon zakázáním schopnost aplikace generovat výstup trasování na stránce, zakázání schopnost zobrazovat podrobné chybové zprávy pro koncové uživatele a zakázání přepínač ladění aplikace.</p><p>Často, přepínače a možnosti, které jsou zaměřené na vývojáře, například se nezdařilo žádosti o trasování a ladění, jsou povolené během vývoje aktivní. Doporučuje se, že metoda nasazení na žádném serveru pro produkční být nastavená na prodejní. otevření souboru machine.config a ujistěte se, že `<deployment retail="true" />` zůstane nastavený na hodnotu true.</p>|

## <a id="fail"></a>Výjimky má bezpečně selhat.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použít technologie** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Bezpečně nezdaří](https://www.owasp.org/index.php/Fail_securely) |
| **Kroky** | Aplikace má bezpečně selhat. Libovolné metody, která vrací logickou hodnotu, podle které určité rozhoduje, měli pečlivě vytvořit bloku výjimky. Existuje mnoho logické chyby kvůli které problémy se zabezpečením nárůstu v při neuváženě zápisu bloku výjimky.|

### <a name="example"></a>Příklad:
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Výše metoda vždy vrátí hodnotu True, pokud dojde k výjimce. Pokud koncový uživatel poskytuje poškozená adresa URL, která respektuje prohlížeče, ale `Uri()` konstruktor není to vyvolá výjimku a napadené budete přesměrováni na adresu URL platná, ale poškozený. 
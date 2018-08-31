---
title: Výjimka Management – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
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
ms.openlocfilehash: ce748be7f11d440e656e4af5cdd3cee3bbc9e313
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302145"
---
# <a name="security-frame-exception-management--mitigations"></a>Zabezpečení Frame: Správa výjimek | Zmírnění rizik 
| Produkt nebo službu | Článek |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – nezahrnujte serviceDebug uzlu v konfiguračním souboru](#servicedebug)</li><li>[WCF – nezahrnujte serviceMetadata uzlu v konfiguračním souboru](#servicemetadata)</li></ul> |
| **Webové rozhraní API** | <ul><li>[Ujistěte se, že se provádí zpracování správné výjimek v rozhraní ASP.NET Web API ](#exception)</li></ul> |
| **Webové aplikace** | <ul><li>[Nezveřejňujte podrobné informace o zabezpečení v chybové zprávy ](#messages)</li><li>[Implementace zpracování stránky výchozí chyb ](#default)</li><li>[Nastavit na prodejní ve službě IIS metoda nasazení](#deployment)</li><li>[Výjimky by selhat bezpečně](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF – nezahrnujte serviceDebug uzlu v konfiguračním souboru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecná rozhraní NET Framework 3 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroky** | Služby Windows Communication Framework (WCF) můžete nakonfigurovat ke zveřejnění informací o ladění. Ladit informace neměli používat v produkčním prostředí. `<serviceDebug>` Značky definuje, zda je povoleno funkce informace ladění služby WCF. Pokud includeExceptionDetailInFaults atributu je nastavena na hodnotu true, informace o výjimce z aplikace se vrátí do klientů. Útočníci mohou využívat další informace, které získávají z k útokům cílené na rozhraní framework, databáze nebo další prostředky, které aplikace používá výstup ladění. |

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
Zakážete informace o ladění ve službě. Toho můžete docílit tak, že odeberete `<serviceDebug>` značky z konfiguračního souboru aplikace. 

## <a id="servicemetadata"></a>WCF – nezahrnujte serviceMetadata uzlu v konfiguračním souboru

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | Obecná rozhraní NET Framework 3 |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroky** | Útočníkům cenné přehled o tom, jak mohou využívat služby můžete poskytnout veřejně odhalení informací o službě. `<serviceMetadata>` Značky povolí funkci publikování metadat. Metadata služby může obsahovat citlivé informace, které by neměly být veřejně přístupná. Minimálně Povolit jenom důvěryhodným uživatelům přístup k metadatům a ujistěte se, že se nevystaví nepotřebné informace. Ještě lepší zcela zakážete publikování metadat. Nesmí obsahovat bezpečné konfigurace WCF `<serviceMetadata>` značky. |

## <a id="exception"></a>Ujistěte se, že se provádí zpracování správné výjimek v rozhraní ASP.NET Web API

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | MVC 5, MVC 6 |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Zpracování výjimek v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling), [Model ověřování v rozhraní ASP.NET Web API](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Ve výchozím nastavení většina nezachycených výjimek v rozhraní ASP.NET Web API jsou přeloženy do odpovědi HTTP se stavovým kódem `500, Internal Server Error`|

### <a name="example"></a>Příklad:
K řízení vrácená rozhraním API, kód stavu `HttpResponseException` je možné, jak je znázorněno níže: 
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
Pro další ovládací prvek na výjimky odpovědi `HttpResponseMessage` třídu lze použít, jak je znázorněno níže: 
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
Pokud chcete zachytávat neošetřené výjimky, které nejsou typu `HttpResponseException`, lze použít filtry výjimek. Filtry výjimek implementovat `System.Web.Http.Filters.IExceptionFilter` rozhraní. Nejjednodušší způsob, jak zápis filtru výjimek je odvozena z `System.Web.Http.Filters.ExceptionFilterAttribute` třídy a přepsat metodu OnException. 

### <a name="example"></a>Příklad:
Tady je filtr, který převede `NotImplementedException` výjimky na stavový kód HTTP `501, Not Implemented`: 
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
- Adaptér
- Globálně

### <a name="example"></a>Příklad:
Použít filtr pro určité akce, přidejte filtr jako atribut na akci: 
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
Použijte filtr na všechny akce na `controller`, přidejte filtr jako atribut, který má `controller` třídy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Příklad:
Chcete-li použít filtr globálně na všechny řadiče webové rozhraní API, přidejte instance filtru, který `GlobalConfiguration.Configuration.Filters` kolekce. Filtry výjimek v této kolekci platí pro všechny akce kontroleru webového rozhraní API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Příklad:
Pro ověření modelu stav modelu, který lze předat CreateErrorResponse způsob, jak je znázorněno níže: 
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

Zkontrolujte odkazy v části odkazy na další podrobnosti o zpracování výjimek a ověření modelu v rozhraní ASP.Net Web API 

## <a id="messages"></a>Nezveřejňujte podrobné informace o zabezpečení v chybové zprávy

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | neuvedeno  |
| **Kroky** | <p>Obecné chybové zprávy jsou k dispozici uživateli bez zahrnutí aplikace citlivá data. Příklady citlivá data:</p><ul><li>Názvy serverů</li><li>Připojovací řetězce</li><li>Uživatelská jména</li><li>Hesla</li><li>Postupy SQL</li><li>Podrobnosti o neúspěšných dynamické SQL</li><li>Trasování zásobníku a řádky kódu</li><li>Proměnné uložené v paměti</li><li>Umístění jednotky a složky</li><li>Body instalace aplikace</li><li>Konfigurace nastavení hostitele</li><li>Další podrobnosti o interní aplikace</li></ul><p>Zachycení všech chyb v rámci aplikace a poskytuje obecné chybové zprávy a také při povolování vlastních chyb v rámci služby IIS vám pomůže zabránili odhalení informací. Databáze systému SQL Server a .NET zpracování výjimek, mimo jiné architektury, pro zpracování chyb jsou obzvláště podrobné a velmi užitečné ke uživatel se zlými úmysly profilování vaší aplikace. Ne přímo zobrazení obsahu třídu odvozenou z třídy výjimek .NET a zkontrolujte, že máte správné zpracování vyjímek tak, aby Neočekávaná výjimka není vyvolána neúmyslně přímo na uživatele.</p><ul><li>Zadejte obecné chybové zprávy přímo na uživatele, který abstraktní tokeny konkrétní podrobnosti, které jsou součástí výjimky nebo chybové zprávy</li><li>Nezobrazovat uživateli obsah výjimky třída rozhraní .NET</li><li>Zachytávat všechny chybové zprávy a v případě potřeby informovat uživatele prostřednictvím obecné chybové zprávy zasílané klientovi aplikace</li><li>Nezveřejňujte obsah třída výjimky přímo na uživatele, zejména návratovou hodnotu z `.ToString()`, nebo hodnoty vlastností zpráv nebo trasování zásobníku. Bezpečně tyto informace protokolu a zobrazit více neškodného zprávu pro uživatele</li></ul>|

## <a id="default"></a>Implementace zpracování stránky výchozí chyb

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Upravit dialogové okno nastavení stránek ASP.NET chyba](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroky** | <p>Když aplikace ASP.NET se nezdaří a způsobí, že o HTTP/1.x 500 interní chybu serveru nebo konfigurace funkcí (jako je například filtrování požadavků) zabraňuje zobrazení stránky, bude vygenerována chybová zpráva. Správci mohou zvolit, jestli aplikace pro klienta, Podrobná chybová zpráva do klienta nebo podrobné chybové zprávy na místního hostitele pouze zobrazeno přátelskou zprávou. <customErrors> Značky v souboru web.config má tří režimů:</p><ul><li>**Na:** Určuje, že jsou povolené vlastní chyby. Pokud není zadán žádný atribut defaultRedirect, uživatelé uvidí Obecná chyba. Vzdálení klienti a místního hostitele, zobrazují se vlastní chyby</li><li>**Vypnuto:** Určuje, že je zakázáno vlastních chyb. Podrobné chyby technologie ASP.NET jsou uvedeny ke vzdáleným klientům a na místního hostitele</li><li>**RemoteOnly:** Určuje, že vlastní chyby jsou zobrazeny pouze vzdáleným klientům a že technologie ASP.NET chyby jsou zobrazeny na místního hostitele. Toto je výchozí hodnota</li></ul><p>Otevřít `web.config` soubor aplikace nebo webu a zkontrolujte, zda má značka buď `<customErrors mode="RemoteOnly" />` nebo `<customErrors mode="On" />` definované.</p>|

## <a id="deployment"></a>Nastavit na prodejní ve službě IIS metoda nasazení

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Nasazení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [nasazení – Element (schéma nastavení technologie ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroky** | <p>`<deployment retail>` Přepínač je určen pro provozní servery služby IIS. Tento přepínač se používá ke spuštění s nejmenšími možnými úniků informace o zabezpečení a nejlepší možný výkon tím, že zakážete schopnost aplikace generovat výstup trasování na stránce, zakázat možnost zobrazit podrobné chybové zprávy, které aplikace koncoví uživatelé a zakázat ladění přepínače.</p><p>Častým, přepínače a možnosti, které jsou zaměřené na vývojáře, jako je nepovedlo požádat o trasování a ladění, se povolují při aktivním vývoji. Doporučuje se, že na prodejní nastavit metodu nasazení na libovolném serveru produkčního prostředí. Otevřete soubor machine.config a ujistěte se, že `<deployment retail="true" />` zůstane nastavený na hodnotu true.</p>|

## <a id="fail"></a>Výjimky by selhat bezpečně

| Titul                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **SDL fáze**               | Sestavení |  
| **Použitelných technologiích** | Obecné |
| **Atributy**              | neuvedeno  |
| **Odkazy**              | [Bezpečně selhání](https://www.owasp.org/index.php/Fail_securely) |
| **Kroky** | Aplikace by selhat bezpečně. Jakoukoli metodu, která vrací logickou hodnotu, podle které určité se rozhodne, měli pečlivě vytvořili bloku výjimky. Existuje mnoho logických chyb kvůli které problémy se zabezpečením nárůst v při neuváženě zápisu bloku výjimky.|

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
Výše uvedené metoda vždy vrátí hodnotu True, pokud dojde k výjimce. Pokud koncový uživatel poskytne poškozenou adresu URL, který respektuje prohlížeče, ale `Uri()` nemá konstruktor, to vyvolá výjimku a napadeným budete přesměrováni na adresu URL platný, ale poškozené. 
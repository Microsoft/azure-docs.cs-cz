---
title: Správa výjimek – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: b8fad566b54ab645660011ad3188394b6f8190b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728072"
---
# <a name="security-frame-exception-management--mitigations"></a>Rámec zabezpečení: Správa výjimek | Skutečnosti snižující závažnost rizika 
| Produkt/služba | Článek |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF- Nezahrnovat serviceDebug uzel v konfiguračním souboru](#servicedebug)</li><li>[WCF- Nezahrnčíte uzel metadat a service Do konfiguračního souboru](#servicemetadata)</li></ul> |
| **Web API** | <ul><li>[Ujistěte se, že správné zpracování výjimek se provádí v ASP.NET webové rozhraní API](#exception)</li></ul> |
| **Webová aplikace** | <ul><li>[Nevystavujte podrobnosti zabezpečení v chybových zprávách](#messages)</li><li>[Implementovat výchozí stránku zpracování chyb](#default)</li><li>[Nastavení metody nasazení na maloobchod ve službách IIS](#deployment)</li><li>[Výjimky by měly bezpečně selhat](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF- Nezahrnovat serviceDebug uzel v konfiguračním souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecný, NET Framework 3 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroky** | Služby WCF (Windows Communication Framework) lze nakonfigurovat tak, aby zpřístupňovaly informace o ladění. Informace o ladění by neměly být používány v produkčním prostředí. Značka `<serviceDebug>` definuje, zda je funkce informací o ladění povolena pro službu WCF. Pokud atribut includeExceptionDetailInFaults je nastavena na hodnotu true, informace o výjimce z aplikace budou vráceny klientům. Útočníci mohou využít další informace, které získají z ladění výstupu připojit útoky cílené na rozhraní, databáze nebo jiné prostředky používané aplikací. |

### <a name="example"></a>Příklad
Značka obsahuje `<serviceDebug>` následující konfigurační soubor: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Zakažte informace o ladění ve službě. Toho lze dosáhnout odebráním `<serviceDebug>` značky z konfiguračního souboru aplikace. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF- Nezahrnčíte uzel metadat a service Do konfiguračního souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Obecný, NET Framework 3 |
| **Odkazy**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Opevnitkrálovství](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroky** | Veřejné vystavení informací o službě může útočníkům poskytnout cenný přehled o tom, jak by mohli službu zneužít. Značka `<serviceMetadata>` umožňuje funkci publikování metadat. Metadata služby mohou obsahovat citlivé informace, které by neměly být veřejně přístupné. Minimálně povolte přístup k metadatům pouze důvěryhodným uživatelům a zajistěte, aby nebyly vystaveny nepotřebné informace. Ještě lépe, zcela zakázat možnost publikovat metadata. Bezpečná konfigurace WCF nebude `<serviceMetadata>` obsahovat značku. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Ujistěte se, že správné zpracování výjimek se provádí v ASP.NET webové rozhraní API

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5, MVC 6 |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Zpracování výjimek v ASP.NET webovérozhraní API](https://www.asp.net/web-api/overview/error-handling/exception-handling), [ověření modelu v ASP.NET webovérozhraní API](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Ve výchozím nastavení je většina nezachycených výjimek v ASP.NET webovém rozhraní API přeložena do odpovědi HTTP se stavovým kódem`500, Internal Server Error`|

### <a name="example"></a>Příklad
Chcete-li řídit stavový kód `HttpResponseException` vrácený rozhraním API, lze použít, jak je znázorněno níže: 
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

### <a name="example"></a>Příklad
Pro další kontrolu odpovědi na `HttpResponseMessage` výjimku lze třídu použít, jak je znázorněno níže: 
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
Chcete-li zachytit neošetřené výjimky, které nejsou typu `HttpResponseException`, filtry výjimek lze použít. Filtry výjimek implementují `System.Web.Http.Filters.IExceptionFilter` rozhraní. Nejjednodušší způsob, jak napsat filtr výjimek `System.Web.Http.Filters.ExceptionFilterAttribute` je odvodit z třídy a přepsat OnException metoda. 

### <a name="example"></a>Příklad
Zde je filtr, `NotImplementedException` který převádí výjimky na stavový kód `501, Not Implemented`HTTP : 
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

Filtr výjimek webového rozhraní API lze zaregistrovat několika způsoby:
- Podle akce
- Podle regulátoru
- Globálně

### <a name="example"></a>Příklad
Chcete-li filtr použít na určitou akci, přidejte filtr jako atribut k akci: 
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
### <a name="example"></a>Příklad
Chcete-li filtr použít na všechny `controller`akce v aplikaci , `controller` přidejte filtr jako atribut do třídy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Příklad
Chcete-li filtr použít globálně na všechny řadiče webového rozhraní `GlobalConfiguration.Configuration.Filters` API, přidejte do kolekce instanci filtru. Filtry výjimek v této kolekci platí pro všechny akce řadiče webového rozhraní API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Příklad
Pro ověření modelu může být stav modelu předán metodě CreateErrorResponse, jak je znázorněno níže: 
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

Další podrobnosti o výjimečném zpracování a ověření modelu v ASP.NET webovérozhraní API naleznete v odkazech v části odkazy. 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Nevystavujte podrobnosti zabezpečení v chybových zprávách

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | Není dostupné.  |
| **Kroky** | <p>Obecné chybové zprávy jsou poskytovány přímo uživateli bez zahrnutí citlivých dat aplikace. Příklady citlivých údajů zahrnují:</p><ul><li>Názvy serverů</li><li>Připojovací řetězce</li><li>Uživatelských jmen</li><li>Hesla</li><li>Postupy SQL</li><li>Podrobnosti o dynamických selháních SQL</li><li>Trasování a řádky kódu zásobníku</li><li>Proměnné uložené v paměti</li><li>Umístění jednotek a složek</li><li>Instalační body aplikace</li><li>Nastavení konfigurace hostitele</li><li>Další interní podrobnosti o aplikaci</li></ul><p>Vytváření přesahů všech chyb v rámci aplikace a poskytování obecných chybových zpráv a povolení vlastních chyb ve službách IIS pomůže zabránit zpřístupnění informací. Sql Server databáze a zpracování výjimek .NET, mimo jiné zpracování architektur, jsou obzvláště podrobné a velmi užitečné pro uživatele se zlými úmysly profilování aplikace. Nezobrazovat přímo obsah třídy odvozené z třídy .NET Exception a ujistěte se, že máte správné zpracování výjimek tak, aby neočekávaná výjimka nebyla neúmyslně vyvolána přímo uživateli.</p><ul><li>Poskytněte obecné chybové zprávy přímo uživateli, který abstrahuje konkrétní podrobnosti nalezené přímo v upozornění/chybové zprávě</li><li>Nezobrazovat obsah třídy výjimek .NET přímo uživateli</li><li>Soutisk všech chybových zpráv a v případě potřeby informovat uživatele pomocí obecné chybové zprávy odeslané klientovi aplikace</li><li>Nevystavujte obsah Exception třídy přímo uživateli, zejména `.ToString()`vrácená hodnota z , nebo hodnoty Message nebo StackTrace vlastnosti. Bezpečně protokolovat tyto informace a zobrazit více neškodné zprávy pro uživatele</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementovat výchozí stránku zpracování chyb

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Dialogové okno Upravit nastavení chybových stránek ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroky** | <p>Pokud ASP.NET aplikace selže a způsobí chybu interního serveru HTTP/1.x 500 nebo konfigurace funkce (například Filtrování požadavků) zabrání zobrazení stránky, bude vygenerována chybová zpráva. Správci mohou zvolit, zda aplikace má zobrazit popisnou zprávu klientovi, podrobnou chybovou zprávu klientovi nebo podrobnou chybovou zprávu pouze localhost. Značka `<customErrors>` v souboru web.config má tři režimy:</p><ul><li>**Zapnuto:** Určuje, že jsou povoleny vlastní chyby. Pokud není zadán žádný atribut defaultRedirect, zobrazí se uživatelům obecná chyba. Vlastní chyby se zobrazí vzdáleným klientům a místnímu hostiteli.</li><li>**Vypnuto:** Určuje, že vlastní chyby jsou zakázány. Podrobné chyby ASP.NET jsou zobrazeny vzdáleným klientům a místnímu hostiteli</li><li>**Pouze vzdálené:** Určuje, že vlastní chyby se zobrazují pouze vzdáleným klientům a že ASP.NET chyby jsou zobrazeny místnímu hostiteli. Toto je výchozí hodnota.</li></ul><p>Otevřete `web.config` soubor aplikace/webu a ujistěte se, že značka má buď `<customErrors mode="RemoteOnly" />` nebo `<customErrors mode="On" />` definované.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Nastavení metody nasazení na maloobchod ve službách IIS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Element nasazení (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroky** | <p>Přepínač `<deployment retail>` je určen pro produkční servery služby IIS. Tento přepínač se používá k pomoci aplikacím spustit s nejlepší možný výkon a co nejmenší možné úniky informací o zabezpečení zakázáním aplikace schopnost generovat výstup trasování na stránce, zakázání schopnost zobrazit podrobné chybové zprávy koncových uživatelů a zakázání přepínače ladění.</p><p>Často časy, přepínače a možnosti, které jsou zaměřené na vývojáře, jako je například neúspěšné trasování požadavků a ladění, jsou povoleny během aktivního vývoje. Doporučuje se, aby metoda nasazení na libovolném produkčním serveru byla nastavena na maloobchodní. otevřete soubor machine.config `<deployment retail="true" />` a ujistěte se, že zůstane nastavena na hodnotu true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Výjimky by měly bezpečně selhat

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není dostupné.  |
| **Odkazy**              | [Bezpečně selhat](https://www.owasp.org/index.php/Fail_securely) |
| **Kroky** | Aplikace by měla bezpečně selhat. Každá metoda, která vrací logickou hodnotu, na základě které je určité rozhodnutí, by měla mít pečlivě vytvořený blok výjimek. Existuje mnoho logických chyb kvůli problémům se zabezpečením, které se přibližují, když je blok výjimek napsán nedbale.|

### <a name="example"></a>Příklad
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
Výše uvedená metoda vždy vrátí True, pokud dojde k nějaké výjimce. Pokud koncový uživatel poskytuje poškozenou adresu URL, kterou `Uri()` prohlížeč respektuje, ale konstruktor nikoliv, vyvolá se výjimka a oběť bude převedena na platnou, ale poškozenou adresu URL. 

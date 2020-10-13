---
title: Správa výjimek – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Přečtěte si o správě výjimek v Threat Modeling Tool. Podívejte se na informace o zmírnění rizik a zobrazte příklady kódu.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f68bc5e4604f35f9c4c45cd3e38ddaf8d24cd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89004455"
---
# <a name="security-frame-exception-management--mitigations"></a>Rámec zabezpečení: Správa výjimek | Hrozeb 
| Produkt/služba | Článek |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF – Nezahrnovat uzel serviceDebug do konfiguračního souboru](#servicedebug)</li><li>[WCF – Nezahrnovat uzel oddílu serviceMetadata do konfiguračního souboru](#servicemetadata)</li></ul> |
| **Web API** | <ul><li>[Zajistěte, aby ve webovém rozhraní API ASP.NET bylo provedeno správné zpracování výjimek.](#exception)</li></ul> |
| **Webová aplikace** | <ul><li>[Nezveřejňujte podrobnosti zabezpečení v chybových zprávách.](#messages)</li><li>[Implementace výchozí stránky pro zpracování chyb](#default)</li><li>[Nastavení metody nasazení na maloobchodní prodej ve službě IIS](#deployment)</li><li>[Výjimky by měly selhat bez obav](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF – Nezahrnovat uzel serviceDebug do konfiguračního souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné, NET Framework 3 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroky** | Služby Windows Communication Framework (WCF) je možné nakonfigurovat tak, aby zveřejnily ladicí informace. Informace o ladění by se neměly používat v produkčním prostředí. `<serviceDebug>`Značka definuje, zda je funkce ladicích informací povolena pro službu WCF. Pokud je atribut zapnete IncludeExceptionDetailInFaults nastaven na hodnotu true, budou klientům vráceny informace o výjimce z aplikace. Útočníci můžou využít další informace, které získají z výstupu ladění, aby dokázali připojit útoky zaměřené na rozhraní, databázi nebo jiné prostředky, které aplikace používá. |

### <a name="example"></a>Příklad
Následující konfigurační soubor obsahuje `<serviceDebug>` značku: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Zakáže informace o ladění ve službě. To lze provést odebráním `<serviceDebug>` značky z konfiguračního souboru aplikace. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF – Nezahrnovat uzel oddílu serviceMetadata do konfiguračního souboru

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | WCF | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Obecné, NET Framework 3 |
| **Reference**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [obohacení království](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroky** | Veřejně vystavení informací o službě může útočníkům poskytnout cenné informace o tom, jak můžou službu zneužít. `<serviceMetadata>`Značka povoluje funkci publikování metadat. Metadata služby mohou obsahovat citlivé informace, které by neměly být veřejně přístupné. Minimálně Umožněte přístup k metadatům jenom důvěryhodným uživatelům a ujistěte se, že nejsou vystavené zbytečné informace. Ještě lepší je, zcela zakázat možnost publikování metadat. Bezpečná konfigurace WCF nebude obsahovat `<serviceMetadata>` značku. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Zajistěte, aby ve webovém rozhraní API ASP.NET bylo provedeno správné zpracování výjimek.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Web API | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | MVC 5, MVC 6 |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Zpracování výjimek ve webovém rozhraní api ASP.NET](https://www.asp.net/web-api/overview/error-handling/exception-handling), [ověřování modelu ve webovém rozhraní API ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroky** | Ve výchozím nastavení se většina nezachycených výjimek ve webovém rozhraní API ASP.NET převede na odpověď HTTP se stavovým kódem. `500, Internal Server Error`|

### <a name="example"></a>Příklad
Chcete-li řídit stavový kód vrácený rozhraním API, `HttpResponseException` lze použít jak je znázorněno níže: 
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
Pro další kontrolu na reakci na výjimku `HttpResponseMessage` lze použít třídu, jak je znázorněno níže: 
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
Chcete-li zachytit neošetřené výjimky, které nejsou typu `HttpResponseException` , lze použít filtry výjimky. Filtry výjimek implementují `System.Web.Http.Filters.IExceptionFilter` rozhraní. Nejjednodušší způsob, jak napsat filtr výjimky, je odvozovat z `System.Web.Http.Filters.ExceptionFilterAttribute` třídy a přepsat metodu s výjimkou. 

### <a name="example"></a>Příklad
Tady je filtr, který převádí `NotImplementedException` výjimky na stavový kód HTTP `501, Not Implemented` : 
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

Existuje několik způsobů, jak zaregistrovat filtr výjimky webového rozhraní API:
- Podle akce
- Podle kontroléru
- Univerzál

### <a name="example"></a>Příklad
Chcete-li použít filtr na konkrétní akci, přidejte filtr jako atribut do akce: 
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
Chcete-li použít filtr na všechny akce na `controller` , přidejte filtr jako atribut do `controller` třídy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Příklad
Pokud chcete filtr použít globálně na všechny řadiče webového rozhraní API, přidejte do kolekce instanci filtru `GlobalConfiguration.Configuration.Filters` . Filtry výjimek v této kolekci platí pro všechny akce kontroleru webového rozhraní API. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Příklad
Pro ověření modelu lze stav modelu předat metodě CreateErrorResponse, jak je znázorněno níže: 
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

Další podrobnosti o mimořádném zpracování a ověřování modelu ve webovém rozhraní API ASP.NET najdete v odkazech v části odkazy. 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Nezveřejňujte podrobnosti zabezpečení v chybových zprávách.

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | Není k dispozici  |
| **Kroky** | <p>Obecné chybové zprávy jsou poskytovány přímo uživateli bez zahrnutí citlivých aplikačních dat. Mezi příklady citlivých dat patří:</p><ul><li>Názvy serverů</li><li>Připojovací řetězce</li><li>Uživatelských jmen</li><li>Hesla</li><li>Procedury SQL</li><li>Podrobnosti o dynamických selháních SQL</li><li>Trasování zásobníku a řádky kódu</li><li>Proměnné uložené v paměti</li><li>Umístění jednotky a složky</li><li>Body instalace aplikace</li><li>Nastavení konfigurace hostitele</li><li>Další podrobnosti o interní aplikaci</li></ul><p>Zachytávání všech chyb v rámci aplikace a poskytování obecných chybových zpráv a povolení vlastních chyb v rámci služby IIS vám pomůže zabránit vyzrazení informací. SQL Server zpracování výjimek databáze a .NET mimo jiné architektury zpracování chyb, jsou obzvláště podrobné a velmi užitečné pro uživatele se zlými úmysly profilování vaší aplikace. Nevybírejte přímo obsah třídy odvozené z třídy výjimek rozhraní .NET a ujistěte se, že máte správné zpracování výjimek, aby nedošlo k neúmyslnému vyvolání neočekávané výjimky přímo uživateli.</p><ul><li>Poskytněte obecné chybové zprávy přímo uživateli, který si vyvolal konkrétní podrobnosti, které se nacházejí přímo v výjimce/chybové zprávě.</li><li>Nezobrazovat obsah třídy výjimky .NET přímo uživateli</li><li>Zachytávání všech chybových zpráv a pokud je to vhodné, informujte uživatele prostřednictvím obecné chybové zprávy odeslané do klienta aplikace.</li><li>Nezveřejňujte obsah třídy Exception přímo uživateli, zejména návratovou hodnotu z `.ToString()` nebo hodnoty vlastností Message nebo trasování zásobníku. Bezpečně protokolujte tyto informace a zobrazí se uživateli neškodného zpráva.</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Implementace výchozí stránky pro zpracování chyb

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Dialogové okno Upravit nastavení chybových stránek ASP.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Kroky** | <p>Když aplikace ASP.NET selže a dojde k chybě interního serveru HTTP/1. x 500 nebo konfigurace funkce (například filtrování požadavků) brání zobrazení stránky, bude vygenerována chybová zpráva. Správci mohou zvolit, zda má aplikace zobrazit popisnou zprávu pro klienta, podrobnou chybovou zprávu klientovi nebo podrobnou chybovou zprávu pouze pro localhost. `<customErrors>`Značka v web.config má tři režimy:</p><ul><li>**Zapnuto:** Určuje, že jsou povoleny vlastní chyby. Pokud není zadán žádný atribut defaultRedirect, zobrazí se uživatelům Obecná chyba. Vlastní chyby se zobrazí vzdáleným klientům a místnímu hostiteli.</li><li>**Vypnuto:** Určuje, jestli jsou vlastní chyby zakázané. Podrobné chyby ASP.NET se zobrazí vzdáleným klientům a místnímu hostiteli.</li><li>**RemoteOnly:** Určuje, že se vlastní chyby zobrazují pouze vzdáleným klientům a že se chyby ASP.NET zobrazují na místním hostiteli. Toto je výchozí hodnota.</li></ul><p>Otevřete `web.config` soubor pro aplikaci nebo web a ujistěte se, že značka má buď `<customErrors mode="RemoteOnly" />` nebo `<customErrors mode="On" />` definován.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Nastavení metody nasazení na maloobchodní prodej ve službě IIS

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Nasazení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Element nasazení (schéma nastavení ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Kroky** | <p>`<deployment retail>`Přepínač je určený pro použití v produkčních serverech služby IIS. Tento přepínač slouží k tomu, aby aplikace běžely s nejlepším možným výkonem a minimálními možnými úniky informací o zabezpečení tím, že zakazují schopnost aplikace generovat výstup trasování na stránce, zakáže možnost zobrazit podrobné chybové zprávy pro koncové uživatele a zakáže přepínač ladění.</p><p>V průběhu aktivního vývoje jsou často povoleny časy, přepínače a možnosti, které jsou zaměřené na vývojáře, jako je například trasování a ladění požadavků na neúspěšné zpracování. Doporučuje se, aby byla metoda nasazení na jakémkoli provozním serveru nastavená na Retail. Otevřete soubor machine.config a ujistěte se, že `<deployment retail="true" />` zůstane nastavené na true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Výjimky by měly selhat bez obav

| Nadpis                   | Podrobnosti      |
| ----------------------- | ------------ |
| **Komponenta**               | Webová aplikace | 
| **Fáze SDL**               | Sestavení |  
| **Použitelné technologie** | Obecné |
| **Atributy**              | Není k dispozici  |
| **Reference**              | [Neúspěšné zabezpečení](https://owasp.org/www-community/Fail_securely) |
| **Kroky** | Aplikace by měla selhat bezpečně. Každá metoda, která vrací logickou hodnotu, na základě toho, jaké je učiněno určité rozhodnutí, by měla být pečlivě vytvořena blok výjimky. Došlo k velkému množství logických chyb z důvodu problémů se zabezpečením, když je blok výjimky napsán carelessly.|

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
Výše uvedená metoda vždycky vrátí true, pokud dojde k nějaké výjimce. Pokud koncový uživatel poskytne poškozenou adresu URL, v prohlížeči se projeví, ale `Uri()` konstruktor ne, vyvolá výjimku a oběť bude provedena na platnou, ale nesprávně vytvořenou adresu URL. 

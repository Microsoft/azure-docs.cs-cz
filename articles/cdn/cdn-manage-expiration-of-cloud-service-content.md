---
title: Správa vypršení platnosti webového obsahu v Azure CDN | Microsoft Docs
description: Naučte se spravovat vypršení platnosti Azure Web Apps/Cloud Services, ASP.NET nebo obsahu služby IIS v Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: fefa19e8dfee295d34231d36df079b80d1e82768
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778593"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Správa platnosti webového obsahu v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Soubory z veřejně přístupných webových serverů můžou být uložené v mezipaměti v Azure Content Delivery Network (CDN), dokud neuplyne jejich doba TTL (Time to Live). Hodnota TTL je určena `Cache-Control` hlavičkou v odpovědi HTTP ze zdrojového serveru. Tento článek popisuje, jak nastavit `Cache-Control` záhlaví pro funkci Web Apps Microsoft Azure App Service, v Azure Cloud Services, aplikacích ASP.NET a Internetová informační Službach lokalitách (IIS), které jsou nakonfigurované podobně. Hlavičku můžete nastavit `Cache-Control` buď pomocí konfiguračních souborů, nebo prostřednictvím kódu programu. 

Nastavení mezipaměti můžete také ovládat z Azure Portal nastavením [pravidel ukládání do mezipaměti CDN](cdn-caching-rules.md). Pokud vytvoříte jedno nebo více pravidel pro ukládání do mezipaměti a nakonfigurujete jejich chování ukládání do mezipaměti pro **přepsání** nebo **obejít mezipaměť** , je původní nastavení ukládání do mezipaměti popsané v tomto článku ignorováno. Informace o obecných konceptech mezipaměti najdete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete zvolit, aby se pro soubor nestavila hodnota TTL. V takovém případě Azure CDN automaticky použije výchozí hodnotu TTL 7 dní, pokud jste nenastavili pravidla ukládání do mezipaměti v Azure Portal. Výchozí hodnota TTL se vztahuje pouze na obecné optimalizace doručování webů. Pro optimalizace velkých souborů je výchozí hodnota TTL jeden den a optimalizace datových proudů médií je výchozí hodnota TTL jeden rok.
> 
> Další informace o tom, jak Azure CDN funguje pro urychlení přístupu k souborům a dalším prostředkům, najdete v tématu [Přehled Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlaviček Cache-Control pomocí pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení hlavičky webového serveru `Cache-Control` je použití pravidel ukládání do mezipaměti v Azure Portal. Další informace o pravidlech ukládání do mezipaměti CDN najdete v tématu [Azure CDN řízení chování při ukládání do mezipaměti pomocí pravidel pro ukládání do](cdn-caching-rules.md)mezipaměti.

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** . Pro **Azure CDN Premium ze profilů Verizon** je nutné použít [modul Azure CDN Rules](./cdn-verizon-premium-rules-engine.md) na portálu pro **správu** pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti CDN** :

1. V Azure Portal vyberte profil CDN a pak vyberte koncový bod webového serveru.

1. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti** .

   ![Tlačítko pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti** .

   ![Stránka ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Nastavení hlaviček Cache-Control webového serveru pomocí globálních pravidel ukládání do mezipaměti:**

1. V části **globální pravidla ukládání do** mezipaměti nastavte **chování při ukládání řetězce dotazu** na **Ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** pro **přepsání** .
      
1. V poli **Doba vypršení platnosti mezipaměti** zadejte 3600 do pole **sekund** nebo 1 v poli **hodiny** . 

   ![Příklad globálních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Toto globální pravidlo ukládání do mezipaměti nastavuje dobu trvání mezipaměti jednu hodinu a ovlivňuje všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` `Expires` hlavičky protokolu HTTP, které jsou odesílány ze zdrojového serveru zadaného koncovým bodem.   

1. Vyberte **Uložit** .

**Nastavení hlaviček souboru webového serveru Cache-Control pomocí vlastních pravidel ukládání do mezipaměti:**

1. V části **vlastní pravidla ukládání do mezipaměti** vytvořte dvě podmínky shody:

     a. U první podmínky shody nastavte **podmínku Match** na **path** a zadejte `/webfolder1/*` **hodnotu shody** . Nastavte **chování ukládání do mezipaměti** pro **přepsání** a do pole **hodiny** zadejte 4.

     b. U podmínky druhé shody nastavte **podmínku Match** na **path** a zadejte `/webfolder1/file1.txt` **hodnotu shody** . Nastavte **chování ukládání do mezipaměti** pro **přepsání** a zadáním 2 do pole **hodiny** .

    ![Příklad vlastních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání mezipaměti pro všechny soubory ve `/webfolder1` složce na zdrojovém serveru určeném vaším koncovým bodem na čtyři hodiny. Druhé pravidlo přepíše první pravidlo pouze pro daný `file1.txt` soubor a nastaví pro něj dobu trvání mezipaměti 2 hodiny.

1. Vyberte **Uložit** .


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Nastavení hlaviček Cache-Control pomocí konfiguračních souborů
Pro statický obsah, jako jsou obrázky a šablony stylů, můžete řídit četnost aktualizace úpravou **applicationHost.config** nebo **Web.config** konfiguračních souborů pro webovou aplikaci. Chcete-li nastavit `Cache-Control` hlavičku obsahu, použijte `<system.webServer>/<staticContent>/<clientCache>` prvek v obou souborech.

### <a name="using-applicationhostconfig-files"></a>Používání ApplicationHost.configch souborů
Soubor **ApplicationHost.config** je kořenovým souborem konfiguračního systému služby IIS. Konfigurační nastavení v souboru **ApplicationHost.config** ovlivňují všechny aplikace v lokalitě, ale jsou přepsány nastavením všech **Web.config** souborů, které existují pro webovou aplikaci.

### <a name="using-webconfig-files"></a>Používání Web.configch souborů
Pomocí souboru **Web.config** můžete přizpůsobit způsob, jakým se chová celá webová aplikace nebo konkrétní adresář ve vaší webové aplikaci. Obvykle máte alespoň jeden **Web.config** soubor v kořenové složce vaší webové aplikace. Pro každý soubor **Web.config** v konkrétní složce má nastavení konfigurace vliv na vše v této složce a jejích podsložkách, pokud nejsou přepsána na úrovni podsložky jiným souborem **Web.config** . 

Například můžete nastavit `<clientCache>` prvek v souboru **Web.config** v kořenové složce webové aplikace tak, aby veškerý statický obsah do mezipaměti po dobu tří dní do celého webu. Můžete také přidat soubor **Web.config** do podsložky s více obsahy proměnné (například `\frequent` ) a nastavit jeho `<clientCache>` element tak, aby obsah podsložky v mezipaměti po dobu šesti hodin. Výsledkem je, že obsah na celém webu je uložen do mezipaměti po dobu tří dnů, s výjimkou jakéhokoli obsahu v `\frequent` adresáři, který je uložen do mezipaměti pouze po dobu šesti hodin.  

Následující příklad konfiguračního souboru XML ukazuje, jak nastavit `<clientCache>` prvek pro zadání maximálního stáří tři dny:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Chcete-li použít atribut **cacheControlMaxAge** , je nutné nastavit hodnotu atributu **cacheControlMode** na `UseMaxAge` . Toto nastavení způsobilo přidání hlavičky protokolu HTTP a direktivy do `Cache-Control: max-age=<nnn>` odpovědi. Formát hodnoty TimeSpan pro atribut **cacheControlMaxAge** je `<days>.<hours>:<min>:<sec>` . Jeho hodnota je převedena na sekundy a použije se jako hodnota `Cache-Control` `max-age` direktivy. Další informace o elementu naleznete `<clientCache>` v tématu [mezipaměť \<clientCache> klienta ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Nastavování hlaviček Cache-Control programově
V případě aplikací ASP.NET řídíte chování ukládání do mezipaměti CDN programově nastavením vlastnosti **HttpResponse. cache** rozhraní .NET API. Informace o vlastnosti **HttpResponse. cache** naleznete v tématu [vlastnost HttpResponse. cache](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) a [třídu HttpCachePolicy](/dotnet/api/system.web.httpcachepolicy).  

Pro programové ukládání obsahu aplikace do mezipaměti v ASP.NET postupujte takto:
   1. Ověřte, že je obsah označený jako mezipaměť nastavením `HttpCacheability` na `Public` . 
   1. Nastavte ověřovací modul mezipaměti voláním jedné z následujících `HttpCachePolicy` metod:
      - Zavolejte `SetLastModified` k nastavení hodnoty časového razítka pro `Last-Modified` hlavičku.
      - Zavolejte `SetETag` na nastavení hodnoty pro `ETag` záhlaví.
   1. Volitelně můžete zadat čas vypršení platnosti mezipaměti voláním `SetExpires` pro nastavení hodnoty `Expires` záhlaví. V opačném případě platí pro použití výchozí heuristiky mezipaměti popsané dříve v tomto dokumentu.

Například pro ukládání obsahu do mezipaměti po dobu jedné hodiny přidejte následující kód jazyka C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testování hlavičky Cache-Control
Můžete snadno ověřit nastavení TTL vašeho webového obsahu. Pomocí [vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)v prohlížeči otestujte, jestli váš webový obsah obsahuje `Cache-Control` hlavičku odpovědi. K prohlédnutí hlaviček odpovědi můžete použít také nástroj, jako je **wget** , [post](https://www.getpostman.com/)nebo [Fiddler](https://www.telerik.com/fiddler) .

## <a name="next-steps"></a>Další kroky
* [Přečíst podrobnosti o elementu **clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Přečtěte si dokumentaci k vlastnosti **HttpResponse. cache.**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Přečtěte si dokumentaci ke **třídě HttpCachePolicy** .](/dotnet/api/system.web.httpcachepolicy)  
* [Přečtěte si o konceptech ukládání do mezipaměti](cdn-how-caching-works.md)
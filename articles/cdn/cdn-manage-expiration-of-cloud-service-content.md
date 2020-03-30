---
title: Správa vypršení platnosti webového obsahu v Azure CDN | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat vypršení platnosti obsahu Azure Web Apps/Cloud Services, ASP.NET nebo IIS v Azure CDN.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: a701c332659181081184906a73826b7137d8c49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593721"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Správa platnosti webového obsahu v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Úložiště objektů blob Azure](cdn-manage-expiration-of-blob-content.md)
> 

Soubory z veřejně přístupných webových serverů původu lze ukládat do mezipaměti v síti Azure Content Delivery Network (CDN) až do doby, kdy jejich doba aktivního vysílání (TTL) neuplyne. TTL je určena `Cache-Control` hlavičkou v odpovědi HTTP z původního serveru. Tento článek popisuje, `Cache-Control` jak nastavit záhlaví pro funkci Webové aplikace služby Microsoft Azure App Service, Cloud Services Azure, ASP.NET aplikací a internetových informačních služeb (IIS), které jsou nakonfigurovány podobně. `Cache-Control` Záhlaví můžete nastavit buď pomocí konfiguračních souborů, nebo programově. 

Nastavení mezipaměti můžete řídit také z portálu Azure nastavením [pravidel ukládání do mezipaměti CDN](cdn-caching-rules.md). Pokud vytvoříte jedno nebo více pravidel ukládání do mezipaměti a nastavíte jejich chování ukládání do mezipaměti na **Přepsání** nebo **Přepsání mezipaměti**, budou nastavení ukládání do mezipaměti zapředpokladutáněně popsané v tomto článku ignorována. Informace o obecných konceptech ukládání do mezipaměti naleznete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádné TTL na soubor. V takovém případě Azure CDN automaticky použije výchozí TTL sedm dní, pokud jste nastavili pravidla ukládání do mezipaměti na webu Azure Portal. Tento výchozí hodnota TTL se vztahuje pouze na obecné optimalizace webového doručování. Pro velké optimalizace souborů je výchozí Hodnota TTL jeden den a pro optimalizace streamování médií je výchozí hodnota TTL jeden rok.
> 
> Další informace o tom, jak Azure CDN funguje pro urychlení přístupu k souborům a dalším prostředkům, najdete [v tématu Přehled sítě pro doručování obsahu Azure](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení záhlaví řízení mezipaměti pomocí pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení záhlaví `Cache-Control` webového serveru je použití pravidel ukládání do mezipaměti na webu Azure Portal. Další informace o pravidlech ukládání do mezipaměti CDN naleznete v [tématu Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici jenom pro **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai.** Pro **profily Azure CDN Premium od Verizonu** je nutné použít [modul pravidel Azure CDN](cdn-rules-engine.md) na portálu Pro **správu** pro podobné funkce.

**Chcete-li přejít na stránku pravidel ukládání do mezipaměti sítě CDN**,

1. Na webu Azure Portal vyberte profil CDN a pak vyberte koncový bod pro webový server.

1. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko cdn caching rules](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka cdn ukládání do mezipaměti](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Nastavení záhlaví ovládacího prvku mezipaměti webového serveru pomocí globálních pravidel ukládání do mezipaměti:**

1. V části **Globální pravidla ukládání do mezipaměti**nastavte chování ukládání řetězce dotazu do **mezipaměti** na **Ignorovat řetězce dotazu** a nastavte **chování ukládání do mezipaměti** na **Přepsat**.
      
1. V **poli Doba vypršení platnosti mezipaměti**zadejte 3600 do pole **Sekundy** nebo 1 do pole **Hodiny.** 

   ![Příklad globálních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Toto pravidlo globálního ukládání do mezipaměti nastaví dobu trvání mezipaměti na jednu hodinu a ovlivní všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` `Expires` nebo HTTP záhlaví, které jsou odesílány zdrojovým serverem určeným koncovým bodem.   

1. Vyberte **Uložit**.

**Nastavení záhlaví správce mezipaměti souboru webového serveru pomocí vlastních pravidel ukládání do mezipaměti:**

1. V části **Vlastní pravidla ukládání do mezipaměti**vytvořte dvě podmínky shody:

     a. Pro první podmínku shody nastavte **podmínku shoda** s **cestou** a zadejte `/webfolder1/*` **hodnotu Shoda**. Nastavte **chování ukládání do mezipaměti** na **Přepsat** a zadejte 4 do pole **Hodiny.**

     b. Pro druhou podmínku shody nastavte **podmínku shoda** s **cestou** a zadejte `/webfolder1/file1.txt` **hodnotu Shoda**. Nastavte **chování ukládání do mezipaměti** na **Přepsat** a zadejte 2 do pole **Hodiny.**

    ![Příklad vlastních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání `/webfolder1` mezipaměti čtyři hodiny pro všechny soubory ve složce na původníserver určený koncovým bodem. Druhé pravidlo přepíše první pravidlo `file1.txt` pouze pro soubor a nastaví dobu trvání mezipaměti dvě hodiny pro něj.

1. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Nastavení záhlaví řízení mezipaměti pomocí konfiguračních souborů
U statického obsahu, jako jsou obrázky a šablony stylů, můžete řídit frekvenci aktualizace úpravou konfiguračních souborů **applicationHost.config** nebo **Web.config** pro webovou aplikaci. Chcete-li `Cache-Control` nastavit záhlaví obsahu, `<system.webServer>/<staticContent>/<clientCache>` použijte prvek v obou souborech.

### <a name="using-applicationhostconfig-files"></a>Použití souborů ApplicationHost.config
Soubor **ApplicationHost.config** je kořenový soubor konfiguračního systému služby IIS. Nastavení konfigurace v souboru **ApplicationHost.config** ovlivní všechny aplikace na webu, ale jsou přepsána nastavením všech souborů **Web.config,** které existují pro webovou aplikaci.

### <a name="using-webconfig-files"></a>Použití souborů Web.config
Pomocí souboru **Web.config** můžete přizpůsobit způsob, jakým se chová celá webová aplikace nebo konkrétní adresář webové aplikace. Obvykle máte alespoň jeden soubor **Web.config** v kořenové složce webové aplikace. Pro každý soubor **Web.config** v určité složce mají nastavení konfigurace vliv na vše v dané složce a jejích podsložkách, pokud nejsou na úrovni podsložky přepsána jiným souborem **Web.config.** 

Můžete například nastavit `<clientCache>` prvek v souboru **Web.config** v kořenové složce webové aplikace tak, aby uklápěl veškerý statický obsah ve webové aplikaci do mezipaměti po dobu tří dnů. Můžete také přidat soubor **Web.config** do podsložky s více `\frequent`proměnným `<clientCache>` obsahem (například) a nastavit jeho prvek tak, aby uklápěl obsah podsložky do mezipaměti po dobu šesti hodin. Čistým výsledkem je, že obsah na celém webu je uložen do `\frequent` mezipaměti po dobu tří dnů, s výjimkou jakéhokoli obsahu v adresáři, který je uložen do mezipaměti pouze šest hodin.  

Následující příklad konfiguračního souboru `<clientCache>` XML ukazuje, jak nastavit prvek tak, aby určovat maximální stáří tři dny:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Chcete-li použít atribut **cacheControlMaxAge,** musíte nastavit hodnotu `UseMaxAge`atributu **cacheControlMode** na . Toto nastavení způsobilo, že `Cache-Control: max-age=<nnn>`do odpovědi byla přidána hlavička a direktiva protokolu HTTP . Formát hodnoty timespan pro atribut **cacheControlMaxAge** je `<days>.<hours>:<min>:<sec>`. Jeho hodnota je převedena na sekundy a `Cache-Control` `max-age` používá se jako hodnota směrnice. Další informace o `<clientCache>` elementu naleznete v tématu [Client Cache \<clientCache>](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Programové nastavení záhlaví cache-control
U ASP.NET aplikací řídíte programové chování ukládání do mezipaměti CDN nastavením vlastnosti **HttpResponse.Cache** rozhraní .NET API. Informace o vlastnosti **HttpResponse.Cache** naleznete v tématu [HttpResponse.Cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) a [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy).  

Chcete-li programově ukládat obsah aplikace do mezipaměti v ASP.NET, postupujte takto:
   1. Nastavením na `HttpCacheability` `Public`hodnotu . 
   1. Nastavte validátor mezipaměti voláním jedné `HttpCachePolicy` z následujících metod:
      - Volání `SetLastModified` nastavit hodnotu časového `Last-Modified` razítka pro záhlaví.
      - Volání `SetETag` nastavit hodnotu `ETag` záhlaví.
   1. Volitelně můžete zadat čas vypršení `SetExpires` platnosti mezipaměti `Expires` voláním nastavení hodnoty pro hlavičku. V opačném případě platí výchozí heuristika mezipaměti popsaná dříve v tomto dokumentu.

Chcete-li například ukládat obsah do mezipaměti po dobu jedné hodiny, přidejte následující kód jazyka C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testování hlavičky Cache-Control
Můžete snadno ověřit nastavení TTL vašeho webového obsahu. Pomocí [vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)prohlížeče otestujte, zda `Cache-Control` váš webový obsah obsahuje záhlaví odpovědi. Můžete také použít nástroj, jako je **wget**, [Postman](https://www.getpostman.com/)nebo [Fiddler](https://www.telerik.com/fiddler) prozkoumat záhlaví odpovědi.

## <a name="next-steps"></a>Další kroky
* [Přečtěte si podrobnosti o elementu **clientCache**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Přečtěte si dokumentaci ke vlastnosti **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Přečtěte si dokumentaci ke **třídě HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Další informace o konceptech ukládání do mezipaměti](cdn-how-caching-works.md)

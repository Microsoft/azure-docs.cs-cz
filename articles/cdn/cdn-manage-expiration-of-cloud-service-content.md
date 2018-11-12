---
title: Správa vypršení platnosti webového obsahu v Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak spravovat platnost obsahu Azure Web Apps/Cloud Services, ASP.NET nebo IIS v Azure CDN.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: 19f928d854618a5e29841dc45d7846faf7fb83b4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51253121"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Správa vypršení platnosti webového obsahu v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Soubory z veřejně přístupné původu webových serverů můžete do mezipaměti v Azure Content Delivery Network (CDN), až uplyne jejich time to live (TTL). Hodnota TTL je určeno `Cache-Control` hlaviček v odpovědi HTTP ze zdrojového serveru. Tento článek popisuje, jak nastavit `Cache-Control` hlavičky pro funkci Web Apps služby Microsoft Azure App Service, Azure Cloud Services, aplikace ASP.NET a weby Internetové informační služby (IIS), které jsou nakonfigurované podobně. Můžete nastavit `Cache-Control` záhlaví s použitím konfiguračních souborů nebo prostřednictvím kódu programu. 

Nastavení mezipaměti na webu Azure Portal můžete také řídit nastavením [pravidla ukládání do mezipaměti CDN](cdn-caching-rules.md). Pokud jste vytvořili jeden nebo více ukládání do mezipaměti pravidla a nastavte jejich chování ukládání do mezipaměti na **přepsat** nebo **Nepoužívat mezipaměť**, zadaný původ ukládání do mezipaměti nastavení popisovaná v tomto článku jsou ignorovány. Informace o získání všeobecného přehledu ukládání do mezipaměti najdete v tématu [funguje jak ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádná hodnota TTL souboru. V tomto případě Azure CDN automaticky použije výchozí hodnota TTL sedm dní, pokud jste nastavili na portálu Azure portal pravidla ukládání do mezipaměti. Toto výchozí nastavení TTL platí jenom pro optimalizace doručení obecné web. Optimalizace velkých souborů je výchozí hodnota TTL je jeden den a pro optimalizace streamování médií, výchozí hodnota TTL je 1 rok.
> 
> Další informace o tom, jak funguje Azure CDN k urychlení přístupu k souborům a dalším prostředkům najdete v tématu [Přehled služby Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlavičky Cache-Control s využitím pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení webového serveru `Cache-Control` záhlaví, je použít pravidla ukládání do mezipaměti na webu Azure Portal. Další informace o pravidla ukládání do mezipaměti CDN najdete v tématu [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profily. Pro **Azure CDN Premium od Verizonu** profily, je nutné použít [stroj pravidel Azure CDN](cdn-rules-engine.md) v **spravovat** portálu pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti CDN**:

1. Na webu Azure Portal vyberte profil CDN a potom vyberte koncový bod pro webový server.

1. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Nastavení webového serveru hlavičky Cache-Control pomocí globální pravidla ukládání do mezipaměti:**

1. V části **globální pravidla ukládání do mezipaměti**, nastavte **chování ukládání řetězců dotazů** k **ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** k  **Přepsat**.
      
1. Pro **doba vypršení platnosti mezipaměti**, zadejte 3600 v **sekund** pole nebo 1 **hodin** pole. 

   ![Příklad globální pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Tento globální pravidla ukládání do mezipaměti nastaví dobu trvání mezipaměti jednu hodinu a má vliv na všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` nebo `Expires` hlavičky HTTP, které se odesílá na zdrojový server určeném na koncový bod.   

1. Vyberte **Uložit**.

**Nastavení webového serveru hlavičky Cache-Control souboru s použitím vlastní pravidla ukládání do mezipaměti:**

1. V části **vlastní pravidla ukládání do mezipaměti**, vytvořte dvě podmínky shody:

     a. Pro první podmínky shody, nastavte **odpovídají podmínce** k **cesta** a zadejte `/webfolder1/*` pro **odpovídají hodnotě**. Nastavte **chování ukládání do mezipaměti** k **přepsat** a zadejte 4 **hodin** pole.

     b. Druhá podmínka shody, nastavte **odpovídají podmínce** k **cesta** a zadejte `/webfolder1/file1.txt` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte v 2 **hodin** pole.

    ![Příklad vlastní pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání čtyři hodiny pro všechny soubory v mezipaměti `/webfolder1` složky na původním serveru určeného váš koncový bod. První pravidlo pro přepíše druhé pravidlo `file1.txt` pouze soubor a nastaví dobu trvání mezipaměti dvě hodiny.

1. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Nastavení hlavičky Cache-Control pomocí konfiguračních souborů
Statický obsah, jako jsou obrázky a šablony stylů, můžete řídit frekvenci aktualizací úpravou **applicationHost.config** nebo **Web.config** konfigurační soubory pro vaši webovou aplikaci. Chcete-li nastavit `Cache-Control` záhlaví pro obsah, použijte `<system.webServer>/<staticContent>/<clientCache>` element v souboru.

### <a name="using-applicationhostconfig-files"></a>Pomocí souboru ApplicationHost.config souborů
**ApplicationHost.config** soubor je v kořenovém souboru systému konfigurace služby IIS. Nastavení konfigurace v **ApplicationHost.config** souboru vliv na všechny aplikace na webu, ale jsou přepsat nastavení libovolného **Web.config** soubory, které existují pro webovou aplikaci.

### <a name="using-webconfig-files"></a>Použití souborů Web.config
S **Web.config** soubor, můžete přizpůsobit tak, jak celý webovou aplikaci nebo konkrétní adresář ve webové aplikaci se chová. Obvykle mají alespoň jednu **Web.config** soubor v kořenové složce webové aplikace. Pro každou **Web.config** souborů do konkrétní složky nastavení konfigurace má vliv všechno, co v této složce a jejích podsložkách nepřepíšete, jsou na úrovni podsložky jiným **Web.config** soubor. 

Například můžete nastavit `<clientCache>` prvek **Web.config** soubor v kořenové složce webové aplikace k ukládání do mezipaměti všechny statický obsah ve webové aplikaci po dobu tří dnů. Můžete také přidat **Web.config** soubor v podsložce s více proměnných obsah (například `\frequent`) a nastavte jeho `<clientCache>` – element pro ukládání do mezipaměti obsah podsložce šest hodin. Net výsledkem je obsahu v celé webové stránky se uloží do mezipaměti po dobu tří dnů, s výjimkou obsahu z `\frequent` adresáře, který se uloží do mezipaměti pouze šest hodin.  

Následující příklad konfigurační soubor XML ukazuje, jak nastavit `<clientCache>` element zadat maximální dobu tří dnů:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Použít **cacheControlMaxAge** atribut, musíte nastavit hodnotu **cacheControlMode** atribut `UseMaxAge`. Toto nastavení způsobila hlavičky protokolu HTTP a směrnice, `Cache-Control: max-age=<nnn>`, přidat do odpovědi. Formát hodnoty timespan pro **cacheControlMaxAge** atribut je `<days>.<hours>:<min>:<sec>`. Jeho hodnota je převedena na sekundy a slouží jako hodnotu `Cache-Control` `max-age` směrnice. Další informace o `<clientCache>` prvku, naleznete v tématu [mezipaměti klienta <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Nastavení hlavičky Cache-Control prostřednictvím kódu programu
Pro aplikace ASP.NET, můžete řídit chování ukládání do mezipaměti prostřednictvím kódu programu nastavením CDN **HttpResponse.Cache** vlastnosti rozhraní .NET API. Informace o **HttpResponse.Cache** vlastnost, naleznete v tématu [HttpResponse.Cache vlastnost](https://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) a [HttpCachePolicy třídy](https://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Programově obsah do mezipaměti aplikace v ASP.NET postupujte podle těchto kroků:
   1. Ověřte, že obsah je označen jako možné ukládat do mezipaměti tak, že nastavíte `HttpCacheability` k `Public`. 
   1. Nastavení mezipaměti validátoru voláním jedné z následujících `HttpCachePolicy` metody:
      - Volání `SetLastModified` nastavit hodnotu časového razítka `Last-Modified` záhlaví.
      - Volání `SetETag` nastavit hodnotu `ETag` záhlaví.
   1. Volitelně můžete určit čas vypršení platnosti mezipaměti voláním `SetExpires` nastavit hodnotu `Expires` záhlaví. Jinak použijte výchozí heuristické metody mezipaměti je popsáno výše v tomto dokumentu.

Například do mezipaměti obsah pro jednu hodinu, přidejte následující kód jazyka C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testování hlavičku Cache-Control
Můžete snadno ověřit nastavení TTL webového obsahu. V prohlížeči [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, který zahrnuje webový obsah `Cache-Control` hlavičky odpovědi. Můžete také použít nástroje jako **wget**, [Postman](https://www.getpostman.com/), nebo [Fiddler](http://www.telerik.com/fiddler) a prověří hlavičky odpovědi.

## <a name="next-steps"></a>Další kroky
* [Přečtěte si podrobnosti o **clientCache** – element](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Přečtěte si jejich dokumentaci **HttpResponse.Cache** vlastnost](https://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Přečtěte si jejich dokumentaci **HttpCachePolicy třídy**](https://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Další informace o ukládání do mezipaměti koncepty](cdn-how-caching-works.md)

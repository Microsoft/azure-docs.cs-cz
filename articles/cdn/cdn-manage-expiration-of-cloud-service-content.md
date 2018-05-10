---
title: Spravovat vypršení platnosti webového obsahu v Azure CDN | Microsoft Docs
description: Zjistěte, jak spravovat platnost obsahu Azure webové aplikace nebo cloudové služby, ASP.NET nebo služby IIS v Azure CDN.
services: cdn
documentationcenter: .NET
author: dksimpson
manager: akucer
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: mazha
ms.openlocfilehash: ec5470587454a35bc7606a3518d61bd3491d653b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Spravovat vypršení platnosti webového obsahu v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

Soubory z veřejně přístupná počátek webových serverů můžete do mezipaměti v Azure Content Delivery Network (CDN), dokud uplynutí jejich time to live (TTL). Hodnota TTL je dáno `Cache-Control` hlavičku HTTP odpovědi ze zdrojového serveru. Tento článek popisuje, jak nastavit `Cache-Control` hlavičky pro funkci webové aplikace Microsoft Azure App Service, Azure Cloud Services, aplikace ASP.NET a weby Internetové informační služby (IIS), které jsou nakonfigurované podobně. Můžete nastavit `Cache-Control` záhlaví buď pomocí konfiguračních souborů nebo prostřednictvím kódu programu. 

Nastavení mezipaměti na portálu Azure můžete také ovládat nastavením [CDN ukládání do mezipaměti pravidla](cdn-caching-rules.md). Pokud vytvoříte jeden nebo více ukládání do mezipaměti pravidla a nastavte své chování ukládání do mezipaměti na **přepsat** nebo **vynechat mezipaměti**, zadaný počátečního nastavení ukládání do mezipaměti popsané v tomto článku jsou ignorovány. Informace o obecné koncepty ukládání do mezipaměti najdete v tématu [průběh ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádné TTL na soubor. V takovém případě Azure CDN automaticky použije výchozí hodnotu TTL sedm dní, pokud jste nastavili ukládání do mezipaměti pravidla na portálu Azure. Toto výchozí nastavení TTL se vztahuje pouze na obecné webové doručení optimalizace. Pro optimalizace na velkých souborů je výchozí hodnota TTL je jeden den a pro streamování optimalizace médií, je výchozí hodnota TTL je jeden rok.
> 
> Další informace o tom, jak funguje Azure CDN pro urychlení přístupu k souborům a dalším prostředkům najdete v tématu [přehled Azure Content Delivery Network](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlavičky Cache-Control pomocí ukládání do mezipaměti pravidla CDN
Upřednostňovanou metodou pro nastavení webového serveru `Cache-Control` záhlaví je používat ukládání do mezipaměti pravidla na portálu Azure. Další informace o ukládání do mezipaměti pravidla CDN najdete v tématu [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Jsou k dispozici pouze pro ukládání do mezipaměti pravidla **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profily. Pro **Azure CDN Premium od společnosti Verizon** profily, je nutné použít [stroj pravidel Azure CDN](cdn-rules-engine.md) v **spravovat** portálu pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti pro CDN**:

1. Na portálu Azure vyberte profil CDN a potom vyberte koncový bod pro webový server.

2. V levém podokně v části nastavení, vyberte **ukládání do mezipaměti pravidla**.

   ![Ukládání do mezipaměti tlačítko pravidla CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   **Ukládání do mezipaměti pravidla** se zobrazí stránka.

   ![Ukládání do mezipaměti stránky CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Nastavení webového serveru hlavičky Cache-Control pomocí globální pravidla pro ukládání do mezipaměti:**

1. V části **globální ukládání do mezipaměti pravidla**, nastavte **chování ukládání řetězců s dotazy** k **ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** k  **Přepsání**.
      
2. Pro **mezipaměti Doba vypršení platnosti**, zadejte 3600 v **sekund** pole nebo 1 v **hodin** pole. 

   ![Příklad pravidla globální ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Toto pravidlo globální ukládání do mezipaměti nastaví dobu trvání mezipaměti jednu hodinu a ovlivňuje všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` nebo `Expires` hlavičky HTTP, které odesílá na zdrojový server zadané v koncovém bodě.   

3. Vyberte **Uložit**.

**Nastavení hlavičky Cache-Control souboru webového serveru pomocí vlastní pravidla pro ukládání do mezipaměti:**

1. V části **vlastní ukládání do mezipaměti pravidla**, vytvořte dvě podmínky shody:

     a. Pro první podmínku shodu nastavit **vyhovují podmínce** k **cesta** a zadejte `/webfolder1/*` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte 4 v **hodin** pole.

     b. Pro druhou podmínku shody, nastavte **vyhovují podmínce** k **cesta** a zadejte `/webfolder1/file1.txt` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte 2 v **hodin** pole.

    ![Příklad vlastní pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Nastaví dobu trvání čtyři hodiny pro všechny soubory v mezipaměti na první vlastní pravidlo pro ukládání do mezipaměti `/webfolder1` složky na původním serveru určeného váš koncový bod. První pravidlo přepíše druhé pravidlo `file1.txt` souboru pouze a nastaví dobu trvání mezipaměti dvou hodin pro ni.

2. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Nastavení hlavičky Cache-Control pomocí konfiguračních souborů
Statický obsah, jako jsou bitové kopie a stylů, můžete řídit četnosti aktualizace úpravou **applicationHost.config** nebo **Web.config** konfigurační soubory pro vaši webovou aplikaci. Chcete-li nastavit `Cache-Control` hlavičky pro obsah, použijte `<system.webServer>/<staticContent>/<clientCache>` element v buď soubor.

### <a name="using-applicationhostconfig-files"></a>Pomocí souboru ApplicationHost.config souborů
**ApplicationHost.config** soubor je v kořenovém souboru systému konfigurace služby IIS. Nastavení konfigurace v **ApplicationHost.config** souboru vliv na všechny aplikace na webu, ale jsou přepsat nastavení všech **Web.config** souborů, které existují pro webovou aplikaci.

### <a name="using-webconfig-files"></a>Pomocí souborů Web.config
S **Web.config** souboru, můžete upravit způsob celé webové aplikace nebo konkrétní adresáře na vaši webovou aplikaci se chová. Obvykle mají alespoň jednu **Web.config** soubor v kořenové složce webové aplikace. Pro každou **Web.config** souboru v konkrétní složce, nastavení konfigurace ovlivnit všechno, co v této složce a jejích podsložkách, pokud je na úrovni podsložky přepsána jiným **Web.config** soubor. 

Například můžete nastavit `<clientCache>` element v **Web.config** soubor v kořenové složce webové aplikace pro ukládání do mezipaměti všechny statický obsah na webové aplikace pro tři dny. Můžete také přidat **Web.config** soubor v podsložce s více proměnné obsah (například `\frequent`) a nastavit jeho `<clientCache>` element pro ukládání do mezipaměti obsah název podsložky pro šest hodin. Net výsledkem je tento obsah na celou webovou stránku se uloží do mezipaměti pro tři dny, s výjimkou žádný obsah `\frequent` adresáři, který se uloží do mezipaměti jenom po dobu šesti hodin.  

Následující příklad souboru XML konfigurace ukazuje, jak nastavit `<clientCache>` elementu, který chcete zadat maximální stáří tří dnů:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Použít **cacheControlMaxAge** atribut, musíte nastavit hodnotu **cacheControlMode** atribut `UseMaxAge`. Toto nastavení způsobila hlavičky protokolu HTTP a direktiva, `Cache-Control: max-age=<nnn>`, který se má přidat do odpovědi. Formát hodnoty časový interval pro **cacheControlMaxAge** atribut je `<days>.<hours>:<min>:<sec>`. Její hodnota je převeden na sekund a slouží jako hodnotu `Cache-Control` `max-age` – direktiva. Další informace o `<clientCache>` elementu, najdete v části [mezipaměti klienta <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Nastavení hlavičky Cache-Control prostřednictvím kódu programu
Pro aplikace ASP.NET, můžete řídit chování ukládání do mezipaměti prostřednictvím kódu programu nastavením CDN **HttpResponse.Cache** vlastnost .NET API. Informace o **HttpResponse.Cache** vlastnost, najdete v části [HttpResponse.Cache vlastnost](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) a [HttpCachePolicy třída](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Prostřednictvím kódu programu obsah do mezipaměti aplikace technologie ASP.NET postupujte takto:
   1. Ověřte, že obsah je označen jako Uložitelný nastavením `HttpCacheability` k `Public`. 
   2. Nastavit validátor mezipaměti voláním jednu z následujících `HttpCachePolicy` metody:
      - Volání `SetLastModified` nastavit hodnotu časového razítka `Last-Modified` záhlaví.
      - Volání `SetETag` nastavit hodnotu `ETag` záhlaví.
   3. Volitelně můžete nastavit dobu platnosti mezipaměti voláním `SetExpires` nastavit hodnotu `Expires` záhlaví. Jinak platí výchozí mezipaměti heuristické metody popsané v tomto dokumentu.

Například do mezipaměti obsah pro jednu hodinu, přidejte následující kód C#:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Testování hlavička Cache-Control
Snadno můžete ověřit nastavení TTL webovému obsahu. V prohlížeči [nástroje pro vývojáře](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, který zahrnuje webový obsah `Cache-Control` hlavičky odpovědi. Můžete také použít nástroj jako **wget**, [Postman](https://www.getpostman.com/), nebo [Fiddler](http://www.telerik.com/fiddler) a prověří hlavičky odpovědi.

## <a name="next-steps"></a>Další kroky
* [Přečtěte si podrobnosti o **clientCache** – element](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Přečtěte si dokumentaci k **HttpResponse.Cache** vlastnost](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
* [Přečtěte si dokumentaci k **HttpCachePolicy – třída**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx)  
* [Další informace o ukládání do mezipaměti koncepty](cdn-how-caching-works.md)

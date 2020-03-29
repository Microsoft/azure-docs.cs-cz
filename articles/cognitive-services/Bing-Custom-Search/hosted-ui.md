---
title: Konfigurace hostovaného nového rozhraní pro vlastní vyhledávání Bingu | Dokumenty společnosti Microsoft
titleSuffix: Azure Cognitive Services
description: Tento článek slouží ke konfiguraci a integraci hostovaného rozhraní pro vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854087"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí pro hostované uživatelské rozhraní

Vlastní vyhledávání Bingu poskytuje hostované uživatelské tlačítko, které můžete snadno integrovat do webových stránek a webových aplikací jako fragment kódu JavaScriptu. Pomocí portálu vlastního vyhledávání Bingu můžete nakonfigurovat možnosti rozložení, barvy a hledání uživatelského rozhraní.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurace vlastního hostovaného rozhraní

Chcete-li nakonfigurovat hostované uživatelské rozhraní pro webové aplikace, postupujte takto. Při provádění změn vám podokno vpravo poskytne náhled vašeho hlavního nastavení. Zobrazené výsledky hledání nejsou skutečné výsledky pro vaši instanci.

1. Přihlaste se k portálu vlastního vyhledávání [Bingu](https://customsearch.ai).  
  
2. Vyberte instanci vlastního vyhledávání Bingu.

3. Klikněte na kartu **Hostované uživatelské rozhraní**.  
  
4. Vyberte rozložení.

    |  |  |
    |---------|---------|
    |Panel hledání a výsledky (výchozí)    | Zobrazí vyhledávací pole s výsledky hledání pod ním.         |
    |Pouze výsledky     | Zobrazí pouze výsledky hledání bez vyhledávacího pole. Při použití tohoto rozložení je nutné`&q=<query string>`zadat vyhledávací dotaz ( ). Přidejte parametr dotazu do adresy URL požadavku v fragmentu JavaScriptu nebo v odkazu koncového bodu HTML.        |
    |Přetekovací     | Poskytuje vyhledávací pole a zobrazuje výsledky hledání v posuvném překrytí.        |
    
5. Vyberte barevný motiv. Barvy můžete přizpůsobit tak, aby odpovídaly aplikaci, klepnutím na **tlačítko Přizpůsobit motiv**. Chcete-li změnit barvu, zadejte hodnotu RGB HEX `#366eb8`barvy (například) nebo klikněte na náhled barvy.

   Změny můžete zobrazit na pravé straně portálu. Kliknutím **na Výchozí nastavení se** změny vrátí k výchozím umítek vybraných motivů.

   > [!NOTE]
   > Při výběru barev zvažte usnadnění přístupu.

6. V části **Další konfigurace**zadejte hodnoty podle potřeby pro vaši aplikaci. Tato nastavení jsou volitelná. Pokud chcete zobrazit efekt jejich použití nebo odebrání, podívejte se na podokno náhledu vpravo. Dostupné možnosti konfigurace jsou:  

7. Zadejte klíč předplatného vyhledávání nebo si ho vyberte z rozevíracího seznamu. Rozevírací seznam je naplněn klíči z předplatných vašeho účtu Azure. Viz [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Pokud jste povolili automatické návrhy, zadejte klíč automatického návrhu předplatného nebo si jej vyberte z rozevíracího seznamu. Rozevírací seznam je naplněn klíči z předplatných vašeho účtu Azure. Vlastní autosuggest vyžaduje konkrétní úroveň předplatného, viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Využití vlastního ui

Chcete-li využívat hostované ui, buď: 

- Zahrnutí skriptu na webovou stránku  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Nebo použijte následující adresu URL ve webovém prohlížeči.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Podle potřeby přidejte do adresy URL následující parametry dotazu. Informace o těchto parametrech naleznete [v tématu Odkaz na vlastní rozhraní API pro vyhledávání.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
  >
  > - q
  > - mkt
  > - Safesearch
  > - setlang

  > [!IMPORTANT]
  > Na stránce nelze zobrazit vaše prohlášení o zásadách ochrany osobních údajů ani jiná oznámení a podmínky. Vhodnost pro vaše použití se může lišit.  

Další informace, včetně id vlastní konfigurace, přejděte na **koncové body** na kartě **Produkční.**

## <a name="configuration-options"></a>Možnosti konfigurace

Chování hostovaného rozhraní můžete nakonfigurovat klepnutím na tlačítko **Další konfigurace**a zadáním hodnot. Tato nastavení jsou volitelná. Pokud chcete zobrazit efekt jejich použití nebo odebrání, podívejte se na podokno náhledu vpravo. 

### <a name="web-search-configurations"></a>Konfigurace vyhledávání na webu

|  |  |
|---------|---------|
|Webové výsledky povoleny    | Určuje, zda je povoleno vyhledávání na webu (v horní části stránky se zobrazí karta Web).        |
|Povolit automatické návrhy     | Určuje, zda je povolen vlastní autosuggest (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) za dodatečné náklady).        |
|Webové výsledky na stránce    | Počet výsledků vyhledávání na webu, které se mají zobrazit najednou (maximální počet je 50 výsledků na stránku).        |
|Titulek obrázku   | Určuje, zda se obrázky zobrazují s výsledky hledání.|


Pokud klepnete na tlačítko **Zobrazit rozšířené konfigurace,** zobrazí se následující konfigurace :


|  | |
|---------|---------|
|Zvýraznění slov     | Určuje, zda jsou výsledky zobrazeny s hledanými výrazy tučně.         |
|Cíl propojení    |  Určuje, zda se webová stránka otevře na nové kartě prohlížeče (prázdné) nebo na stejné kartě prohlížeče (vlastní), když uživatel klepne na výsledek hledání.        |

### <a name="image-search-configurations"></a>Konfigurace vyhledávání obrázků

| | |
|---------|---------|
|Povolené výsledky obrázku     | Určuje, zda je povoleno vyhledávání obrázků (v horní části stránky se zobrazí karta Obrázky).            |
|Výsledky obrazu na stránce     | Počet výsledků hledání obrázků, které se mají zobrazit najednou (maximální počet je 150 výsledků na stránku).          |

Následující konfigurace se zobrazí, pokud klepnete na tlačítko **Zobrazit upřesňující konfigurace**.  
  
| | |
|---------|---------|
| Povolení filtrů     | Přidá filtry, které může uživatel použít k filtrování obrázků, které bing vrací. Uživatel může například filtrovat výsledky pouze pro animované gify.|

### <a name="video-search-configurations"></a>Konfigurace vyhledávání videí

|  | |
|---------|---------|
|Povolené výsledky videa     | Určuje, zda je povoleno vyhledávání videí (v horní části stránky se zobrazí karta Videa).           |
|Výsledky videa na stránce   | Počet výsledků vyhledávání videa, které se mají zobrazit najednou (maximální počet je 150 výsledků na stránku).        |

Následující konfigurace se zobrazí, pokud klepnete na tlačítko **Zobrazit upřesňující konfigurace**.  
  
|  | |
|---------|---------|
|Povolení filtrů    | Přidá filtry, které může uživatel použít k filtrování videí, která bing vrací. Uživatel může například filtrovat výsledky videí s určitým rozlišením nebo videí zjištěných za posledních 24 hodin.          |

### <a name="miscellaneous-configurations"></a>Různé konfigurace


| |  |
|---------|---------|
|Nadpis stránky   | Text zobrazený v oblasti nadpisu stránky s výsledky hledání (ne pro rozložení pro přeteknutí).        |
|Motiv panelu nástrojů    | Určuje barvu pozadí oblasti nadpisu stránky s výsledky hledání. |

Následující konfigurace se zobrazí, pokud klepnete na tlačítko **Zobrazit upřesňující konfigurace**.  

|Sloupec1  |Sloupec2  |
|---------|---------|
|Zástupný symbol textu vyhledávacího pole   | Text zobrazený ve vyhledávacím poli před vstupem.        |
|Adresa url odkazu na název    |Cíl pro odkaz na název.         |
|Adresa URL loga     | Obrázek zobrazený vedle názvu.         |
|Favicon    | Ikona zobrazená v záhlaví prohlížeče.          |

Následující konfigurace platí pouze v případě, že spotřebováváte hostované uI prostřednictvím koncového bodu HTML (neplatí, pokud používáte fragment JavaScriptu).

- Nadpis stránky
- Motiv panelu nástrojů
- Adresa URL odkazu nadpisu
- Adresa URL loga
- Faviicon URL  

## <a name="next-steps"></a>Další kroky

- [Zvýraznění textu pomocí dekoračních značek](../bing-web-search/hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)

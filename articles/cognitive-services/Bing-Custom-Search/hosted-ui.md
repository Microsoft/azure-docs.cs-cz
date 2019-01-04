---
title: Konfigurace prostředí uživatelského rozhraní pro vlastní vyhledávání Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Tento článek slouží ke konfiguraci a integraci prostředí uživatelského rozhraní pro vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: aahi
ms.openlocfilehash: e30c36cbde3bf112b012526e6268dfc4414f64a9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555468"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí uživatelského rozhraní

Vlastní vyhledávání Bingu poskytuje hostované uživatelské rozhraní, které můžete snadno integrovat do webových stránek a webových aplikací jako fragment kódu jazyka JavaScript. Pomocí portálu pro vlastní vyhledávání Bingu, můžete konfigurovat rozložení, barvu a možnosti hledání uživatelského rozhraní.



## <a name="configure-the-custom-hosted-ui"></a>Konfigurace vlastního prostředí uživatelského rozhraní

Ke konfiguraci prostředí uživatelského rozhraní pro vaše webové aplikace, postupujte podle těchto kroků. Při provádění změn, v podokně na pravé straně vám poskytne vaše uživatelské rozhraní ve verzi preview. Zobrazené výsledky nejsou skutečné výsledky pro vaši instanci.

1. Přihlaste se k vlastní vyhledávání Bingu [portál](https://customsearch.ai).  
  
2. Vyberte instanci vlastního vyhledávání Bingu.

3. Klikněte na kartu **Hostované uživatelské rozhraní**.  
  
4. Vyberte rozložení.

    |  |  |
    |---------|---------|
    |Panel hledání a výsledky (výchozí)    | Zobrazí vyhledávací pole s výsledky hledání pod ní.         |
    |Pouze výsledky     | Zobrazí pouze výsledky hledání bez vyhledávací pole. Při použití toto rozložení, je nutné zadat vyhledávací dotaz (`&q=<query string>`). Přidáte parametr dotazu na adresu URL požadavku na fragment kódu jazyka JavaScript nebo koncový bod odkazu HTML.        |
    |POP – selhání     | Poskytuje vyhledávací pole a zobrazí výsledky hledání v posuvné překrytí.        |
    
5. Vyberte barevný motiv. Můžete také přizpůsobit barvy podle vaší aplikace kliknutím **vlastní motiv**. Chcete-li změnit barvu, zadejte hodnotu barvy RGB HEX (například `#366eb8`), nebo klikněte na barvu ve verzi preview.

  Můžete zobrazit náhled změn můžete zobrazit na pravé straně portálu. Kliknutím na **obnovit výchozí** se vrátit změny k výchozí barvy pro vybraný motiv.

  > [!NOTE]
  > Při výběru barvy, zvažte usnadnění přístupu.

6. V části **další konfigurace**, zadejte hodnoty jako vhodná pro vaši aplikaci. Tato nastavení jsou volitelná. Efekt použití nebo odstranění najdete v tématu podokno náhledu na pravé straně. Výběr dostupných možností konfigurace jsou:  

7. Zadejte klíč předplatného vyhledávání nebo si vybrat jednoho z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíčů z předplatného účtu Azure. Zobrazit [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Pokud jste nepovolili automatické návrhy, zadejte klíč předplatného pro automatické návrhy nebo si vybrat jednoho z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíčů z předplatného účtu Azure. Funkci vlastních automatických návrhů vyžaduje úroveň konkrétní předplatné, najdete v článku [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Využívat vlastního uživatelského rozhraní

Hostované uživatelského rozhraní, využívat buď: 

- Zahrnout skriptu webové stránky  
  
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

- Nebo můžete použít následující adresu URL ve webovém prohlížeči.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Přidejte následující parametry dotazu k adrese URL podle potřeby. Informace o těchto parametrech naleznete v tématu [rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) odkaz.
  >
  > - q
  > - mkt
  > - bezpečné hledání
  > - setlang

  > [!IMPORTANT]
  > Na stránce nelze zobrazit zásady ochrany osobních údajů nebo jiné oznámení a podmínky. Vhodnosti pro používání se můžou lišit.  

Další informace, včetně vaší vlastní ID konfigurace, přejděte na **koncové body** pod **produkční** kartu.

## <a name="configuration-options"></a>Možnosti konfigurace

Můžete provádět konfiguraci chování hostované uživatelském rozhraní kliknutím **další konfigurace**a poskytování hodnoty. Tato nastavení jsou volitelná. Efekt použití nebo odstranění najdete v tématu podokno náhledu na pravé straně. 

### <a name="web-search-configurations"></a>Konfigurace webového vyhledávání

|  |  |
|---------|---------|
|Výsledky z webu povolen    | Určuje, zda je povoleno pro vyhledávání na webu (zobrazí se karta Web v horní části stránky)        |
|Povolit pro automatické návrhy     | Určuje Pokud vlastních automatických návrhů je povoleno (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) jakýchkoli dalších nákladů).        |
|Webových výsledků na stránku    | Počet výsledků hledání webových zobrazíte najednou (maximální délka je 50 výsledků na stránku).        |
|Popisek obrázku   | Určuje, zda Image se zobrazí s výsledky hledání.|


Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**:


|  | |
|---------|---------|
|Zvýrazňovat slova     | Určuje, zda se zobrazí výsledky s hledané termíny tučným písmem.         |
|Cíl odkazu    |  Určuje, pokud webová stránka se otevře na nové kartě prohlížeče (prázdné) nebo na stejné kartě prohlížeče (vlastní) po kliknutí na výsledek hledání.        |

### <a name="image-search-configurations"></a>Konfigurace hledání obrázků

| | |
|---------|---------|
|Výsledky hledání obrázků, které jsou povolené     | Určuje, zda je povoleno pro vyhledávání obrázků (zobrazí se kartu Image v horní části stránky).            |
|Obrázek výsledků na stránku     | Počet výsledků hledání obrázek zobrazíte v čase (maximální počet je 150 výsledků na stránku).          |

Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
| | |
|---------|---------|
| Povolení filtrů     | Přidá filtry, které uživatel může použít k filtrování imagí, které Bing vrátí. Uživatel například můžete filtrovat výsledky pro pouze animované Gify.|

### <a name="video-search-configurations"></a>Konfigurace pro vyhledávání videí

|  | |
|---------|---------|
|Povolené výsledky videa.     | Určuje, zda je povoleno pro vyhledávání videí (zobrazí se karta videa v horní části stránky).           |
|Video výsledků na stránku   | Počet výsledků hledání videí k zobrazení v čase (maximální počet je 150 výsledků na stránku).        |

Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
|  | |
|---------|---------|
|Povolení filtrů    | Přidá filtry, které uživatel může použít k filtrování videa, která vrátí Bingu. Uživatele můžete například filtrovat výsledky pro videa pomocí konkrétního řešení nebo videa zjištěné za posledních 24 hodin.          |

### <a name="miscellaneous-configurations"></a>Různé konfigurace


| |  |
|---------|---------|
|Název stránky   | Text zobrazený v nadpisu stránka výsledků hledání (ne pro rozložení přes pop).        |
|Panel nástrojů motiv    | Určuje barvu pozadí oblasti názvu stránku výsledků hledání. |

Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  

|Sloupec1  |Column2  |
|---------|---------|
|Zástupný text pole hledání   | Text zobrazený ve vyhledávání předchozí pole k zadání.        |
|Adresa url odkazu na nadpis    |Cíl pro název odkazu.         |
|Adresa URL loga     | Obrázek zobrazený vedle názvu.         |
|Favicon    | Ikona zobrazovaná ve výsledcích záhlaví okna prohlížeče.          |

Následující konfigurace platí jenom v případě, že využívání rozhraní hostovaná prostřednictvím koncového bodu ve formátu HTML (nemohou být použity používáte javascriptový fragment kódu).

- Název stránky
- Panel nástrojů motiv
- Adresa URL odkazu na nadpis
- Adresa URL loga
- Adresa URL Faviicon  

## <a name="next-steps"></a>Další postup

- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)

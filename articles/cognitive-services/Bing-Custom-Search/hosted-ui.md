---
title: 'Vlastní vyhledávání Bingu: Hledání na webu | Dokumentace Microsoftu'
description: Popisuje, jak nakonfigurovat prostředí uživatelského rozhraní
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7f2b97479ffcdb7ec8b3a1a635562d1fe68c3269
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158403"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí uživatelského rozhraní
Po dokončení konfigurace vaší instance vlastního hledání, můžete volat rozhraní API pro vlastní vyhledávání k získání výsledků hledání a zobrazí je ve vaší aplikaci. Nebo, pokud je vaše aplikace webová aplikace, můžete použít hostované uživatelského rozhraní, která poskytuje vlastní vyhledávání.   

## <a name="configure-custom-hosted-ui"></a>Konfigurace vlastního prostředí uživatelského rozhraní
Použijte následující pokyny ke konfiguraci prostředí uživatelského rozhraní pro zahrnutí do vaší webové aplikace.
1.  Přihlaste se k vlastní vyhledávání [portál](https://customsearch.ai).
2.  Klikněte na instanci vlastního vyhledávání. Vytvoření instance najdete v tématu [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).
3.  Klikněte na tlačítko **hostované uživatelského rozhraní** kartu.
4.  Vyberte rozložení.
    - Panel hledání a výsledky (výchozí) &mdash; zobrazit vyhledávací pole a výsledky hledání
    - Výsledky pouze &mdash; nechcete zobrazit vyhledávací pole, zobrazit pouze výsledky
    - POP – selhání &mdash; nechcete zobrazit vyhledávací pole, zobrazit výsledky pouze v posuvné překrytí
    
   > [!IMPORTANT]
   > Nezapomeňte zahrnout parametr dotazu customConfig při výběru **pouze výsledky** rozložení, naleznete v tématu [parametrů dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  V části **další konfigurace**, zadejte hodnoty jako vhodná pro vaši aplikaci. Tato nastavení jsou volitelná. Efekt použití nebo odstranění najdete v tématu podokno náhledu na pravé straně.  Výběr dostupných možností konfigurace jsou:
    - Konfigurace webového vyhledávání:
        - Webové výsledky s povolenou &mdash; Určuje, pokud jsou vráceny výsledky hledání na webu
        - Povolit pro automatické návrhy &mdash; určuje zda vlastních automatických návrhů je povolen
        - Počet výsledků na stránku webové &mdash; počet výsledky hledání na webu pro zobrazení v čase
        - Popisek image &mdash; Určuje, zda se zobrazí obrázky s výsledky hledání
        - Zvýrazňovat slova &mdash; Určuje, zda se zobrazí výsledky s hledané termíny tučným písmem
    - Obrázek konfigurace hledání:
        - Obrázek výsledky s povolenou &mdash; Určuje, pokud jsou vráceny výsledky hledání obrázků
    - Různé konfigurace:
        - Název stránky &mdash; Text zobrazený v nadpisu stránky
        - Panel nástrojů motiv &mdash; Určuje barvu pozadí oblasti nadpis stránky
        - Hledat zástupný text box &mdash; Text zobrazený ve vyhledávání předchozí pole k zadání
        - Adresa url odkazu na nadpis &mdash; cílový název odkazu
        - Adresa url loga &mdash; obrázek zobrazený vedle názvu 
        - Adresa url Favicon &mdash; ikona zobrazovaná ve výsledcích záhlaví okna prohlížeče

   > [!IMPORTANT]
   > Musí být povolená aspoň jedna pro vyhledávání obrázků nebo vyhledávání na webu.

6.  Zadejte klíč předplatného vyhledávání, nebo si vybrat jednoho z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíče z vašeho účtu předplatného Azure. Zobrazit [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Pokud jste nepovolili automatické návrhy, zadejte klíč předplatného pro automatické návrhy nebo zvolte jednu z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíče z vašeho účtu předplatného Azure. Funkci vlastních automatických návrhů vyžaduje úroveň předplatného specifiec, najdete v článku [stránkách s cenami](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Při provádění změn pro vlastní prostředí konfigurace uživatelského rozhraní, poskytuje na podokno na pravé straně visual odkaz pro změny. Zobrazené výsledky nejsou skutečné výsledky pro vaši instanci

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Využívat vlastního uživatelského rozhraní
Hostované uživatelského rozhraní, využívat buď: 

- Zahrnout skriptu webové stránky
    ``` html
    <html>
        <body>
            <script type="text/javascript"
                id="bcs_js_snippet"            
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">            
            </script>
        </body>    
    </html>
    ```

- Použijte adresu URL k dispozici `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Na stránce nelze zobrazit zásady ochrany osobních údajů nebo jiné oznámení a podmínky. Vhodnosti pro používání se můžou lišit.  

Další informace, včetně vaší vlastní ID konfigurace, přejděte na **koncové body** pod **produkční** kartu.

## <a name="next-steps"></a>Další postup
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
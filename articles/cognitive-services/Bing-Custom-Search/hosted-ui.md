---
title: 'Bing vlastní vyhledávání: Hledání lokality | Microsoft Docs'
description: Popisuje postup konfigurace hostované uživatelského rozhraní
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 593ea4d23f8ddcec8efc4be632afa2aab1a5210f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342863"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí hostované uživatelského rozhraní
Po dokončení konfigurace vaše vlastní hledání instance, můžete volat rozhraní API služby vlastní Search a získat výsledky hledání je zobrazit v aplikaci. Nebo, pokud je vaše aplikace webové aplikace, můžete použít hostované uživatelské rozhraní, které poskytuje vlastní vyhledávání.   

## <a name="configure-custom-hosted-ui"></a>Konfigurace vlastní hostované uživatelského rozhraní
Použijte následující pokyny ke konfiguraci hostované uživatelského rozhraní pro zahrnutí do vaší webové aplikace.
1.  Přihlaste se k hledání vlastní [portál](https://customsearch.ai).
2.  Klikněte na tlačítko Vlastní hledání instance. K vytvoření instance, najdete v části [vytvoření vaší první instance Bing vlastní vyhledávání](quick-start.md).
3.  Klikněte **hostované uživatelského rozhraní** kartě.
4.  Vyberte rozložení.
    - Panel hledání a výsledky (výchozí) &mdash; zobrazit vyhledávací pole a výsledky hledání
    - Výsledky pouze &mdash; nemáte zobrazit vyhledávací pole, zobrazit pouze výsledky
    - POP přes &mdash; nemáte zobrazit vyhledávací pole, zobrazit pouze výsledky v posuvné překrytí
    
   > [!IMPORTANT]
   > Nezapomeňte zahrnout parametr dotazu customConfig při výběru **výsledky pouze** rozložení, najdete v části [parametrů dotazu](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters).

5.  V části **další konfigurace**, zadejte hodnoty jako vhodné pro vaši aplikaci. Tato nastavení jsou volitelné. Účinek použití nebo je odstranit, najdete v podokně náhledu na pravé straně.  Výběr dostupných možností konfigurace jsou:
    - Webové hledání konfigurace:
        - Webové výsledky povoleno &mdash; Určuje, pokud jsou vráceny výsledky hledání web
        - Povolit pro automatické návrhy &mdash; určuje Pokud vlastní pro automatické návrhy je povoleno
        - Webové výsledky na stránce &mdash; počet výsledků hledání webové zobrazíte v čase
        - Popisek bitové kopie &mdash; Určuje, zda se zobrazí bitové kopie s výsledky hledání
        - Zvýrazňovat slova &mdash; Určuje, zda se zobrazí výsledky s hledaným výrazům tučným písmem
    - Obrázek hledání konfigurace:
        - Obrázek výsledky povoleno &mdash; Určuje, pokud jsou vráceny výsledky hledání bitové kopie
    - Různé konfigurace:
        - Název stránky &mdash; Text zobrazovaný v oblasti název stránky
        - Panel nástrojů motiv &mdash; Určuje barvu pozadí oblasti název stránky
        - Hledání zástupný text pole &mdash; Text zobrazovaný v před pole hledání jako vstup
        - Adresa url odkazu název &mdash; cíl pro název odkazu
        - Adresa url loga &mdash; bitové kopie, zobrazí vedle názvu 
        - Adresa url Favicon &mdash; ikona zobrazí v záhlaví prohlížeče

   > [!IMPORTANT]
   > Musí být povolena alespoň jeden z bitové kopie hledání nebo vyhledávání na webu.

6.  Zadejte klíč předplatného hledání nebo vyberte jeden z rozevíracího seznamu. Z rozevírací nabídky se zobrazí v klíče z vašeho účtu předplatných Azure. V tématu [kognitivní rozhraní API služby účet](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
7.  Pokud jste povolili automatických návrhů, zadejte klíč předplatného automatických návrhů nebo vyberte jeden z rozevíracího seznamu. Z rozevírací nabídky se zobrazí v klíče z vašeho účtu předplatných Azure. Vlastní automatických návrhů vyžaduje předplatné vrstvu specifiec naleznete [ceny stránky](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Při provádění změn do vlastní konfigurace hostované uživatelského rozhraní, poskytuje podokně na pravé straně referenční dokumentace jazyka visual změny provedené v. Výsledky zobrazené vyhledávání nejsou skutečné výsledky pro vaše instance

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Využívat vlastní uživatelské rozhraní
Rozhraní hostované využívat buď: 

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

- Pomocí adresy URL poskytnuté `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`

  > [!IMPORTANT]
  > Stránku nelze zobrazit zásady ochrany osobních údajů nebo jiných oznámení a podmínky. Vhodnosti pro použití se může lišit.  

Další informace, včetně ID vaše vlastní konfigurace, přejděte na **koncové body** pod **produkční** kartě.

## <a name="next-steps"></a>Další postup
- [Použití decoration značky zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
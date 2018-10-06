---
title: Prohledávání webového serveru, použijte prostředí vlastní vyhledávání Bingu uživatelského rozhraní
titlesuffix: Azure Cognitive Services
description: Popisuje, jak nakonfigurovat vlastní vyhledávání Bingu hostované uživatelského rozhraní.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 77845cb60dac707326acdb08b0198f8725a36f62
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48813970"
---
# <a name="configure-your-hosted-ui-experience"></a>Konfigurace prostředí uživatelského rozhraní

Po dokončení konfigurace vaší instance vlastního hledání, můžete volat rozhraní API pro vlastní vyhledávání k získání výsledků hledání a zobrazí je ve vaší aplikaci. Nebo, pokud je vaše aplikace webová aplikace, můžete použít hostované uživatelského rozhraní, která poskytuje vlastní vyhledávání.   

## <a name="configure-custom-hosted-ui"></a>Konfigurace vlastního prostředí uživatelského rozhraní

Ke konfiguraci prostředí uživatelského rozhraní pro vaši webovou aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k vlastní vyhledávání [portál](https://customsearch.ai).  
  
2. Klikněte na instanci vlastního vyhledávání. Vytvoření instance najdete v tématu [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).  

3. Klikněte na tlačítko **hostované uživatelského rozhraní** kartu.  
  
4. Vyberte rozložení.
  
  - Panel hledání a výsledky (výchozí) &mdash; toto rozložení je stránce tradiční hledání pomocí vyhledávacího pole a výsledky hledání.
  - Výsledky pouze &mdash; toto rozložení zobrazuje jenom výsledky hledání. Toto rozložení nezobrazí vyhledávací pole. Je nutné zadat vyhledávací dotaz tak, že přidáte parametr dotazu (& q =\<řetězec dotazu >) na adrese URL požadavku na fragment kódu jazyka JavaScript nebo koncový bod odkazu HTML.
  - POP – selhání &mdash; toto rozložení poskytuje vyhledávací pole a zobrazí výsledky hledání v posuvné překrytí.
      
5. Vyberte barvu motivu. Je to možné motivy jsou: 
  
  - Classic
  - Tmavý
  - Snímky měst modrá

  Klikněte na jednotlivé motivy, které chcete zobrazit, jaký motiv funguje nejlépe s vaší webovou aplikací. Pokud je potřeba optimalizovat barevný motiv k lepší integraci s vaší webovou aplikací, klikněte na tlačítko **vlastní motiv**. Ne všechny barva vaše konfigurace vztahovat na všechny motivy rozložení. Chcete-li změnit barvu, zadejte do příslušného textového pole barvy RGB HEXADECIMÁLNÍ hodnotu (například #366eb8). Nebo, klikněte na tlačítko barvy a potom klikněte na odstín, který vám vyhovuje. 
  
  Po změně barvy, podívejte se jak tato změna má vliv na pravé straně příkladu ve verzi preview. Vždy můžete kliknout na **obnovit výchozí** se můžete vrátit k výchozí barvy pro vybraný motiv.

  > [!NOTE]
  > Pokud změníte barevný motiv, vezměte v úvahu přístupnost při výběru barvy.

5. V části **další konfigurace**, zadejte hodnoty jako vhodná pro vaši aplikaci. Tato nastavení jsou volitelná. Efekt použití nebo odstranění najdete v tématu podokno náhledu na pravé straně. Výběr dostupných možností konfigurace jsou:  
  
  - Konfigurace webového vyhledávání:
    - Webové výsledky s povolenou &mdash; Určuje, zda je povoleno pro vyhledávání na webu (zobrazí se karta Web v horní části stránky).
    - Povolit pro automatické návrhy &mdash; určuje zda vlastních automatických návrhů je povoleno (viz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) jakýchkoli dalších nákladů).
    - Počet výsledků na stránku webové &mdash; číslo zobrazíte v čase (maximální délka je 50 výsledků na stránku) výsledky hledání na webu.
    - Popisek image &mdash; Určuje, zda se zobrazí obrázky s výsledky hledání.
  
    Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
    - Zvýrazňovat slova &mdash; Určuje, zda se zobrazí výsledky s hledané termíny tučným písmem. 
    - Cíl odkazu &mdash; určuje Pokud webová stránka se otevře na nové kartě prohlížeče (prázdné) nebo na stejné kartě prohlížeče (vlastní) po kliknutí na výsledek hledání. 

  - Obrázek konfigurace hledání:
    - Obrázek výsledky s povolenou &mdash; Určuje, zda je povoleno pro vyhledávání obrázků (zobrazí se kartu Image v horní části stránky).   
    - Obrázek výsledků na stránku &mdash; počet výsledků hledání obrázek zobrazíte v čase (maximální počet je 150 výsledků na stránku).  
  
    Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
    - Povolit filtry &mdash; Přidá filtry, které uživatel může použít k filtrování imagí, které Bing vrátí. Uživatel například můžete filtrovat výsledky pro pouze animované Gify.

  - Konfigurace pro vyhledávání videí:
    - Video výsledky s povolenou &mdash; Určuje, zda je povoleno pro vyhledávání videí (zobrazí se karta videa v horní části stránky).  
    - Video výsledků na stránku &mdash; počet výsledků hledání videí k zobrazení v čase (maximální počet je 150 výsledků na stránku).
  
    Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
    - Povolit filtry &mdash; Přidá filtry, které uživatel může použít k filtrování videa, která vrátí Bingu. Uživatele můžete například filtrovat výsledky pro videa pomocí konkrétního řešení nebo videa zjištěné za posledních 24 hodin.

  - Různé konfigurace:
    - Název stránky &mdash; Text zobrazený v nadpisu stránka výsledků hledání (ne pro rozložení přes pop).
    - Panel nástrojů motiv &mdash; Určuje barvu pozadí oblasti názvu stránku výsledků hledání.  
  
    Následující konfigurace se zobrazí, pokud kliknete **Zobrazit pokročilou konfiguraci**.  
  
    - Hledat zástupný text box &mdash; Text zobrazený ve vyhledávání předchozí pole k zadání.
    - Adresa url odkazu na nadpis &mdash; cílový název odkazu.
    - Adresa url loga &mdash; obrázek zobrazený vedle názvu. 
    - Adresa url Favicon &mdash; ikona zobrazovaná ve výsledcích záhlaví okna prohlížeče.  

    Následující konfigurace platí jenom v případě, že využívání rozhraní hostovaná prostřednictvím koncového bodu ve formátu HTML (nemohou být použity používáte javascriptový fragment kódu).
    
    - Název stránky
    - Panel nástrojů motiv
    - Adresa URL odkazu na nadpis
    - Adresa URL loga
    - Adresa URL Faviicon  
  
6. Zadejte klíč předplatného vyhledávání nebo si vybrat jednoho z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíčů z předplatného účtu Azure. Zobrazit [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

7. Pokud jste nepovolili automatické návrhy, zadejte klíč předplatného pro automatické návrhy nebo si vybrat jednoho z rozevíracího seznamu. V rozevíracím seznamu se vyplní klíčů z předplatného účtu Azure. Funkci vlastních automatických návrhů vyžaduje úroveň konkrétní předplatné, najdete v článku [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

> [!NOTE]
> Při provádění změn pro vlastní prostředí konfigurace uživatelského rozhraní, poskytuje na podokno na pravé straně visual odkaz pro změny. Zobrazené výsledky nejsou skutečné výsledky pro vaši instanci.

[!INCLUDE[publish or revert](./includes/publish-revert.md)]

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

## <a name="next-steps"></a>Další postup

- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
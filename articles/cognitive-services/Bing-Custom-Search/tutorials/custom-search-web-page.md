---
title: 'Kurz: Vytvoření webové stránky pro vlastní vyhledávání – Vlastní vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Tento kurz popisuje postup konfigurace instance vlastního vyhledávání a její integrace do webové stránky.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: tutorial
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 3e892131a0109d2fff924940542b5d8b2b701950
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815371"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Kurz: Vytvoření webové stránky pro vlastní vyhledávání

Vlastní vyhledávání Bingu umožňuje vytvářet přizpůsobená vyhledávací prostředí pro témata, o která máte zájem. Pokud například vlastníte web věnovaný bojovým uměním, který poskytuje možnosti vyhledávání, můžete určit domény, podřízené weby a webové stránky, které Bing prohledává. Uživatelům se budou zobrazovat výsledky hledání přizpůsobené obsahu, který je zajímá, a nebudou tak muset procházet obecné výsledky hledání, které můžou obsahovat irelevantní obsah. 

Tento kurz ukazuje postup konfigurace instance vlastního vyhledávání a její integrace do nové webové stránky.

Probírají se tyto úlohy:

> [!div class="checklist"]
> - Vytvoření instance vlastního vyhledávání
> - Přidání aktivních položek
> - Přidání blokovaných položek
> - Přidání připnutých položek
> - Integrace vlastního vyhledávání do webové stránky

## <a name="prerequisites"></a>Požadavky

- Abyste mohli postupovat podle tohoto kurzu, potřebujete klíč předplatného pro rozhraní API pro vlastní vyhledávání Bingu.  Postup, jak získat klíč, naleznete v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání

Vytvoření instance vlastního vyhledávání Bingu:

1. Otevřete internetový prohlížeč.  
  
2. Přejděte na [portál](https://customsearch.ai) vlastního vyhledávání.  
  
3. Přihlaste se k portálu pomocí účtu Microsoft. Pokud účet Microsoft nemáte, klikněte na **Vytvořit účet Microsoft**. Pokud portál používáte poprvé, zobrazí se výzva k udělení oprávnění pro přístup k vašim datům. Klikněte na **Ano**.  
  
4. Po přihlášení klikněte na **Nové vlastní vyhledávání**. V okně **Vytvořit novou instanci vlastního vyhledávání** zadejte smysluplný název popisující typ obsahu, který vyhledávání vrací. Název můžete kdykoli změnit.  
  
  ![Snímek obrazovky s oknem Vytvořit novou instanci vlastního vyhledávání](../media/newCustomSrch.png)  
  
5. Klikněte na OK, zadejte adresu URL a určete, jestli se mají zahrnout i podstránky této adresy URL.  
  
  ![Snímek obrazovky se stránkou pro definici adresy URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Přidání aktivních položek

Pokud chcete zahrnout výsledky z konkrétních webů nebo adres URL, přidejte je na kartě **Aktivní**.

1.  Na stránce **Konfigurace** klikněte na kartu **Aktivní** a zadejte adresy URL webů, které chcete do vyhledávání zahrnout.

    ![Snímek obrazovky editoru definice s kartou Aktivní](../media/customSrchEditor.png)

2.  Pokud chcete ověřit, že vaše instance vrací výsledky, zadejte dotaz do podokna náhledu na pravé straně. Bing vrátí výsledky pouze pro veřejné weby, které indexoval.

## <a name="add-blocked-entries"></a>Přidání blokovaných položek

Pokud chcete vyloučit výsledky z konkrétních webů nebo adres URL, přidejte je na kartě **Blokované**.

1. Na stránce **Konfigurace** klikněte na kartu **Blokované** a zadejte adresy URL webů, které chcete z vyhledávání vyloučit.

    ![Snímek obrazovky editoru definice s kartou Blokované](../media/blockedCustomSrch.png)


2. Pokud chcete ověřit, že vaše instance nevrací výsledky z blokovaných webů, zadejte dotaz do podokna náhledu na pravé straně. 

## <a name="add-pinned-entries"></a>Přidání připnutých položek

Pokud chcete určitou webovou stránku připnout na začátek výsledků hledání, přidejte danou webovou stránku a termín dotazu na kartě **Připnuté**. Karta **Připnuté** obsahuje seznam párů webových stránek a termínů dotazu určujících webové stránky, které se zobrazí jako nejlepší výsledek pro konkrétní dotaz. Webová stránka se připne pouze v případě, že řetězec dotazu uživatele odpovídá řetězci dotazu připnuté webové stránky na základě podmínky shody připnuté webové stránky. [Další informace](../define-your-custom-view.md#pin-to-top).

1. Na stránce **Konfigurace** klikněte na kartu **Připnuté** a zadejte webovou stránku, kterou chcete vrátit jako nejlepší výsledek, a její termín dotazu.  
  
2. Ve výchozím nastavení musí řetězec dotazu uživatele přesně odpovídat řetězci dotazu připnuté webové stránky, aby Bing tuto webovou stránku vrátil jako nejlepší výsledek. Pokud chcete podmínku shody změnit, upravte připnutou webovou stránku (kliknutím na ikonu tužky), ve sloupci **Podmínka shody dotazu** klikněte na Přesná a vyberte podmínku shody vhodnou pro vaši aplikaci.  
  
    ![Snímek obrazovky editoru definice s kartou Připnuté](../media/pinnedCustomSrch.png)
  
3. Pokud chcete ověřit, že vaše instance jako nejlepší výsledek vrací zadanou webovou stránku, zadejte připnutý termín dotazu do podokna náhledu na pravé straně.

## <a name="configure-hosted-ui"></a>Konfigurace hostovaného uživatelského rozhraní

Vlastní vyhledávání poskytuje hostované uživatelské rozhraní pro vykreslení odpovědi JSON vaší instance vlastního vyhledávání. Definice uživatelského rozhraní:

1. Klikněte na kartu **Hostované uživatelské rozhraní**.  
  
2. Vyberte rozložení.  
  
  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a krokem Výběr rozložení](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Vyberte barevný motiv.  
  
  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a výběrem barevného motivu](./media/custom-search-hosted-ui-select-color-theme.png)  

  Pokud potřebujete barevný motiv doladit, aby se lépe integroval s vaší webovou aplikací, klikněte na **Přizpůsobit motiv**. Ne všechny konfigurace barev je možné použít ve všech motivech rozložení. Pokud chcete změnit barvu, zadejte do odpovídajícího textového pole šestnáctkovou hodnotu RGB barvy (například #366eb8). Případně můžete kliknout na tlačítko barvy a pak kliknout na odstín, který vám vyhovuje. Při výběru barev mějte vždy na paměti přístupnost.
  
  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a přizpůsobením barevného motivu](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Zadejte další možnosti konfigurace.  
  
  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a krokem Další konfigurace](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Pokud chcete zobrazit pokročilou konfiguraci, klikněte na **Zobrazit pokročilou konfiguraci**. Tím se přidají například konfigurace *Cíl odkazu* mezi možnosti Vyhledávání na webu, *Povolit filtry* mezi možnosti Vyhledávání obrázků a videí a *Zástupný text ve vyhledávacím poli* mezi možnosti Různé.

  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a krokem Pokročilá konfigurace](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. V rozevíracím seznamu vyberte klíče svého předplatného. Případně můžete klíč předplatného zadat ručně. Informace o získání klíčů najdete v tématu [Zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![Snímek obrazovky s kartou Hostované uživatelské rozhraní a krokem Další konfigurace](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Používání hostovaného uživatelského rozhraní

Hostované uživatelské rozhraní můžete používat dvěma způsoby.  

- Možnost 1: Integrace poskytnutého fragmentu kódu JavaScriptu do aplikace
- Možnost 2: Použití poskytnutého koncového bodu HTML

Zbývající část tohoto kurzu ilustruje **Možnost 1: Fragment kódu JavaScriptu**.  

## <a name="set-up-your-visual-studio-solution"></a>Nastavení řešení v sadě Visual Studio

1. Otevřete na svém počítači sadu **Visual Studio**.  
  
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.  
  
3. V okně **Nový projekt** vyberte **Visual C# > Web > Webová aplikace ASP.NET Core**, zadejte název projektu a pak klikněte na **OK**.  
  
  ![Snímek obrazovky s oknem Nový projekt](./media/custom-search-new-project.png)  
  
4. V okně **Nová webová aplikace ASP.NET Core** vyberte **Webová aplikace** a klikněte na **OK**.  
  
  ![Snímek obrazovky s oknem Nový projekt](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Úprava souboru index.cshtml

1. V **Průzkumníku řešení** rozbalte **Stránky** a dvojím kliknutím na soubor **index.cshtml** ho otevřete.  
  
  ![Snímek obrazovky Průzkumníka řešení s rozbalenou položkou Stránky a vybraným souborem index.cshtml](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. V souboru index.cshtml odstraňte vše od řádku 7 dál.  
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Přidejte element zalomení řádku a element div, který bude fungovat jako kontejner.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. Na stránce **Hostované uživatelské rozhraní** se posuňte dolů do části **Používání uživatelského rozhraní**. Kliknutím na *Koncové body* přejděte k fragmentu kódu JavaScriptu. K fragmentu kódu můžete přejít také kliknutím na **Produkce** a pak na kartu **Hostované uživatelské rozhraní**.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
5. Vložte element script do kontejneru, který jste přidali.  
  
  ``` html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. V **Průzkumníku řešení** klikněte pravým tlačítkem na **wwwroot** a pak na **Zobrazit v prohlížeči**.  
  
  ![Snímek obrazovky Průzkumníka řešení s výběrem možnosti Zobrazit v prohlížeči v místní nabídce wwwroot](./media/custom-search-webapp-view-in-browser.png)  

Vaše nová webová stránka pro vlastní vyhledávání by měla vypadat nějak takto:

![Snímek obrazovky webové stránky pro vlastní vyhledávání](./media/custom-search-webapp-browse-index.png)

Při vyhledávání se následujícím způsobem budou vykreslovat výsledky:

![Snímek obrazovky s výsledky vlastního vyhledávání](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Volání koncového bodu vlastního vyhledávání Bingu (C#)](../call-endpoint-csharp.md)
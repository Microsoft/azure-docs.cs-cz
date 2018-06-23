---
title: 'Hledání vlastní Bing: Vytvoření vlastní vyhledávání webové stránky | Microsoft Docs'
description: Popisuje, jak nakonfigurovat vlastní hledání instance a integrovat do webové stránky
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: c1431ec852cab943e00d3933ef4f0500a4fdb151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342869"
---
# <a name="build-a-custom-search-web-page"></a>Vytvoření vlastní vyhledávání webové stránky
Bing vlastní vyhledávání můžete vytvořit prostředí šité na míru vyhledávání pro témata, která se zajímáte o. Například pokud jste vlastníkem nástrojů bojových sportů web, který poskytuje možnosti vyhledávání, můžete domény, podřízené weby a webové stránky, která hledá Bing. Uživatelům se zobrazí výsledky hledání přizpůsobit obsah, který zajímají místo stránkování prostřednictvím výsledky obecné hledání, které mohou obsahovat důležité obsah. 

Tento kurz ukazuje, jak nakonfigurovat vlastní hledání instance a integrovat do nové webové stránky.

Úlohy popsané jsou:

> [!div class="checklist"]
> - Vytvoření instance vlastní vyhledávání
> - Přidat aktivní položky
> - Přidání blokovaných položek
> - Přidání definovaného položek
> - Integrovat vlastní vyhledávání do webové stránky

## <a name="prerequisites"></a>Požadavky
- Chcete-li postupovat podle kurzu, je třeba klíč předplatného pro rozhraní API služby Bing vlastní Search.  Získat klíč, najdete v části [zkuste kognitivní služby](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastní vyhledávání
K vytvoření instance služby Bing vlastní vyhledávání:

1.  Otevřete internetový prohlížeč.
2.  Přejděte do vlastního vyhledávání [portál](https://customsearch.ai).
3.  Přihlaste se k portálu pomocí účtu Microsoft (MSA). Pokud nemáte účtu spravované služby, klikněte na tlačítko **vytvořit účet Microsoft**. Pokud je poprvé pomocí portálu, bude požádat o oprávnění k přístupu k datům. Klikněte na **Ano**.
4.  Po přihlášení, klikněte na tlačítko **nové vlastní hledání**. V **vytvořit novou instanci vlastní vyhledávání** okno, zadejte název, který má smysl a popisuje typ obsahu, které vrátí vyhledávání. Kdykoli můžete změnit název.
 
    ![Snímek obrazovky znázorňující vytvořit nové vlastní vyhledávací pole instance](../media/newCustomSrch.png)

5.  Klikněte na tlačítko OK, zadejte adresu URL a jestli se mají zahrnout podstránky základní stránky:

    ![Snímek obrazovky stránky definice adresy URL](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Přidat aktivní položky
Zahrnout výsledky z konkrétní lokality nebo adresy URL, přidejte je do **Active** kartě.

1.  V **definice Editor**, klikněte na tlačítko **Active** kartě a zadejte adresu URL jednu nebo více lokalit, které chcete zahrnout do hledání.

    ![Snímek obrazovky s kartou active definice editoru](../media/customSrchEditor.png)

2.  Potvrďte, že vaše instance vrátí výsledky, zadejte dotaz v podokně náhledu na pravé straně. Bing vrátí výsledky pouze pro veřejné weby, které má indexovat.

## <a name="add-blocked-entries"></a>Přidání blokovaných položek
Pokud chcete výsledky vyloučit z konkrétní lokality nebo adresy URL, přidejte je do **blokováno** kartě.

1. V **definice Editor**, klikněte na tlačítko **blokováno** kartě a zadejte adresu URL jednu nebo více lokalit, které chcete vyloučit z hledání.

    ![Snímek obrazovky Editor definice blokované karta](../media/blockedCustomSrch.png)


2. Potvrďte, že vaše instance není vrácení výsledků z blokovaných webů, zadejte dotaz v podokně náhledu na pravé straně. 

## <a name="add-pinned-entries"></a>Přidání definovaného položek
Připnutí konkrétní webové stránky do horní části výsledků vyhledávání, přidejte na webovou stránku a dotaz termín **Pinned** kartě.  **Pinned** karta obsahuje seznam párů termín webové stránky a dotazů, které zadat webovou stránku, který se zobrazí v horní důsledku pro konkrétní dotaz. Termín dotazu uživatele musí přesně shodovat termín definovaného dotazu pro webovou stránku být připojena k nejvyšší.

1. V **definice Editor**, klikněte na tlačítko **Pinned** kartě a zadejte webovou stránku a dotaz termín webové stránky, která by měla vrátit jako hlavní výsledek.

    ![Snímek obrazovky Editor definice připnutý karta](../media/pinnedCustomSrch.png)

2. Chcete-li potvrdit, že vaše instance jako hlavní výsledek vrátí Zadaná webová stránka, zadejte termín dotazu, které jste připnuli v podokně náhledu na pravé straně.

## <a name="configure-hosted-ui"></a>Konfigurace hostované uživatelského rozhraní
Vlastní vyhledávání poskytuje hostované uživatelského rozhraní k vykreslení odpovědi JSON vaší vlastní hledání instance.  Chcete-li definovat uživatelské rozhraní:

1. Klikněte **hostované uživatelského rozhraní** kartě.
2. Vyberte rozložení.

    ![Snímek obrazovky uživatelského rozhraní hostované vyberte krok rozložení](./media/custom-search-hosted-ui-select-layout.png)

3. Vyberte barvu motivu.

    ![Snímek obrazovky uživatelského rozhraní hostované vyberte krok rozložení](./media/custom-search-hosted-ui-select-color-theme.png)

4. Zadejte další možnosti konfigurace.

    ![Snímek obrazovky kroku další konfigurace hostované uživatelského rozhraní](./media/custom-search-hosted-ui-additional-configurations.png)

5. Vložení vaše **klíč předplatného**. V tématu [vyzkoušení služeb kognitivní](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Snímek obrazovky kroku další konfigurace hostované uživatelského rozhraní](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Využívání hostované uživatelského rozhraní
Existují dva způsoby, jak využívat hostované uživatelského rozhraní.  

- Možnost 1: Integrované zadaný fragment kódu JavaScript do vaší aplikace.
- Možnost 2: Pomocí HTML koncový bod, pokud jsou.

Zbývající část tohoto kurzu znázorňuje **možnost 1: fragment kódu Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Nastavit řešení sady Visual Studio
1. Otevřete na svém počítači sadu **Visual Studio**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V **nový projekt** vyberte **Visual C# / Web / webové aplikace ASP.NET Core**, pojmenujte svůj projekt a pak klikněte na tlačítko **OK**.
   
    ![Snímek obrazovky okna Nový projekt](./media/custom-search-new-project.png)

4. V **nové webové aplikace ASP.NET Core** vyberte **webové aplikace** a klikněte na tlačítko **OK**.
    
    ![Snímek obrazovky okna Nový projekt](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Upravit index.cshtml
1. V **Průzkumníku řešení**, rozbalte položku **stránky** a dvakrát klikněte na **index.cshtml** k otevření souboru.

    ![Snímek obrazovky Průzkumníka řešení se stránkami rozšířit a index.cshtml vybrané](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. V index.cshtml odstraňte všechny položky od z řádku 7 a pod ním.
    
    ``` razor
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }    
    ```

3. Přidáte element zalomení řádku a div tak, aby fungoval jako kontejner.

    ``` html
    @page
    @model IndexModel
    @{
        ViewData["Title"] = "Home page";
    }
    <br />
    <div id="customSearch"></div>
    ```

4. Z **hostované uživatelského rozhraní** kartě, posuňte se dolů části s názvem **využívání rozhraní**. Zkopírujte fragment kódu jazyka JavaScript.

    ![Snímek obrazovky uživatelského rozhraní hostované tlačítko Uložit.](./media/custom-search-hosted-ui-consuming-ui.png)

5. Script element umožňuje vložte do kontejneru, který jste přidali.

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
                src="https://ui.customsearch.ai/api/ux/render?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate">
        </script>
    </div>
    ```

6. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **wwwroot** a klikněte na tlačítko **zobrazit v prohlížeči**.

    ![Snímek obrazovky Průzkumníka řešení, výběr zobrazení v místní nabídce wwwroot v prohlížeči](./media/custom-search-webapp-view-in-browser.png)

Nové webové stránky vlastní hledání by měl vypadat podobně jako tento:

![Snímek obrazovky vlastní vyhledávání webové stránky](./media/custom-search-webapp-browse-index.png)

Prohledávání vykreslí výsledky takto.

![Snímek obrazovky s výsledky hledání vlastní](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> - Vytvořit vlastní hledání instance
> - Přidání aktivní položky
> - Přidat blokovaných položek
> - Přidané definovaného položky
> - Integrované hledání vlastní do webové stránky

Nyní můžete přejít k volání hledání Bing vlastní koncové body prostřednictvím kódu programu.

> [!div class="nextstepaction"]
> [Koncový bod služby Bing vlastní vyhledávání volání (C#)](../call-endpoint-csharp.md)
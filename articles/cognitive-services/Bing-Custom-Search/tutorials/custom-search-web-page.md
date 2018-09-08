---
title: 'Vlastní vyhledávání Bingu: Vytvoření vlastního vyhledávání webové stránky | Dokumentace Microsoftu'
description: Popisuje, jak nakonfigurovat instanci vlastního vyhledávání a integrují je do webové stránky
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/16/2017
ms.author: v-brapel
ms.openlocfilehash: 1f9b689ac6127bc2f7d1e810356ae9a23b8e0996
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44162389"
---
# <a name="build-a-custom-search-web-page"></a>Vytvoření webové stránky pro vlastní vyhledávání
Vlastní vyhledávání Bingu umožňuje vytvořit míru vyhledávací prostředí pro témata, které vás zajímají. Například pokud vlastníte bojová umění web, který poskytuje možnosti vyhledávání, určíte domén, podřízené weby a webové stránky, které vyhledává Bing. Uživatelům se zobrazí výsledky hledání přizpůsobená pro obsah, na kterých záleží místo procházení obecné výsledky, které mohou obsahovat irelevantní obsah. 

Tento kurz ukazuje, jak nakonfigurovat instanci vlastního vyhledávání a integrují je do nové webové stránky.

Úlohy popsané jsou:

> [!div class="checklist"]
> - Vytvoření instance vlastního vyhledávání
> - Přidat aktivní položky
> - Přidat blokovaných položek
> - Přidání připojených položek
> - Integrace vlastního vyhledávání do webové stránky

## <a name="prerequisites"></a>Požadavky
- Pokud chcete postupovat v kurzu, budete potřebovat klíč předplatného pro rozhraní API pro vlastní vyhledávání Bingu.  Chcete-li získat klíče, naleznete v tématu [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).

## <a name="create-a-custom-search-instance"></a>Vytvoření instance vlastního vyhledávání
K vytvoření instance vlastního vyhledávání Bingu:

1.  Otevřete internetový prohlížeč.
2.  Přejděte do vlastního vyhledávání [portál](https://customsearch.ai).
3.  Přihlaste se k portálu pomocí účtu Microsoft (MSA). Pokud nemáte MSA, klikněte na tlačítko **vytvořit účet Microsoft**. Pokud je vaše první přihlášení pomocí portálu, požádá o zadání oprávnění pro přístup k datům. Klikněte na **Ano**.
4.  Po přihlášení klikněte na tlačítko **nové vlastní vyhledávání**. V **vytvořit novou instanci vlastního vyhledávání** okno, zadejte název, který má smysl a popisuje typ obsahu, které vrátí vyhledávání. Kdykoli můžete změnit název.
 
    ![Snímek obrazovky okna vytvořit nové pole instance vlastního vyhledávání](../media/newCustomSrch.png)

5.  Klikněte na tlačítko OK, zadejte adresu URL a jestli se mají zahrnout podstránky základní stránky:

    ![Snímek obrazovky stránky adresu URL definice](../media/newCustomSrch1-a.png)

## <a name="add-active-entries"></a>Přidat aktivní položky
Chcete-li zahrnout výsledky z konkrétní lokality nebo adresy URL, přidejte je do **aktivní** kartu.

1.  V **Editor definic**, klikněte na tlačítko **aktivní** kartě a zadejte adresu URL z jedné nebo víc lokalit, které chcete zahrnout při hledání.

    ![Snímek obrazovky okna na aktivní kartu Editor definic](../media/customSrchEditor.png)

2.  Pokud chcete potvrdit, že vaše instance vrátí výsledky, zadejte dotaz v podokně náhledu na pravé straně. Bing vrátí výsledky pouze pro veřejné weby, které se má indexovat.

## <a name="add-blocked-entries"></a>Přidat blokovaných položek
Pokud chcete výsledky vyloučit z konkrétní lokality nebo adresy URL, přidejte je do **blokováno** kartu.

1. V **Editor definic**, klikněte na tlačítko **blokováno** kartě a zadejte adresu URL z jedné nebo víc lokalit, které chcete vyloučit z hledání.

    ![Snímek obrazovky Editor definic blokované kartu](../media/blockedCustomSrch.png)


2. Pokud chcete potvrdit, že vaše instance nevrací výsledky z blokovaných webů, zadejte dotaz v podokně náhledu na pravé straně. 

## <a name="add-pinned-entries"></a>Přidání připojených položek
Pro Připnutí konkrétní webové stránky do horní části výsledků vyhledávání přidat webovou stránku a dotaz termín, který **Pinned** kartu.  **Pinned** karta obsahuje seznam webové stránky a dotaz páry termín zadejte webovou stránku, která se zobrazí jako nejlepší výsledek pro konkrétní dotaz. Výraz dotazu uživatele musí přesně odpovídat termín připnuté dotazu pro připnout do horní části webové stránky.

1. V **Editor definic**, klikněte na tlačítko **Pinned** kartě a zadejte výraz webové stránky a dotaz z webové stránky, která by měla vrátit jako nejlepší výsledek.

    ![Snímek obrazovky Editor definic připnuté karty](../media/pinnedCustomSrch.png)

2. Potvrďte, že vaše instance jako nejlepší výsledek vrátí Zadaná webová stránka, zadejte výraz dotazu, které jste připnuli v podokně náhledu na pravé straně.

## <a name="configure-hosted-ui"></a>Konfigurace prostředí uživatelského rozhraní
Vlastní vyhledávání poskytuje hostované uživatelské rozhraní k vykreslení odpovědi JSON vaší instance vlastního hledání.  Definování uživatelského rozhraní:

1. Klikněte na tlačítko **hostované uživatelského rozhraní** kartu.
2. Vyberte rozložení.

    ![Snímek obrazovky uživatelského rozhraní hostované vyberte krok rozložení](./media/custom-search-hosted-ui-select-layout.png)

3. Vyberte barvu motivu.

    ![Snímek obrazovky uživatelského rozhraní hostované vyberte krok rozložení](./media/custom-search-hosted-ui-select-color-theme.png)

4. Zadejte další možnosti konfigurace.

    ![Snímek obrazovky s dalším konfiguracím krok hostované uživatelského rozhraní](./media/custom-search-hosted-ui-additional-configurations.png)

5. Vložit vaše **klíč předplatného**. Zobrazit [vyzkoušet služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).

    ![Snímek obrazovky s dalším konfiguracím krok hostované uživatelského rozhraní](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Použití prostředí uživatelského rozhraní
Existují dva způsoby, jak používat prostředí uživatelského rozhraní.  

- Možnost 1: Integrace zadaný fragment kódu jazyka JavaScript do vaší aplikace.
- Možnost 2: Použití koncového bodu ve formátu HTML za předpokladu.

Zbývající část tohoto kurzu ukazuje **možnost 1: fragment kódu jazyka Javascript**.  

## <a name="set-up-your-visual-studio-solution"></a>Nastavení řešení v sadě Visual Studio
1. Otevřete na svém počítači sadu **Visual Studio**.
2. V nabídce **Soubor** vyberte **Nový** a zvolte **Projekt**.
3. V **nový projekt** okně **Visual C# / Web / webové aplikace ASP.NET Core**, pojmenujte svůj projekt a potom klikněte na tlačítko **OK**.
   
    ![Snímek obrazovky okna Nový projekt](./media/custom-search-new-project.png)

4. V **nová webová aplikace ASP.NET Core** okně **webovou aplikaci** a klikněte na tlačítko **OK**.
    
    ![Snímek obrazovky okna Nový projekt](./media/custom-search-new-webapp.png)

## <a name="edit-indexcshtml"></a>Upravit index.cshtml
1. V **Průzkumníka řešení**, rozbalte **stránky** a dvakrát klikněte na panel **index.cshtml** k otevření souboru.

    ![Snímek obrazovky Průzkumníka řešení rozšířit stránky a vybrali index.cshtml](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)

2. V index.cshtml vymazat úplně všechno od z řádku 7 a níže.
    
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

4. Z **hostované uživatelského rozhraní** karty, posuňte dolů k části s názvem **využívání rozhraní**. Zkopírujte fragment kódu jazyka JavaScript.

    ![Snímek obrazovky uživatelského rozhraní hostované tlačítko Uložit](./media/custom-search-hosted-ui-consuming-ui.png)

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

6. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **wwwroot** a klikněte na tlačítko **zobrazit v prohlížeči**.

    ![Snímek obrazovky Průzkumníka řešení vyberete zobrazení v místní nabídce wwwroot v prohlížeči](./media/custom-search-webapp-view-in-browser.png)

Nové webové stránky pro vlastní vyhledávání by měl vypadat nějak takto:

![Snímek obrazovky okna vlastního vyhledávání webové stránky](./media/custom-search-webapp-browse-index.png)

Prohledávání vykreslí výsledky následujícím způsobem.

![Snímek obrazovky s vlastní výsledky hledání](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Další postup
V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> - Vytvoří instanci vlastního vyhledávání
> - Přidání aktivní položky
> - Přidání blokovaných položek
> - Přidání připojených položek
> - Integrované vlastní vyhledávání do webové stránky

Teď můžete přejít k volání koncových bodů vlastního vyhledávání Bingu prostřednictvím kódu programu.

> [!div class="nextstepaction"]
> [Volání koncového bodu pro vlastní vyhledávání Bingu (C#)](../call-endpoint-csharp.md)
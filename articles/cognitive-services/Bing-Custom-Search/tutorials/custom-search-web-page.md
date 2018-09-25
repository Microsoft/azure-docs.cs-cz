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
ms.openlocfilehash: 8bc1520325afc256ac62cc1f1dfaf24c53da4b83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979994"
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

1. Otevřete internetový prohlížeč.  
  
2. Přejděte do vlastního vyhledávání [portál](https://customsearch.ai).  
  
3. Přihlaste se k portálu pomocí účtu Microsoft (MSA). Pokud nemáte MSA, klikněte na tlačítko **vytvořit účet Microsoft**. Pokud je vaše první přihlášení pomocí portálu, požádá o zadání oprávnění pro přístup k datům. Klikněte na **Ano**.  
  
4. Po přihlášení klikněte na tlačítko **nové vlastní vyhledávání**. V **vytvořit novou instanci vlastního vyhledávání** okno, zadejte název, který má smysl a popisuje typ obsahu, které vrátí vyhledávání. Kdykoli můžete změnit název.  
  
  ![Snímek obrazovky s vytvořením nového vlastního vyhledávacího pole instance](../media/newCustomSrch.png)  
  
5. Klikněte na tlačítko OK, zadejte adresu URL a jestli se mají zahrnout podstránky adresy URL.  
  
  ![Stránka definice – snímek obrazovky z adresy URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Přidat aktivní položky

Chcete-li zahrnout výsledky od určitých webů nebo adresy URL, přidejte je do **aktivní** kartu.

1.  Na **konfigurace** stránky, klikněte na tlačítko **aktivní** kartě a zadejte adresu URL jedna nebo více stránek, které chcete zahrnout při hledání.

    ![Snímek obrazovky na aktivní kartu Editor definic](../media/customSrchEditor.png)

2.  Pokud chcete potvrdit, že vaše instance vrátí výsledky, zadejte dotaz v podokně náhledu na pravé straně. Bing vrátí pouze výsledky pro veřejné weby, které se má indexovat.

## <a name="add-blocked-entries"></a>Přidat blokovaných položek

Pokud chcete výsledky vyloučit ze konkrétní weby nebo adresy URL, přidejte je do **blokováno** kartu.

1. Na **konfigurace** stránky, klikněte na tlačítko **blokováno** kartě a zadejte adresu URL jedna nebo více stránek, které chcete vyloučit z hledání.

    ![Snímek obrazovky blokované kartu Editor definic](../media/blockedCustomSrch.png)


2. Pokud chcete potvrdit, že vaše instance není vrácení výsledků z blokovaných webů, zadejte dotaz v podokně náhledu na pravé straně. 

## <a name="add-pinned-entries"></a>Přidání připojených položek

Pro Připnutí konkrétní webové stránky do horní části výsledků vyhledávání přidat webovou stránku a dotaz termín, který **Pinned** kartu. **Pinned** karta obsahuje seznam webové stránky a dotaz páry termín zadejte webovou stránku, která se zobrazí jako nejlepší výsledek pro konkrétní dotaz. Webová stránka je připnutá pouze v případě, že řetězec dotazu odpovídající řetězec dotazu PIN kódu na základě podmínky shody PIN kód. [Přečtěte si další informace](../define-your-custom-view.md#pin-to-top).

1. Na **konfigurace** stránky, klikněte na tlačítko **Pinned** kartě a zadejte výraz webové stránky a dotaz webové stránky, které se mají vracet jako nejlepší výsledek.  
  
2. Ve výchozím nastavení řetězce dotazu musí přesně odpovídat řetězec dotazu svůj PIN kód pro Bing se vraťte na webovou stránku jako nejlepší výsledek. Chcete-li změnit podmínky shody, upravte kód pin (klikněte na ikonu tužky), klikněte na tlačítko Exact v **podmínce shody dotazu** sloupci a vyberte podmínce shody, které je nejvhodnější pro vaši aplikaci.  
  
    ![Snímek obrazovky Editor definic připnuté karty](../media/pinnedCustomSrch.png)
  
3. Potvrďte, že vaše instance jako nejlepší výsledek vrátí Zadaná webová stránka, zadejte výraz dotazu, které jste připnuli v podokně náhledu na pravé straně.

## <a name="configure-hosted-ui"></a>Konfigurace prostředí uživatelského rozhraní

Vlastní vyhledávání poskytuje hostované uživatelské rozhraní k vykreslení odpovědi JSON vaší instance vlastního hledání. Definování uživatelského rozhraní:

1. Klikněte na tlačítko **hostované uživatelského rozhraní** kartu.  
  
2. Vyberte rozložení.  
  
  ![Snímek obrazovky uživatelského rozhraní hostované vyberte krok rozložení](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Vyberte barvu motivu.  
  
  ![Snímek obrazovky uživatelského rozhraní hostované vyberte barevný motiv](./media/custom-search-hosted-ui-select-color-theme.png)  

  Pokud je potřeba optimalizovat barevný motiv k lepší integraci s vaší webovou aplikací, klikněte na tlačítko **vlastní motiv**. Ne všechny barva vaše konfigurace vztahovat na všechny motivy rozložení. Chcete-li změnit barvu, zadejte do příslušného textového pole barvy RGB HEXADECIMÁLNÍ hodnotu (například #366eb8). Nebo, klikněte na tlačítko barvy a potom klikněte na odstín, který vám vyhovuje. Vždy uvažovat o usnadnění přístupu, při výběru barvy.
  
  ![Snímek obrazovky uživatelského rozhraní hostované přizpůsobit barevný motiv](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Zadejte další možnosti konfigurace.  
  
  ![Snímek obrazovky s dalším konfiguracím krok hostované uživatelského rozhraní](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Chcete-li získat pokročilou konfiguraci, klikněte na tlačítko **Zobrazit pokročilou konfiguraci**. Tento postup přidá konfigurace, jako *cíl odkazu* na webové vyhledávání možnosti *Povolit filtry* možností Image a videa, a *zástupný text pole hledání* na různé Možnosti.

  ![Snímek obrazovky rozšířených konfigurací kroku hostované uživatelského rozhraní](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Vyberte klíče předplatného z rozevíracích seznamů. Nebo můžete ručně zadat klíč předplatného. Informace o získávání klíčů najdete v tématu [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![Snímek obrazovky s dalším konfiguracím krok hostované uživatelského rozhraní](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE[publish or revert](../includes/publish-revert.md)]

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
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Přidáte element zalomení řádku a div tak, aby fungoval jako kontejner.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. V **hostované uživatelského rozhraní** stránky, přejděte dolů k části s názvem **využívání rozhraní**. Klikněte na tlačítko *koncové body* pro přístup k fragment kódu jazyka JavaScript. Můžete také získáte fragment kódu kliknutím **produkční** a pak **hostované uživatelského rozhraní** kartu.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
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
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **wwwroot** a klikněte na tlačítko **zobrazit v prohlížeči**.  
  
  ![Snímek obrazovky Průzkumníka řešení vyberete zobrazení v místní nabídce wwwroot v prohlížeči](./media/custom-search-webapp-view-in-browser.png)  

Nové webové stránky pro vlastní vyhledávání by měl vypadat nějak takto:

![Snímek obrazovky webové stránky, vlastní vyhledávání](./media/custom-search-webapp-browse-index.png)

Prohledávání vykreslí výsledky takto:

![Snímek obrazovky s vlastní výsledky hledání](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Volání koncového bodu pro vlastní vyhledávání Bingu (C#)](../call-endpoint-csharp.md)
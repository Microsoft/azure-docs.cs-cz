---
title: 'Kurz JavaScriptu: nasazení webu s povoleným vyhledáváním'
titleSuffix: Azure Cognitive Search
description: Nasaďte web s povoleným hledáním do statické webové aplikace Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726844"
---
# <a name="3---deploy-the-search-enabled-website"></a>3. nasazení webu s povoleným vyhledáváním

Nasaďte web s povoleným vyhledáváním jako statickou webovou aplikaci Azure. Toto nasazení zahrnuje aplikaci s funkcí reakce i aplikace Function App.  

Statická webová aplikace přebírá informace a soubory pro nasazení z GitHubu pomocí vašeho rozvětvení úložiště ukázek.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Vytvoření statické webové aplikace v Visual Studio Code

1. Na řádku aktivity vyberte **Azure** a pak na bočním panelu vyberte **statické Web Apps** . 
1. Klikněte pravým tlačítkem na název předplatného a pak vyberte **vytvořit statickou webovou aplikaci (rozšířené)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Klikněte pravým tlačítkem na název předplatného a pak vyberte * * vytvořit statickou webovou aplikaci (rozšířené) * *.":::

1. Podle zobrazených výzev zadejte následující informace:

    |Výzva|Enter|
    |--|--|
    |Jak chcete vytvořit statickou webovou aplikaci?|Použít existující úložiště GitHub|
    |Zvolit organizaci|Jako organizaci vyberte svůj _vlastní_ alias GitHubu.|
    |Zvolit úložiště|V seznamu vyberte **Azure-Search-JavaScript-Samples** . |
    |Zvolit větev úložiště|V seznamu vyberte možnost **Hlavní** . |
    |Zadejte název nové statické webové aplikace.|Pro svůj prostředek Vytvořte jedinečný název. Můžete například předřadit své jméno do názvu úložiště, například, `joansmith-azure-search-javascript-samples` . |
    |Vyberte skupinu prostředků pro nové prostředky.|Použijte skupinu prostředků, kterou jste vytvořili pro tento kurz.|
    |Vyberte přednastavení sestavení pro konfiguraci výchozí struktury projektu.|Vybrat **vlastní**|
    |Vyberte umístění kódu aplikace.|`search-website`|
    |Vyberte umístění kódu funkce Azure.|`search-website/api`|
    |Zadejte cestu výstupu sestavení...|sestavení|
    |Vyberte umístění pro nové prostředky.|Vyberte oblast blízko od vás.|

1. Prostředek se vytvoří, z oznámení vyberte **otevřít akce z GitHubu** . Otevře se okno prohlížeče, které ukazuje na vaše rozvětvené úložiště. 

    Seznam akcí znamená, že vaše webová aplikace, klient i funkce byly úspěšně vloženy do vaší statické webové aplikace Azure. 

    Než budete pokračovat, počkejte na dokončení sestavení a nasazení. Dokončení tohoto může trvat minutu nebo dvě.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Získat Kognitivní hledání klíč dotazu v Visual Studio Code

1. V Visual Studio Code otevřete [řádek aktivity](https://code.visualstudio.com/docs/getstarted/userinterface)a vyberte ikonu Azure. 

1. Na bočním panelu vyberte své předplatné Azure v oblasti **Azure: kognitivní hledání** , potom klikněte pravým tlačítkem na prostředek hledání a vyberte **Kopírovat klíč dotazu**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Na bočním panelu vyberte své předplatné Azure v oblasti * * Azure: Kognitivní hledání * *, potom klikněte pravým tlačítkem na prostředek hledání a vyberte * * Kopírovat klíč dotazu * *.":::

1. Tento klíč dotazu uložte, budete ho muset použít v další části. Klíč dotazu může dotazovat váš index. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Přidat nastavení konfigurace v Visual Studio Code

Aplikace funkce Azure nebude vracet data hledání, dokud nejsou v nastavení vyhledávacích tajných kódů. 

1. Na řádku aktivity vyberte **Azure** a pak na bočním panelu vyberte **statické Web Apps** . 
1. Rozšiřte novou statickou webovou aplikaci, dokud se nezobrazí **nastavení aplikace** .
1. Klikněte pravým tlačítkem na **nastavení aplikace** a pak vyberte **Přidat nové nastavení**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Klikněte pravým tlačítkem na * * nastavení aplikace * * a pak vyberte * * Přidat nové nastavení * *.":::

1. Přidejte následující nastavení:

    |Nastavení|Hodnota prostředku hledání|
    |--|--|
    |SearchApiKey|Váš klíč vyhledávacího dotazu|
    |SearchServiceName|Název hledaného prostředku|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Použití vyhledávání ve vaší statické webové aplikaci

1. V Visual Studio Code otevřete [řádek aktivity](https://code.visualstudio.com/docs/getstarted/userinterface)a vyberte ikonu Azure.
1. Na bočním panelu **klikněte pravým tlačítkem na své předplatné Azure** v `Static web apps` oblasti a vyhledejte statickou webovou aplikaci, kterou jste vytvořili pro tento kurz.
1. Klikněte pravým tlačítkem myši na název statické webové aplikace a vyberte **Procházet lokalita**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Klikněte pravým tlačítkem myši na název statické webové aplikace a vyberte * * procházet web * *.":::    

1. V místním dialogovém okně vyberte **otevřít** .
1. Na panelu hledání webu zadejte vyhledávací dotaz `code` , například, _pomaleji_ , aby funkce navrhnout navrhla názvy knih. Vyberte návrh nebo pokračujte v zadávání vlastního dotazu. Po dokončení vyhledávacího dotazu stiskněte klávesu ENTER. 
1. Zkontrolujte výsledky a potom vyberte jednu z knih pro zobrazení dalších podrobností. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, odstraňte skupinu prostředků.

1. V Visual Studio Code otevřete [řádek aktivity](https://code.visualstudio.com/docs/getstarted/userinterface)a vyberte ikonu Azure. 

1. Na bočním panelu **klikněte pravým tlačítkem na své předplatné Azure** v `Resource Groups` oblasti a vyhledejte skupinu prostředků, kterou jste pro tento kurz vytvořili.
1. Klikněte pravým tlačítkem na název skupiny prostředků a vyberte **Odstranit**.
    Tím se odstraní prostředky hledání i statické webové aplikace.
1. Pokud už nechcete, aby se rozvětvení GitHubu v ukázce vyřadí, nezapomeňte ji odstranit na GitHubu. Přejít na **Nastavení** rozvětvení a pak větev odstranit. 


## <a name="next-steps"></a>Další kroky

* [Vysvětlení integrace vyhledávání na webu s povoleným vyhledáváním](tutorial-javascript-search-query-integration.md)

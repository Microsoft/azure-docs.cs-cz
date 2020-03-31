---
title: Vytvoření vyhledávací aplikace na Webu Azure Portal
titleSuffix: Azure Cognitive Search
description: Spusťte průvodce vytvořením aplikace (preview) a vygenerujte stránky HTML a skript pro provozní webovou aplikaci. Stránka obsahuje panel hledání, oblast výsledků, postranní panel a podporu dopředného psaní.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369708"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Úvodní příručka: Vytvoření vyhledávací aplikace na portálu (Azure Cognitive Search)

Pomocí průvodce **vytvořením vyhledávací aplikace** portálu vygenerujte webovou aplikaci ve stylu localhost, která se spouští v prohlížeči ke stažení. V závislosti na konfiguraci je vygenerovaná aplikace funkční při prvním použití s živým připojením ke vzdálenému indexu. Výchozí aplikace může obsahovat panel hledání, oblast výsledků, filtry postranního panelu a podporu dopředného psaní.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete. 

## <a name="prerequisites"></a>Požadavky

Upgradujte na [nejnovější verzi aplikace Microsoft Edge](https://www.microsoft.com/edge) nebo pro tento rychlý start použijte prohlížeč Google Chrome.

[Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý start můžete využít bezplatnou službu. 

[Vytvořte index,](search-create-index-portal.md) který chcete použít jako základ aplikace. 

Tento rychlý start používá předdefinovaná ukázková data a index nemovitostí, protože obsahuje miniatury (průvodce podporuje přidávání obrázků na stránku s výsledky). Chcete-li vytvořit index použitý v tomto cvičení, spusťte Průvodce **importem dat** a zvolte zdroj dat *realestate-us-sample.*

![stránka zdroje dat pro ukázková data](media/search-create-app-portal/import-data-realestate.png)

Když je index připraven k použití, přejděte k dalšímu kroku.

## <a name="start-the-wizard"></a>Spuštění průvodce

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a [vyhledejte svou vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na stránce Přehled vyberte z odkazů uprostřed stránky **položku Indexy**. 

1. Ze seznamu existujících indexů zvolte *realestate-us-sample-index.*

1. Na stránce rejstříku vhorní části vyberte **Vytvořit vyhledávací aplikaci (náhled)** a spusťte průvodce.

1. Na první stránce průvodce vyberte **Povolit sdílení prostředků mezi zdroji (CORS),** chcete-li přidat podporu CORS do definice indexu. Tento krok je volitelný, ale místní webová aplikace se bez něj ke vzdálenému indexu nepřipojí.

## <a name="configure-search-results"></a>Konfigurace výsledků hledání

Průvodce poskytuje základní rozložení pro vykreslené výsledky hledání, které obsahuje místo pro miniaturu, název a popis. Zálohování každého z těchto prvků je pole v indexu, který poskytuje data. 

1. V části Miniatura zvolte pole *miniatur* v indexu *realestate-us-sample.* Tato ukázka obsahuje miniatury obrázků ve formě obrázků url uložených v poli nazvaném *miniatura*. Pokud index obrázky neobsahuje, ponechte toto pole prázdné.

1. V poli, které vyjadřuje jedinečnost každého dokumentu, zvolte pole, které vyjadřuje jedinečnost jednotlivých dokumentů. V této ukázce je ID výpisu přiměřeným výběrem.

1. V poli Description zvolte pole, které obsahuje podrobnosti, které může někomu pomoci rozhodnout se, zda na daný dokument klikne.

![stránka zdroje dat pro ukázková data](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Přidání postranního panelu

Vyhledávací služba podporuje fazetovou navigaci, která je často vykreslena jako postranní panel. Omezující vlastnosti jsou založeny na filtrovatelných a omezujících vlastnostech, jak je vyjádřeno ve schématu indexu.

V Azure Cognitive Search famitní navigace je kumulativní filtrování prostředí. V rámci kategorie výběr více filtrů rozbalí výsledky (například výběrseattle a Bellevue v rámci města). Výběr více filtrů napříč kategoriemi výsledky zužuje.

> [!TIP]
> Můžete zobrazit úplné schéma indexu na portálu. Vyhledejte odkaz **definice indexu (JSON)** na stránce přehledu každého indexu. Pole, která mají nárok na fazetovou navigaci, mají atributy "filterable: true" a "facetable: true".

Přijměte aktuální výběr omezujících stránek a pokračujte na další stránku.


## <a name="add-typeahead"></a>Přidání typu dopřed

Funkce dopředného dopředného dotazu je k dispozici ve formě automatických dokončování a návrhů dotazů. Průvodce podporuje návrhy dotazů. Na základě vstupů stisknutí klávesy poskytnutých uživatelem vrátí vyhledávací služba seznam "dokončených" řetězců dotazu, které lze vybrat jako vstup.

Návrhy jsou povoleny pro konkrétní definice polí. Průvodce nabízí možnosti konfigurace, kolik informací je zahrnuto v návrhu. 

Následující snímek obrazovky ukazuje možnosti v průvodci, vedle sebe s vykreslenou stránkou v aplikaci. Můžete vidět, jak se používají výběry polí a jak se v návrhu používá "Zobrazit název pole" k zahrnutí nebo vyloučení označení.

![Konfigurace návrhů dotazů](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Vytvořit, stáhnout a spustit

1. Vyberte **Vytvořit vyhledávací aplikaci,** chcete-li vygenerovat soubor HTML.

1. Po zobrazení výzvy vyberte **Stáhnout aplikaci** a stáhněte soubor.

1. Otevřete tento soubor. Měli byste vidět stránku podobnou následujícímu snímku obrazovky. Zadejte termín a pomocí filtrů zúžíte výsledky. 

Základní index se skládá z fiktivních generovaných dat, která byla duplikována napříč dokumenty, a popisy někdy neodpovídají obrázku. Při vytváření aplikace založené na vlastních indexech můžete očekávat soudržnější prostředí.

![Spuštění aplikace](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem. 

## <a name="next-steps"></a>Další kroky

Zatímco výchozí aplikace je užitečná pro počáteční průzkum a malé úkoly, kontrola api v rané fázi vám pomůže pochopit koncepty a pracovní postupy na hlubší úrovni:

> [!div class="nextstepaction"]
> [Vytvoření indexu pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
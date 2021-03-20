---
title: Vytvoření ukázkové aplikace v Azure Portal
titleSuffix: Azure Cognitive Search
description: Spusťte Průvodce vytvořením ukázkové aplikace (Preview) a vygenerujte HTML stránky a skript pro provozní webovou aplikaci. Stránka obsahuje panel hledání, oblast výsledků, postranní panel a podporu typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745746"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Rychlý Start: vytvoření ukázkové aplikace na portálu (Azure Kognitivní hledání)

Pomocí průvodce **vytvořením ukázkové aplikace** Azure Portal vygenerujte webovou aplikaci ve stylu "localhost", která běží v prohlížeči. V závislosti na konfiguraci je vygenerovaná aplikace při prvním použití funkční, s živým připojením jen pro čtení ke vzdálenému indexu. Výchozí aplikace může obsahovat panel hledání, oblast výsledků, filtry bočního panelu a podporu typeahead.

Ukázková aplikace vám pomůže vizualizovat, jak bude index fungovat v klientské aplikaci, ale není určený pro produkční scénáře. Klientské aplikace by měly zahrnovat zabezpečení, zpracování chyb a logiku hostování, kterou neposkytuje vygenerovaná stránka HTML. Až budete připraveni vytvořit klientskou aplikaci, přečtěte si téma [Vytvoření první aplikace pro vyhledávání pomocí sady .NET SDK](tutorial-csharp-create-first-app.md) pro další kroky.

## <a name="prerequisites"></a>Předpoklady

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

+ Služba Azure Kognitivní hledání. [Vytvořte službu](search-create-service-portal.md) nebo [vyhledejte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

+ [Microsoft Edge (nejnovější verze)](https://www.microsoft.com/edge) nebo Google Chrome.

+ [Index hledání](search-what-is-an-index.md) , který se má použít jako základ vaší vygenerované aplikace 

  V tomto rychlém startu se používá integrovaná ukázková data a index reálného majetku, protože obsahuje miniatury (Průvodce podporuje přidávání imagí na stránku výsledků). Pokud chcete vytvořit index použitý v tomto cvičení, spusťte průvodce **importem dat** a vyberte zdroj dat *realestate-US-Sample* .

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="Stránka zdroje dat pro ukázková data" border="false":::

Až bude index připravený k použití, přejděte k dalšímu kroku.

## <a name="start-the-wizard"></a>Spustit Průvodce

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

1. [Vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a na stránce Přehled v odkazech uprostřed stránky vyberte **indexy**. 

1. V seznamu existujících indexů vyberte *realestate-US-Sample-index* .

1. V horní části stránky index vyberte **vytvořit ukázkovou aplikaci (Preview)** a spusťte tak průvodce.

1. Na první stránce průvodce vyberte **Povolit sdílení prostředků mezi zdroji (CORS)** a přidejte do definice indexu podporu CORS. Tento krok je nepovinný, ale vaše místní webová aplikace se nebude bez něj připojovat ke vzdálenému indexu.

## <a name="configure-search-results"></a>Konfigurace výsledků hledání

Průvodce poskytuje základní rozložení pro vykreslené výsledky hledání, které obsahují místo pro miniaturu obrázku, název a popis. Zálohování každého z těchto prvků je pole v indexu, které poskytuje data. 

1. V části Miniatura vyberte pole *Miniatura* v indexu *realestate-US-Sample* . Tato ukázka se stane zahrnutím miniatur obrázků do formátu obrázků adres URL uložených v poli s názvem *Miniatura*. Pokud váš index neobsahuje obrázky, ponechte toto pole prázdné.

1. V části název vyberte pole, které vyjadřuje jedinečnost každého dokumentu. V této ukázce je ID výpisu rozumného výběru.

1. V poli Popis zvolte pole, které poskytuje podrobné informace, které mohou někomu pomáhat při rozhodování, zda kliknout do konkrétního dokumentu.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="Konfigurace výsledků pro ukázková data" border="false":::

## <a name="add-a-sidebar"></a>Přidat postranní panel

Vyhledávací služba podporuje omezující navigaci, která se často vykresluje jako postranní panel. Omezující vlastnosti jsou založené na filtrovacích a tváře polích, jak je vyjádřeno ve schématu indexu.

V Azure Kognitivní hledání je nahodnocená navigace kumulativním prostředím pro filtrování. Výběr více filtrů v rámci kategorie rozšiřuje výsledky (například výběr Praha a Bellevue v rámci města). V různých kategoriích výběr více filtrů zužuje výsledky.

> [!TIP]
> Úplné schéma indexu můžete zobrazit na portálu. Vyhledejte odkaz **definice indexu (JSON)** na stránce Přehled v jednotlivých indexech. Pole, která jsou kvalifikována pro přecházení s omezujícími vlastnostmi, mají "filtrovatelné: true" a "FACET: true" atributy.

1. V průvodci vyberte kartu **boční panel** v horní části stránky. Zobrazí se seznam všech polí, která jsou s atributy filtrovatelné a plošky v indexu.

1. Přijměte aktuální výběr omezujících polí a pokračujte na další stránku.

## <a name="add-typeahead"></a>Přidat typeahead

Funkce typeahead je k dispozici ve formě automatického dokončování a návrhů dotazů. Průvodce podporuje návrhy dotazů. Na základě vstupů z klávesnice, které poskytuje uživatel, služba Search vrátí seznam "dokončených" řetězců dotazů, které je možné vybrat jako vstup.

Návrhy jsou povolené pro konkrétní definice polí. Průvodce vám nabídne možnosti konfigurace, kolik informací je součástí návrhu. 

Následující snímek obrazovky ukazuje možnosti v průvodci, juxtaposed s vykreslenou stránkou v aplikaci. Můžete vidět, jak se používají výběry polí, a jak se má v návrhu zahrnout nebo vyloučit označení "Zobrazit název pole".

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Konfigurace návrhů dotazů":::

## <a name="add-suggestions"></a>Přidat návrhy

Návrhy odkazují na automatické výzvy dotazů, které jsou připojeny k vyhledávacímu poli. Kognitivní hledání podporuje dvě: *doplňování* částečně zadaného hledaného termínu a *návrhy* na rozevírací seznam potenciálních dokumentů, které jsou na bázi.

Průvodce podporuje návrhy a pole, která mohou poskytnout navrhované výsledky, jsou odvozena z [`Suggesters`](index-add-suggesters.md) konstrukce v indexu:

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. V průvodci vyberte kartu **návrhy** v horní části stránky. Zobrazí se seznam všech polí, která jsou určena ve schématu indexu jako poskytovatelé návrhů.

1. Přijměte aktuální výběr a pokračujte na další stránku.

## <a name="create-download-and-execute"></a>Vytvoření, stažení a spuštění

1. V dolní části stránky vyberte **vytvořit ukázkovou aplikaci** , aby se vygeneroval soubor HTML.

1. Po zobrazení výzvy vyberte **Stáhnout aplikaci** a Stáhněte si soubor.

1. Otevřete soubor a klikněte na tlačítko Hledat. Tato akce spustí dotaz, což může být prázdný dotaz ( `*` ), který vrací libovolnou sadu výsledků dotazu. Stránka by měla vypadat podobně jako na následujícím snímku obrazovky. Zadejte termín a použijte filtry k zúžení výsledků. 

Základní index se skládá z fiktivních generovaných dat, která byla duplikována v rámci dokumentů, a popisy se někdy neshodují s obrázkem. Pokud vytváříte aplikaci na základě vlastních indexů, můžete očekávat ucelenější prostředí.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="Spuštění aplikace":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Ukázková aplikace je užitečná pro vytváření prototypů, protože můžete simulovat činnost koncového uživatele, aniž byste museli psát JavaScript nebo front-end kód. Pokud chcete získat další informace o funkcích front-endu, začněte s omezujícími navigačními prvky:

> [!div class="nextstepaction"]
> [Postup sestavení filtru omezující vlastnosti](search-filters-facets.md)
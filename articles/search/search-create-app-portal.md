---
title: Vytvoření vyhledávací aplikace v Azure Portal
titleSuffix: Azure Cognitive Search
description: Spusťte Průvodce vytvořením aplikace (Preview) a vygenerujte HTML stránky a skript pro provozní webovou aplikaci. Stránka obsahuje panel hledání, oblast výsledků, postranní panel a podporu typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/25/2020
ms.openlocfilehash: 248ef093601eda7a180a6465ccb97e6fc1c9fe41
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80369708"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Rychlý Start: Vytvoření vyhledávací aplikace na portálu (Azure Kognitivní hledání)

Pomocí průvodce **vytvořením vyhledávací aplikace** na portálu vygenerujte webovou aplikaci ve stylu "localhost", která běží v prohlížeči. V závislosti na konfiguraci je vygenerovaná aplikace při prvním použití funkční, s živým připojením ke vzdálenému indexu. Výchozí aplikace může obsahovat panel hledání, oblast výsledků, filtry bočního panelu a podporu typeahead.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Požadavky

Upgradujte na [nejnovější verzi Microsoft Edge](https://www.microsoft.com/edge) nebo pro tento rychlý Start použijte prohlížeč Chrome pro Google.

[Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. Pro tento rychlý Start můžete použít bezplatnou službu. 

[Vytvořte index](search-create-index-portal.md) , který se použije jako základ vaší aplikace. 

V tomto rychlém startu se používá integrovaná ukázková data a index reálného majetku, protože obsahuje miniatury (Průvodce podporuje přidávání imagí na stránku výsledků). Pokud chcete vytvořit index použitý v tomto cvičení, spusťte průvodce **importem dat** a vyberte zdroj dat *realestate-US-Sample* .

![Stránka zdroje dat pro ukázková data](media/search-create-app-portal/import-data-realestate.png)

Až bude index připravený k použití, přejděte k dalšímu kroku.

## <a name="start-the-wizard"></a>Spustit Průvodce

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a [vyhledejte vyhledávací službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na stránce Přehled v odkazech uprostřed stránky vyberte **indexy**. 

1. V seznamu existujících indexů vyberte *realestate-US-Sample-index* .

1. Na stránce index v horní části vyberte **vytvořit vyhledávací aplikaci (Preview)** a spusťte tak průvodce.

1. Na první stránce průvodce vyberte **Povolit sdílení prostředků mezi zdroji (CORS)** a přidejte do definice indexu podporu CORS. Tento krok je nepovinný, ale vaše místní webová aplikace se nebude bez něj připojovat ke vzdálenému indexu.

## <a name="configure-search-results"></a>Konfigurace výsledků hledání

Průvodce poskytuje základní rozložení pro vykreslené výsledky hledání, které obsahují místo pro miniaturu obrázku, název a popis. Zálohování každého z těchto prvků je pole v indexu, které poskytuje data. 

1. V části Miniatura vyberte pole *Miniatura* v indexu *realestate-US-Sample* . Tato ukázka se stane zahrnutím miniatur obrázků do formátu obrázků adres URL uložených v poli s názvem *Miniatura*. Pokud váš index neobsahuje obrázky, ponechte toto pole prázdné.

1. V části název vyberte pole, které vyjadřuje jedinečnost každého dokumentu. V této ukázce je ID výpisu rozumného výběru.

1. V poli Popis zvolte pole, které poskytuje podrobné informace, které mohou někomu pomáhat při rozhodování, zda kliknout do konkrétního dokumentu.

![Stránka zdroje dat pro ukázková data](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Přidat postranní panel

Vyhledávací služba podporuje omezující navigaci, která se často vykresluje jako postranní panel. Omezující vlastnosti jsou založené na filtrovacích a tváře polích, jak je vyjádřeno ve schématu indexu.

V Azure Kognitivní hledání je nahodnocená navigace kumulativním prostředím pro filtrování. Výběr více filtrů v rámci kategorie rozšiřuje výsledky (například výběr Praha a Bellevue v rámci města). V různých kategoriích výběr více filtrů zužuje výsledky.

> [!TIP]
> Úplné schéma indexu můžete zobrazit na portálu. Vyhledejte odkaz **definice indexu (JSON)** na stránce Přehled v jednotlivých indexech. Pole, která jsou kvalifikována pro přecházení s omezujícími vlastnostmi, mají "filtrovatelné: true" a "FACET: true" atributy.

Přijměte aktuální výběr omezujících vlastností a pokračujte na další stránku.


## <a name="add-typeahead"></a>Přidat typeahead

Funkce typeahead je k dispozici ve formě automatického dokončování a návrhů dotazů. Průvodce podporuje návrhy dotazů. Na základě vstupů z klávesnice, které poskytuje uživatel, služba Search vrátí seznam "dokončených" řetězců dotazů, které je možné vybrat jako vstup.

Návrhy jsou povolené pro konkrétní definice polí. Průvodce vám nabídne možnosti konfigurace, kolik informací je součástí návrhu. 

Následující snímek obrazovky ukazuje možnosti v průvodci, juxtaposed s vykreslenou stránkou v aplikaci. Můžete vidět, jak se používají výběry polí, a jak se má v návrhu zahrnout nebo vyloučit označení "Zobrazit název pole".

![Konfigurace návrhů dotazů](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Vytvoření, stažení a spuštění

1. Vyberte **vytvořit vyhledávací aplikaci** pro vygenerování souboru HTML.

1. Po zobrazení výzvy vyberte **Stáhnout aplikaci** a Stáhněte si soubor.

1. Otevřete tento soubor. Měla by se zobrazit stránka podobná následujícímu snímku obrazovky. Zadejte termín a použijte filtry k zúžení výsledků. 

Základní index se skládá z fiktivních generovaných dat, která byla duplikována v rámci dokumentů, a popisy se někdy neshodují s obrázkem. Pokud vytváříte aplikaci na základě vlastních indexů, můžete očekávat ucelenější prostředí.

![Spuštění aplikace](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

I když je výchozí aplikace užitečná při počátečním a malém úkolu, podrobnější informace o rozhraních API vám pomůžou porozumět konceptům a pracovním postupům na hlubší úrovni:

> [!div class="nextstepaction"]
> [Vytvoření indexu pomocí sady .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)
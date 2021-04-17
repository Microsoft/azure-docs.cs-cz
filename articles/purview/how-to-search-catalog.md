---
title: 'Postupy: hledání Data Catalog'
description: Tento článek poskytuje přehled o tom, jak hledat v katalogu dat.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 178604335968c3664bde51c144759c1c040c359d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564909"
---
# <a name="search-the-azure-purview-data-catalog"></a>Hledání ve službě Azure dosah Data Catalog

Zjišťování dat je prvním krokem pro úlohy analýzy dat nebo zásad správného řízení dat pro příjemce dat. Zjišťování dat může být časově náročné, protože možná nevíte, kde najít data, která chcete. I po nalezení dat můžete mít pochybnosti o tom, jestli data můžete nebo nedůvěřujete, a zařídit se na nich závislost.

Cílem hledání ve službě Azure dosah je urychlit proces zjišťování dat, abyste mohli rychle najít data, která jsou v oblasti. Tento článek popisuje, jak ve službě Azure dosah Data Catalog vyhledat data, která hledáte, k rychlému vyhledání.

## <a name="search-the-catalog-for-assets"></a>Hledání prostředků v katalogu

V Azure dosah se panel hledání nachází v horní části uživatelského prostředí dosah studia.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Snímek obrazovky znázorňující umístění panelu hledání Azure dosah" border="true":::

Po kliknutí na panel hledání uvidíte poslední historii hledání a nedávno použité prostředky. Výběrem Zobrazit vše zobrazíte všechny naposledy zobrazené prostředky.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="Snímek obrazovky zobrazující panel hledání před zadáním jakýchkoli klíčových slov" border="true":::

Zadejte klíčová slova, která vám pomůžou identifikovat vaše assety, jako je jeho název, datový typ, klasifikace a terminologie glosáře. Když zadáte klíčová slova vztahující se k požadovanému prostředku, Azure dosah zobrazí návrhy na hledání a potenciální shody prostředků. Chcete-li dokončit hledání, klikněte na tlačítko "Zobrazit výsledky hledání" nebo stiskněte klávesu ENTER.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="Snímek obrazovky znázorňující panel hledání jako uživatel zadá klíčová slova" border="true":::

Na stránce výsledky hledání se zobrazuje seznam assetů, které odpovídají klíčovým slovům uvedeným v pořadí podle relevance. Existují různé faktory, které mohou ovlivnit skóre relevance assetu. Seznam více můžete vyfiltrovat tak, že vyberete konkrétní úložiště dat, klasifikace, kontakty, štítky a podmínky glosáře, které se vztahují k assetu, který hledáte.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Snímek obrazovky znázorňující výsledky hledání" border="true":::

 Kliknutím na požadovaný prostředek zobrazíte stránku podrobnosti o aktivech, kde můžete zobrazit vlastnosti, včetně schématu, řádku a vlastníků prostředků.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="Snímek obrazovky zobrazující stránku s podrobnostmi o prostředcích" border="true":::

## <a name="search-query-syntax"></a>Syntaxe vyhledávacího dotazu

Všechny vyhledávací dotazy se skládají z klíčových slov a operátorů. Klíčové slovo je něco, co by bylo součástí vlastností prostředku. Možná klíčová slova mohou být klasifikace, Glosář termínů, popis prostředku nebo název prostředku. Klíčové slovo může být pouze součástí vlastnosti, na kterou se shoduje. Pomocí klíčových slov a níže uvedených operátorů zajistíte, že Azure dosah vrátí assety, které hledáte. 

Níže jsou uvedeny operátory, které lze použít k vytvoření vyhledávacího dotazu. Operátory lze v jednom dotazu kombinovat tolikrát, kolikrát potřebujete.

| Operátor | Definice | Příklad |
| -------- | ---------- | ------- |
| NEBO | Určuje, že Asset musí mít alespoň jedno ze dvou klíčových slov. Musí být ve všech velkých písmenech. Prázdný znak je také operátor OR.  | Dotaz `hive OR database` vrátí assety, které obsahují klíčové slovo "podregistr" nebo "Database" nebo obojí. |
| A | Určuje, že Asset musí mít obě klíčová slova. Musí být ve všech velkých písmenech. | Dotaz `hive AND database` vrátí assety, které obsahují jak podregistr, tak i Database. |
| NOT | Určuje, že Asset nemůže obsahovat klíčové slovo napravo od klauzule NOT. | Dotaz `hive NOT database` vrátí prostředky, které obsahují "podregistr", ale ne "Database". |
| () | Seskupuje sadu klíčových slov a operátorů společně. Při kombinování více operátorů určují závorky pořadí operací. | Dotaz `hive AND (database OR warehouse)` vrátí assety, které obsahují ' podregistr ' a buď ' Database ' nebo ' Warehouse ', nebo obojí. |
| "" | Určuje přesný obsah ve frázi, které musí dotaz odpovídat. | Dotaz `"hive database"` vrátí do vlastností prostředky, které obsahují frázi "databáze podregistru". |
| * | Zástupný znak, který odpovídá jednomu na mnoho znaků. V klíčovém slově nemůže být první znak. | Dotaz `dat*` vrátí assety, které mají vlastnosti, které začínají na "dat", jako jsou data nebo databáze. |
| ? | Zástupný znak, který odpovídá jednomu znaku. Nejde o první znak v klíčovém slově. | Dotaz `dat?` vrátí assety, které mají vlastnosti, které začínají na "dat" a jsou čtyři písmena, například data nebo data. |

> [!Note]
> Vždy zadejte logické operátory (**a**, **nebo**, **ne**) u všech velkých písmen. V opačném případě nezáleží na velikosti písmen a udělejte nadbytečné mezery.

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet, importovat a exportovat terminologie glosáře](how-to-create-import-export-glossary.md)
- [Správa termínových šablon pro obchodní Glosář](how-to-manage-term-templates.md)

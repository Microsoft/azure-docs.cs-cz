---
title: Úvod do integrované podpory Jupyter poznámkových bloků v Azure Cosmos DB (Preview)
description: Přečtěte si, jak můžete použít integrovanou podporu Jupyter poznámkových bloků v Azure Cosmos DB k interaktivnímu spouštění dotazů.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588151"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Integrovaná podpora Jupyter poznámkových bloků v Azure Cosmos DB (Preview)

Jupyter Notebook je webová aplikace open source, která vám umožňuje vytvářet a sdílet dokumenty, které obsahují živý kód, rovnice, vizualizace a doprovodný text. 

Azure Cosmos DB integrovaných notebooků Jupyter se přímo integrují do Azure Portal a účtů Azure Cosmos DB, takže jsou praktické a snadno použitelné. Vývojáři, odborníci na data, technici a analytiké můžou pomocí známých prostředí Jupyter poznámkových bloků provádět zkoumání dat, čištění dat, transformaci dat, numerické simulace, statistické modelování, vizualizaci dat a strojové učení.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Vizualizace poznámkových bloků Jupyter v Azure Cosmos DB":::

Azure Cosmos DB podporuje poznámkové bloky C# i Python pro všechna rozhraní API, včetně Core (SQL), Cassandra, Gremlin, Table a API pro MongoDB. V poznámkovém bloku můžete využít integrované příkazy a funkce, které usnadňují vytváření Azure Cosmos DBch prostředků, nahrávání dat a dotazování a vizualizaci dat v Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Vizualizace poznámkových bloků Jupyter v Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Výhody Jupyter poznámkových bloků

Poznámkové bloky Jupyter byly původně vyvinuty pro aplikace pro datové vědy napsané v Pythonu a R. Lze je však použít různými způsoby pro různé druhy projektů, včetně:

**Vizualizace dat:** Jupyter poznámkové bloky umožňují vizualizovat data ve formě sdíleného poznámkového bloku, který vykresluje datovou sadu jako grafiku. Můžete vytvářet vizualizace, provádět interaktivní změny sdíleného kódu a sady dat a sdílet výsledky.

**Sdílení kódu:** Služby, jako je GitHub, poskytují způsob, jak sdílet kód, ale jsou převážně neinteraktivní. Pomocí Jupyter Notebook můžete zobrazit kód, spustit ho a zobrazit výsledky přímo v Azure Portal.

**Živé interakce s kódem:** Kód v Jupyter Notebook je dynamický; můžete ho upravovat a spouštět aktualizace postupně v reálném čase. Můžete také vložit uživatelské ovládací prvky (například posuvníky nebo textová pole), které se používají jako vstupní zdroje pro kód, ukázky nebo zkušební hodnoty konceptů (POCs).

**Dokumentace k ukázkám kódu a výsledkům průzkumu dat:** Pokud máte část kódu a chcete vysvětlit řádek po řádku, který funguje, můžete ho vložit do Jupyter Notebook. Současně můžete přidat interaktivitu spolu s dokumentací.

**Integrované příkazy pro Azure Cosmos DB:** Integrované příkazy Magic pro Azure Cosmos DB usnadňují interakci s vaším účtem. Můžete použít příkazy jako%% upload a%% SQL pro nahrání dat do kontejneru a dotazování pomocí [syntaxe rozhraní SQL API](sql-query-getting-started.md). Nemusíte psát další vlastní kód.

**Vše v jednom místě prostředí:** Jupyter poznámkové bloky kombinují kód, bohatou text, obrázky, videa, animace, matematické rovnice, vykresluje, Maps, interaktivní obrázky, widgety a grafická uživatelská rozhraní do jednoho dokumentu.

## <a name="components-of-a-jupyter-notebook"></a>Součásti Jupyter Notebook

Jupyter poznámkové bloky můžou zahrnovat několik typů komponent, které jsou uspořádané do diskrétních bloků nebo buněk:

**Text a HTML:** V případě prostého textu nebo textu s poznámkou syntaxe Markdownu pro generování kódu HTML lze v libovolném bodě vkládat do dokumentu. Styly CSS lze také zahrnout do šablony použité k vygenerování poznámkového bloku nebo je do ní přidat.

**Kód a výstup:** Poznámkové bloky Jupyter podporují Python a kód C#. Výsledky spouštěného kódu se zobrazí ihned po blocích kódu a bloky kódu lze spustit několikrát v libovolném pořadí.

**Vizualizace:** Můžete generovat grafiku a grafy z kódu pomocí modulů, jako je matplotlib, prokreslovat, rozostření a další. Podobně jako výstup se tyto vizualizace zobrazí jako vložené vedle kódu, který je generuje. Podobně jako výstup se tyto vizualizace zobrazí jako vložené vedle kódu, který je generuje.

**Multimédia:** Vzhledem k tomu, že poznámkové bloky Jupyter jsou postaveny na webové technologii, mohou zobrazit všechny typy multimédií podporované webovou stránkou. Můžete je zahrnout do poznámkového bloku jako prvky HTML, nebo je můžete vygenerovat programově pomocí `IPython.display` modulu.

**Data:** Data z kontejnerů Azure Cosmos nebo výsledky dotazů můžete importovat do Jupyter Notebook programově. K nahrávání nebo dotazování dat v Azure Cosmos DB použijte integrované příkazy Magic. 

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s integrovanými Jupyter poznámkami v Azure Cosmos DB, přečtěte si následující články:

* [Povolení poznámkových bloků v účtu Azure Cosmos](enable-notebooks.md)
* [Použití funkcí a příkazů poznámkového bloku Pythonu](use-python-notebook-features-and-commands.md)
* [Použití funkcí a příkazů pro Poznámkový blok jazyka C#](use-csharp-notebook-features-and-commands.md)

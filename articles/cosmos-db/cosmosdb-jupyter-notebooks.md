---
title: Úvod do integrované podpory Jupyter poznámkových bloků v Azure Cosmos DB (Preview)
description: Přečtěte si, jak můžete použít integrovanou podporu Jupyter poznámkových bloků v Azure Cosmos DB k interaktivnímu spouštění dotazů.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760279"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Integrovaná podpora Jupyter poznámkových bloků v Azure Cosmos DB (Preview)

Jupyter Poznámkový blok je open-source webová aplikace, která umožňuje vytvářet a sdílet dokumenty obsahující živý kód, rovnice, vizualizace a mluvený text. Azure Cosmos DB podporuje integrované poznámkové bloky Jupyter pro všechna rozhraní API, jako je Cassandra, MongoDB, SQL, Gremlin a Table. Integrovaná podpora poznámkových bloků pro všechna Azure Cosmos DB rozhraní API a datové modely umožňují interaktivní spouštění dotazů. Poznámkové bloky Jupyter se spouštějí v rámci účtů Azure Cosmos a umožňují vývojářům provádět zkoumání dat, čištění dat, transformace dat, číselné simulace, statistické modelování, vizualizaci dat a strojové učení.

![Vizualizace poznámkových bloků Jupyter v Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Poznámkové bloky Jupyter podporují funkci Magic, která rozšiřuje možnosti jádra tím, že podporuje další příkazy. Cosmos Magic je příkaz, který rozšiřuje možnosti jádra Pythonu v poznámkovém bloku Jupyter, takže můžete kromě Apache Spark spustit i dotazy k rozhraní SQL API Azure Cosmos. Můžete snadno kombinovat dotazy Pythonu a SQL API a dotazovat se na data a vizualizovat je pomocí bohatých knihoven vizualizace integrovaných s příkazy vykreslení.
Azure Portal nativně integruje prostředí poznámkového bloku Jupyter do účtů Azure Cosmos, jak je znázorněno na následujícím obrázku:

![Podpora notebooků Jupyter v Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Výhody Jupyter poznámkových bloků

Poznámkové bloky Jupyter byly původně vyvinuty pro aplikace pro datové vědy napsané v Pythonu, R. Lze je však použít různými způsoby pro různé druhy projektů, jako například:

* ***vizualizace dat:** poznámkové bloky Jupyter umožňují vizualizovat data ve formě sdíleného poznámkového bloku, který vykresluje určitou sadu dat jako grafiku. Poznámkový blok Jupyter vám umožňuje vytvářet vizualizace, sdílet je a umožňovat interaktivní změny sdíleného kódu a sady dat.

* **Sdílení kódu:** Služby, jako je GitHub, poskytují způsob, jak sdílet kód, ale jsou převážně neinteraktivní. Pomocí poznámkového bloku Jupyter můžete zobrazit kód, spustit ho a výsledky zobrazit přímo v Azure Portal.

* **Živé interakce s kódem:** Kód poznámkového bloku Jupyter je dynamický; dá se upravit a znovu spustit přírůstkově v reálném čase. Poznámkové bloky mohou také vkládat uživatelské ovládací prvky (například posuvníky nebo textová pole), které lze použít jako vstupní zdroje pro kód, ukázky nebo zkoušku konceptů (POCs).

* **Dokumentace k ukázkám kódu a výsledkům průzkumu dat:** Pokud máte část kódu a chcete vysvětlit, jak funguje v Azure Cosmos DB, s výstupem v reálném čase tak, jak ho můžete vložit do Jupyter Notebook. Kód bude zůstat plně funkční. Současně můžete přidat interaktivitu spolu s dokumentací.

* **Cosmos Magic – příkazy:** V poznámkových blocích Jupyter můžete použít vlastní příkazy Magic pro Azure Cosmos DB k usnadnění interaktivního výpočetního prostředí. Například%% SQL Magic, který umožňuje jednomu pro dotazování kontejneru Cosmos pomocí rozhraní SQL API přímo v poznámkovém bloku.

* **Vše v jednom místě prostředí:** Jupyter poznámkové bloky kombinují kód, bohatou text, obrázky, videa, animace, matematické rovnice, vykresluje, Maps, interaktivní obrázky, widgety a grafická uživatelská rozhraní do jednoho dokumentu.

## <a name="components-of-a-jupyter-notebook"></a>Součásti Jupyter poznámkového bloku

Jupyter poznámkové bloky můžou zahrnovat několik typů komponent, které jsou uspořádané do diskrétních bloků:

* **Text a HTML:** V případě prostého textu nebo textu s poznámkou syntaxe Markdownu pro generování kódu HTML lze v libovolném bodě vkládat do dokumentu. Styly CSS lze také zahrnout do šablony použité k vygenerování poznámkového bloku nebo je do ní přidat.

* **Kód a výstup:** Poznámkové bloky Jupyter podporují Python Code. Výsledky spouštěného kódu se zobrazí ihned po blocích kódu a bloky kódu lze spustit několikrát v libovolném pořadí.

* **Vizualizace:** Grafiky a grafy je možné vygenerovat z kódu pomocí modulů, jako je matplotlib, prokreslovat nebo rozostření. Podobně jako výstup se tyto vizualizace zobrazí jako vložené vedle kódu, který je generuje.

* **Multimédia:** Vzhledem k tomu, že Poznámkový blok Jupyter je postaven na webové technologii, může zobrazit všechny typy multimédií podporované na webové stránce. Můžete je zahrnout do poznámkového bloku jako prvky HTML, nebo je můžete vygenerovat programově pomocí modulu `IPython.display`.

* **Data:** Data z kontejnerů Azure Cosmos a výsledky dotazů je možné naimportovat do poznámkového bloku Jupyter programově. Například zahrnutím kódu do poznámkového bloku můžete zadávat dotazy na data pomocí kterékoli z Cosmos DB rozhraní API nebo nativně integrovaných Apache Spark.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s integrovanými Jupyter poznámkami v Azure Cosmos DB, přečtěte si následující články:

* [Povolení poznámkových bloků v účtu Azure Cosmos](enable-notebooks.md)
* [Použití funkcí a příkazů poznámkového bloku](use-notebook-features-and-commands.md)




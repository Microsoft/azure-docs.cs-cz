---
title: Úvod k integrované podpoře notebooků Jupyter v Azure Cosmos DB (Preview)
description: Zjistěte, jak můžete k interaktivnímu spouštění dotazů používat integrovanou podporu poznámkových bloků Jupyter v Azure Cosmos DB.
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/22/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 5b320485001d6cbc457d39ef193ed8c57f7161df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760279"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Integrovaná podpora notebooků Jupyter v Azure Cosmos DB (preview)

Jupyter notebook je open-source webová aplikace, která vám umožní vytvářet a sdílet dokumenty obsahující živý kód, rovnice, vizualizace a narativní text. Azure Cosmos DB podporuje integrované poznámkové bloky Jupyter pro všechna rozhraní API, jako jsou Cassandra, MongoDB, SQL, Gremlin a Table. Integrovaná podpora poznámkových bloků pro všechna api azure cosmos DB a datové modely umožňuje interaktivně spouštět dotazy. Poznámkové bloky Jupyter uvolni v rámci účtů Azure Cosmos a umožňují vývojářům provádět zkoumání dat, čištění dat, transformace dat, numerické simulace, statistické modelování, vizualizaci dat a strojové učení.

![Vizualizace notebooků Jupyter v Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png)

Jupyter notebooky podporuje magické funkce, které rozšiřují možnosti jádra tím, že podporuje další příkazy. Cosmos magic je příkaz, který rozšiřuje možnosti jádra Pythonu v poznámkovém bloku Jupyter, takže kromě Apache Spark můžete spouštět dotazy Azure Cosmos SQL API. Dotazy pythonu a rozhraní SQL API můžete snadno kombinovat a dotazovat se a vizualizovat data pomocí bohatých vizualizačních knihoven integrovaných s příkazy vykreslení.
Portál Azure nativně integruje uživatelské prostředí jupyteru do účtů Azure Cosmos, jak je znázorněno na následujícím obrázku:

![Podpora notebooků Jupyter v Azure Cosmos DB](./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png)

## <a name="benefits-of-jupyter-notebooks"></a>Výhody notebooků Jupyter

Jupyter notebooky byly původně vyvinuty pro aplikace datové vědy napsané v Pythonu, R. Mohou však být použity různými způsoby pro různé druhy projektů, jako jsou:

* ***Vizualizace dat:** Poznámkové bloky Jupyter umožňují vizualizovat data ve formě sdíleného poznámkového bloku, který vykresluje některé datové sady jako grafiku. Jupyter notebook umožňuje vytvářet vizualizace, sdílet je a povolit interaktivní změny sdíleného kódu a datové sady.

* **Sdílení kódu:** Služby, jako je GitHub, poskytují způsoby sdílení kódu, ale jsou z velké části neinteraktivní. Pomocí poznámkového bloku Jupyter můžete zobrazit kód, spustit ho a zobrazit výsledky přímo na webu Azure Portal.

* **Živé interakce s kódem:** Jupyter notebook kód je dynamický; lze upravovat a znovu spustit postupně v reálném čase. Poznámkové bloky mohou také vkládat uživatelské ovládací prvky (např. posuvníky nebo pole pro zadávání textu), které lze použít jako vstupní zdroje pro kód, ukázky nebo ověření konceptů(BOC).

* **Dokumentace vzorků kódu a výsledků zkoumání dat:** Pokud máte část kódu a chcete vysvětlit řádek po řádku, jak to funguje v Azure Cosmos DB, s výstupem v reálném čase po celou cestu, můžete vložit do poznámkového bloku Jupyter. Kód zůstane plně funkční. Můžete přidat interaktivitu spolu s dokumentací ve stejnou dobu.

* **Magické příkazy Cosmos:** V poznámkových blocích Jupyter uděláte pomocí vlastních magických příkazů pro Azure Cosmos DB a usnadníte tak interaktivní práci s počítačem. Například %%sql magic, který umožňuje zadat dotaz na kontejner Cosmos pomocí rozhraní SQL API přímo v poznámkovém bloku.

* **Vše na jednom místě prostředí:** Jupyter notebooky kombinovat kód, bohatý text, obrázky, videa, animace, matematické rovnice, pozemky, mapy, interaktivní postavy, widgety a grafické uživatelské rozhraní do jednoho dokumentu.

## <a name="components-of-a-jupyter-notebook"></a>Součásti notebooku Jupyter

Jupyter notebooky mohou obsahovat několik typů komponent, z nichž každý uspořádány do diskrétní bloky:

* **Text a HTML:** Prostý text nebo text anotovaný v syntaxi značky pro generování HTML lze do dokumentu vložit v libovolném bodě. Css styling může být také zahrnuta vřadit nebo přidat do šablony slouží ke generování poznámkového bloku.

* **Kód a výstup:** Jupyter notebooky podporují kód Pythonu. Výsledky provedeného kódu se zobrazí bezprostředně po bloky kódu a bloky kódu mohou být provedeny vícekrát v libovolném pořadí, které se vám líbí.

* **Vizualizace:** Grafika a grafy mohou být generovány z kódu, pomocí modulů jako Matplotlib, Plotly, nebo Bokeh. Podobně jako výstup se tyto vizualizace zobrazí v řádkové vedle kódu, který je generuje.

* **Multimédia:** Vzhledem k tomu, Jupyter notebook je postaven na webové technologie, může zobrazit všechny typy multimédií podporovaných na webové stránce. Můžete je zahrnout do poznámkového bloku jako elementy HTML nebo `IPython.display` je můžete generovat programově pomocí modulu.

* **Údaje:** Data z kontejnerů Azure Cosmos a výsledky dotazů lze importovat do poznámkového bloku Jupyter programově. Například zahrnutím kódu do poznámkového bloku pro dotazování na data pomocí libovolného kódu COSMOS DB API nebo nativně integrované Apache Spark.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s integrovanými poznámkovými bloky Jupyter v Azure Cosmos DB, přečtěte si následující články:

* [Povolení poznámkových bloků v účtu Azure Cosmos](enable-notebooks.md)
* [Použití funkcí a příkazů poznámkového bloku](use-notebook-features-and-commands.md)




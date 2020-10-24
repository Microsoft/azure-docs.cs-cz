---
title: Modelování dat grafu pro Azure Cosmos DB rozhraní Gremlin API
description: Naučte se modelovat databázi grafu pomocí rozhraní Azure Cosmos DB Gremlin API. Tento článek popisuje, kdy použít databázi grafů a osvědčené postupy pro modelování entit a vztahů.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 12/02/2019
ms.author: jasonh
ms.openlocfilehash: 2176708d3b5371a9bb66a59a7c6c0af56c337e28
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490624"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelování dat grafu pro Azure Cosmos DB rozhraní Gremlin API

Následující dokument je navržený tak, aby poskytoval doporučení pro modelování dat grafu. Tento krok je důležitý, aby se zajistila škálovatelnost a výkon databázového systému grafu jako vývoj dat. Efektivní datový model je obzvláště důležitý u rozsáhlých grafů.

## <a name="requirements"></a>Požadavky

Proces popsaný v této příručce je založený na následujících předpokladech:
 * Identifikují se **entity** v místě problému. Tyto entity mají být pro každý požadavek spotřebovány _atomicky_ . Jinými slovy, databázový systém není navržený tak, aby načetl data jedné entity v rámci více požadavků na dotazy.
 * Pro databázový systém je potřeba pochopit **požadavky na čtení a zápis** . Tyto požadavky budou mít na výběr optimalizace potřebné pro datový model grafu.
 * Principy [standardu grafu vlastností Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) se dobře rozumí.

## <a name="when-do-i-need-a-graph-database"></a>Kdy potřebuji databázi grafu?

Řešení databáze grafu se dá optimálně použít, pokud entity a relace v datové doméně mají některou z těchto vlastností: 

* Entity jsou **vysoce propojené** prostřednictvím popisných vztahů. Výhodou v tomto scénáři je skutečnost, že se relace ukládají do úložiště.
* Existují **cyklické relace** nebo **entity s odkazem na sebe**. Tento model je často problémem při použití relačních nebo dokumentových databází.
* Mezi entitami probíhá **dynamické vyvíjejíní vztahů** . Tento model je obzvláště použitelný pro hierarchická nebo strukturovaná data s mnoha úrovněmi.
* Mezi entitami existuje několik **vztahů mezi několika různými** entitami.
* Existují **požadavky na zápis a čtení u entit i vztahů**. 

Pokud jsou výše uvedená kritéria splněna, je pravděpodobnější, že přístup k databázi grafu bude mít výhody pro **složitost dotazů**, **škálovatelnost datového modelu**a **výkon dotazů**.

Dalším krokem je určit, jestli se má graf použít pro účely analýzy nebo transakce. Pokud je graf určený k použití pro náročné úlohy výpočtů a zpracování dat, je vhodné prozkoumat [Cosmos DB konektor Spark](./spark-connector.md) a použít [knihovnu GRAPHX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Jak používat objekty grafu

[Standard pro Tinkerpop sady Apache](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definuje dva typy objektů **vrcholů** a **hran**. 

Níže jsou uvedené osvědčené postupy pro vlastnosti v objektech grafu:

| Objekt | Vlastnost | Typ | Poznámky |
| --- | --- | --- |  --- |
| Úpravě | ID | Řetězec | Jedinečně vynutilo na oddíl. Pokud při vložení není zadána hodnota, bude uložen automaticky generovaný identifikátor GUID. |
| Úpravě | label | Řetězec | Tato vlastnost slouží k definování typu entity, kterou vrchol představuje. Pokud hodnota není zadaná, použije se výchozí hodnota "vrchol". |
| Úpravě | properties | Řetězec, logická hodnota, číselná hodnota | Seznam samostatných vlastností uložených jako páry klíč-hodnota v každém vrcholu. |
| Úpravě | klíč oddílu | Řetězec, logická hodnota, číselná hodnota | Tato vlastnost určuje, kde budou uloženy vrcholy a jejich odchozí okraje. Přečtěte si další informace o [dělení grafu](graph-partitioning.md). |
| Edge | ID | Řetězec | Jedinečně vynutilo na oddíl. Automaticky generuje standardně. Okraje obvykle nemají jedinečně načteny IDENTIFIKÁTORem. |
| Edge | label | Řetězec | Tato vlastnost slouží k definování typu vztahu, který má dva vrcholy. |
| Edge | properties | Řetězec, logická hodnota, číselná hodnota | Seznam samostatných vlastností uložených jako páry klíč-hodnota v jednotlivých hraničních zařízeních. |

> [!NOTE]
> Okraje nevyžadují hodnotu klíče oddílu, protože její hodnota je automaticky přiřazena na základě jejich zdrojového vrcholu. Další informace najdete v článku [dělení grafů](graph-partitioning.md) .

## <a name="entity-and-relationship-modeling-guidelines"></a>Pravidla modelování entit a vztahů mezi entitami

Níže jsou uvedeny pokyny pro přístup k modelování dat pro Azure Cosmos DB databázi rozhraní API Gremlin. Tyto pokyny předpokládají, že existuje existující definice datové domény a dotazů.

> [!NOTE]
> Níže uvedené kroky jsou uvedeny jako doporučení. Finální model by měl být vyhodnocen a testován před jeho zvážením jako připravený pro produkční prostředí. Kromě toho níže uvedená doporučení jsou specifická pro implementaci rozhraní Gremlin API pro Azure Cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Vrcholy a vlastnosti modelování 

Prvním krokem pro datový model grafu je namapovat každou identifikovanou entitu na **objekt vrcholu**. Jedno pro mapování všech entit na vrcholy by mělo být počáteční krok a může se změnit.

Jednou z běžných Pitfall je mapovat vlastnosti jedné entity jako samostatné vrcholy. Vezměte v úvahu následující příklad, ve kterém je stejná entita zastoupena dvěma různými způsoby:

* **Vlastnosti založené na vrcholu**: v tomto přístupu entita používá tři samostatné vrcholy a dva hrany k popsání jejích vlastností. I když tento přístup může snížit redundanci, zvyšuje složitost modelu. Zvýšení složitosti modelu může mít za následek přidání latence, složitosti dotazu a výpočetních nákladů. Tento model může také prezentovat problémy při dělení.

:::image type="content" source="./media/graph-modeling/graph-modeling-1.png" alt-text="Model entity s vrcholy pro vlastnosti" border="false":::

* **Vrcholy vložené vlastností**: Tento přístup využívá výhod seznamu párů klíč-hodnota, který představuje všechny vlastnosti entity uvnitř vrcholu. Tento přístup poskytuje nižší složitost modelu, což vede k jednodušším dotazům a efektivnějším procházeníům.

:::image type="content" source="./media/graph-modeling/graph-modeling-2.png" alt-text="Model entity s vrcholy pro vlastnosti" border="false":::

> [!NOTE]
> Výše uvedené příklady znázorňují zjednodušený model grafu pro zobrazení porovnání dvou způsobů dělení vlastností entit.

Vzor **vrcholů vložené vlastnosti** obecně poskytuje pokročilejší a škálovatelný přístup. Výchozí přístup k novému datovému modelu grafu by měl gravitate k tomuto vzoru.

Existují však situace, kdy odkazování na vlastnost může poskytovat výhody. Příklad: Pokud je odkazovaná vlastnost často aktualizována. Použití samostatného vrcholu pro reprezentaci vlastnosti, která se neustále mění, by minimalizovalo množství operací zápisu, které by tato aktualizace vyžadovala.

### <a name="relationship-modeling-with-edge-directions"></a>Modelování vztahů s směry Edge

Po modelování vrcholů lze přidat okraje k označení vztahů mezi nimi. První aspekt, který je nutné vyhodnotit, je **Směr vztahu**. 

Při použití funkce or mají objekty Edge výchozí směr, který následuje po průchodu `out()` `outE()` . Použití tohoto přirozeného směru vede k efektivní operaci, protože všechny vrcholy jsou uloženy spolu s jejich odchozími okraji. 

Procházení v opačném směru hrany na okraji ale pomocí `in()` funkce vždy způsobí dotaz na více oddílů. Přečtěte si další informace o [dělení grafu](graph-partitioning.md). Pokud je potřeba nepřetržitě procházet pomocí `in()` funkce, doporučujeme přidat hrany v obou směrech.

Můžete určit směr okraje pomocí `.to()` `.from()` predikátů nebo v `.addE()` kroku Gremlin. Nebo pomocí [knihovny hromadných prováděcích modulů pro rozhraní Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Objekty Edge mají ve výchozím nastavení směr.

### <a name="relationship-labeling"></a>Popisky vztahů

Použití popisků popisných vztahů může zlepšit efektivitu operací řešení Edge. Tento model lze použít následujícími způsoby:
* Použijte neobecné výrazy k označení vztahu.
* Přidružte popisek zdrojového vrcholu k popisku cílového vrcholu s názvem relace.

:::image type="content" source="./media/graph-modeling/graph-modeling-3.png" alt-text="Model entity s vrcholy pro vlastnosti" border="false":::

Přesnější popisek, který bude používat k filtrování okrajů, lepší. Toto rozhodnutí může mít výrazný dopad i na náklady na dotaz. Náklady na dotaz můžete kdykoli vyhodnotit [pomocí kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Další kroky: 
* Podívejte se na seznam podporovaných [Gremlin kroků](gremlin-support.md).
* Přečtěte si o [vytváření oddílů databáze grafů](graph-partitioning.md) , které vám umožní pracovat s velkými grafy.
* Vyhodnoťte dotazy Gremlin pomocí [kroku profil spuštění](graph-execution-profile.md).
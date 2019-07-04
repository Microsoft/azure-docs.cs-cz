---
title: Data grafu modelování pro rozhraní Gremlin API služby Azure Cosmos DB
description: Zjistěte, jak model databáze grafu pomocí Gremlin API služby Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: c6ae23efa90874bbefc2aff35f8798aa6c0da791
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503417"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Data grafu modelování pro rozhraní Gremlin API služby Azure Cosmos DB

Následující dokument je navržené pro poskytování doporučení modelování dat grafu. Tento krok je důležité, aby bylo možné zajistit škálovatelnost a výkon databáze systému grafu jako data vyvíjí. Efektivní datový model je obzvláště důležité pomocí rozsáhlých grafů.

## <a name="requirements"></a>Požadavky

Proces popsaný v této příručce jsou založené na následující předpoklady:
 * **Entity** jsou určeny v oboru problém. Tyto entity jsou určené ke spotřebě _atomicky_ pro každý požadavek. Jinými slovy databázový systém není určená k načtení jedné entity data ve více požadavků na dotazy.
 * Je znalost **čtení a zápis požadavků** pro databázový systém. Tyto požadavky vás provedou optimalizace potřebné pro grafovému datovému modelu.
 * Zásady [Apache Tinkerpop vlastnost graph standardní](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) jsou dobře pochopitelné.

## <a name="when-do-i-need-a-graph-database"></a>Kdy potřebuji databáze grafů?

Řešení databáze grafu je možné optimálně případě použít entit a vztahů v doméně data mají některý z následujících vlastností: 

* Entity jsou **vysoce připojené** prostřednictvím popisný vztahy. Výhoda v tomto scénáři je skutečnost, že jsou vztahy uchovávaných v úložišti.
* Existují **cyklické vztahy** nebo **svým odkazované entity**. Tento model se často výzvy při použití relační nebo dokumentu databáze.
* Existují **dynamicky vyvíjejí vztahy** mezi entitami. Tento model platí zejména pro hierarchické nebo stromové struktury strukturovaná data s více úrovněmi.
* Existují **many-to-many vztahy** mezi entitami.
* Existují **zápis a čtení požadavky na entitami a relacemi**. 

Splnění výše uvedená kritéria, je pravděpodobné, že přístup databáze grafu poskytne výhody pro **dotazování složitost**, **datový model rozšiřitelnosti**, a **výkonudotazů**.

Dalším krokem je určit, pokud se bude používat pro účely analýzy a transakční grafu. Pokud graf je určena pro použití pro náročné výpočty a zpracování dat, bylo by vhodné prozkoumat [konektor Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) a použití [GraphX knihovny](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Jak pomocí objektů grafu

[Apache Tinkerpop vlastnost graph standardní](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) definuje dva typy objektů **vrcholy** a **hrany**. 

Tady jsou osvědčené postupy pro vlastnosti v grafu objektů:

| Object | Vlastnost | Type | Poznámky |
| --- | --- | --- |  --- |
| Vertex | id | String | Jednoznačně vynucena na oddíl. Pokud hodnota není zadaná při vložení a automaticky generovaný identifikátor GUID se uloží. |
| Vertex | label | String | Tato vlastnost se používá k definování typu entity, která reprezentuje vrchol. Pokud není zadána hodnota, použije se výchozí hodnota "vrcholu". |
| Vertex | properties | Řetězec, logická hodnota, číselné | Seznam samostatné vlastnosti uložené jako páry klíč hodnota v každý vrchol. |
| Vertex | Klíč oddílu | Řetězec, logická hodnota, číselné | Tato vlastnost definuje, kde bude uložena vrcholu a hrany, které její odchozí. Další informace o [dělení grafů](graph-partitioning.md). |
| Edge | id | String | Jednoznačně vynucena na oddíl. Automaticky generované ve výchozím nastavení. Okraje obvykle nemají potřeba jednoznačně načíst podle ID. |
| Edge | label | String | Tato vlastnost se používá k definování typu vztahu, který jste dvěma vrcholy. |
| Edge | properties | Řetězec, logická hodnota, číselné | Seznam samostatné vlastnosti uložené jako páry klíč hodnota v každé hrany. |

> [!NOTE]
> Okraje nevyžadují hodnotu klíče oddílu, protože jeho hodnota se automaticky přiřadí podle jejich zdrojový vrchol. Další informace najdete v [dělení grafů](graph-partitioning.md) článku.

## <a name="entity-and-relationship-modeling-guidelines"></a>Entity a pokyny pro modelování vztahů

Následují sadu pokynů k přístup datům modelování pro databázi Azure Cosmos DB Gremlin API graph. Tyto pokyny předpokládají, že je existující definici dat domény a dotazy pro něj.

> [!NOTE]
> Kroky uvedené níže jsou uvedeny jako doporučení. Finálního modelu musí být vyhodnocen a před uvážení jako připravené pro produkční prostředí otestovat. Kromě toho jsou specifické pro implementaci rozhraní Gremlin API služby Azure Cosmos DB následující doporučení. 

### <a name="modeling-vertices-and-properties"></a>Modelování vrcholů a vlastnosti 

Prvním krokem pro grafovému datovému modelu je pro každé zjištěné entity k mapování **vrcholu objekt**. Mapování 1: 1 všechny entity, které vrcholy by měla být počáteční krok a mohou se změnit.

Jeden společný trávení je do mapy – vlastnosti jedné entity jako samostatné vrcholy. Podívejte se na příklad, níže, kde je stejná entita reprezentován dvěma různými způsoby:

* **Na základě vrcholu vlastnosti**: V takovém případě entity používá tři samostatné vrcholů a hran dvě popsat její vlastnosti. Přestože tento přístup může vést ke snížení redundance, jeho hodnota se zvyšuje složitost modelu. Zvýšení složitosti modelu může vést k přidání latenci, složitosti dotazu a výpočtu nákladů. Tento model může také nést výzvy při dělení.

![Model entity s vrcholů vlastnosti.](./media/graph-modeling/graph-modeling-1.png)

* **Vložit vlastnost vrcholy**: Tento přístup využívá dvojice klíč hodnota v seznamu k reprezentaci všech vlastností entity uvnitř vrcholu. Tento přístup poskytuje snížené modelu složitost, což povede k jednodušší dotazy a další nákladově efektivní procházení.

![Model entity s vrcholů vlastnosti.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Výše uvedené příklady ukazují zjednodušený graf z modelu chcete zobrazit pouze porovnání mezi dvěma způsoby dělení hodnoty vlastností entity.

**Vložených vlastností vrcholy** vzor obvykle poskytuje další výkonné a škálovatelný přístup. Výchozí přístup k nové grafovému datovému modelu by měl gravitate pro tento model.

Existují ale scénáře, kde odkazuje na vlastnost může poskytovat výhody. Příklad: Pokud vlastnost odkazované se často aktualizuje. Pomocí samostatných vrcholu za účelem zastoupení neustále změněná vlastnost by minimalizovali množství operací zápisu, které by vyžadovaly aktualizace.

### <a name="relationship-modeling-with-edge-directions"></a>Vztah modelování edge pokyny

Jakmile jsou modelovány vrcholy, hrany lze přidat k označení vztahy mezi nimi. Je první aspektem, který je potřeba zhodnotit **směr relace**. 

Hraniční objekty mají výchozí směr, který je následován přechod zálohovaných při použití `out()` nebo `outE()` funkce. Pomocí této přirozené směr výsledkem efektivní operace, protože jsou uložené všechny vrcholy s jejich odchozí okrajů. 

Ale procházení v opačném směru okraj, pomocí `in()` fungovat, bude vždy výsledkem dotazu napříč oddíly. Další informace o [dělení grafů](graph-partitioning.md). Pokud je potřeba neustále procházet pomocí `in()` funkce, doporučuje se přidat hrany v obou směrech.

Směr edge můžete určit pomocí `.to()` nebo `.from()` predikáty k `.addE()` kroku Gremlin. Nebo s použitím [BulkExecutor knihovny pro rozhraní Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Hraniční objekty mají ve výchozím nastavení směru.

### <a name="relationship-labeling"></a>Popisky vztahů

Používání popisků popisný vztah může zvýšit efektivitu edge rozlišení operací. Tento vzor lze použít následujícími způsoby:
* Obecné podmínky použití pro popisek vztahu
* Popisek Zdrojový vrchol s popiskem Cílový vrchol přidružte název relace.

![Vztah, označování popisky příklady.](./media/graph-modeling/graph-modeling-3.png)

Čím více konkrétní popisek, který traverser použije k filtrování hrany, tím lépe. Toto rozhodnutí může mít významný dopad na i náklady na dotazu. Kdykoli můžete vyhodnotit náklady na dotaz [použití kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Další kroky: 
* Podívejte se na seznam nepodporuje [kroků konzoly Gremlin](gremlin-support.md).
* Další informace o [grafu, vytváření oddílů databáze](graph-partitioning.md) řešit rozsáhlé grafy.
* Vyhodnoťte vaše dotazy Gremlin pomocí [kroku spuštění profilu](graph-execution-profile.md).

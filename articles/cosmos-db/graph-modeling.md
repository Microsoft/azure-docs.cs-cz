---
title: Modelování dat grafu pro rozhraní API Azure Cosmos DB Gremlin
description: Zjistěte, jak modelovat databázi grafů pomocí rozhraní API Azure Cosmos DB Gremlin. Tento článek popisuje, kdy použít databázi grafů a osvědčené postupy k modelování entit a vztahů.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898327"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Modelování dat grafu pro rozhraní API Azure Cosmos DB Gremlin

Následující dokument je navržen tak, aby poskytoval doporučení pro modelování dat grafu. Tento krok je nezbytný pro zajištění škálovatelnosti a výkonu databázového systému grafu podle vývoje dat. Efektivní datový model je obzvláště důležitý u rozsáhlých grafů.

## <a name="requirements"></a>Požadavky

Proces uvedený v této příručce je založen na následujících předpokladech:
 * Entity **entities** v problémovém prostoru jsou identifikovány. Tyto entity jsou určeny k spotřebovávají _atomicky_ pro každý požadavek. Jinými slovy databázový systém není určen k načtení dat jedné entity ve více požadavcích na dotaz.
 * Je pochopení požadavků na **čtení a zápis** pro databázový systém. Tyto požadavky budou vodítkem optimalizace potřebné pro datový model grafu.
 * Principy [standardu grafu vlastností Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) jsou dobře pochopeny.

## <a name="when-do-i-need-a-graph-database"></a>Kdy potřebuji databázi grafů?

Řešení databáze grafů lze optimálně použít, pokud entity a vztahy v datové doméně mají některou z následujících charakteristik: 

* Entity jsou **vysoce propojeny** prostřednictvím popisných vztahů. Výhodou v tomto scénáři je skutečnost, že vztahy jsou trvalé v úložišti.
* Existují **cyklické vztahy** nebo **entity s vlastním odkazem**. Tento vzor je často výzvou při použití relačních nebo dokumentových databází.
* Existují **dynamicky se vyvíjející vztahy** mezi entitami. Tento vzor je použitelný zejména pro hierarchická nebo stromově strukturovaná data s mnoha úrovněmi.
* Mezi **entitami** existuje mnoho vztahů.
* Existují **požadavky na zápis a čtení na entity a vztahy**. 

Pokud jsou splněna výše uvedená kritéria, je pravděpodobné, že přístup databáze grafu bude poskytovat výhody pro **složitost dotazu**, **škálovatelnost datového modelu**a **výkon dotazu**.

Dalším krokem je zjistit, zda graf bude použit pro analytické nebo transakční účely. Pokud je graf určen pro náročné úlohy výpočtů a zpracování dat, stálo by za to prozkoumat [konektor Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector) a použití [knihovny GraphX](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Použití objektů grafu

[Standard grafu vlastností Apache Tinkerpop](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) definuje dva typy objektů **Vrcholy** a **hrany**. 

Následující jsou doporučené postupy pro vlastnosti v objektech grafu:

| Objekt | Vlastnost | Typ | Poznámky |
| --- | --- | --- |  --- |
| Vrchol | ID | Řetězec | Jedinečně vynuceno na oddíl. Pokud hodnota není zadána při vložení, bude uložen automaticky generovaný identifikátor GUID. |
| Vrchol | label | Řetězec | Tato vlastnost se používá k definování typu entity, kterou představuje vrchol. Pokud není zadána hodnota, použije se výchozí hodnota "vrchol". |
| Vrchol | properties | Řetězec, logická hodnota, číselné | Seznam samostatných vlastností uložených jako dvojice klíč-hodnota v každém vrcholu. |
| Vrchol | klíč oddílu | Řetězec, logická hodnota, číselné | Tato vlastnost definuje, kde bude uložen vrchol a jeho odchozí hrany. Přečtěte si další informace o [dělení grafů](graph-partitioning.md). |
| Edge | ID | Řetězec | Jedinečně vynuceno na oddíl. Automaticky generované ve výchozím nastavení. Hrany obvykle nemají potřebu být jedinečně načteny id. |
| Edge | label | Řetězec | Tato vlastnost se používá k definování typu vztahu, který mají dva vrcholy. |
| Edge | properties | Řetězec, logická hodnota, číselné | Seznam samostatných vlastností uložených jako dvojice klíč-hodnota v každé hraně. |

> [!NOTE]
> Hrany nevyžadují hodnotu klíče oddílu, protože jeho hodnota je automaticky přiřazena na základě jejich zdrojového vrcholu. Další informace najdete v článku [o rozdělení grafů.](graph-partitioning.md)

## <a name="entity-and-relationship-modeling-guidelines"></a>Pokyny pro modelování entit a vztahů

Následuje sada pokynů pro přístup k modelování dat pro databázi grafrozhraní API Azure Cosmos DB Gremlin. Tyto pokyny předpokládají, že existuje existující definice datové domény a dotazy na něj.

> [!NOTE]
> Níže uvedené kroky jsou uvedeny jako doporučení. Konečný model by měl být vyhodnocen a testován před jeho zvážením jako připravený na výrobu. Kromě toho níže uvedená doporučení jsou specifické pro implementaci rozhraní API Gremlin azure cosmos DB. 

### <a name="modeling-vertices-and-properties"></a>Modelování vrcholů a vlastností 

Prvním krokem pro datový model grafu je mapování každé identifikované entity na **objekt vrcholu**. Mapování jedna ku jedné všem entitám na vrcholy by mělo být prvním krokem a mělo by se změnit.

Jedním z běžných úskalí je mapování vlastností jedné entity jako samostatných vrcholů. Vezměme si níže uvedený příklad, kde je stejná entita zastoupena dvěma různými způsoby:

* **Vlastnosti založené na vrcholu**: V tomto přístupu entita používá k popisu svých vlastností tři samostatné vrcholy a dvě hrany. Zatímco tento přístup může snížit redundanci, zvyšuje složitost modelu. Zvýšení složitosti modelu může mít za následek přidanou latenci, složitost dotazu a náklady na výpočty. Tento model může také představovat problémy v dělení.

![Model entity s vrcholy pro vlastnosti.](./media/graph-modeling/graph-modeling-1.png)

* **Vrcholy vložené vlastnostmi**: Tento přístup využívá seznamu dvojice klíč-hodnota k reprezentaci všech vlastností entity uvnitř vrcholu. Tento přístup poskytuje sníženou složitost modelu, což povede k jednodušší dotazy a nákladově efektivnější průchody.

![Model entity s vrcholy pro vlastnosti.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> Výše uvedené příklady ukazují zjednodušený model grafu, který zobrazuje pouze porovnání mezi dvěma způsoby dělení vlastností entity.

Vzor **vrcholů vložené vlastnostmi** obecně poskytuje výkonnější a škálovatelnější přístup. Výchozí přístup k novému datovému modelu grafu by měl tíhnout k tomuto vzoru.

Existují však scénáře, kde odkazování na vlastnost může poskytnout výhody. Například: pokud je odkazovaná vlastnost často aktualizována. Použití samostatného vrcholu k reprezentaci vlastnosti, která se neustále mění, by minimalizovalo množství operací zápisu, které by aktualizace vyžadovala.

### <a name="relationship-modeling-with-edge-directions"></a>Modelování vztahů s hranami

Po modelování vrcholů lze hrany přidat a osvětlit vztahy mezi nimi. Prvním aspektem, který je třeba vyhodnotit, je **směr vztahu**. 

Objekty hran mají výchozí směr, za kterým `out()` následuje `outE()` průchod při použití funkce nebo. Použití tohoto přirozeného směru má za následek efektivní operaci, protože všechny vrcholy jsou uloženy s jejich odchozími hranami. 

Procházení v opačném směru hrany pomocí `in()` funkce však bude vždy mít za následek dotaz mezi oddíly. Další informace o [dělení grafů](graph-partitioning.md). Pokud je potřeba neustále procházet pomocí `in()` funkce, doporučujeme přidat hrany v obou směrech.

Směr hrany můžete určit `.to()` pomocí `.from()` predikátů `.addE()` nebo do Kroku Gremlin. Nebo pomocí [knihovny hromadného vykonavatele pro gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Objekty hran mají ve výchozím nastavení směr.

### <a name="relationship-labeling"></a>Označení vztahu

Použití popisných popisků vztahů může zlepšit efektivitu operací rozlišení hrany. Tento vzor lze použít následujícími způsoby:
* K označení relace použijte neobecné termíny.
* Přidružte popisek vrcholu zdroje k popisku cílového vrcholu k názvu relace.

![Příklady označování vztahů.](./media/graph-modeling/graph-modeling-3.png)

Čím konkrétnější popisek, který bude traverz používat k filtrování hran, tím lépe. Toto rozhodnutí může mít významný dopad na náklady na dotaz také. Náklady na dotaz můžete kdykoli vyhodnotit [pomocí kroku executionProfile](graph-execution-profile.md).


## <a name="next-steps"></a>Další kroky: 
* Prohlédněte si seznam podporovaných [kroků aplikace Gremlin](gremlin-support.md).
* Další informace o [dělení databáze grafů](graph-partitioning.md) pro řešení rozsáhlých grafů.
* Vyhodnoťte gremlinské dotazy pomocí [kroku Profil spuštění](graph-execution-profile.md).

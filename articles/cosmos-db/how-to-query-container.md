---
title: Dotazování kontejnerů ve službě Azure Cosmos DB
description: Naučte se dotazovat kontejnery v Azure Cosmos DB pomocí dotazů v oddílu a mezi oddíly.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 0f08ca84597b08b9a236b7bfb0fc9c849423a752
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335887"
---
# <a name="query-an-azure-cosmos-container"></a>Dotazování kontejneru Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek vysvětluje, jak zadat dotaz na kontejner (kolekci, graf nebo tabulku) v Azure Cosmos DB. Konkrétně se zabývá tím, jak dotazy v oddílu a mezi oddíly fungují v Azure Cosmos DB.

## <a name="in-partition-query"></a>Dotaz v rámci oddílu

Pokud má dotaz dotaz na data z kontejnerů, je-li v dotazu zadán filtr klíčů oddílu, Azure Cosmos DB automaticky optimalizuje dotaz. Směruje dotaz na [fyzické oddíly](partitioning-overview.md#physical-partitions) , které odpovídají hodnotám klíče oddílu zadaným ve filtru.

Například zvažte následující dotaz s filtrem rovnosti na `DeviceId` . Pokud tento dotaz spustíte na kontejneru rozděleném na oddíly `DeviceId` , tento dotaz se vyfiltruje na jeden fyzický oddíl.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Stejně jako v předchozím příkladu tento dotaz bude také filtrovat na jeden oddíl. Přidání dalšího filtru se `Location` nezmění:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Tady je dotaz, který má pro klíč oddílu Filtr rozsahu a nebude vymezen na jeden fyzický oddíl. Aby byl dotaz v rámci oddílu, dotaz musí mít filtr rovnosti, který zahrnuje klíč oddílu:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Dotazování napříč oddíly

Následující dotaz nemá filtr na klíč oddílu ( `DeviceId` ). Proto musí být ventilátor na všechny fyzické oddíly, kde se spouští s indexem každého oddílu:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Každý fyzický oddíl má svůj vlastní index. Proto když v kontejneru spustíte dotaz na více oddílů, efektivně spustíte jeden dotaz *na* fyzický oddíl. Azure Cosmos DB budou automaticky agregovat výsledky napříč různými fyzickými oddíly.

Indexy v různých fyzických oddílech jsou od sebe nezávisle. V Azure Cosmos DB neexistuje žádný globální index.

## <a name="parallel-cross-partition-query"></a>Paralelní dotazování napříč oddíly

Sady Azure Cosmos DB SDK 1.9.0 a novější podporují možnosti paralelního provádění dotazů. Paralelní dotazy napříč oddíly umožňují provádět dotazy napříč oddíly s nízkou latencí.

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

- **MaxConcurrency** : nastaví maximální počet současných síťových připojení k oddílům kontejneru. Pokud nastavíte tuto vlastnost na `-1` , sada SDK bude spravovat stupeň paralelismu. Pokud je  `MaxConcurrency` nastavena na `0` , je k oddílům kontejneru jediné síťové připojení.

- **MaxBufferedItemCount:** Vyvažuje latenci dotazů a využití paměti na straně klienta. Pokud je tato možnost vynechána nebo chcete-li ji nastavit na hodnotu-1, sada SDK spravuje počet položek ukládaných do vyrovnávací paměti během paralelního provádění dotazů.

Vzhledem k tomu, že Azure Cosmos DB je možné paralelizovatovat dotazy na více oddílů, bude latence dotazů obecně škálovatelná, protože systém přidává [fyzické oddíly](partitioning-overview.md#physical-partitions). Poplatek za RU se ale výrazně zvýší, protože se zvýší celkový počet fyzických oddílů.

Když spustíte dotaz pro různé oddíly, budete v podstatě provádět samostatný dotaz na jednotlivé fyzické oddíly. I když dotazy mezioddílu budou používat index, pokud je k dispozici, stále nejsou skoro stejně efektivní jako dotazy v rámci oddílu.

## <a name="useful-example"></a>Užitečný příklad

Pro lepší pochopení dotazů mezi oddíly je tu analogický:

Představte si, že jste ovladač pro doručování, který musí doručovat balíčky do různých typů Apartment. Každý model Apartment má seznam na místě, kde jsou všechna čísla jednotek rezidenta. Každý izolovaný model Apartment můžeme porovnat s fyzickým oddílem a každým seznamem s indexem fyzického oddílu.

Pomocí tohoto příkladu můžeme porovnat dotazy v oddílu a mezi oddíly:

### <a name="in-partition-query"></a>Dotaz v rámci oddílu

Pokud ovladač pro doručování zná správný komplexní model Apartment (fyzický oddíl), může ihned prosazovat správnou sestavování. Ovladač může kontrolovat seznam čísel jednotek rezidentního typu na izolovaném typu (index) a rychle doručovat příslušné balíčky. V takovém případě ovladač neodstraní žádné časy ani úsilí, aby se kontrolovaly s izolovaným prostorem, aby zkontroloval a viděli, jestli tam všichni příjemci balíčku žijí.

### <a name="cross-partition-query-fan-out"></a>Dotaz na více oddílů (ventilátor-out)

Pokud ovladač pro doručování nezná správný komplexní model Apartment (fyzický oddíl), bude nutné, aby provedl každou jednotlivou budova na izolovaném typu Apartment a zkontroloval seznam se všemi čísly jednotek rezidenta (index). Jakmile se dostanou do každého typu apartment, budou moci používat seznam adres každého rezidenta. Budou se ale muset podívat na všechny komplexní seznamy s izolovaným modelem, ať už v něm existují všichni příjemci balíčku. To je způsob fungování dotazů mezi oddíly. I když můžou použít index (nemusíte vykrojit na všech jednoduchých dveřích), musí samostatně kontrolovat index pro každý fyzický oddíl.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Dotaz na více oddílů (omezený na několik fyzických oddílů)

Pokud ovladač pro doručování ví, že všichni příjemci balíčku jsou v některém z několika složitých komplexních komplexních, nemusíte je nastavovat na každou jednu z nich. I když řízení na několika komplexních objektech Apartment bude stále vyžadovat více práce, než když navštívíte jenom jednu budovu, ovladač pro doručování pořád ukládá výrazný čas a úsilí. Pokud má dotaz klíč oddílu ve svém filtru s `IN` klíčovým slovem, zkontroluje pouze příslušné indexy fyzického oddílu pro data.

## <a name="avoiding-cross-partition-queries"></a>Předcházení dotazům mezi oddíly

U většiny kontejnerů je nevyhnutelné, že budete mít několik dotazů mezi oddíly. Některé dotazy na více oddílů jsou v pořádku. Téměř všechny operace dotazů jsou podporovány napříč oddíly (klíče logického oddílu a fyzické oddíly). Azure Cosmos DB také obsahuje mnoho optimalizací v dotazovacím stroji a klientských sadách SDK pro paralelizovat provádění dotazů napříč fyzickými oddíly.

U většiny scénářů pro čtení a čtení doporučujeme v filtrech dotazů jednoduše vybrat nejběžnější vlastnost. Ujistěte se také, že klíč oddílu dodržuje jiné [osvědčené postupy výběru klíče oddílu](partitioning-overview.md#choose-partitionkey).

Vyloučení dotazů mezi oddíly obvykle většinou pouze s velkými kontejnery. Při každé kontrole indexu fyzického oddílu na výsledky se vám bude účtovat minimálně 2,5 RU, a to i v případě, že se filtr dotazu neshoduje s žádnými položkami ve fyzickém oddílu. Pokud máte jenom jeden (nebo jen několik) fyzických oddílů, dotazy na více oddílů nebudou spotřebovávat významně větší než dotazy na oddíly.

Počet fyzických oddílů je vázaný na míru zřízeného RU. Každý fyzický oddíl umožňuje až 10 000 zřídit RU a může ukládat až 50 GB dat. Azure Cosmos DB bude pro vás automaticky spravovat fyzické oddíly. Počet fyzických oddílů ve vašem kontejneru závisí na zřízené propustnosti a spotřebovaném úložišti.

Měli byste se pokusit vyhnout se dotazům mezi oddíly, pokud vaše úlohy splňují následující kritéria:
- Máte v plánu, aby bylo zajištěno více než 30 000 RU.
- Máte v plánu ukládat více než 100 GB dat

## <a name="next-steps"></a>Další kroky

Informace o dělení v Azure Cosmos DB najdete v následujících článcích:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)

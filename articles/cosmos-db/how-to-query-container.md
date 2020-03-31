---
title: Dotazování kontejnerů ve službě Azure Cosmos DB
description: Zjistěte, jak dotazovat kontejnery v Azure Cosmos DB pomocí dotazů v oddílech a mezi oddíly
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131400"
---
# <a name="query-an-azure-cosmos-container"></a>Dotaz na kontejner Azure Cosmos

Tento článek vysvětluje, jak dotaz na kontejner (kolekce, graf nebo tabulka) v Azure Cosmos DB. Zejména zahrnuje, jak v oddílu a mezioddíly dotazy pracovat v Azure Cosmos DB.

## <a name="in-partition-query"></a>Dotaz v rámci oddílu

Když dotazujete data z kontejnerů, pokud dotaz obsahuje zadaný filtr klíče oddílu, Azure Cosmos DB dotaz automaticky optimalizuje. Směruje dotaz na [fyzické oddíly](partition-data.md#physical-partitions) odpovídající hodnotám klíče oddílu zadaným ve filtru.

Zvažte například níže uvedený dotaz s `DeviceId`filtrem rovnosti na . Pokud spustíme tento dotaz v `DeviceId`kontejneru rozděleném na , bude tento dotaz filtrovat do jednoho fyzického oddílu.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Stejně jako v předchozím příkladu bude tento dotaz také filtrovat do jednoho oddílu. Přidání majek/přidání mašitna `Location` se nezmění na tomto:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Zde je dotaz, který má filtr rozsahu na klíč oddílu a nebude vymezen na jeden fyzický oddíl. Chcete-li být v oddílu dotazu, dotaz musí mít filtr rovnosti, který obsahuje klíč oddílu:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Dotazování napříč oddíly

Následující dotaz nemá filtr na klíč oddílu`DeviceId`( ). Proto musí fan-out na všechny fyzické oddíly, kde je spuštěn proti indexu každého oddílu:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Každý fyzický oddíl má svůj vlastní index. Proto při spuštění dotazu mezi oddíly v kontejneru, jsou efektivně spuštěnjeden dotaz *na* fyzický oddíl. Azure Cosmos DB automaticky agreguje výsledky napříč různými fyzickými oddíly.

Indexy v různých fyzických oddílů jsou nezávislé na sobě navzájem. V Azure Cosmos DB neexistuje žádný globální index.

## <a name="parallel-cross-partition-query"></a>Paralelní dotazování napříč oddíly

Sady Azure Cosmos DB SDK 1.9.0 a novější podporují možnosti paralelního spuštění dotazu. Paralelní dotazy napříč oddíly umožňují provádět dotazy napříč oddíly s nízkou latencí.

Paralelní provádění dotazů můžete spravovat laděním následujících parametrů:

- **MaxConcurrency**: Nastaví maximální počet současných síťových připojení k oddílům kontejneru. Pokud nastavíte `-1`tuto vlastnost , sada SDK spravuje stupeň paralelismu. Pokud `MaxConcurrency` je `0`nastavena na , je jedno síťové připojení k oddíly kontejneru.

- **MaxBufferedItemCount:** Vyvažuje latenci dotazů a využití paměti na straně klienta. Pokud je tato možnost vynechána nebo nastavena na hodnotu -1, sada SDK spravuje počet položek, které jsou uloženy do vyrovnávací paměti během paralelního provádění dotazu.

Vzhledem k schopnosti Azure Cosmos DB paralelizovat dotazy mezi oddíly, latence dotazu se obecně škáluje stejně jako systém přidá [fyzické oddíly](partition-data.md#physical-partitions). Poplatek žP se však výrazně zvýší s tím, jak se zvyšuje celkový počet fyzických oddílů.

Při spuštění dotazu mezi oddíly, v podstatě provádíte samostatný dotaz na jednotlivé fyzické oddíly. Zatímco dotazy mezi oddíly budou používat index, pokud jsou k dispozici, stále nejsou ani zdaleka tak efektivní jako dotazy v oddílu.

## <a name="useful-example"></a>Užitečný příklad

Zde je analogie pro lepší pochopení dotazů mezi oddíly:

Představme si, že jste řidič dodávky, který musí doručit balíčky do různých bytových komplexů. Každý bytový komplex má seznam v prostorách, který má všechna čísla jednotek rezidenta. Můžeme porovnat každý bytový komplex s fyzickým oddílem a každý seznam s indexem fyzického oddílu.

Můžeme porovnat in-partition a cross-partition dotazy pomocí tohoto příkladu:

### <a name="in-partition-query"></a>Dotaz v rámci oddílu

Pokud řidič dodávky zná správný bytový komplex (fyzický oddíl), může okamžitě jet do správné budovy. Řidič může zkontrolovat seznam bytových komplexů čísel jednotek rezidenta (index) a rychle doručit příslušné balíčky. V tomto případě řidič neztrácí čas ani úsilí jízdou do bytového komplexu, aby zkontroloval a zjistil, zda tam žijí příjemci balíčku.

### <a name="cross-partition-query-fan-out"></a>Dotaz napříč oddíly (fan-out)

Pokud řidič dodávky nezná správný bytový komplex (fyzický oddíl), bude muset jet do každého bytového domu a zkontrolovat seznam se všemi čísly jednotek rezidenta (index). Jakmile dorazí do každého bytového komplexu, budou stále moci používat seznam adres každého obyvatele. Budou však muset zkontrolovat seznam každého bytového komplexu, zda tam žijí příjemci balíčků nebo ne. To je, jak fungují dotazy mezi oddíly. Zatímco oni mohou použít index (není nutné klepat na každé jednotlivé dveře), musí samostatně zkontrolovat index pro každý fyzický oddíl.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Dotaz mezi oddíly (s rozsahem pouze na několik fyzických oddílů)

Pokud řidič dodávky ví, že všichni příjemci balíčků žijí v několika bytových komplexech, nebudou muset jet do každého. Při jízdě do několika bytových komplexů bude stále vyžadovat více práce než návštěva jen jedné budovy, řidič dodávky stále šetří značný čas a úsilí. Pokud dotaz má klíč oddílu ve `IN` svém filtru s klíčovým slovem, bude pouze kontrolovat příslušné fyzické oddíly indexy pro data.

## <a name="avoiding-cross-partition-queries"></a>Předcházení dotazům mezi oddíly

Pro většinu kontejnerů je nevyhnutelné, že budete mít některé dotazy mezi oddíly. S některými dotazy cross-partition je v pořádku! Téměř všechny operace dotazu jsou podporovány napříč oddíly (klíče logických oddílů i fyzické oddíly). Azure Cosmos DB má také mnoho optimalizací v dotazovacím stroji a klientských sadách SDK pro paralelizaci provádění dotazů napříč fyzickými oddíly.

Pro většinu scénářů náročné na čtení doporučujeme jednoduše vybrat nejběžnější vlastnosti ve filtrech dotazů. Měli byste se také ujistit, že klíč oddílu dodržuje osvědčené [postupy výběru jiného klíče oddílu](partitioning-overview.md#choose-partitionkey).

Vyhnout se dotazy mezi oddíly obvykle záleží pouze s velké kontejnery. Při každé kontrole indexu fyzického oddílu pro výsledky se účtuje minimálně přibližně 2,5 RU, a to i v případě, že žádné položky ve fyzickém oddílu neodpovídají filtru dotazu. Jako takové pokud máte pouze jeden (nebo jen několik) fyzické oddíly, dotazy mezi oddíly nebude spotřebovávat výrazně více RU než v oddílu dotazy.

Počet fyzických oddílů je vázánna na množství zřízených ŽP. Každý fyzický oddíl umožňuje až 10 000 zřízených RU a může ukládat až 50 GB dat. Azure Cosmos DB bude automaticky spravovat fyzické oddíly za vás. Počet fyzických oddílů v kontejneru závisí na zřízené propustnosta a spotřebované úložiště.

Měli byste se pokusit vyhnout se dotazům mezi oddíly, pokud vaše úloha splňuje následující kritéria:
- Plánujete mít více než 30 000 zřízených
- Plánujete uložit více než 100 GB dat

## <a name="next-steps"></a>Další kroky

V následujících článcích se dozvíte o dělení v Azure Cosmos DB:

- [Dělení ve službě Azure Cosmos DB](partitioning-overview.md)
- [Syntetické klíče oddílů ve službě Azure Cosmos DB](synthetic-partition-keys.md)

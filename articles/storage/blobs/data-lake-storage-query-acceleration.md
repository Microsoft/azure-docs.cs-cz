---
title: Akcelerace dotazu Azure Data Lake Storage (preview)
description: Akcelerace dotazů (preview) je nová funkce pro Azure Data Lake Storage, která umožňuje aplikacím a analytickým rámcům výrazně optimalizovat zpracování dat načtením pouze dat, která jsou potřebná pro operaci zpracování.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 04/21/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 191a3280075403c8c5b57c5ffca1c7707d1ddb11
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771817"
---
# <a name="azure-data-lake-storage-query-acceleration-preview"></a>Akcelerace dotazu Azure Data Lake Storage (preview)

Akcelerace dotazů (preview) je nová funkce pro Azure Data Lake Storage, která umožňuje aplikacím a analytickým rámcům výrazně optimalizovat zpracování dat načtením pouze dat, která potřebují k provedení dané operace. To snižuje čas a výpočetní výkon, který je nutný k získání kritického přehledu o uložených datech.

> [!NOTE]
> Funkce akcelerace dotazu je ve verzi Public Preview a je k dispozici v oblastech Kanada – střed a Francie – střed. Omezení najdete v článku [Známé problémy.](data-lake-storage-known-issues.md) Chcete-li se zaregistrovat do náhledu, přečtěte [si tento formulář](https://aka.ms/adls/qa-preview-signup).  

## <a name="overview"></a>Přehled

Akcelerace dotazu přijímá filtrování *predikátů* a *sloupových projekcí,* které aplikacím umožňují filtrovat řádky a sloupce v době, kdy jsou data čtena z disku. Do aplikace jsou přenášena pouze data, která splňují podmínky predikátu. To snižuje latenci sítě a náklady na výpočetní prostředky.  

Sql můžete použít k určení predikáty filtru řádků a projekce sloupců v požadavku akcelerace dotazu. Požadavek zpracovává pouze jeden soubor. Proto rozšířené relační funkce SQL, jako jsou spojení a seskupení podle agregace, nejsou podporovány. Akcelerace dotazu podporuje csv a JSON formátovaná data jako vstup pro každý požadavek.

Funkce akcelerace dotazu se neomezuje pouze na úložiště datových jezer (účty úložiště, u kterých je povolen hierarchický obor názvů). Akcelerace dotazu je zcela kompatibilní s objekty BLOB v účtech úložiště, které **nemají** povolen hierarchický obor názvů. To znamená, že můžete dosáhnout stejného snížení latence sítě a výpočetních nákladů při zpracování dat, která jste už uložili jako objekty BLOB v účtech úložiště.

Příklad použití akcelerace dotazu v klientské aplikaci najdete v tématu [Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="data-flow"></a>Tok dat

Následující diagram znázorňuje, jak typická aplikace používá akceleraci dotazu ke zpracování dat.

> [!div class="mx-imgBorder"]
> ![Přehled akcelerace dotazu](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. Klientská aplikace požaduje data souboru zadáním predikátů a sloupových projekcí.

2. Akcelerace dotazu analyzuje zadaný dotaz SQL a distribuuje práci na analýzu a filtrování dat.

3. Procesory číst data z disku, analyzuje data pomocí příslušného formátu a potom filtruje data pomocí zadané predikáty a sloupcové projekce.

4. Akcelerace dotazu kombinuje šimech odpovědí pro datový proud zpět do klientské aplikace.

5. Klientská aplikace přijímá a analyzuje streamované odpovědi. Aplikace nemusí filtrovat žádná další data a můžete použít požadovaný výpočet nebo transformaci přímo.

## <a name="better-performance-at-a-lower-cost"></a>Lepší výkon při nižších nákladech

Akcelerace dotazu optimalizuje výkon snížením množství dat, která se přenášejí a zpracovávají vaší aplikací.

Chcete-li vypočítat agregovanou hodnotu, aplikace běžně načítají **všechna** data ze souboru a pak zpracovávají a filtrují data místně. Analýza vstupních a výstupních vzorů pro analytické úlohy ukazují, že aplikace obvykle vyžadují pouze 20 % dat, která čtou k provedení daného výpočtu. Tato statistika platí i po použití technik, jako je [prořezávání oddílů](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning). To znamená, že 80 % těchto dat je zbytečně přenášeno po síti, analyzováno a filtrováno podle aplikací. Tento vzor, který je v podstatě navržen tak, aby odstranil nepotřebná data, má za následek značné náklady na výpočetní prostředky.  

I když Azure nabízí špičkovou síť, z hlediska propustnosti i latence, je zbytečně přenos dat v této síti stále nákladný pro výkon aplikací. Filtrováním nežádoucích dat během požadavku na úložiště eliminuje akcelerace dotazu tuto cenu.

Zatížení procesoru, které je nutné analyzovat a filtrovat nepotřebná data, navíc vyžaduje, aby vaše aplikace zřizovala větší počet a větší virtuální počítače, aby mohla fungovat. Přenosem tohoto výpočetního zatížení na akceleraci dotazu mohou aplikace dosáhnout významných úspor nákladů.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>Aplikace, které mohou využívat akcelerace dotazu

Akcelerace dotazů je určena pro distribuované analytické architektury a aplikace pro zpracování dat. 

Distribuované analytické architektury jako Apache Spark a Apache Hive zahrnují vrstvu abstrakce úložiště v rámci. Tyto moduly také zahrnují optimalizace dotazů, které mohou zahrnovat znalosti o možnostech základní vstupně-v., při určování optimálního plánu dotazů pro dotazy uživatelů. Tyto architektury začínají integrovat akceleraci dotazu. V důsledku toho uživatelům těchto rámců uvidí lepší latence dotazu a nižší celkové náklady na vlastnictví bez nutnosti provádět žádné změny v dotazech. 

Akcelerace dotazu je také určena pro aplikace pro zpracování dat. Tyto typy aplikací obvykle provádějí rozsáhlé transformace dat, které nemusí přímo vést k analytickým přehledům, takže ne vždy používají zavedené architektury distribuované analýzy. Tyto aplikace mají často přímější vztah s základní službou úložiště, takže mohou přímo využívat funkce, jako je akcelerace dotazů. 

Příklad, jak může aplikace integrovat akceleraci dotazu, najdete v [tématu Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md).

## <a name="pricing"></a>Ceny

Vzhledem ke zvýšenému výpočetnímu zatížení v rámci služby Azure Data Lake Storage se cenový model pro použití akcelerace dotazů liší od normálního transakčního modelu Azure Data Lake Storage. Akcelerace dotazu účtuje náklady na množství naskenovaných dat a také náklady na množství dat vrácených volajícímu.

Navzdory změně modelu fakturace je cenový model akcelerace dotazu navržený tak, aby snížil celkové náklady na vlastnictví pro úlohu, vzhledem ke snížení mnohem dražších nákladů na virtuální počítač.

## <a name="next-steps"></a>Další kroky

- [Formulář pro zápis akcelerace dotazu](https://aka.ms/adls/qa-preview-signup)    
- [Filtrování dat pomocí akcelerace dotazu Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md)
- [Referenční příručka jazyka SQL akcelerace dotazu (náhled)](query-acceleration-sql-reference.md)
- Odkaz na rozhraní REST API akcelerace dotazu




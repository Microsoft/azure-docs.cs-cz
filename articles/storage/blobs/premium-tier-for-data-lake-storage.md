---
title: Úroveň Premium pro Azure Data Lake Storage | Microsoft Docs
description: Použijte úroveň výkonu Premium s Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/30/2020
ms.author: normesta
ms.openlocfilehash: be440407fb6f4d9715ba80c584af023a5f662394
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93324219"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Úroveň Premium pro Azure Data Lake Storage

Azure Data Lake Storage Gen2 teď podporuje [úroveň výkonu Premium](storage-blob-performance-tiers.md#premium-performance). Úroveň výkonu Premium je ideální pro aplikace pro analýzu velkých objemů dat a úlohy, které vyžadují nízkou latenci a mají vysoký počet transakcí.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Úlohy, které můžou těžit z úrovně výkonu Premium

Příklad úloh zahrnuje interaktivní úlohy, IoT, streamování, umělou analýzu a strojové učení. 

**Interaktivní úlohy** 

Tyto úlohy vyžadují okamžité aktualizace a zpětnou vazbu od uživatelů, jako jsou elektronické obchodování a aplikace s mapováním, interaktivní aplikace pro video atd. Například v aplikaci elektronického obchodování nejspíš méně často zobrazené položky neukládá do mezipaměti. Musí se ale okamžitě zobrazit zákazníkovi na vyžádání. Jako jiný příklad mohou odborníci na data, analytiké a vývojáři odvodit časově citlivé přehledy ještě rychleji spuštěním dotazů na data uložená v účtu, který využívá úroveň výkonu Premium. 

**Analýzy IoT/streamování** 

Ve scénáři IoT může být do cloudu každou sekundu vložená spousta menších operací zápisu. Velké objemy dat může být ingestované, agregované pro účely analýzy a následně se odstraní téměř okamžitě. Vysoké možnosti přijímání v úrovni výkonu úrovně Premium umožňují, aby tento typ úlohy byl účinný. 

**Umělá logika a strojové učení (AI/ML)** 

AI/ML se zabývá spotřebou a zpracováním různých datových typů, jako jsou vizuály, řeč a text. Tento vysoce výkonný výpočetní typ úloh se zabývá velkými objemy dat, která pro analýzu dat vyžaduje rychlou odezvu a efektivní dobu přijímání. 

## <a name="cost-effectiveness"></a>Efektivita nákladů

Úroveň výkonu Premium má vyšší náklady na úložiště, ale nižší náklady na transakci ve srovnání se standardní úrovní výkonu. Pokud vaše aplikace a úlohy spouštějí velký počet transakcí, úroveň výkonu Premium může být nákladově efektivní.

Následující tabulka ukazuje cenovou efektivitu úrovně Premium pro Azure Data Lake Storage. Každý sloupec představuje počet transakcí v měsíci.  Každý řádek představuje procento transakcí, které čtou transakce. Každá buňka v tabulce zobrazuje procento snížení nákladů spojené s procentem transakce čtení a počet provedených transakcí. 

Předpokládejme například, že váš účet je v oblasti Východní USA 2, počet transakcí s vaším účtem překračuje 90 miliónů a 70% těchto transakcí jsou transakce čtení, úroveň výkonu Premium je cenově výhodnější.

> [!div class="mx-imgBorder"]
> ![sem patří obrázek.](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Pokud dáváte přednost vyhodnocení efektivity nákladů na základě počtu transakcí za sekundu pro každé TB dat, můžete použít záhlaví sloupců, která se zobrazí v dolní části tabulky.

Další informace o cenách najdete na stránce s [cenami Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) .

## <a name="feature-availability"></a>Dostupnost funkcí 

Některé funkce služby Blob Storage pravděpodobně nejsou k dispozici nebo mohou mít pouze částečnou podporu s úrovní výkonu Premium. Úplný seznam najdete [v tématu funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md). Pak si přečtěte seznam [známých problémů](data-lake-storage-known-issues.md) , ve kterém můžete vyhodnotit jakékoli mezery ve funkcích.

## <a name="enabling-the-premium-performance-tier"></a>Povolení úrovně výkonu Premium 

Úroveň Premium pro Azure Data Lake Storage můžete použít tak, že vytvoříte účet BlockBlobStorage s **povoleným** nastavením **hierarchického oboru názvů** . Podrobné pokyny najdete v tématu [Vytvoření účtu účtu BlockBlobStorage](storage-blob-create-account-block-blob.md) .

Když vytváříte účet, ujistěte se, že jste zvolili možnost výkon **Premium** a typ účtu **BlockBlobStorage** .

> [!div class="mx-imgBorder"]
> ![Vytvořit blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Povolte nastavení **hierarchického oboru názvů** na kartě **Upřesnit** na stránce **vytvořit účet úložiště** . Toto nastavení musíte povolit při vytváření účtu. Nemůžete ho později povolit.

Toto nastavení se zobrazuje na stránce **vytvořit účet úložiště** na následujícím obrázku.

> [!div class="mx-imgBorder"]
> ![Nastavení hierarchického oboru názvů](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Regionální dostupnost

Úroveň Premium pro Azure Data Lake Storage je k dispozici v následujících oblastech.

|Oblast|Redundance|
|--|--|
|East US|LRS, ZRS|
|USA – východ 2|LRS, ZRS|
|USA – střed|LRS|
|USA – západ|LRS|
|Západní USA 2|LRS, ZRS|
|USA – středozápad|LRS|
|Středojižní USA|LRS|
|Střední Kanada|LRS|
|Kanada – východ|LRS|
|Severní Evropa|LRS, ZRS|
|West Europe|LRS, ZRS|
|Spojené království – jih|LRS|
|Spojené království – západ|LRS|
|Francie – střed|LRS|
|Východní Asie|LRS|
|Jižní Korea – střed|LRS|
|Jižní Korea – jih|LRS|
|Indie – střed|LRS|
|Západní Indie|LRS|
|Spojené arabské emiráty sever|LRS|
|Japonsko – východ|LRS|
|Japonsko – západ|LRS|
|Jihovýchodní Asie|LRS, ZRS|
|Austrálie – východ|LRS, ZRS|
|Austrálie – jihovýchod|LRS|
|Brazílie – jih|LRS|

## <a name="next-steps"></a>Další kroky

Použijte úroveň Premium pro Azure Data Lake Storage s vaší oblíbenou analytickou službou, jako je Azure Databricks, Azure HDInsight a Azure synapse Analytics. 

- [Kurz: Azure Data Lake Storage Gen2, Azure Databricks & Spark](data-lake-storage-use-databricks-spark.md) 
- [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) HDInsight aktuálně podporuje účet, který používá úroveň výkonu Premium, spolu s clusterem HBA, který má povolené urychlené zápisy.
- [Rychlý Start: vytvoření pracovního prostoru synapse](../../synapse-analytics/quickstart-create-workspace.md)


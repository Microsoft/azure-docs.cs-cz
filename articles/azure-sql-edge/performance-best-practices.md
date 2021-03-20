---
title: Osvědčené postupy a pokyny pro konfiguraci – Azure SQL Edge
description: Seznamte se s osvědčenými postupy pro výkon a pokyny pro konfiguraci v Azure SQL Edge.
keywords: SQL Edge, uchovávání dat
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392006"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>Osvědčené postupy a pokyny pro konfiguraci výkonu

Azure SQL Edge nabízí několik funkcí a možností, které se dají použít ke zlepšení výkonu nasazení SQL Edge. Tento článek poskytuje některé osvědčené postupy a doporučení pro maximalizaci výkonu. 

## <a name="best-practices"></a>Osvědčené postupy 

### <a name="configure-multiple-tempdb-data-files"></a>Konfigurace více datových souborů databáze tempdb

Azure SQL Edge ve výchozím nastavení vytvoří v rámci inicializace kontejneru jenom jeden datový soubor tempdb. Doporučujeme zvážit vytvoření více datových souborů databáze tempdb po nasazení. Další informace najdete v pokynech v článku, [doporučení pro omezení sporu přidělení v SQL Server databázi tempdb](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Používejte clusterované indexy columnstore tam, kde je to možné

Zařízení IoT a Edge obvykle generují velké objemy dat, která jsou obvykle agregována za určité časové období pro účely analýzy. Jednotlivé datové řádky se u jakékoli analýzy zřídka používají. Indexy columnstore jsou ideální pro ukládání a dotazování těchto velkých datových sad. Tento index využívá úložiště dat založené na sloupcích a zpracování dotazů, aby bylo možné získat zisky až do deseti dob výkonu dotazů v tradičním orientovaném úložišti. Pro komprimaci dat v nekomprimovaných datech můžete také dosáhnout nárůstu až desetkrát. Další informace najdete v tématu [indexy columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview) .

Kromě toho další funkce Azure SQL Edge, jako je streamování dat a ukládání dat, z optimalizace columnstore při vkládání a odstraňování dat. 

### <a name="simple-recovery-model"></a>Jednoduchý model obnovení

Vzhledem k tomu, že úložiště může být omezené na hraničních zařízeních, všechny uživatelské databáze ve službě Azure SQL Edge ve výchozím nastavení používají jednoduchý model obnovení. Jednoduchý model obnovení automaticky znovu přihlásí místo v protokolu, aby byly požadavky na místo malé, a v podstatě eliminují nutnost spravovat místo transakčního protokolu. To může být užitečné u hraničních zařízení s dostupným omezeným úložištěm. Další informace o jednoduchém modelu obnovení a dalších dostupných modelech obnovení najdete v tématu [modely obnovení](/sql/relational-databases/backup-restore/recovery-models-sql-server) .

Operace, jako je přesouvání protokolu a obnovení k bodu v čase, které vyžadují zálohování transakčního protokolu, nejsou podporovány jednoduchým modelem obnovení.  

## <a name="advanced-configuration"></a>Pokročilá konfigurace 

### <a name="setting-memory-limits"></a>Nastavení limitů paměti

Azure SQL Edge podporuje až 64 GB paměti pro fond vyrovnávacích pamětí, zatímco satelitní procesy běžící v rámci kontejneru SQL Edge můžou vyžadovat další paměť. U menších hraničních zařízení s omezenou pamětí je vhodné omezit paměť dostupnou pro kontejnery SQL Edge, aby hostitel Docker a další hraniční procesy nebo moduly mohly správně fungovat. Celková paměť dostupná pro SQL Edge se dá řídit pomocí následujících mechanismů. 

- Omezení dostupné paměti pro kontejnery SQL Edge: Celková paměť dostupná pro kontejner SQL Edge může být omezená pomocí možnosti konfigurace modulu runtime kontejneru `--memory` . Další informace o omezení dostupné paměti pro kontejner SQL Edge najdete v tématu [Možnosti modulu runtime s pamětí, procesory a GPU](https://docs.docker.com/config/containers/resource_constraints/).

- Omezení paměti dostupné pro proces SQL v rámci kontejneru: ve výchozím nastavení proces SQL v kontejneru používá k dispozici pouze 80% fyzické paměti RAM. Ve většině nasazení bude výchozí konfigurace v pořádku. Existují však situace, kdy může být vyžadováno další paměť pro streamování dat a procesy ONNX běžící uvnitř kontejnerů SQL Edge. V takových scénářích může být paměť dostupná pro proces SQL řízena pomocí `memory.memorylimitmb` nastavení v souboru MSSQL-conf. Další informace najdete v tématu [Konfigurace pomocí souboru MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

Při nastavování limitů paměti buďte opatrní, abyste tuto hodnotu nestavili příliš nízkou. Pokud pro proces SQL nenastavíte dostatek paměti, může to mít vážně dopad na výkon SQL.

### <a name="delayed-durability"></a>Opožděná trvanlivost

Transakce ve službě Azure SQL Edge můžou být buď plně trvanlivé, SQL Server výchozí nebo zpožděné (označované také jako opožděné potvrzení).

Plně trvalá potvrzení transakcí jsou synchronní a nahlásí potvrzení jako úspěšné a vrátí řízení klientovi až poté, co jsou záznamy protokolu pro transakci zapisovány na disk. Opožděná trvalá potvrzení transakcí jsou asynchronní a zapisují potvrzení jako úspěšné předtím, než se záznamy protokolu pro transakci zapisují na disk. Zápis položek protokolu transakcí na disk je nutný, aby transakce byla trvalá. Zpožděné trvalé transakce se stanou netrvanlivé, když jsou položky transakčního protokolu vyprázdněny na disk. 

V nasazeních, u kterých je možné tolerovat **určitou ztrátu dat** nebo na hraničních zařízeních s pomalým úložištěm, je možné použít odloženou odolnost k optimalizaci přijímání dat a vyčištění na základě uchovávání dat. Další informace naleznete v tématu [řízení odolnosti transakce](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Konfigurace operačního systému Linux 

Zvažte použití následujících nastavení [Konfigurace operačního systému Linux](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) k dosažení nejlepšího výkonu pro instalaci SQL.
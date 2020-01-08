---
title: Prostředky DTU – omezení pro jednotlivé databáze
description: Tato stránka popisuje některé běžné limity prostředků DTU pro izolované databáze v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75637588"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí modelu nákupu DTU

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database izolované databáze pomocí modelu nákupu DTU.

Omezení prostředků modelu nákupu pro elastické fondy najdete v tématu [omezení prostředků DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md). Omezení prostředků vCore najdete v tématu omezení [prostředků Vcore – jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků Vcore – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). Další informace o různých nákupních modelech najdete v tématu [nakupování modelů a úrovní služeb](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Samostatná databáze: velikosti úložiště a velikosti výpočtů

V následujících tabulkách jsou uvedeny prostředky, které jsou k dispozici pro izolovanou databázi v každé úrovni služby a výpočetní velikost. Můžete nastavit úroveň služby, výpočetní velikost a množství úložiště pro jednu databázi pomocí [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShellu](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), rozhraní příkazového [řádku Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)nebo [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování izolované databáze](sql-database-single-database-scale.md) .

### <a name="basic-service-tier"></a>Úroveň služby Basic

| **Velikost výpočetního prostředí** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |Nevztahuje se |
| Maximální počet souběžných pracovních procesů (požadavků) | 30 |
| Maximální počet souběžných relací | 300 |
|||

> [!IMPORTANT]
> Úroveň služeb Basic poskytuje méně než jeden vCore (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služby S3 nebo vyšší. 
>
>V souvislosti s úložištěm dat je vrstva základní služby umístěna do objektů blob stránky úrovně Standard. Objekty blob stránky úrovně Standard využívají úložná média založená na pevných discích (HDD) a jsou nejvhodnější pro vývoj, testování a jiné zřídka používané úlohy, které jsou méně citlivé na variabilitu výkonu.
>

### <a name="standard-service-tier"></a>Úroveň služby Standard

| **Velikost výpočetního prostředí** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | Nevztahuje se | Nevztahuje se | Nevztahuje se | Nevztahuje se |
| Maximální počet souběžných pracovních procesů (požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Úrovně Standard S0, S1 a S2 poskytují méně než jeden vCore (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služby S3 nebo vyšší. 
>
>V souvislosti s úložištěm dat jsou standardní úrovně služby S0 a S1 umístěné na standardních objektech blob stránky. Objekty blob stránky úrovně Standard využívají úložná média založená na pevných discích (HDD) a jsou nejvhodnější pro vývoj, testování a jiné zřídka používané úlohy, které jsou méně citlivé na variabilitu výkonu.
>

### <a name="standard-service-tier-continued"></a>Úroveň služby úrovně Standard (pokračování)

| **Velikost výpočetního prostředí** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | Nevztahuje se | Nevztahuje se | Nevztahuje se | Nevztahuje se |Nevztahuje se |
| Maximální počet souběžných pracovních procesů (požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služby Premium

| **Velikost výpočetního prostředí** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1 000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Maximální počet možností úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Maximální úložiště OLTP v paměti (GB) | 1\. místo | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* od 1024 GB do 4096 GB v přírůstcích po 256 GB

> [!IMPORTANT]
> Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Středozápadní USA, US DoD oblasti a státní správy USA – střed. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB.  Další informace najdete v tématu [aktuální omezení P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Omezení `tempdb` najdete v tématu [omezení databáze tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Další kroky

- Omezení prostředků vCore pro izolovanou databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu Vcore](sql-database-vcore-resource-limits-single-databases.md) .
- Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu Vcore](sql-database-vcore-resource-limits-elastic-pools.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-elastic-pools.md) .
- Omezení prostředků pro spravované instance najdete v tématu [omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na databázovém serveru najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.

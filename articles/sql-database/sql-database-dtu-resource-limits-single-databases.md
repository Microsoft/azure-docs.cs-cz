---
title: Prostředky DTU omezují jednotlivé databáze
description: Tato stránka popisuje některé běžné limity prostředků DTU pro jednotlivé databáze v Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256312"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Limity prostředků pro jednoúčelové databáze využívající nákupní model založený na jednotkách DTU

Tento článek obsahuje podrobné limity prostředků pro azure SQL database jednotlivé databáze pomocí nákupního modelu DTU.

Omezení prostředků nákupního modelu DTU pro elastické fondy naleznete v tématu [Limity prostředků DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md). Omezení prostředků virtuálních jader najdete [v tématu omezení prostředků virtuálních jader – jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md) a omezení prostředků [virtuálních jader – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md). Další informace týkající se různých nákupních modelů naleznete v [tématu Nákupní modely a úrovně služeb](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Jedna databáze: Velikosti a výpočetní velikosti úložiště

V následujících tabulkách jsou uvedeny prostředky, které jsou k dispozici pro jednu databázi na každé úrovni služby a velikost výpočetních prostředků. Můžete nastavit úroveň služby, velikost výpočetních prostředků a velikost úložiště pro jednu databázi pomocí [portálu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShellu](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [rozhraní API Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)nebo rozhraní REST [API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Pokyny a důležité informace o škálování najdete [v tématu Škálování jedné databáze](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Úroveň služby Basic

| **Velikost výpočetních prostředků** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (CZ) | 2 |
| Maximální možnosti úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |Není dostupné. |
| Maximální počet souběžných pracovníků (požadavky) | 30 |
| Maximální počet souběžných relací | 300 |
|||

> [!IMPORTANT]
> Základní úroveň služby poskytuje méně než jeden virtuální jádra (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služeb S3 nebo vyšší. 
>
>Pokud jde o ukládání dat, úroveň základní služby je umístěna na objektůch BLOB standardní stránky. Objekty BLOB se standardní stránkou používají paměťová média založená na pevném disku (HDD) a jsou nejvhodnější pro vývoj, testování a další zřídka přístupné úlohy, které jsou méně citlivé na variabilitu výkonu.
>

### <a name="standard-service-tier"></a>Úroveň služby Standard

| **Velikost výpočetních prostředků** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (CZ) | 250 | 250 | 250 | 250 |
| Maximální možnosti úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | Není dostupné. | Není dostupné. | Není dostupné. | Není dostupné. |
| Maximální počet souběžných pracovníků (požadavky)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> Úrovně Standard S0, S1 a S2 poskytují méně než jeden virtuální jádro (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služeb S3 nebo vyšší. 
>
>Pokud jde o ukládání dat, úrovně služeb Standard S0 a S1 jsou umístěny na objekty BLOB standardní stránky. Objekty BLOB se standardní stránkou používají paměťová média založená na pevném disku (HDD) a jsou nejvhodnější pro vývoj, testování a další zřídka přístupné úlohy, které jsou méně citlivé na variabilitu výkonu.
>

### <a name="standard-service-tier-continued"></a>Standardní úroveň služeb (pokračování)

| **Velikost výpočetních prostředků** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (CZ) | 250 | 250 | 250 | 250 | 250 |
| Maximální možnosti úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | Není dostupné. | Není dostupné. | Není dostupné. | Není dostupné. |Není dostupné. |
| Maximální počet souběžných pracovníků (požadavky)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služby Premium

| **Velikost výpočetních prostředků** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (CZ) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximální možnosti úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovníků (požadavky)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\*Od 1024 GB do 4096 GB v krocích po 256 GB

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je v současné době k dispozici ve všech oblastech kromě: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Západní střed USA, USA DoD a Us Government Central. V těchto oblastech je maximální úložiště na úrovni Premium omezeno na 1 TB.  Další informace naleznete v [aktuálních omezeních P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Omezení `tempdb` naleznete v tématu [omezení tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Další kroky

- Omezení prostředků virtuálních jader pro jednu databázi najdete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-single-databases.md)
- Omezení prostředků virtuálních jader pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-elastic-pools.md)
- Omezení prostředků DTU pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance naleznete v tématu [limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru naleznete v [tématu přehled omezení prostředků na serveru DATABÁZE SQL](sql-database-resource-limits-database-server.md) informace o omezení na serveru a odběr úrovně.

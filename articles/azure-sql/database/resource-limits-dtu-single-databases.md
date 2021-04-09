---
title: Prostředky DTU – omezení pro jednotlivé databáze
description: Tato stránka popisuje některé běžné limity prostředků DTU pro izolované databáze v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: c530d584282cebba78c095798944e48d7efe2c66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625621"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Omezení prostředků pro izolované databáze pomocí modelu nákupu DTU – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database izolované databáze pomocí modelu nákupu DTU.

Omezení prostředků modelu nákupu pro elastické fondy najdete v tématu [omezení prostředků DTU – elastické fondy](resource-limits-dtu-elastic-pools.md). Omezení prostředků vCore najdete v tématu omezení [prostředků Vcore – jednotlivé databáze](resource-limits-vcore-single-databases.md) a [omezení prostředků Vcore – elastické fondy](resource-limits-vcore-elastic-pools.md). Další informace o různých nákupních modelech najdete v tématu [nakupování modelů a úrovní služeb](purchasing-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Samostatná databáze: velikosti úložiště a velikosti výpočtů

V následujících tabulkách jsou uvedeny prostředky, které jsou k dispozici pro izolovanou databázi v každé úrovni služby a výpočetní velikost. Můžete nastavit úroveň služby, výpočetní velikost a množství úložiště pro jednu databázi pomocí [Azure Portal](single-database-manage.md#the-azure-portal), [jazyka Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShellu](single-database-manage.md#powershell), rozhraní příkazového [řádku Azure](single-database-manage.md#the-azure-cli)nebo [REST API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování izolované databáze](single-database-scale.md) .

### <a name="basic-service-tier"></a>Úroveň služby Basic

| **Velikost výpočetního prostředí** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální velikost úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |– |
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
| Zahrnuté úložiště (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Maximální velikost úložiště (GB) | 250 | 250 | 250 | 1024 |
| Maximální úložiště OLTP v paměti (GB) | N/A | N/A | N/A | N/A |
| Maximální počet souběžných pracovních procesů (požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/single/) .

> [!IMPORTANT]
> Úrovně Standard S0, S1 a S2 poskytují méně než jeden vCore (CPU).  Pro úlohy náročné na procesor se doporučuje úroveň služby S3 nebo vyšší.
>
>V souvislosti s úložištěm dat jsou standardní úrovně služby S0 a S1 umístěné na standardních objektech blob stránky. Objekty blob stránky úrovně Standard využívají úložná média založená na pevných discích (HDD) a jsou nejvhodnější pro vývoj, testování a jiné zřídka používané úlohy, které jsou méně citlivé na variabilitu výkonu.
>

### <a name="standard-service-tier-continued"></a>Úroveň služby úrovně Standard (pokračování)

| **Velikost výpočetního prostředí** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Maximální velikost úložiště (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Maximální úložiště OLTP v paměti (GB) | N/A | N/A | N/A | N/A |N/A |
| Maximální počet souběžných pracovních procesů (požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/single/) .

### <a name="premium-service-tier"></a>Úroveň služby Premium

| **Velikost výpočetního prostředí** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maximální velikost úložiště (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (požadavků)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/single/) .

<sup>2</sup> z 1024 gb až 4096 GB v přírůstcích po 256 GB.

> [!IMPORTANT]
> Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod. V těchto oblastech je maximální velikost úložiště na úrovni Premium omezená na 1 TB.  Další informace najdete v tématu [aktuální omezení P11-P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).
> [!NOTE]
> `tempdb`Omezení najdete v tématu [omezení tempdb](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Další kroky

- Omezení prostředků vCore pro izolovanou databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu Vcore](resource-limits-vcore-single-databases.md) .
- Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu Vcore](resource-limits-vcore-elastic-pools.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](resource-limits-dtu-elastic-pools.md) .
- Omezení prostředků pro spravované instance ve spravované instanci SQL Azure najdete v tématu [omezení prostředků spravované instance SQL](../managed-instance/resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na logickém SQL serveru najdete v tématu [Přehled omezení prostředků na logickém SQL serveru](resource-limits-logical-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
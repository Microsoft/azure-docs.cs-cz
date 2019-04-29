---
title: Prostředek založený na DTU databáze SQL Azure omezuje izolované databáze | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na DTU pro izolované databáze ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: b3514eca8db5d5b68b3e5784ee95e8583813945c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075231"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU

Tento článek obsahuje podrobné prostředků limity pro izolované databáze Azure SQL Database pomocí nákupní model založený na DTU.

Založený na DTU nákupní model prostředků omezení pro elastické fondy najdete v tématu [omezení prostředků založený na DTU - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md). Omezení prostředků založený na virtuálních jádrech najdete v tématu [omezení prostředků na základě virtuálních jader – izolované databáze](sql-database-vcore-resource-limits-single-databases.md) a [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md). Další informace týkající se různých tyto modely nákupu najdete v tématu [nákupu modely a úrovní služeb](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Izolované databáze: Velikosti úložiště a výpočty velikostí

Následující tabulky ukazují prostředky dostupné pro izolované databáze na jednotlivých úrovních služby a vypočítat velikost. Můžete nastavit úroveň služby, výpočetního prostředí a velikost úložiště pro izolovanou databázi pomocí [webu Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [příkazů jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), nebo [rozhraní REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Škálování pokyny a důležité informace, najdete v části [škálování izolované databáze](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Úroveň služby Basic

| **Vypočítat velikost** | **Basic** |
| :--- | --: |
| Maximální počet DTU | 5 |
| Zahrnuté úložiště (GB) | 2 |
| Maximální počet možností úložiště (GB) | 2 |
| Maximální úložiště OLTP v paměti (GB) |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků) | 30 |
| Maximální počet souběžných relací | 300 |
|||

### <a name="standard-service-tier"></a>Úroveň služby Standard

| **Vypočítat velikost** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximální počet DTU | 10 | 20 | 50 | 100 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 60 | 90 | 120 | 200 |
| Maximální počet souběžných relací |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Úrovně služeb Standard (pokračování)

| **Vypočítat velikost** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximální počet DTU | 200 | 400 | 800 | 1600 | 3000 |
| Zahrnuté úložiště (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximální počet možností úložiště (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximální úložiště OLTP v paměti (GB) | neuvedeno | neuvedeno | neuvedeno | neuvedeno |neuvedeno |
| Maximální počet souběžných pracovních procesů (požadavků)| 400 | 800 | 1600 | 3200 |6000 |
| Maximální počet souběžných relací |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Úroveň služby Premium

| **Vypočítat velikost** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximální počet DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Zahrnuté úložiště (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximální počet možností úložiště (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maximální úložiště OLTP v paměti (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximální počet souběžných pracovních procesů (požadavků)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximální počet souběžných relací | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* Od 1 024 GB až 4096 GB v přírůstcích po 256 GB

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech s výjimkou: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, střed USA – Západ, oblastí pro úlohy ministerstva obrany USA a US Government centrální. V těchto oblastech je úložiště na úrovni Premium omezeno na 1 TB.  Další informace najdete v tématu [aktuálních omezení pro P11 – P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Pro `tempdb` omezení, najdete v článku [omezení databáze tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Další postup

- VCore omezení prostředků pro izolovanou databázi, naleznete v tématu [omezení prostředků pro izolované databáze pomocí nákupní model založený na virtuálních jádrech](sql-database-vcore-resource-limits-single-databases.md)
- VCore omezení prostředků pro elastické fondy najdete v tématu [limity pro elastické fondy pomocí nákupní model založený na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md)
- Omezení prostředků DTU pro elastické fondy najdete v tématu [limity pro elastické fondy pomocí nákupní model založený na DTU prostředků](sql-database-dtu-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance najdete v tématu [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru najdete v tématu [přehled omezení prostředků na serveru služby SQL Database](sql-database-resource-limits-database-server.md) informace o omezeních na úrovni serveru a předplatné.

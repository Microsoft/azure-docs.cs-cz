---
title: Škálování elastického fondu zdrojů – Azure SQL Database | Dokumentace Microsoftu
description: Tato stránka popisuje škálování prostředků pro elastické fondy Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 3/14/2019
ms.openlocfilehash: d8aaf51c836a8e88c4e9b92798067167cd044e72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848076"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Škálování elastického fondu prostředků ve službě Azure SQL Database

Tento článek popisuje, jak škálovat výpočetní a úložné prostředky dostupné pro elastické fondy a databáze ve fondu ve službě Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Změna výpočetní prostředky (virtuální jádra nebo Dtu)

Po počátečním výběru počet virtuálních jader nebo Edtu, můžete vertikálně elastického fondu navýšení nebo snížení kapacity dynamicky na základě aktuálních zkušeností pomocí [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [rozhraní příkazového řádku Azure ](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), nebo [rozhraní REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Změna velikost výpočetní služby vrstvě nebo změny měřítka

Změna služby vrstvy nebo výpočty velikosti elastického fondu následuje vzor podobné jako u izolovaných databází a hlavně zahrnuje službu provedením následujících kroků:

1. Vytvořit nové výpočetní instance pro elastický fond  

    Nové výpočetní instance pro elastický fond je vytvořen s požadovanou službu úrovně a výpočetního prostředí. Pro některé kombinace úroveň služby a změny velikosti výpočetního repliky každé databáze musí být vytvořeny v nové výpočetní instance, která zahrnuje kopírování dat a důrazně může ovlivnit celkovou latenci. Bez ohledu na to během tohoto kroku zůstat online databáze a připojení i nadále směrovat do databáze na původní instanci výpočetní prostředky.

2. Přepnout směrování připojení k nové výpočetní instance

    Existující připojení k databázím v původní výpočetní instance se zahodí. Žádná nová připojení jsou vytvořeny pro databáze v nové výpočetní instance. Pro některé kombinací úroveň služby a změny velikosti výpočetní prostředky jsou soubory databáze odpojit a znovu připojit během přepínač.  Přepínač bez ohledu na to, může vést ke krátkodobému přerušení služeb, když databáze nejsou k dispozici, obvykle pro méně než 30 sekund a často jenom pár sekund. Pokud jsou dlouho trvající transakce spuštěn, když se zahodí připojení, doba trvání tohoto kroku může trvat déle, aby bylo možné obnovit přerušené transakce. [Obnovení databáze Accelerated](sql-database-accelerated-database-recovery.md) snížit dopad přerušuje se dlouho probíhající transakce.

> [!IMPORTANT]
> Žádná data nejsou ztracena během libovolný krok v pracovním postupu.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latence Změna velikosti výpočetní služby vrstvě nebo změny měřítka

Latence změnit úroveň služby nebo změnit velikost výpočetních izolovanou databázi nebo elastický fond je parametrizované následujícím způsobem:

|Úroveň služeb|Základní izolované databáze</br>Standard (S0-S1)|Základní elastického fondu</br>Standard (S2-S12), </br>Hyperškálovatelného </br>Elastický fond nebo izolovanou databázi pro obecné účely|Elastický fond nebo izolovanou databázi Premium nebo pro důležité obchodní informace|
|:---|:---|:---|:---|
|**Základní izolovanou databázi,</br> Standard (S0-S1)**|&bull; &nbsp;Konstantní časovou náročnost nezávislé použitého místa</br>&bull; &nbsp;Obvykle méně než 5 minut|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|
|**Základní elastického fondu, </br>Standard (S2 S12), </br>Hyperškálovatelného </br>elastický fond nebo izolovanou databázi pro obecné účely**|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Konstantní časovou náročnost nezávislé použitého místa</br>&bull; &nbsp;Obvykle méně než 5 minut|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|
|**Elastický fond nebo izolovanou databázi Premium nebo pro důležité obchodní informace**|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|&bull; &nbsp;Latence úměrná místa v databázi použít kvůli kopírování dat</br>&bull; &nbsp;Obvykle, menší než 1 minuta za využité GB|

> [!NOTE]
>
> - V případě změna úrovně služby nebo změny měřítka výpočetní prostředky pro elastický fond, by měla sloužit k výpočtu odhadu souhrn využité napříč všemi databázemi ve fondu.
> - V případě přesunutí databáze z elastického fondu, ovlivňuje pouze místo databáze používá latence, ne podle mezery, používat elastický fond.
>
> [!TIP]
> Monitorování operací v průběhu najdete v tématu: [Správa operací pomocí rozhraní SQL API REST](https://docs.microsoft.com/rest/api/sql/operations/list), [správě operací pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledování operací s použitím jazyka T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dva příkazy Powershellu: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) a [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Další aspekty při změně služby velikost výpočetní vrstvě nebo změny měřítka

- Při downsizing virtuálních jader a počet jednotek Edtu pro elastický fond, musí být menší než maximální povolenou velikost cílové služby vrstvy a fondu Edtu fondu použít místo.
- Když změny měřítka virtuálních jader a počet jednotek Edtu pro elastický fond, náklady na úložiště platí v případě, že (1) na maximální velikost fondu úložiště jsou podporovány cílový fond, a (2) je maximální velikost úložiště překračuje velikost zahrnutého úložiště cílový fond. Například pokud se 100 jednotkami eDTU fondu úrovně Standard s maximální velikostí 100 GB downsized na standardní fond s 50 eDTU, pak náklady na úložiště platí od cílový fond podporuje maximální velikost 100 GB a jeho velikost zahrnutého úložiště je pouze 50 GB. Ano velikost dodatečného úložiště je 100 GB – 50 GB = 50 GB. Ceny za dodatečné úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud se skutečné množství využité je menší než velikost zahrnutého úložiště, pak toho dalších poplatků se lze vyvarovat snížením maximální velikost databáze na objemu zahrnutého v ceně.

### <a name="billing-during-rescaling"></a>Fakturace během změny měřítka

Se vám účtovat každá hodina existence databáze pomocí nejvyšší úroveň služby a vypočítat velikost, která během této hodiny využívalo, bez ohledu na využití nebo na to, jestli byl databáze aktivní kratší dobu než hodinu. Například pokud vytvoření izolované databáze a po pěti minutách ji odstraníte vyúčtování projeví účtovat jedna hodina používání databáze.

## <a name="change-elastic-pool-storage-size"></a>Změnit velikost úložiště elastického fondu

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

- Úložiště lze zřídit až do limitu maximální velikost:

  - Pro úložiště úrovně standard nebo obecné účely zvětšit nebo zmenšit velikost v přírůstcích po 10 GB
  - Pro úložiště úrovně premium nebo pro důležité obchodní informace úrovně služeb, zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- Zvýšením nebo snížením své maximální velikosti se dá zřídit úložiště pro elastický fond.
- Cena úložiště pro elastický fond je velikost úložiště vynásobí jednotkovou cenu úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

- Cena eDTU pro elastický fond obsahuje objem úložiště bez dalších poplatků. Dodatečné úložiště nad rámec objemu zahrnutého v ceně je možné zřídit za poplatek až po limit maximální velikosti, v přírůstcích po 250 GB až 1 TB a potom dokupuje se násobek 256 GB nad rámec 1 TB. Částky zahrnutého úložiště a omezení maximální velikosti najdete v tématu [elastického fondu: velikosti úložiště a výpočty velikostí](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- Dodatečné úložiště pro elastický fond je možné zřídit zvýšením jeho pomocí maximální velikosti [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update), nebo [rozhraní REST API ](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- Cena dodatečného úložiště pro elastický fond je velikost dodatečného úložiště vynásobí jednotkovou cenu dodatečné úložiště na úrovni služby. Podrobnosti o cenách dodatečného úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Další postup

Celkové omezení prostředků najdete v tématu [omezení prostředků založený na virtuálních jádrech SQL Database – elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md) a [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-elastic-pools.md).

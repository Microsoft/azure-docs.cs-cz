---
title: Azure SQL Data Warehouse zpráva k vydání verze. června 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 86aadcbdd8d7168440726d6dbed996629cad3ff7
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285329"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Červenec 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny do července 2018.


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Rozlišovací schopnosti pro různé oblasti a server obnovení
Teď můžete obnovit napříč oblastmi a servery s použitím libovolného bodu obnovení místo výběru geograficky redundantní zálohy, které se provádějí každých 24 hodin. Pro obě body definované uživatelem nebo automatické obnovení povolení rozlišovací schopnosti pro další ochranu dat jsou podporovány pro různé oblasti a server obnovení. Další body obnovení k dispozici můžete si být jisti, že bude váš datový sklad logicky konzistentní při obnovování napříč oblastmi.

![Obnovení Azure SQL Data Warehouse Command -](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![možnosti obnovení – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Další informace najdete v tématu [Accelerated a body obnovení flexibilní](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogový příspěvek.

## <a name="20-minute-restorations"></a>Obnovení na 20 minut
SQL Data Warehouse teď nabízí obnovení každý datový sklad **méně než 20 minut** ve stejné oblasti bez ohledu na velikost databáze. Čas obnovení platí pro obnovení je stejný nebo jiný logický server ve stejné oblasti. Kromě toho proces snímku vylepšili jsme zkrátit čas potřebný k vytvoření bodu obnovení. V nižší úrovně výkonu (nižší nastavení jednotek DWU), zlepšení je *2 x snížení* v čase pro vytvoření snímku.

Další informace najdete v tématu [Accelerated a body obnovení flexibilní](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) blogový příspěvek.

## <a name="custom-restoration-configurations"></a>Vlastní obnovení konfigurace
Nyní můžete změnit úroveň výkonu (DWU) při obnovení na portálu Azure portal. Můžete také obnovit službu upgradovaná data warehouse Gen2. Obnovení na 2. generace instanci, můžete nyní hodnotit dopad Gen2 před [upgrade datového skladu Gen1](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Konfigurace vlastní obnovení – Azure SQL Data Warehouse](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>PROCEDUŘE SP_DESCRIBE_UNDECLARED_PARAMETERS
[Proceduře sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) uloženou proceduru se často používá nástroji získat metadata o parametrech v dávce Transact-SQL. Postup vrátí jeden řádek pro každý parametr v dávce s odvozeným typem informace pro tento parametr. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Sada výsledků obsahuje metadata o `@id` parametr (částečné výsledky zobrazeny)
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```

## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvoření SQL Data Warehouse] [vytvoření SQL Data Warehouse] a [ukázková data načíst] [ukázková data načíst]. Pokud jste ještě do Azure, můžete zjistit [Glosář Azure] [Glosář Azure] užitečné jako narazíte na novou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse

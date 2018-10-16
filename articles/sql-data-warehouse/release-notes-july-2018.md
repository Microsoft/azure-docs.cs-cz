---
title: Azure SQL Data Warehouse zpráva k vydání verze. července 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: c7d2211ca69fcd18588ea1b20b638b2970b8439c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318836"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Červenec 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny do července 2018.

## <a name="lightning-fast-query-performance"></a>Bleskově rychlý dotaz výkonu
[Azure SQL Data Warehouse](https://aka.ms/sqldw) nastaví nové srovnávacího testu výkonu po zavedení služby okamžitý přístup k datům, která zvyšuje náhodné operace. Okamžitý přístup k datům snižuje režijní náklady pro operace přesunu dat pomocí přímá odpověď ze serveru SQL do systému SQL Server native datové operace. Integrace s modulem SQL serveru přímo v případě přesunu dat znamená, že je teď SQL Data Warehouse **67 % rychlejší než Amazon Redshift** pomocí úlohy odvozen od standardní uznávané oborové [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/).

![Azure SQL Data Warehouse je rychlejší a levnější než Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>zdroj: [sestavy analytik společnosti Gigaom Research: datového skladu v cloudu srovnávacích testů](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Nad rámec výkonu modulu runtime [společnosti Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) sestavy také měří poměr cena – výkon kvantifikovat USD náklady na konkrétní úlohy. SQL Data Warehouse byla **23 procent levnější** než Redshift pro úlohy s 30 TB. S SQL Data Warehouse umožňuje Elasticky škálovat výpočetní prostředky a pozastavit a obnovit úlohy zákazníci platí pouze v případě, že služba se používá, dále snižuje náklady.
![Azure SQL Data Warehouse je rychlejší a levnější než Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>zdroj: [sestavy analytik společnosti Gigaom Research: datového skladu v cloudu srovnávacích testů](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

### <a name="query-concurrency"></a>Dotaz souběžnosti
SQL Data Warehouse také zajišťuje, že data jsou přístupné napříč vaší organizací. Microsoft má vylepšenou služba podporovala 128 souběžných dotazů tak, aby více uživatelů stejné databáze můžete dotazovat a nebudou blokovat jiné požadavky. Porovnání Amazon Redshift omezuje maximální počet souběžných dotazů na 50, omezení přístupu k datům v rámci organizace.

SQL Data Warehouse nabízí tyto dotazu výkon a dotaz souběžnosti přínosy bez jakékoli zvýšení ceny a vytváření při jeho jedinečná architektura s oddělující úložiště a výpočetního výkonu.

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
## <a name="sprefreshsqlmodule"></a>ULOŽENÉ PROCEDURY SP_REFRESHSQLMODULE
[Uložené procedury sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) uložená procedura aktualizuje metadata pro objekt databáze, pokud má základní metadata začnou být zastaralé z důvodu změny podkladových objektů. Tato situace může nastat, pokud se změní základní tabulky pro zobrazení a zobrazení nebyl byly znovu vytvořeny. To vám ušetří na krok odstranit a znovu vytvořit závislé objekty.

Následující příklad ukazuje zobrazení, který je zastaralá, protože podkladové tabulky změn. Můžete si všimnout, že je správný pro první změny sloupců (1 až Mollie) data, ale název sloupce není platný a druhý sloupec není k dispozici. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

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
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

---
title: Jednotky datového skladu (DWU) pro vyhrazený fond SQL (dříve SQL DW)
description: Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU) pro optimalizaci ceny a výkonu a změnu počtu jednotek.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d9d5d4009ad40eecee26271b726c6a3e9aeb8b6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459191"
---
# <a name="data-warehouse-units-dwus-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Jednotky datového skladu (DWU) pro vyhrazený fond SQL (dříve SQL DW) ve službě Azure synapse Analytics

Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU) pro optimalizaci ceny a výkonu a změnu počtu jednotek.

## <a name="what-are-data-warehouse-units"></a>Co jsou jednotky datového skladu

[Vyhrazený fond SQL (dříve SQL DW)](sql-data-warehouse-overview-what-is.md) představuje kolekci analytických prostředků, které se zřídí. Analytické prostředky jsou definovány jako kombinace procesoru, paměti a vstupně-výstupních operací.

Tyto tři prostředky jsou seskupené do jednotek výpočetního škálování s názvem jednotky datového skladu (DWU). DWU představuje abstraktní normalizovanou míru výpočetních prostředků a výkonu.

Změna úrovně služby mění počet DWU, které jsou k dispozici pro systém, což zase upravuje výkon a náklady vašeho systému.

Pro vyšší výkon můžete zvýšit počet jednotek datového skladu. Pro méně výkon omezte jednotky datového skladu. Náklady na úložiště a výpočetní prostředky se účtují zvlášť, takže změna jednotek datového skladu nemá vliv na náklady na úložiště.

Výkon pro jednotky datového skladu je založen na těchto metrikách úloh datového skladu:

- Jak rychle standardní vyhrazený fond SQL (dřív SQL DW) dotaz může kontrolovat velký počet řádků a pak provádět komplexní agregace. Tato operace je v/v a náročná na výkon procesoru.
- Jak rychle vyhrazený fond SQL (dřív SQL DW) může ingestovat data z Azure Storage objektů BLOB nebo Azure Data Lake. Tato operace je náročná na síť a využití procesoru.
- Jak rychle [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkaz T-SQL může zkopírovat tabulku. Tato operace zahrnuje čtení dat z úložiště, jejich distribuci napříč uzly zařízení a zpětný zápis do úložiště. Tato operace je náročná na procesor, v/v a na síť.

Zvyšování DWU:

- Lineárně mění výkon systému pro kontroly, agregace a příkazy CTAS.
- Zvyšuje počet čtenářů a zapisovačů pro operace základního zatížení.
- Zvyšuje maximální počet souběžných dotazů a souběžných slotů.

## <a name="service-level-objective"></a>Service Level Objective (cíl úrovně služby)

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje náklady a úroveň výkonu datového skladu. Úrovně služeb pro Gen2 se měří v jednotkách služby COMPUTE Data Warehouse (cDWU), například DW2000c. Úrovně služeb Gen1 se měří v DWU, například DW2000.

Cíl na úrovni služby (SLO) je nastavení škálovatelnosti, které určuje náklady a úroveň výkonu vyhrazeného fondu SQL (dříve SQL DW). Úrovně služeb pro vyhrazený fond SQL Gen2 (dřív SQL DW) se měří v jednotkách datového skladu (DWU), například DW2000c.

> [!NOTE]
> Vyhrazený fond SQL (dřív SQL DW) Gen2 nedávno přidané další možnosti škálování pro podporu výpočetních úrovní, které jsou nízké jako 100 cDWU. Stávající datové sklady, které jsou aktuálně na Gen1, které vyžadují nižší výpočetní úrovně, teď můžou upgradovat na Gen2 v oblastech, které jsou momentálně dostupné bez dalších nákladů.  Pokud vaše oblast ještě není podporovaná, můžete i nadále upgradovat na podporovanou oblast. Další informace najdete v tématu [upgrade na Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

V T-SQL nastavení SERVICE_OBJECTIVE určuje úroveň služby a úroveň výkonu pro vyhrazený fond SQL (dřív SQL DW).

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Úrovně výkonu a jednotky datového skladu

Každá úroveň výkonu používá pro své jednotky datového skladu mírně odlišnou měrnou jednotku. Tento rozdíl se odráží na faktuře, protože jednotka škálování je přímo převedená na fakturaci.

- Gen1 datové sklady se měří v jednotkách datového skladu (DWU).
- Gen2 datové sklady se měří v jednotkách výpočetního datového skladu (cDWUs).

DWU i cDWUs podporují škálování výpočetních prostředků nahoru i dolů a pozastaví výpočetní výkon, pokud nepotřebujete datový sklad používat. Tyto operace jsou všechny na vyžádání. Gen2 používá k vylepšení výkonu místní mezipaměť na disku na výpočetních uzlech. Při škálování nebo pozastavení systému se mezipaměť zruší a proto je potřeba zahřívání mezipaměti, aby se dosáhlo optimálního výkonu.  

Každý SQL Server (například myserver.database.windows.net) má kvótu pro [databázovou jednotku (DTU)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) , která umožňuje určit počet jednotek datového skladu. Další informace najdete v tématu [omezení kapacity správy úloh](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="capacity-limits"></a>Omezení kapacity

Každý SQL Server (například myserver.database.windows.net) má kvótu pro [databázovou jednotku (DTU)](../../sql-database/sql-database-what-is-a-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , která umožňuje určit počet jednotek datového skladu. Další informace najdete v tématu [omezení kapacity správy úloh](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotek datového skladu potřebuji

Ideální počet jednotek datového skladu závisí hodně na vašich úlohách a na množství dat, která jste do systému načetli.

Postup pro vyhledání nejlepšího DWU pro vaši úlohu:

1. Začněte výběrem menšího DWU.
2. Monitorujte výkon aplikace při testování zátěžových dat do systému a sledujte počet DWU vybraných v porovnání s výkonem, které sledujete.
3. Identifikujte všechny další požadavky na pravidelné doby aktivity špičky. Úlohy, které zobrazují významné špičky a žlaby v aktivitě, se můžou potřebovat často škálovat.

Vyhrazený fond SQL (dřív SQL DW) je systém škálování na více systémů, který dokáže zřídit obrovské množství výpočetních a dotazových výraznou množství dat.

Pokud chcete zobrazit jeho skutečné možnosti pro škálování, zejména u větších DWU, doporučujeme škálovat datovou sadu při škálování, abyste měli jistotu, že budete mít k dispozici dostatek dat k vytvoření kanálu pro procesory. Pro testování škálování doporučujeme používat aspoň 1 TB.

> [!NOTE]
>
> Výkon dotazů se zvyšuje s větší paralelismuou, pokud je možné rozdělit práci mezi výpočetními uzly. Pokud zjistíte, že škálování nemění váš výkon, možná budete muset vyladit návrh tabulky a dotazy. Pokyny k ladění dotazů najdete v tématu [Správa uživatelských dotazů](cheat-sheet.md).

## <a name="permissions"></a>Oprávnění

Změna jednotek datového skladu vyžaduje oprávnění popsaná v [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Předdefinované role Azure, jako je Přispěvatel databáze SQL a SQL Server přispěvatel, můžou měnit nastavení DWU.

## <a name="view-current-dwu-settings"></a>Zobrazit aktuální nastavení DWU

Chcete-li zobrazit aktuální nastavení DWU:

1. Otevřete Průzkumník objektů systému SQL Server v aplikaci Visual Studio.
2. Připojte se k hlavní databázi přidružené k logickému SQL serveru.
3. Vyberte v zobrazení dynamické správy sys.database_service_objectives. Tady je příklad:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Změnit jednotky datového skladu

### <a name="azure-portal"></a>portál Azure

Postup změny DWU:

1. Otevřete [Azure Portal](https://portal.azure.com), otevřete databázi a klikněte na možnost **škálovat**.

2. V části **škálovat** přesuňte posuvník doleva nebo doprava, abyste změnili nastavení DWU.

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ke změně DWU použijte rutinu [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) prostředí PowerShell. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Další informace najdete v tématu [rutiny PowerShellu pro vyhrazený fond SQL (dřív SQL DW)](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

### <a name="t-sql"></a>T-SQL

Pomocí T-SQL můžete zobrazit aktuální DWUsettings, změnit nastavení a podívat se na průběh.

Postup změny DWU:

1. Připojte se k hlavní databázi přidružené k vašemu serveru.
2. Použijte příkaz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) TSQL. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000c pro MySQLDW databáze.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit DWU, použijte příkaz [Create nebo Update Database](/rest/api/sql/databases/createorupdate) REST API. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000c pro databázi MySQLDW, která je hostována na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Další REST API příklady najdete v tématu [rozhraní REST API pro vyhrazený fond SQL (dřív SQL DW)](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="check-status-of-dwu-changes"></a>Kontrolovat stav DWU změn

Dokončení DWU změn může trvat několik minut. Pokud provádíte automatické škálování, zvažte implementaci logiky, abyste zajistili, že byly před pokračováním v jiné akci dokončeny určité operace.

Kontrola stavu databáze prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci. Portál poskytuje oznámení po dokončení operace a aktuálních stavech databáze, ale neumožňuje programovou kontrolu stavu.

Nelze kontrolovat stav databáze pro operace škálování na více instancí pomocí Azure Portal.

Postup kontroly stavu DWU změn:

1. Připojte se k hlavní databázi přidružené k vašemu serveru.
2. Odešlete následující dotaz pro kontrolu stavu databáze.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Odešlete následující dotaz pro kontrolu stavu operace.

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

Tento DMV vrací informace o různých operacích správy vyhrazeného fondu SQL (dříve SQL DW), jako je například operace a stav operace, která je buď IN_PROGRESS nebo dokončeno.

## <a name="the-scaling-workflow"></a>Pracovní postup škálování

Když zahájíte operaci škálování, systém nejdřív odchode všechny otevřené relace a vrátí otevřené transakce, aby se zajistil konzistentní stav. V případě operací škálování dojde k škálování pouze po dokončení tohoto vrácení transakce zpět.  

- V případě operace škálování se systémem odpojí všechny výpočetní uzly, zřídí další výpočetní uzly a pak se znovu připojí k vrstvě úložiště.
- V případě operace horizontálního rozšíření systému odpojí všechny výpočetní uzly a pak znovu připojí pouze potřebné uzly do vrstvy úložiště.

## <a name="next-steps"></a>Další kroky

Další informace o správě výkonu najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md) a [omezení pro využití paměti a souběžnosti](memory-concurrency-limits.md).

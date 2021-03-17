---
title: Synapse využití prostředků SQL
description: Přečtěte si o modelech spotřeby synapse SQL ve službě Azure synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 03144d478be0053ac77709132b08cdf17b062fb0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666298"
---
# <a name="synapse-sql-resource-consumption"></a>Synapse využití prostředků SQL

Tento článek popisuje modely spotřeby prostředků synapse SQL.

## <a name="serverless-sql-pool"></a>Bezserverový fond SQL

Fond SQL bez serveru je platíte za službu Query Service, která nevyžaduje, abyste vybrali správnou velikost. Systém se automaticky přizpůsobí podle vašich požadavků a uvolňuje vám od správy infrastruktury a vybírá správnou velikost pro vaše řešení.

## <a name="dedicated-sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Vyhrazený fond SQL – jednotky datového skladu (DWU) a jednotky datového skladu COMPUTE (cDWUs)

Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU) pro optimalizaci ceny a výkonu a změnu počtu jednotek.

### <a name="data-warehouse-units"></a>Jednotky datového skladu

Synapse fond SQL představuje kolekci analytických prostředků, které se zřídí. Analytické prostředky jsou definovány jako kombinace procesoru, paměti a vstupně-výstupních operací. Tyto tři prostředky jsou seskupené do jednotek výpočetního škálování s názvem jednotky datového skladu (DWU). DWU představuje abstraktní normalizovanou míru výpočetních prostředků a výkonu. Změna úrovně služby změní počet DWU, které jsou k dispozici pro systém. Tato změna pak upraví výkon a náklady na váš systém.

Pro vyšší výkon můžete zvýšit počet jednotek datového skladu. Pro méně výkon omezte jednotky datového skladu. Náklady na úložiště a výpočetní prostředky se účtují zvlášť, takže změna jednotek datového skladu nemá vliv na náklady na úložiště.

Výkon pro jednotky datového skladu je založen na těchto metrikách úloh datového skladu:

- Jak rychle standardní dotaz na datové sklady může kontrolovat velký počet řádků a pak provádět komplexní agregace. Tato operace je v/v a náročná na výkon procesoru.
- Jak rychle může datový sklad ingestovat data z Azure Storage objektů BLOB nebo Azure Data Lake. Tato operace je náročná na síť a využití procesoru.
- Jak rychle [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) příkaz T-SQL může zkopírovat tabulku. Tato operace zahrnuje čtení dat z úložiště, jejich distribuci napříč uzly zařízení a zpětný zápis do úložiště. Tato operace je náročná na procesor, v/v a na síť.

Zvyšování DWU:

- Lineárně mění výkon systému pro kontroly, agregace a příkazy CTAS.
- Zvyšuje počet čtenářů a zapisovačů pro operace základního zatížení.
- Zvyšuje maximální počet souběžných dotazů a souběžných slotů.

### <a name="service-level-objective"></a>Service Level Objective (cíl úrovně služby)

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje náklady a úroveň výkonu datového skladu. Úrovně služeb pro Gen2 se měří v jednotkách služby COMPUTE Data Warehouse (cDWU), například DW2000c. Úrovně služeb Gen1 se měří v DWU, například DW2000.

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje náklady a úroveň výkonu datového skladu. Úrovně služeb pro vyhrazený fond SQL Gen2 se měří v jednotkách datového skladu (DWU), například DW2000c.

> [!NOTE]
> Azure synapse Analytics Gen2 nedávno přidaly další možnosti škálování pro podporu výpočetních úrovní, které jsou nízké jako 100 cDWU. Stávající datové sklady, které jsou aktuálně na Gen1, které vyžadují nižší výpočetní úrovně, teď můžou upgradovat na Gen2 v oblastech, které jsou momentálně dostupné bez dalších nákladů.  Pokud vaše oblast ještě není podporovaná, můžete i nadále upgradovat na podporovanou oblast. Další informace najdete v tématu [upgrade na Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

V T-SQL nastavení SERVICE_OBJECTIVE určuje úroveň služby a úroveň výkonu pro vyhrazený fond SQL.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Úrovně výkonu a jednotky datového skladu

Každá úroveň výkonu používá pro své jednotky datového skladu mírně odlišnou měrnou jednotku. Tento rozdíl se odráží na faktuře, protože jednotka škálování je přímo převedená na fakturaci.

- Gen1 datové sklady se měří v jednotkách datového skladu (DWU).
- Gen2 datové sklady se měří v jednotkách výpočetního datového skladu (cDWUs).

DWU i cDWUs podporují škálování výpočetních prostředků nahoru i dolů a pozastaví výpočetní výkon, pokud nepotřebujete datový sklad používat. Tyto operace jsou všechny na vyžádání. Gen2 používá k vylepšení výkonu místní mezipaměť na disku na výpočetních uzlech. Při škálování nebo pozastavení systému se mezipaměť zruší a proto je potřeba zahřívání mezipaměti, aby se dosáhlo optimálního výkonu.  

Při zvyšování počtu jednotek datového skladu lineárně roste výpočetní prostředky. Gen2 poskytuje nejlepší výkon dotazů a nejvyšší měřítko. Gen2 systémy také využívají mezipaměť.

#### <a name="capacity-limits"></a>Omezení kapacity

Každý SQL Server (například myserver.database.windows.net) má kvótu pro [databázovou jednotku (DTU)](../../azure-sql/database/service-tiers-dtu.md) , která umožňuje určit počet jednotek datového skladu. Další informace najdete v tématu [omezení kapacity správy úloh](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="assess-the-number-of-data-warehouse-units-you-need"></a>Vyhodnoťte počet jednotek datového skladu, které potřebujete.

Ideální počet jednotek datového skladu závisí hodně na vašich úlohách a na množství dat, která jste do systému načetli.

Postup pro vyhledání nejlepšího DWU pro vaši úlohu:

1. Začněte výběrem menšího DWU.
2. Monitorujte výkon aplikace při testování zátěžových dat do systému a sledujte počet DWU vybraných v porovnání s výkonem, které sledujete.
3. Identifikujte všechny další požadavky na pravidelné doby aktivity špičky. Úlohy, které zobrazují významné špičky a žlaby v aktivitě, se můžou potřebovat často škálovat.

Fond SQL je systém škálování na více systémů, který dokáže zřídit obrovské množství výpočetních a dotazových výraznou množství dat. Pokud chcete zobrazit jeho skutečné možnosti pro škálování, zejména u větších DWU, doporučujeme škálovat datovou sadu při škálování, abyste měli jistotu, že budete mít k dispozici dostatek dat k vytvoření kanálu pro procesory. Pro testování škálování doporučujeme používat aspoň 1 TB.

> [!NOTE]
>
> Výkon dotazů se zvyšuje s větší paralelismuou, pokud je možné rozdělit práci mezi výpočetními uzly. Pokud zjistíte, že škálování nemění váš výkon, možná budete muset vyladit návrh tabulky a dotazy. Pokyny k ladění dotazů najdete v tématu [Správa uživatelských dotazů](../overview-terminology.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="permissions"></a>Oprávnění

Změna jednotek datového skladu vyžaduje oprávnění popsaná v [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Předdefinované role Azure, jako je Přispěvatel databáze SQL a SQL Server přispěvatel, můžou měnit nastavení DWU.

#### <a name="view-current-dwu-settings"></a>Zobrazit aktuální nastavení DWU

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

### <a name="change-data-warehouse-units"></a>Změnit jednotky datového skladu

#### <a name="azure-portal"></a>portál Azure

Postup změny DWU:

1. Otevřete [Azure Portal](https://portal.azure.com), otevřete databázi a vyberte možnost **škálovat**.

2. V části **škálovat** přesuňte posuvník doleva nebo doprava, abyste změnili nastavení DWU.

3. Vyberte **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na tlačítko **Ano** potvrďte nebo **ne** .

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ke změně DWU použijte rutinu [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) prostředí PowerShell. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Další informace najdete v tématu [rutiny PowerShellu pro Azure synapse Analytics](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) .

#### <a name="t-sql"></a>T-SQL

Pomocí T-SQL můžete zobrazit aktuální DWUsettings, změnit nastavení a podívat se na průběh.

Postup změny DWU:

1. Připojte se k hlavní databázi přidružené k vašemu serveru.
2. Použijte příkaz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) TSQL. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000c pro MySQLDW databáze.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit DWU, použijte příkaz [Create nebo Update Database](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) REST API. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000c pro databázi MySQLDW, která je hostována na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Další příklady REST API najdete v tématu [rozhraní REST API pro Azure synapse Analytics](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Kontrolovat stav DWU změn

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

Tento DMV vrací informace o různých operacích správy vyhrazeného fondu SQL, jako je například operace a stav operace, který je buď IN_PROGRESS nebo dokončen.

### <a name="the-scaling-workflow"></a>Pracovní postup škálování

Když zahájíte operaci škálování, systém nejdřív odchode všechny otevřené relace a vrátí otevřené transakce, aby se zajistil konzistentní stav. V případě operací škálování dojde k škálování pouze po dokončení tohoto vrácení transakce zpět.  

- V případě operace škálování se systémem odpojí všechny výpočetní uzly, zřídí další výpočetní uzly a pak se znovu připojí k vrstvě úložiště.
- V případě operace horizontálního rozšíření systému odpojí všechny výpočetní uzly a pak znovu připojí pouze potřebné uzly do vrstvy úložiště.

## <a name="next-steps"></a>Další kroky

Další informace o správě výkonu najdete v tématu [třídy prostředků pro správu úloh](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) a [omezení pro využití paměti a souběžnosti](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
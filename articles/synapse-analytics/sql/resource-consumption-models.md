---
title: Syn zdrojů SQL synapse Analytics
description: Další informace o modelech spotřeby synapse SQL v Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424857"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Azure Synapse Analytics spotřeba prostředků SQL

Tento článek popisuje modely spotřeby prostředků Synapse SQL (preview).

## <a name="sql-on-demand"></a>SQL na vyžádání

SQL na vyžádání je služba platby za dotaz, která nevyžaduje výběr správné velikosti. Systém se automaticky přizpůsobí na základě vašich požadavků, čímž vás osvobodí od správy infrastruktury a výběru správné velikosti pro vaše řešení.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Fond SQL – jednotky datového skladu (DWU) a výpočetní jednotky datového skladu (cDWU)

Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU) pro optimalizaci ceny a výkonu a jak změnit počet jednotek.

### <a name="what-are-data-warehouse-units"></a>Co jsou jednotky datového skladu

Fond SQL Synapse představuje kolekci analytických prostředků, které jsou zřizovány. Analytické prostředky jsou definovány jako kombinace procesoru, paměti a vi. Tyto tři prostředky jsou sdružené do jednotek výpočetního měřítka nazývaných jednotky datového skladu (DW). DWU představuje abstraktní normalizovanou míru výpočetních prostředků a výkonu. Změna úrovně služeb změní počet dlus, které jsou k dispozici v systému, což zase upravuje výkon a náklady na váš systém.

Pro vyšší výkon můžete zvýšit počet jednotek datového skladu. Pro nižší výkon snižte jednotky datového skladu. Náklady na úložiště a výpočetní prostředky se účtují zvlášť, takže změna jednotek datového skladu nemá vliv na náklady na úložiště.

Výkon pro jednotky datového skladu je založen na těchto metrikách zatížení datového skladu:

- Jak rychle může standardní dotaz na ukládání dat prohledávat velký počet řádků a potom provést komplexní agregaci. Tato operace je náročná na vstupně-up a procesor.
- Jak rychle může datový sklad ingestovat data z objektů Blobs azure storage nebo Azure Data Lake. Tato operace je náročná na síť a procesor.
- Jak rychle [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) může příkaz T-SQL zkopírovat tabulku. Tato operace zahrnuje čtení dat z úložiště, jejich distribuci mezi uzly zařízení a zápis do úložiště znovu. Tato operace je náročná na procesor, vi a síť.

Zvýšení DWUs:

- Lineárně změní výkon systému pro skenování, agregace a příkazy CTAS
- Zvyšuje počet čtecích a zapisovatelů pro operace zatížení PolyBase.
- Zvyšuje maximální počet souběžných dotazů a slotů souběžnosti.

### <a name="service-level-objective"></a>Service Level Objective (cíl úrovně služby)

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje úroveň nákladů a výkonu datového skladu. Úrovně služeb pro Gen2 se měří v jednotkách výpočetního datového skladu (cDWU), například DW2000c. Úrovně služeb Gen1 se měří v DW, například DW2000.

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje úroveň nákladů a výkonu datového skladu. Úrovně služeb pro Fond Gen2 SQL se měří v jednotkách datového skladu (DWU), například DW2000c.

> [!NOTE]
> Azure SQL Data Warehouse Gen2 nedávno přidal další škálovací funkce pro podporu výpočetních vrstev již od 100 cDWU. Existující datové sklady aktuálně na Gen1, které vyžadují nižší výpočetní vrstvy, teď můžou upgradovat na Gen2 v oblastech, které jsou momentálně dostupné bez dalších nákladů.  Pokud vaše oblast ještě není podporovaná, můžete přesto upgradovat na podporovanou oblast. Další informace naleznete v [tématu Upgrade na Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

V T-SQL nastavení SERVICE_OBJECTIVE určuje úroveň služby a úroveň výkonu pro váš fond SQL.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Úrovně výkonu a jednotky datového skladu

Každá úroveň výkonu používá pro své jednotky datového skladu mírně odlišnou měrnou jednotku. Tento rozdíl se projeví na faktuře jako jednotka škálování přímo překládá k fakturaci.

- Datové sklady Gen1 se měří v jednotkách datového skladu (DW.
- Datové sklady Gen2 se měří ve výpočetních jednotkách datového skladu (cDWU).

DwU i cDwu podporují škálování výpočetních prostředků nahoru nebo dolů a pozastavení výpočetních prostředků, když nepotřebujete použít datový sklad. Všechny tyto operace jsou na vyžádání. Gen2 používá místní diskovou mezipaměť na výpočetních uzlech ke zlepšení výkonu. Při škálování nebo pozastavení systému je zneplatněna mezipaměť, takže před dosažením optimálního výkonu je vyžadováno období zahřívání mezipaměti.  

Při zvyšování jednotek datového skladu lineárně zvyšujete výpočetní prostředky. Gen2 poskytuje nejlepší výkon dotazu a nejvyšší škálování. Systémy Gen2 také maximálně využívají mezipaměť.

#### <a name="capacity-limits"></a>Omezení kapacity

Každý server SQL (například myserver.database.windows.net) má kvótu [databázové transakční jednotky (DTU),](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) která umožňuje určitý počet jednotek datového skladu. Další informace naleznete v tématu [omezení kapacity správy úloh](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotek datového skladu potřebuji

Ideální počet jednotek datového skladu do značné míry závisí na vaší pracovní zátěži a množství dat, která jste načetli do systému.

Kroky pro nalezení nejlepšího dwu pro vaši pracovní zátěž:

1. Začněte výběrem menšího DWU.
2. Sledujte výkon aplikace při testování načítání dat do systému a sledujte počet vybraných dus ve srovnání s výkonem, který sledujete.
3. Určete jakékoli další požadavky na periodická období špičkové aktivity. Úlohy, které vykazují významné špičky a koryta v aktivitě může být nutné škálovat často.

SQL fond je horizontální navýšení kapacity systému, který může zřídit obrovské množství výpočetních a dotazů značné množství dat. Chcete-li zobrazit jeho skutečné možnosti pro škálování, zejména na větší chudinském procesoru, doporučujeme škálování sady dat při škálování, abyste zajistili, že máte dostatek dat pro přenos procesorů. Pro škálování testování, doporučujeme použít alespoň 1 TB.

> [!NOTE]
>
> Výkon dotazu se zvyšuje pouze s větší paralelizace, pokud práce lze rozdělit mezi výpočetní uzly. Pokud zjistíte, že škálování nemění výkon, budete muset vyladit návrh tabulky nebo dotazy. Pokyny k ladění dotazů naleznete v [tématu Správa uživatelských dotazů](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="permissions"></a>Oprávnění

Změna jednotek datového skladu vyžaduje oprávnění popsaná v [databázi ALTER](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Předdefinované role pro prostředky Azure, jako je sql db přispěvatel a přispěvatel SQL Serveru, můžou změnit nastavení DWU.

#### <a name="view-current-dwu-settings"></a>Zobrazit aktuální nastavení DWU

Zobrazení aktuálního nastavení DWU:

1. Sem otevřete Průzkumníka objektů serveru SQL Server v sadě Visual Studio.
2. Připojte se k hlavní databázi přidružené k logickému serveru SQL Database.
3. Vyberte ze zobrazení dynamické správy sys.database_service_objectives. Zde naleznete příklad:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Změna jednotek datového skladu

#### <a name="azure-portal"></a>portál Azure

Změna du-s:

1. Otevřete [portál Azure](https://portal.azure.com), otevřete databázi a klikněte na **Škálovat**.

2. V části **Měřítko**posuňte posuvník doleva nebo doprava, abyste změnili nastavení DWU.

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Chcete-li změnit dwu, použijte [Rutina Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) PowerShell. Následující příklad nastaví cíl úrovně služby na DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Další informace naleznete v [tématu Rutiny prostředí PowerShell pro SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

S T-SQL můžete zobrazit aktuální DWUsettings, změnit nastavení a zkontrolovat průběh.

Změna du-s:

1. Připojte se k hlavní databázi přidružené k logickému serveru SQL Database.
2. Použijte příkaz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL. Následující příklad nastaví cíl na úrovni služby dw1000c pro databázi MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit dwu, použijte [vytvořit nebo aktualizovat databázi](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) REST API. Následující příklad nastaví cíl úrovně služby na DW1000c pro databázi MySQLDW, která je hostována na serveru MyServer. Server se nachází ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Další příklady rozhraní REST API naleznete v tématu [REST API for SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Zkontrolovat stav změn DWU

Provedení změn DWU může trvat několik minut. Pokud máte měřítko automaticky, zvažte implementaci logiky, abyste zajistili, že některé operace byly dokončeny před pokračováním v jiné akci.

Kontrola stavu databáze prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci. Portál poskytuje oznámení po dokončení operace a databáze aktuální stav, ale neumožňuje programovou kontrolu stavu.

Nelze zkontrolovat stav databáze pro operace horizontálního navýšení kapacity s portálem Azure.

Kontrola stavu změn DWU:

1. Připojte se k hlavní databázi přidružené k logickému serveru SQL Database.
2. Odešlete následující dotaz ke kontrole stavu databáze.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Odešlete následující dotaz ke kontrole stavu operace

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Tento DMV vrátí informace o různých operacích správy ve fondu SQL, jako je operace a stav operace, která je buď IN_PROGRESS nebo DOKONČENA.

### <a name="the-scaling-workflow"></a>Pracovní postup škálování

Při spuštění operace škálování systém nejprve zabije všechny otevřené relace, vrácení zpět všechny otevřené transakce k zajištění konzistentního stavu. Pro operace škálování škálování dochází pouze po dokončení této transakční vrácení zpět.  

- Pro operace škálování systémodpojí všechny výpočetní uzly, zřídí další výpočetní uzly a pak se znovu připojí k vrstvě úložiště.
- Pro operaci škálování dolů systém odpojí všechny výpočetní uzly a pak znovu připojí pouze potřebné uzly do vrstvy úložiště.

## <a name="next-steps"></a>Další kroky

Další informace o správě výkonu najdete v [tématu Třídy prostředků pro správu úloh](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) a [omezení paměti a souběžnosti](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

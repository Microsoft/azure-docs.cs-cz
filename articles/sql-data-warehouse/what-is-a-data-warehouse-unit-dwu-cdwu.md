---
title: Jednotky datového skladu (DWU, cDWUs) v Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU, cDWUs) pro optimalizaci ceny a výkonu a změnu počtu jednotek.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 05/30/2019
ms.author: martinle
ms.reviewer: igorstan
mscustom: sqlfreshmay19
ms.openlocfilehash: 282fab70e3b6d1fcf81814b2dd599259e2396fb3
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036046"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednotky datového skladu (DWU) a jednotky datového skladu COMPUTE (cDWUs)

Doporučení pro výběr ideálního počtu jednotek datového skladu (DWU, cDWUs) pro optimalizaci ceny a výkonu a změnu počtu jednotek.

## <a name="what-are-data-warehouse-units"></a>Co jsou jednotky datového skladu

Azure SQL Data Warehouse CPU, paměti a vstupně-výstupních operací jsou součástí jednotek výpočetních prostředků s názvem jednotky datového skladu (DWU). DWU představuje abstraktní a normalizovanou míru výpočetních prostředků a výkonu. Změna úrovně služby mění počet DWU, které jsou k dispozici pro systém, což zase upravuje výkon a náklady vašeho systému.

Pro vyšší výkon můžete zvýšit počet jednotek datového skladu. Pro méně výkon omezte jednotky datového skladu. Náklady na úložiště a výpočetní výkon se účtují samostatně, takže změna jednotek datového skladu nemá vliv na náklady na úložiště.

Výkon pro jednotky datového skladu je založen na těchto metrikách úloh datového skladu:

- Jak rychle standardní dotaz na datové sklady může kontrolovat velký počet řádků a pak provádět komplexní agregace. Tato operace je v/v a náročná na výkon procesoru.
- Jak rychle může datový sklad ingestovat data z Azure Storage objektů BLOB nebo Azure Data Lake. Tato operace je náročná na síť a využití procesoru.
- Jak rychle [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkaz T-SQL může zkopírovat tabulku. Tato operace zahrnuje čtení dat z úložiště, jejich distribuci napříč uzly zařízení a zpětný zápis do úložiště. Tato operace je náročná na procesor, v/v a na síť.

Zvyšování DWU:

- Lineárně mění výkon systému pro kontroly, agregace a příkazy CTAS.
- Zvyšuje počet čtenářů a zapisovačů pro operace základního zatížení.
- Zvyšuje maximální počet souběžných dotazů a souběžných slotů.

## <a name="service-level-objective"></a>Cíl na úrovni služby

Cíl úrovně služeb (SLO) je nastavení škálovatelnosti, které určuje náklady a úroveň výkonu datového skladu. Úrovně služeb pro Gen2 se měří v jednotkách služby COMPUTE Data Warehouse (cDWU), například DW2000c. Úrovně služeb Gen1 se měří v DWU, například DW2000.
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 nedávno přidal další možnosti škálování pro podporu výpočetních úrovní, které jsou nízké jako 100 cDWU. Stávající datové sklady, které jsou aktuálně na Gen1, které vyžadují nižší výpočetní úrovně, teď můžou upgradovat na Gen2 v oblastech, které jsou momentálně dostupné bez dalších nákladů.  Pokud vaše oblast ještě není podporovaná, můžete i nadále upgradovat na podporovanou oblast. Další informace najdete v tématu [upgrade na Gen2](upgrade-to-latest-generation.md).

V T-SQL nastavení SERVICE_OBJECTIVE určuje úroveň služby a úroveň výkonu pro datový sklad.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
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

Při zvyšování počtu jednotek datového skladu lineárně roste výpočetní prostředky. Gen2 poskytuje nejlepší výkon dotazů a nejvyšší měřítko. Gen2 systémy také využívají mezipaměť.

### <a name="capacity-limits"></a>Omezení kapacity

Každý SQL Server (například myserver.database.windows.net) má kvótu pro [databázovou jednotku (DTU)](../sql-database/sql-database-what-is-a-dtu.md) , která umožňuje určit počet jednotek datového skladu. Další informace najdete v tématu [omezení kapacity správy úloh](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotek datového skladu potřebuji

Ideální počet jednotek datového skladu závisí hodně na vašich úlohách a na množství dat, která jste do systému načetli.

Postup pro vyhledání nejlepšího DWU pro vaši úlohu:

1. Začněte výběrem menšího DWU.
2. Monitorujte výkon aplikace při testování zátěžových dat do systému a sledujte počet DWU vybraných v porovnání s výkonem, které sledujete.
3. Identifikujte všechny další požadavky na pravidelné doby aktivity špičky. Úlohy, které zobrazují významné špičky a žlaby v aktivitě, se můžou potřebovat často škálovat.

SQL Data Warehouse je systém škálování na více systémů, který dokáže zřídit obrovské množství výpočetních dat a výraznouí dotazů. Pokud chcete zobrazit jeho skutečné možnosti pro škálování, zejména u větších DWU, doporučujeme škálovat datovou sadu při škálování, abyste měli jistotu, že budete mít k dispozici dostatek dat k vytvoření kanálu pro procesory. Pro testování škálování doporučujeme používat aspoň 1 TB.

> [!NOTE]
>
> Výkon dotazů se zvyšuje s větší paralelismuou, pokud je možné rozdělit práci mezi výpočetními uzly. Pokud zjistíte, že škálování nemění váš výkon, možná budete muset vyladit návrh tabulky a dotazy. Pokyny k ladění dotazů najdete v tématu [Správa uživatelských dotazů](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>Oprávnění

Změna jednotek datového skladu vyžaduje oprávnění popsaná v [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql).

Předdefinované role pro prostředky Azure, jako je Přispěvatel databáze SQL a SQL Server přispěvatel, můžou měnit nastavení DWU.

## <a name="view-current-dwu-settings"></a>Zobrazit aktuální nastavení DWU

Chcete-li zobrazit aktuální nastavení DWU:

1. Otevřete Průzkumník objektů systému SQL Server v aplikaci Visual Studio.
2. Připojte se k hlavní databázi přidružené k logickému SQL Database serveru.
3. Vyberte ze zobrazení dynamické správy sys. database_service_objectives. Zde naleznete příklad:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Změnit jednotky datového skladu

### <a name="azure-portal"></a>portál Azure

Změna DWU nebo cDWUs:

1. Otevřete [Azure Portal](https://portal.azure.com), otevřete databázi a klikněte na možnost **škálovat**.

2. V části **škálovat**přesuňte posuvník doleva nebo doprava, abyste změnili nastavení DWU.

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete změnit DWU nebo cDWUs, použijte rutinu [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) prostředí PowerShell. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Další informace najdete v tématu [rutiny PowerShellu pro SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md) .

### <a name="t-sql"></a>T-SQL

Pomocí T-SQL můžete zobrazit aktuální nastavení DWU nebo cDWU, změnit nastavení a podívat se na průběh.

Změna DWU nebo cDWUs:

1. Připojte se k hlavní databázi přidružené k vašemu logickému SQL Database serveru.
2. Použijte příkaz [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000 pro MySQLDW databáze.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit DWU, použijte příkaz [Create nebo Update Database](/rest/api/sql/databases/createorupdate) REST API. Následující příklad nastaví cíl na úrovni služby tak, aby DW1000 pro databázi MySQLDW, která je hostována na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Další příklady REST API najdete v tématu [rozhraní REST API pro SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Kontrolovat stav DWU změn

Dokončení DWU změn může trvat několik minut. Pokud provádíte automatické škálování, zvažte implementaci logiky, abyste zajistili, že byly před pokračováním v jiné akci dokončeny určité operace.

Kontrola stavu databáze prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci. Portál poskytuje oznámení po dokončení operace a aktuálních stavech databáze, ale neumožňuje programovou kontrolu stavu.

Nelze kontrolovat stav databáze pro operace škálování na více instancí pomocí Azure Portal.

Postup kontroly stavu DWU změn:

1. Připojte se k hlavní databázi přidružené k vašemu logickému SQL Database serveru.
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

Tento DMV vrací informace o různých SQL Data Warehouse operacích správy, jako je například operace a stav operace, což je buď IN_PROGRESS nebo COMPLETED.

## <a name="the-scaling-workflow"></a>Pracovní postup škálování

Když zahájíte operaci škálování, systém nejdřív odchode všechny otevřené relace a vrátí otevřené transakce, aby se zajistil konzistentní stav. V případě operací škálování dojde k škálování pouze po dokončení tohoto vrácení transakce zpět.  

- V případě operace škálování se systémem odpojí všechny výpočetní uzly, zřídí další výpočetní uzly a pak se znovu připojí k vrstvě úložiště.
- V případě operace horizontálního rozšíření systému odpojí všechny výpočetní uzly a pak znovu připojí pouze potřebné uzly do vrstvy úložiště.

## <a name="next-steps"></a>Další kroky

Další informace o správě výkonu najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md) a [omezení pro využití paměti a souběžnosti](memory-and-concurrency-limits.md).

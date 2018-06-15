---
title: Jednotky datového skladu (Dwu, cDWUs) v Azure SQL Data Warehouse | Microsoft Docs
description: Doporučení pro výběr ideální počet jednotky datového skladu (Dwu, cDWUs) k optimalizaci ceny a výkonu a jak změnit počet jednotek.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 94791e4dc3d3c841dde4685d34d4e3fdaf7d9af7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185956"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednotky datového skladu (Dwu) a výpočetní jednotky datového skladu (cDWUs)
Doporučení pro výběr ideální počet jednotky datového skladu (Dwu, cDWUs) k optimalizaci ceny a výkonu a jak změnit počet jednotek. 

## <a name="what-are-data-warehouse-units"></a>Jaké jsou jednotky datového skladu?
S SQL Data Warehouse procesoru, paměti a vstupně-výstupní operace jsou seskupeny do jednotky škálování výpočetní názvem jednotky datového skladu (Dwu). DWU představuje abstraktní, normalizovaný měření výpočetních prostředků a výkonu. Tak, že změníte úroveň služby změnit počet jednotek Dwu, které jsou přiděleny na systém, který naopak upraví výkon a náklady, vašeho systému. 

K platbě za vyšší výkon, můžete zvýšit počet jednotky datového skladu. Chcete-li platit za méně výkonu, snižte jednotky datového skladu. Náklady na úložiště a výpočty se účtují samostatně, takže změna jednotky datového skladu nemá vliv na náklady na úložiště.

Výkon jednotky datového skladu je založena na tyto metriky úlohy datového skladu:

- Jak rychle můžete dotazu standardní datového skladu naskenovat velký počet řádků a potom proveďte komplexní agregaci? Tato operace je náročná na výkon vstupně-výstupních operací a procesoru.
- Jak rychle dokáže datového skladu přijímat data z úložiště objektů BLOB Azure nebo Azure Data Lake? Tato operace je síť a náročné na prostředky procesoru. 
- Jak rychle můžete [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL příkazu zkopírujte tabulku? Tato operace vyžaduje čtení dat z úložiště, distribuci mezi uzly zařízení a zápis do úložiště znovu. Tato operace je procesoru, vstupně-výstupních operací a sítě náročné.

Zvýšení Dwu:
- Lineárně změní výkon systému pro prohledávání, agregace a funkce CTAS příkazy
- Počet čtení a zápis pro operace PolyBase zatížení
- Zvyšuje maximální počet souběžných dotazů a sloty souběžnosti.

## <a name="service-level-objective"></a>Cíl na úrovni služby
Cíl na úrovni služby (SLO) je škálovatelnost nastavení, které určuje úroveň nákladů a výkonu datového skladu. Úrovně služeb pro Gen2 se měří v výpočetní jednotky datového skladu (cDWU), například DW2000c. Úrovně služeb Gen1 se měří v Dwu, například DW2000. 

V T-SQL SERVICE_OBJECTIVE nastavení určuje úroveň služby a úroveň výkonu pro datový sklad.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Úrovně výkonu a jednotky datového skladu

Každá úroveň výkonu používá mírně různých Měrná jednotka pro jejich jednotky datového skladu. Tento rozdíl se projeví na faktuře jako jednotka škálování překládá přímo k fakturaci.

- Gen1 datové sklady se měří v jednotky datového skladu (Dwu).
- Gen2 data warehousesr se měří v výpočetní jednotky datového skladu (cDWUs). 

Počet Dwu a cDWUs podporovat škálování výpočetní nahoru nebo dolů a pozastavení výpočetní při nemusíte používat datového skladu. Tyto operace jsou všechny na vyžádání. Gen2 používá místní mezipaměti na disku na výpočetních uzlech ke zlepšení výkonu. Při škálování nebo pozastavit systému, je mezipaměť zneplatněna a období zahájení práce s mezipaměti se proto vyžaduje, aby je dosaženo optimální výkon.  

Jako zvýšíte jednotky datového skladu, jsou lineárně zvýšení výpočetních prostředků. Gen2 poskytuje nejlepší výkon dotazů a nejvyšší škálování, ale má vyšší cenu položky. Je určený pro firmy, které mají konstantní vyžádání výkonu. Tyto systémy zkontrolujte většina použití mezipaměti. 

### <a name="capacity-limits"></a>Omezení kapacity
Má každý systém SQL server (například myserver.database.windows.net) [jednotka DTU (Database Transaction)](../sql-database/sql-database-what-is-a-dtu.md) kvótu, která umožňuje konkrétní počet jednotky datového skladu. Další informace najdete v tématu [limity kapacity úlohy správy](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotky datového skladu potřebuji?
Ideální počet jednotky datového skladu velmi mnohem závisí na velikosti pracovní zátěže a množství dat, které načetli jste do systému.

Postup pro vyhledání nejlepší DWU pro úlohy:

1. Začněte tím, že vyberete menší DWU. 
2. Při testování načítání dat do systému, sledování počet Dwu vybrané ve srovnání s výkonu a zjistíte, monitorujte výkon aplikace.
3. Určete veškeré další požadavky pro pravidelné období aktivity ve špičce. Pokud úlohy zobrazuje důležité vrcholů a žlaby aktivity a je důvodem k často škálování.

SQL Data Warehouse je systém Škálováním na více systémů, který můžete zřídit obrovské objemy výraznou objemy dat výpočetní a dotazů. Zobrazíte možnosti true pro škálování, zejména u větší Dwu, doporučujeme škálování sadu dat, jako je přizpůsobit pro zajistěte, abyste měli dostatek dat ke kanálu procesorů. Pro testování škálování, doporučujeme používat alespoň 1 TB.

> [!NOTE]
>
> Výkon dotazů pouze hodnota se zvyšuje s další paralelizace Pokud práci můžete rozdělit mezi výpočetní uzly. Pokud zjistíte, že škálování není změna výkon, musíte k vyladění váš návrh tabulky nebo své dotazy. Dotaz ladění pokyny, najdete v části [spravovat dotazy uživatele](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Oprávnění

Změna jednotky datového skladu vyžaduje oprávnění popsaná v [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

## <a name="view-current-dwu-settings"></a>Aktuální nastavení DWU zobrazení

Chcete-li zobrazit aktuální nastavení DWU:

1. Otevřete Průzkumník objektů systému SQL Server v sadě Visual Studio.
2. Připojení k databázi hlavní přidružené k logickému serveru služby SQL Database.
3. Vyberte ze zobrazení dynamické správy sys.database_service_objectives. Zde naleznete příklad: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Změnit jednotky datového skladu

### <a name="azure-portal"></a>Azure Portal
Chcete-li změnit Dwu nebo cDWUs:

1. Otevřete [portál Azure](https://portal.azure.com), otevřete databázi a klikněte na tlačítko **škálování**.

2. V části **škálování**, jezdce doleva nebo přímo na změnit nastavení DWU.

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

### <a name="powershell"></a>PowerShell
Chcete-li změnit Dwu nebo cDWUs, použijte [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) rutiny prostředí PowerShell. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW, která je hostovaná na serveru MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Další informace najdete v tématu [rutiny prostředí PowerShell pro datový sklad SQL](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
Pomocí T-SQL můžete zobrazit aktuální nastavení DWU nebo cDWU, změňte nastavení a zkontrolovat průběh. 

Chcete-li změnit Dwu nebo cDWUs:

1. Připojení k databázi hlavní přidružené k logické databáze SQL serveru.
2. Použití [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) příkaz TSQL. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit jednotkami Dwu, použijte [vytvoření nebo aktualizace databáze](/rest/api/sql/databases/createorupdate) REST API. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW, který je hostován na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

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

## <a name="check-status-of-dwu-changes"></a>Zkontrolujte stav DWU změn

DWU změny může trvat několik minut. Pokud jsou škálování automaticky, zvažte implementaci logiku a ujistěte se, že některé operace dokončeny před pokračováním další akci. 

Kontroluje se stav databáze prostřednictvím různých koncové body umožňuje správně implementovat automatizace. Na portálu poskytuje oznámení po dokončení operace a aktuální stav databáze, ale neumožňuje programový kontroly stavu. 

Nelze zkontrolovat stav databáze pro operace Škálováním na více systémů pomocí portálu Azure.

Chcete-li zkontrolovat stav DWU změny:

1. Připojení k databázi hlavní přidružené k logické databáze SQL serveru.
2. Odešlete následující dotaz pro kontrolu stavu databáze.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Odeslat následující dotaz a zkontrolujte stav operace

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Tato DMV vrací informace o různých management operace v SQL Data Warehouse například operaci a stav operace, která je buď být IN_PROGRESS nebo byla DOKONČENA.

## <a name="the-scaling-workflow"></a>Škálování pracovního postupu

Při zahájení operace škálování systému nejprve ukončí všechny otevřené relace, vrácení zpět všechny otevřené transakce zajistit konzistentní stav. U operace škálování škálování dochází k jenom po dokončení této transakční vrácení zpět.  

- U operace škálování, systém ustanovení další výpočetní a pak reattaches do vrstvy úložiště. 
- Pro operaci vertikální snížení kapacity, nepotřebné uzly odpojit z úložiště a připojte k zbývající uzly.

## <a name="next-steps"></a>Další postup
Další informace o správě výkonu najdete v tématu [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md) a [omezení paměti a souběžnost](memory-and-concurrency-limits.md).




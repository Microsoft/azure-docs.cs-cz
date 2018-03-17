---
title: "Jaké jsou jednotky datového skladu (Dwu, cDWUs) v Azure SQL Data Warehouse? | Dokumenty Microsoft"
description: "Výkon škálování možnosti v Azure SQL Data Warehouse. Horizontální navýšení kapacity úpravou Dwu, cDWUs, nebo pozastavení a obnovení výpočetní prostředky, abyste ušetřili náklady."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 03/15/2018
ms.author: jrj;barbkess
ms.openlocfilehash: f634bdde2c71f7563df11f686d7ce217311df81d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednotky datového skladu (Dwu) a výpočetní jednotky datového skladu (cDWUs)
Popisuje, jednotky datového skladu (Dwu) a výpočetní jednotky datového skladu (cDWUS) pro Azure SQL Data Warehouse. Zahrnout doporučení pro výběr ideální počet jednotky datového skladu a jak chcete změnit číslo z nich. 

## <a name="what-are-data-warehouse-units"></a>Jaké jsou jednotky datového skladu?
S SQL Data Warehouse procesoru, paměti a vstupně-výstupní operace jsou seskupeny do jednotky škálování výpočetní názvem jednotky datového skladu (Dwu). DWU představuje abstraktní, normalizovaný měření výpočetních prostředků a výkonu. Tak, že změníte úroveň služby změnit počet jednotek Dwu, které jsou přiděleny na systém, který naopak upraví výkon a náklady, vašeho systému. 

K platbě za vyšší výkon, můžete zvýšit počet jednotky datového skladu. Chcete-li platit za méně výkonu, snižte jednotky datového skladu. Náklady na úložiště a výpočty se účtují samostatně, takže změna jednotky datového skladu nemá vliv na náklady na úložiště.

Výkon jednotky datového skladu je založena na tyto metriky úlohy datového skladu:

- Jak rychle můžete dotazu standardní datového skladu naskenovat velký počet řádků a potom proveďte komplexní agregaci? Tato operace je náročná na výkon vstupně-výstupních operací a procesoru.
- Jak rychle dokáže datového skladu přijímat data z úložiště objektů BLOB Azure nebo Azure Data Lake? Tato operace je síť a náročné na prostředky procesoru. 
- Jak rychle můžete [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL příkazu zkopírujte tabulku? Tato operace vyžaduje čtení dat z úložiště, distribuci mezi uzly zařízení a zápis do úložiště znovu. Tato operace je procesoru, vstupně-výstupních operací a sítě náročné.

Zvýšení Dwu:
- Lineárně změní výkon systému pro prohledávání, agregace a funkce CTAS příkazy
- Počet čtení a zápis pro operace PolyBase zatížení
- Zvyšuje maximální počet souběžných dotazů a sloty souběžnosti.

## <a name="performance-tiers-and-data-warehouse-units"></a>Úrovně výkonu a jednotky datového skladu

Každá úroveň výkonu používá mírně různých Měrná jednotka pro jejich jednotky datového skladu. Tento rozdíl se projeví na faktuře jako jednotka škálování překládá přímo k fakturaci.

- Optimalizovaná pro úroveň výkonu pružnost se měří v jednotky datového skladu (Dwu).
- Optimalizovaná pro výpočetní výkon vrstvy se měří v výpočetní jednotky datového skladu (cDWUs). 

Počet Dwu a cDWUs podporovat škálování výpočetní nahoru nebo dolů a pozastavení výpočetní při nemusíte používat datového skladu. Tyto operace jsou všechny na vyžádání. Optimalizovaná pro výpočet úroveň výkonu také používá místní mezipaměti na disku na výpočetních uzlech ke zlepšení výkonu. Při škálování nebo pozastavit systému, je mezipaměť zneplatněna a období zahájení práce s mezipaměti se proto vyžaduje, aby je dosaženo optimální výkon.  

Jako zvýšíte jednotky datového skladu, jsou lineárně zvýšení výpočetních prostředků. Optimalizovaná pro výpočet úroveň výkonu poskytuje nejlepší výkon dotazů a nejvyšší škálování ale s vyšší cenu položky. Je určený pro firmy, které mají konstantní vyžádání výkonu. Tyto systémy zkontrolujte většina použití mezipaměti. 

### <a name="capacity-limits"></a>Omezení kapacity
Má každý systém SQL server (například myserver.database.windows.net) [jednotka DTU (Database Transaction)](../sql-database/sql-database-what-is-a-dtu.md) kvótu, která umožňuje konkrétní počet jednotky datového skladu. Další informace najdete v tématu [limity kapacity úlohy správy](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotky datového skladu potřebuji?
Ideální počet jednotky datového skladu velmi mnohem závisí na velikosti pracovní zátěže a množství dat, které načetli jste do systému.

Postup pro vyhledání nejlepší DWU pro úlohy:

1. Během vývoje Začněte výběrem menší DWU pomocí optimalizovaná pro úroveň výkonu pružnost.  Vzhledem k tomu, že problém v této fázi je funkční ověření, je optimalizovaný pro úroveň výkonu pružnost přiměřené možnost. Je to dobrý výchozí bod DW200. 
2. Při testování načítání dat do systému, sledování počet Dwu vybrané ve srovnání s výkonu a zjistíte, monitorujte výkon aplikace.
3. Určete veškeré další požadavky pro pravidelné období aktivity ve špičce. Pokud úlohy zobrazuje důležité vrcholů a žlaby aktivity a je důvodem k často škálování a potom upřednostnit optimalizovaná pro úroveň výkonu pružnost.
4. Pokud potřebujete více než 1 000 DWU, pak upřednostnit optimalizovaný pro výpočetní výkon vrstvě vzhledem k tomu, že to poskytuje nejlepší výkon.

SQL Data Warehouse je systém Škálováním na více systémů, který můžete zřídit obrovské objemy výraznou objemy dat výpočetní a dotazů. Zobrazíte možnosti true pro škálování, zejména u větší Dwu, doporučujeme škálování sadu dat, jako je přizpůsobit pro zajistěte, abyste měli dostatek dat ke kanálu procesorů. Pro testování škálování, doporučujeme používat alespoň 1 TB.

> [!NOTE]
>
> Výkon dotazů pouze hodnota se zvyšuje s další paralelizace Pokud práci můžete rozdělit mezi výpočetní uzly. Pokud zjistíte, že škálování není změna výkon, musíte k vyladění váš návrh tabulky nebo své dotazy. Dotaz ladění pokyny, naleznete v následujících [výkonu](sql-data-warehouse-overview-manage-user-queries.md) články. 

## <a name="permissions"></a>Oprávnění

Změna jednotky datového skladu vyžaduje oprávnění popsaná v [ALTER DATABASE][ALTER DATABASE]. 

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

3. Klikněte na **Uložit**. Zobrazí potvrzovací zpráva. Klikněte na tlačítko **Ano** potvrďte nebo **žádné** zrušit.

### <a name="powershell"></a>PowerShell
Chcete-li změnit Dwu nebo cDWUs, použijte rutinu prostředí PowerShell [Set-AzureRmSqlDatabase] [Set-AzureRmSqlDatabase]. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW, která je hostovaná na serveru MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
Pomocí T-SQL můžete zobrazit aktuální nastavení DWU nebo cDWU, změňte nastavení a zkontrolovat průběh. 

Chcete-li změnit Dwu nebo cDWUs:

1. Připojení k databázi hlavní přidružené k logické databáze SQL serveru.
2. Použití [ALTER DATABASE] [ ALTER DATABASE] příkaz TSQL. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit jednotkami Dwu, použijte [vytvoření nebo aktualizace databáze] [vytvoření nebo aktualizace databáze] REST API. Následující příklad nastaví DW1000 cíle na úrovni služby pro databázi MySQLDW, který je hostován na serveru MyServer. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


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
Najdete v následujících článcích, které vám pomohou pochopit některé pojmy další klíče výkonu:

* [Správa úloh a souběžnost][Workload and concurrency management]
* [Přehled návrhu tabulky][Table design overview]
* [distribuce tabulky][Table distribution]
* [Tabulka indexování][Table indexing]
* [Vytváření oddílů tabulky][Table partitioning]
* [Statistiky tabulky][Table statistics]
* [Doporučené postupy][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./resource-classes-for-workload-management.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]: ../active-directory/role-based-access-built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/

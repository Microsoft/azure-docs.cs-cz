---
title: Jednotky datového skladu (Dwu, cDWUs) ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Doporučení pro výběr ideální počet jednotek datového skladu (Dwu, cDWUs) optimalizovat ceny a výkonu a jak změnit počet jednotek.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 50e70ab9be87c15816dc6471a2a29afd0f17d907
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301241"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Jednotky datového skladu (Dwu) a výpočetní jednotky datového skladu (cDWUs)
Doporučení pro výběr ideální počet jednotek datového skladu (Dwu, cDWUs) optimalizovat ceny a výkonu a jak změnit počet jednotek. 

## <a name="what-are-data-warehouse-units"></a>Co jsou jednotky datového skladu?
S SQL Data Warehouse procesoru, paměti a vstupně-výstupní operace jsou seskupeny do jednotky škálování výpočetních volá jednotek datového skladu (Dwu). Jednotka DWU představuje abstraktní, normalizovaná měření výpočetních prostředků a výkonu. Změnou vaší úrovni služby upravíte počtu jednotek Dwu, které jsou přiděleny do systému, které zase upraví výkon a náklady na vašem systému. 

Platí pro vyšší výkon můžete zvýšit počet jednotek datového skladu. Za nižší výkon, snižte jednotky datového skladu. Náklady na úložiště a výpočetní prostředky se účtují zvlášť, takže změna jednotek datového skladu nemá vliv na náklady na úložiště.

Výkon za jednotky datového skladu je založená na tyto metriky úlohy datového skladu:

- Jak rychle můžete dotaz standardních datových skladů Kontrola velký počet řádků a potom provádění komplexní agregaci? Tato operace je náročná na výkon vstupně-výstupních operací a procesoru.
- Jak rychle můžete datový sklad ingestování dat z objektů BLOB Azure Storage nebo Azure Data Lake? Tato operace je síť a náročná na procesor. 
- Jak rychle můžete [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) příkaz T-SQL, zkopírujte tabulku? Tato operace zahrnuje čtení dat z úložiště, jejich distribuci napříč uzly zařízení a zpětný zápis do úložiště. Tato operace je využití procesoru, vstup/výstup a sítí.

Zvýšení jednotek Dwu:
- Lineárně změní výkon systému pro kontroly, agregace a příkazů CTAS
- Zvýší počet čtečky a zapisovače pro operace načítání PolyBase
- Zvyšuje maximální počet souběžných dotazů a slotů souběžnosti.

## <a name="service-level-objective"></a>Cíl na úrovni služby
Cíl na úrovni služby (SLO) je škálovatelnost, která určuje úroveň náklady a výkon datového skladu. Úrovně služeb pro Gen2 se měří v výpočetní jednotky datového skladu (cDWU), třeba DW2000c. Úrovně služeb Gen1 se měří v jednotkách Dwu, třeba DW2000. 

Nastavení SERVICE_OBJECTIVE v T-SQL určuje úroveň služby a úroveň výkonu pro datový sklad.

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

Každá úroveň výkonu používá mírně odlišné jednotka měření pro jejich jednotky datového skladu. Tento rozdíl je na faktuře projeví jako jednotka škálování překládá přímo na fakturaci.

- Datové sklady Gen1 se měří v jednotkách datového skladu (Dwu).
- Warehousesr Gen2 dat se měří v výpočetní jednotky datového skladu (cDWUs). 

Jednotky Dwu a cDWUs podporují škálování výpočetního výkonu navýšit nebo snížit kapacitu a pozastavení výpočetních, když není potřeba použít datový sklad. Tyto operace jsou všechny na vyžádání. Gen2 používá místní mezipaměti na disku na výpočetních uzlech ke zlepšení výkonu. Při škálování nebo pozastavit systému, platnost mezipaměti a tak dobu mezipaměti připravuje se musí provést dosáhnout optimálního výkonu.  

Zvyšuje se jednotky datového skladu, jsou lineárně zvýšení výpočetních prostředků. Gen2 poskytuje nejlepší výkon dotazů a co největším měřítku, ale má vyšší cenu položka. Je určená pro podniky, které mají konstantní nároky na výkon. Aby tyto systémy se nejlépe využil mezipaměti. 

### <a name="capacity-limits"></a>Omezení kapacity
Každý server SQL (např. myserver.database.windows.net) [jednotky transakcí databáze (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvótu, která umožňuje určitý počet jednotek datového skladu. Další informace najdete v tématu [limity kapacity úloh správy](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Kolik jednotek datového skladu budu potřebovat?
Ideální počet jednotek datového skladu velké části závisí na vašich úloh a množství dat, která jste načetli do systému.

Postup pro vyhledání nejlepších DWU pro vaše úlohy:

1. Začněte tím, že vyberete menší DWU. 
2. Při testování načtení dat do systému, sledování počtu jednotek Dwu vybrané ve srovnání s výkonem, které můžete sledovat, monitorujte výkon vašich aplikací.
3. Identifikujte veškeré další požadavky pro pravidelné období nejvyšší aktivity. Pokud zatížení zobrazuje důležité špičky a žlaby v aktivitě a není dobrý důvod, proč škálování často.

SQL Data Warehouse je horizontální navýšení kapacity systému, který můžete zřídit ohromná množství výpočetních a dotaz pořádnou množství dat. Zjistit její skutečné schopnosti škálování, zejména u větších Dwu, doporučujeme škálování sady dat při škálování na Ujistěte se, že máte k dispozici dostatek dat. kanálu procesory. Pro účely testování škálování, doporučujeme použít aspoň 1 TB.

> [!NOTE]
>
> Výkon dotazů pouze hodnota se zvyšuje s další paralelizace Pokud je možné rozdělit práci mezi jednotlivými výpočetními uzly. Pokud zjistíte, že škálování se mění výkon, budete muset vyladit váš návrh tabulky a/nebo vaše dotazy. Pokyny k ladění dotazů, najdete v části [spravovat dotazy uživatelů](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Oprávnění

Změna jednotek datového skladu vyžaduje oprávnění popsaná v [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

## <a name="view-current-dwu-settings"></a>Zobrazit aktuální nastavení DWU

Chcete-li zobrazit aktuální nastavení jednotek datového skladu:

1. Otevřete Průzkumník objektů systému SQL Server v sadě Visual Studio.
2. Připojte k hlavní databázi, které jsou přidružené k logickému serveru SQL Database.
3. Vyberte z dynamické správy Sys.database_service_objectives. Zde naleznete příklad: 

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
Chcete-li změnit jednotky Dwu nebo cDWUs:

1. Otevřete [webu Azure portal](https://portal.azure.com), otevřete databázi a klikněte na tlačítko **škálování**.

2. V části **škálování**, přesuňte posuvník doleva nebo doprava a změnit nastavení jednotek datového skladu.

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

### <a name="powershell"></a>PowerShell
Chcete-li změnit jednotky Dwu nebo cDWUs, použijte [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) rutiny Powershellu. Následující příklad nastaví cíl na úrovni služby na úroveň DW1000 a MySQLDW, který je hostitelem serveru MyServer databáze.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Další informace najdete v tématu [rutin Powershellu pro službu SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
Pomocí jazyka T-SQL můžete zobrazit aktuální nastavení DWU nebo cDWU, změňte nastavení a zkontrolovat průběh. 

Chcete-li změnit jednotky Dwu nebo cDWUs:

1. Připojte k hlavní databázi, které jsou přidružené k svému logickému serveru SQL Database.
2. Použití [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) příkaz TSQL. Následující příklad nastaví cíl na úrovni služby na úroveň DW1000 a pro databázi MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>Rozhraní REST API

Chcete-li změnit jednotkami Dwu, použijte [vytvořit nebo aktualizovat databázi](/rest/api/sql/databases/createorupdate) rozhraní REST API. Následující příklad nastaví cíl na úrovni služby na úroveň DW1000 a MySQLDW, který je hostitelem serveru MyServer databáze. Server je ve skupině prostředků Azure s názvem ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Další příklady rozhraní REST API najdete v tématu [rozhraní REST API pro službu SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Stav kontroly změn DWU

Změny DWU může trvat několik minut. Pokud jsou škálování automaticky, zvažte implementaci logiku a ujistěte se, že než budete pokračovat s další akce byly dokončeny určité operace. 

Kontroluje se stav databáze prostřednictvím různých koncových bodů umožňuje správnou implementaci automatizace. Portál poskytuje oznámení po dokončení operace a aktuální stav databáze, ale neumožňuje programové kontroly stavu. 

Nelze zkontrolovat stav databáze pro operace škálování pomocí webu Azure portal.

Kontrola stavu DWU změny:

1. Připojte k hlavní databázi, které jsou přidružené k svému logickému serveru SQL Database.
2. Odešlete následující dotaz, který zkontrolujte stav databáze.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Odeslat následující dotaz, kterým zkontrolujete stav operace

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Toto zobrazení dynamické správy vrátí informace o různých správy týkající se služby SQL Data Warehouse, jako je například operace a stav operace, která je buď in_progress nebo DOKONČENO.

## <a name="the-scaling-workflow"></a>Škálování pracovního postupu

Při zahájení operace škálování systému nejdřív ukončí všechny otevřené relace, vrácení zpět všech otevřených transakcí k zajištění konzistentního stavu. Pro operace škálování škálování dochází pouze po dokončení tohoto transakční vrácení zpět.  

- Pro operaci vertikálně navýšit kapacitu systém zřídí další výpočetní a pak znovu do vrstvy úložiště. 
- Pro operaci vertikální snížení kapacity nepotřebné uzly odpojit od úložiště a znovu připojte zbývající uzly.

## <a name="next-steps"></a>Další postup
Další informace o správě výkonu najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md) a [omezení paměti a souběžnosti](memory-and-concurrency-limits.md).




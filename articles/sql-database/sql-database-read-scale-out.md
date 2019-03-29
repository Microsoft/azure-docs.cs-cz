---
title: Azure SQL Database – čtení dotazy na replikách | Dokumentace Microsoftu
description: Azure SQL Database poskytuje možnost zatížení vyrovnávat jen pro čtení úlohy s využitím kapacity repliky jen pro čtení – volá horizontální navýšení kapacity pro čtení.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 03/28/2019
ms.openlocfilehash: d9ad859ef24b51dc337dc23281d2fe4e1eada1e6
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619887"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Načtení vyrovnávat zatížení dotazu jen pro čtení pomocí repliky jen pro čtení

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

**Horizontální navýšení kapacity pro čtení** vám umožní načíst jen pro čtení úlohy serveru zůstatek Azure SQL Database pomocí kapacita jednu repliku pouze pro čtení.

Každá databáze na úrovni Premium ([nákupní model založený na DTU](sql-database-service-tiers-dtu.md)) nebo na úrovni pro důležité obchodní informace ([nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md)) je automaticky zřízena několik replik AlwaysON Podpora smlouva SLA o dostupnosti. To je znázorněno v následujícím diagramu.

![Repliky jen pro čtení](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Sekundární repliky se zřizují se stejnou velikostí výpočetních jako primární repliku. **Horizontální navýšení kapacity pro čtení** funkce vám umožní načíst zůstatek SQL jen pro čtení na zatížení databáze využití kapacity o jednu z replik jen pro čtení místo sdílení repliky pro čtení i zápis. Tímto způsobem úlohy jen pro čtení budou z hlavní úlohy čtení a zápis izolovaných a nebude mít vliv na jeho výkon. Tato funkce je určená pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako jsou třeba analýzy a proto by mohl získat zvýšit efektivitu tuto dodatečnou kapacitu bez dalších poplatků.

Použít funkci škálování pro čtení s danou databází, musíte výslovně povolit ho při vytváření databáze nebo později změnou jeho konfigurace přes PowerShell voláním [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) nebo [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) rutiny nebo pomocí rozhraní REST API Azure Resource Manageru [databází – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) metoda.

Po povolení horizontální navýšení kapacity pro čtení pro databázi aplikace připojení k databázi, budete přesměrováni bránou repliky pro čtení a zápis nebo jen pro čtení replik databáze podle `ApplicationIntent` vlastnost gurovaný připojovací řetězec vaší aplikace. Informace o tom, `ApplicationIntent` vlastnost, naleznete v tématu [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud je zakázán horizontální navýšení kapacity pro čtení nebo nastavte vlastnost ReadScale v vrstvu Nepodporovaná služba, všechna připojení jsou směrované na repliky pro čtení i zápis, nezávisle `ApplicationIntent` vlastnost.

> [!NOTE]
> Dotaz Data Store, rozšířených událostí, SQL Profiler a auditu funkce nejsou podporovány u replik jen pro čtení. 
## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod repliky je, že tyto repliky jsou vždy transakčně konzistentní stav, ale v různých okamžicích v době může některé malé latence mezi existovat různých replik. Horizontální navýšení kapacity pro čtení podporuje relace úrovně konzistence. Znamená to, pokud relace jen pro čtení obnoví po připojení chyby způsobené nedostupnost repliky, je možné přesměrovat do repliky, který není 100 % naskočíte repliky pro čtení i zápis. Podobně pokud aplikace zapíše data pomocí relace pro čtení i zápis a okamžitě přečte pomocí relaci jen pro čtení, je možné, že nejnovější aktualizace nejsou okamžitě viditelné v replice. Latence je způsobené operací asynchronní transakce protokolu znovu.

> [!NOTE]
> Jsou nízké latence replikace v rámci oblasti a tato situace není obvyklé.

## <a name="connect-to-a-read-only-replica"></a>Připojíte k replice jen pro čtení

Když povolíte horizontální navýšení kapacity pro čtení pro databázi, `ApplicationIntent` možnost připojovacího řetězce, který klient poskytl Určuje, zda je připojení směrovat zápisu repliku nebo repliku pouze pro čtení. Konkrétně Pokud `ApplicationIntent` hodnotu `ReadWrite` (výchozí hodnota), připojení, budete přesměrováni na repliky pro čtení a zápis databáze. To je stejný jako stávající chování. Pokud `ApplicationIntent` hodnotu `ReadOnly`, připojení se směruje do repliky jen pro čtení.

Například následující připojovací řetězec připojení klienta k repliky jen pro čtení (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jednu z následujících řetězců připojení klient připojí k repliky pro čtení i zápis (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Ověřte, zda je připojení k replice jen pro čtení

Můžete ověřit, zda jste připojeni k repliky jen pro čtení spuštěním následujícího dotazu. Vrátí READ_ONLY při připojení k replice jen pro čtení.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> V každém okamžiku pouze jeden z replik AlwaysON přístupný relacemi jen pro čtení.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorování a řešení potíží s repliky jen pro čtení

Při připojení k replice jen pro čtení, můžete přistupovat pomocí metrik výkonu `sys.dm_db_resource_stats` zobrazení dynamické správy. Chcete-li získat přístup k plánu statistiky dotazů, použijte `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` a `sys.dm_exec_sql_text` zobrazení dynamické správy.

> [!NOTE]
> Zobrazení dynamické správy `sys.resource_stats` v logickou hlavní databázi vrátí data o využití a úložiště procesoru primární repliky.


## <a name="enable-and-disable-read-scale-out"></a>Povolení a zákaz horizontální navýšení kapacity pro čtení

Ve výchozím nastavení v je povolené horizontální navýšení kapacity pro čtení [Managed Instance](sql-database-managed-instance.md) úroveň pro důležité obchodní informace. By měla být explicitně povolená v [databáze umístěna na serveru služby SQL Database](sql-database-servers.md) úrovně Premium a pro důležité obchodní informace. Metody pro povolení a zakázání horizontální navýšení kapacity pro čtení je zde popsáno.

### <a name="powershell-enable-and-disable-read-scale-out"></a>PowerShell: Povolení a zákaz horizontální navýšení kapacity pro čtení

Správa Škálováním pro čtení v prostředí Azure PowerShell vyžaduje prosince 2016 prostředí Azure PowerShell verze nebo novější. Nejnovější verze prostředí PowerShell najdete v části [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Povolení nebo zakázání čtení horizontální navýšení kapacity v prostředí Azure PowerShell vyvoláním [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) rutiny a předejte hodnotu požadovaného – `Enabled` nebo `Disabled` --pro `-ReadScale` parametr. Alternativně můžete použít [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) rutina pro vytvoření nové databáze s čtení horizontální navýšení kapacity povolena.

Například chcete povolit číst horizontální navýšení kapacity pro existující databáze (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Chcete-li zakázat čtení horizontální navýšení kapacity pro existující databázi (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Chcete-li vytvořit novou databázi s čtení horizontální navýšení kapacity povolena (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

### <a name="rest-api-enable-and-disable-read-scale-out"></a>REST API: Povolení a zákaz horizontální navýšení kapacity pro čtení

K vytvoření databáze s čtení horizontální navýšení kapacity povolena, nebo k povolení nebo zakázání čtení horizontální navýšení kapacity pro existující databázi, vytvořit nebo aktualizovat odpovídající entita databáze s `readScale` nastavenou na `Enabled` nebo `Disabled` stejně jako v následující ukázky požadavek.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
}
```

Další informace najdete v tématu [databází – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Pomocí databáze TempDB na repliku pouze pro čtení

Databáze TempDB nejsou replikována do repliky jen pro čtení. Každá replika má svou vlastní verzi databáze TempDB, který je vytvořen při vytvoření repliky. Zajišťuje, že databáze TempDB je možné aktualizovat a můžete upravovat během provádění vašeho dotazu. Pokud vaše úlohy jen pro čtení, závisí na používání objektů databáze TempDB, měli byste vytvořit tyto objekty jako součást vašeho skriptu dotazu. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Horizontální navýšení kapacity pro čtení pomocí geograficky replikované databáze

Pokud používáte další horizontální navýšení kapacity načíst vyrovnávat zatížení jen pro čtení na databázi, která je geograficky replikovaný (například jako člen skupiny převzetí služeb při selhání), ujistěte se, že pro čtení pro primární a geograficky replikovanou sekundární databází je povolené horizontální navýšení kapacity. Tato konfigurace zajistí, že pokud vaše aplikace připojuje k nové primární po převzetí služeb při selhání bude pokračovat stejné prostředí vyrovnávání zatížení. Pokud se chcete připojit do geograficky replikované sekundární databáze se Škálováním pro čtení povolené, vaše relace `ApplicationIntent=ReadOnly` se budou směrovat na jednu z replik stejným způsobem můžeme směrovat připojení na primární databázi.  Relace bez `ApplicationIntent=ReadOnly` se budou směrovat na primární repliku geograficky replikované sekundární, což je také jen pro čtení. Protože geograficky replikované sekundární databáze má jiný koncový bod než primární databázi, v minulosti pro přístup k sekundární ho nebyl vyžadují `ApplicationIntent=ReadOnly`. K zajištění zpětné kompatibility `sys.geo_replication_links` zobrazení dynamické správy ukazuje `secondary_allow_connections=2` (nepovoluje se žádné připojení klienta).

> [!NOTE]
> Kruhové dotazování nebo jakékoli jiné s vyrovnáváním zatížení směrování mezi místní replik sekundární databáze se nepodporuje.

## <a name="next-steps"></a>Další postup

- Informace o použití prostředí PowerShell k nastavení čtení horizontální navýšení kapacity najdete v tématu [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) nebo [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) rutiny.
- Informace o nastavení čtení horizontální navýšení kapacity pomocí rozhraní REST API najdete v tématu [databází – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

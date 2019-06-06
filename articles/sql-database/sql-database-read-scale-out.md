---
title: Azure SQL Database – čtení dotazy na replikách | Dokumentace Microsoftu
description: Azure SQL Database umožňuje Vyrovnávání zatížení jen pro čtení úlohy s využitím kapacity repliky jen pro čtení – volá horizontální navýšení kapacity pro čtení.
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
ms.date: 06/03/2019
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492722"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Použít repliky jen pro čtení pro úlohy dotazu jen pro čtení – nástroj pro vyrovnávání zatížení

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jako součást [architektura pro vysokou dostupnost](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), každá databáze v rámci úrovně služeb Premium, pro důležité obchodní informace nebo velkokapacitní je automaticky zřízena primární repliku a několika sekundárních replik. Sekundární repliky se zřizují se stejnou velikostí výpočetních jako primární repliku. **Horizontální navýšení kapacity pro čtení** funkce umožňuje Vyrovnávání zatížení SQL jen pro čtení na zatížení databáze využití kapacity o jednu z replik jen pro čtení místo sdílení repliky pro čtení i zápis. Tímto způsobem úlohy jen pro čtení budou z hlavní úlohy čtení a zápis izolovaných a nebude mít vliv na jeho výkon. Tato funkce je určena pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako jsou třeba analýzy. Získají může zvýšit efektivitu tuto dodatečnou kapacitu bez dalších poplatků.

Následující diagram znázorňuje pomocí databázi pro důležité obchodní informace.

![Repliky jen pro čtení](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Ve výchozím nastavení na nové úrovně Premium, pro důležité obchodní informace a velkokapacitní databáze je povolena funkce škálování pro čtení. Pokud je nakonfigurované připojovací řetězec SQL `ApplicationIntent=ReadOnly`, aplikace bude přesměrován prostřednictvím brány do repliky jen pro čtení této databáze. Informace o tom, jak používat `ApplicationIntent` vlastnost, naleznete v tématu [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud chcete zajistit, že aplikace se připojí k primární replice, bez ohledu na to `ApplicationIntent` nastavení připojovacího řetězce SQL, je nutné explicitně zakázat čtení Škálováním při vytváření databáze nebo při změně jeho konfiguraci. Například pokud databázi upgradovat z úrovně Standard nebo obecné účely na úroveň Premium, pro důležité obchodní informace nebo Velkokapacitní a ujistěte se, že všechna připojení dále přejděte k primární replice, zakažte horizontální navýšení kapacity pro čtení. Podrobnosti o tom, jak zakázat najdete v tématu [povolit a zakázat horizontální navýšení kapacity pro čtení](#enable-and-disable-read-scale-out).

> [!NOTE]
> Dotaz Data Store, rozšířených událostí, SQL Profiler a auditu funkce nejsou podporovány u replik jen pro čtení. 

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod repliky je, že tyto repliky jsou vždy transakčně konzistentní stav, ale v různých okamžicích v době může některé malé latence mezi existovat různých replik. Horizontální navýšení kapacity pro čtení podporuje relace úrovně konzistence. Znamená to, pokud relace jen pro čtení obnoví po připojení chyby způsobené nedostupnost repliky, může být přesměrován na repliky, který není 100 % naskočíte repliky pro čtení i zápis. Podobně pokud aplikace zapíše data pomocí relace pro čtení i zápis a okamžitě přečte pomocí relaci jen pro čtení, je možné, že nejnovější aktualizace nejsou okamžitě viditelné v replice. Latence je způsobené operací asynchronní transakce protokolu znovu.

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

Horizontální navýšení kapacity pro čtení je ve výchozím nastavení úrovně služeb Premium, pro důležité obchodní informace a velkokapacitní povolená. Horizontální navýšení kapacity pro čtení nelze povolit v úrovních služeb Basic, Standard nebo obecné účely. Horizontální navýšení kapacity pro čtení je v Hyperškálovacím databází nakonfigurovaných v 0 repliky automaticky zakázána. 

Můžete zakázat a znovu povolit škálování čtení na izolované databáze a elastický fond databází Premium nebo pro důležité obchodní informace vrstvy služeb pomocí následujících metod.

> [!NOTE]
> Zakázat horizontální navýšení kapacity pro čtení je k dispozici z důvodu zpětné kompatibility.

### <a name="azure-portal"></a>portál Azure

Můžete spravovat nastavení Sacle navýšením kapacity pro čtení na **konfigurovat** databázové okno. 

### <a name="powershell"></a>PowerShell

Správa Škálováním pro čtení v prostředí Azure PowerShell vyžaduje prosince 2016 prostředí Azure PowerShell verze nebo novější. Nejnovější verze prostředí PowerShell najdete v části [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Můžete zakázat nebo znovu povolit čtení Scale-Out v prostředí Azure PowerShell vyvoláním [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) rutiny a předejte hodnotu požadovaného – `Enabled` nebo `Disabled` --pro `-ReadScale` parametr. 

Chcete-li zakázat čtení horizontální navýšení kapacity na existující databázi (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Chcete-li zakázat čtení horizontální navýšení kapacity na novou databázi (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Chcete-li znovu povolit čtení horizontální navýšení kapacity na existující databázi (položky v lomených závorkách nahraďte správné hodnoty pro vaše prostředí a vyřadit ostrých závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

K vytvoření databáze s čtení horizontální navýšení kapacity zakázán, nebo chcete-li změnit nastavení pro existující databázi, použijte metodu s `readScale` vlastnost nastavena na hodnotu `Enabled` nebo `Disabled` stejně jako v následující ukázkový požadavek.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Další informace najdete v tématu [databází – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Pomocí databáze TempDB na repliku pouze pro čtení

Databáze TempDB nejsou replikována do repliky jen pro čtení. Každá replika má svou vlastní verzi databáze TempDB, který je vytvořen při vytvoření repliky. Zajišťuje, že databáze TempDB je možné aktualizovat a můžete upravovat během provádění vašeho dotazu. Pokud vaše úlohy jen pro čtení, závisí na používání objektů databáze TempDB, měli byste vytvořit tyto objekty jako součást vašeho skriptu dotazu. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Horizontální navýšení kapacity pro čtení pomocí geograficky replikované databáze

Pokud používáte škálování čtení na úlohy jen pro čtení – nástroj pro vyrovnávání zatížení v databázi, která je geograficky replikovaný (například jako člen skupiny převzetí služeb při selhání), ujistěte se, že čtení horizontální navýšení kapacity je povolena na primární a geograficky replikované sekundární databáze. Tato konfigurace zajistí, že pokud vaše aplikace připojuje k nové primární po převzetí služeb při selhání bude pokračovat stejné prostředí vyrovnávání zatížení. Pokud se chcete připojit do geograficky replikované sekundární databáze se Škálováním pro čtení povolené, vaše relace `ApplicationIntent=ReadOnly` se budou směrovat na jednu z replik stejným způsobem můžeme směrovat připojení na primární databázi.  Relace bez `ApplicationIntent=ReadOnly` se budou směrovat na primární repliku geograficky replikované sekundární, což je také jen pro čtení. Protože geograficky replikované sekundární databáze má koncový bod jiné než primární databázi, v minulosti pro přístup k sekundární ho nebyl vyžadují `ApplicationIntent=ReadOnly`. K zajištění zpětné kompatibility `sys.geo_replication_links` zobrazení dynamické správy ukazuje `secondary_allow_connections=2` (nepovoluje se žádné připojení klienta).

> [!NOTE]
> Kruhové dotazování nebo jakékoli jiné s vyrovnáváním zatížení směrování mezi místní replik sekundární databáze se nepodporuje.

## <a name="next-steps"></a>Další postup

- Informace o nabídce Hyperškálovatelného SQL Database najdete v tématu [úroveň služby Hyperškálovatelného](./sql-database-service-tier-hyperscale.md).

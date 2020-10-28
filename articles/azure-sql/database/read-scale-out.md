---
title: Čtení dotazů na replikách
description: Azure SQL poskytuje možnost použít kapacitu replik jen pro čtení pro úlohy čtení, která se nazývá horizontální navýšení kapacity čtení.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9c09a54daa482d738ded9f7aca1c95c2b640617e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790266"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Přesměrování zatížení dotazů jen pro čtení pomocí replik jen pro čtení
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

V rámci [architektury vysoké dostupnosti](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)se každá samostatná databáze, databáze elastického fondu a spravovaná instance v úrovni služeb Premium a pro důležité obchodní informace automaticky zřídí s primární replikou pro čtení a zápis a několika sekundárními replikami jen pro čtení. Sekundární repliky se zřídí se stejnou výpočetní velikostí jako primární replika. Funkce *škálování čtení* na více instancí umožňuje přesměrovat úlohy jen pro čtení pomocí výpočetní kapacity jedné z replik jen pro čtení namísto jejich spuštění v replice pro čtení i zápis. Tímto způsobem mohou být některé úlohy jen pro čtení izolované od úloh pro čtení a zápis a nebudou mít vliv na jejich výkon. Tato funkce je určená pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako je například analýza. V úrovních služeb Premium a Pro důležité obchodní informace můžou aplikace získat výhody výkonu na základě této další kapacity bez dalších poplatků.

Funkce *škálování pro čtení* je k dispozici také v úrovni služby škálování na více instancí, pokud je vytvořena alespoň jedna sekundární replika. Pro úlohy vyrovnávání zatížení, které vyžadují více prostředků, než je k dispozici v jedné sekundární replice, lze použít více sekundárních replik.

Architektura vysoké dostupnosti pro úrovně služeb Basic, Standard a Pro obecné účely nezahrnuje žádné repliky. Funkce *škálování pro čtení* na více instancí není v těchto úrovních služby k dispozici.

Funkce je znázorněna na následujícím obrázku.

![Repliky jen pro čtení](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkce *škálování čtení* na více instancí je ve výchozím nastavení povolená pro nové databáze Premium, pro důležité obchodní informace a v databázi s měřítkem. V případě velkého rozsahu je pro nové databáze ve výchozím nastavení vytvořena jedna sekundární replika. 

> [!NOTE]
> U Pro důležité obchodní informace úrovně služby spravované instance je škálování pro čtení vždy povolené.

Pokud je v připojovacím řetězci SQL nakonfigurován `ApplicationIntent=ReadOnly` , aplikace bude přesměrována do repliky, která je jen pro čtení této databáze nebo spravované instance. Informace o použití `ApplicationIntent` vlastnosti naleznete v tématu [určení záměru aplikace](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud chcete zajistit, aby se aplikace připojovala k primární replice bez ohledu na `ApplicationIntent` nastavení v připojovacím řetězci SQL, je nutné explicitně zakázat horizontální navýšení kapacity čtení při vytváření databáze nebo změny konfigurace. Pokud například upgradujete databázi ze úrovně Standard nebo Pro obecné účely na úroveň Premium, Pro důležité obchodní informace nebo úrovně škálování a chcete zajistit, aby všechna vaše připojení pokračovala v přechodu na primární repliku, zakažte horizontální navýšení kapacity čtení. Podrobnosti o tom, jak ho zakázat, najdete v tématu [povolení a zakázání horizontálního navýšení kapacity pro čtení](#enable-and-disable-read-scale-out).

> [!NOTE]
> V replikách jen pro čtení nejsou podporované funkce úložiště dotazů a SQL profileru. 

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod repliky je, že repliky jsou vždycky v konzistentním stavu, ale v různých časových okamžicích může dojít k malé latenci mezi různými replikami. Škálování na více instancí podporuje konzistenci na úrovni relace. To znamená, že pokud se relace jen pro čtení znovu připojí po chybě připojení způsobené nedostupností repliky, může být přesměrována do repliky, která není 100%, s replikou pro čtení i zápis. Podobně platí, že pokud aplikace zapisuje data pomocí relace čtení i zápisu a hned ji přečte pomocí relace jen pro čtení, je možné, že nejnovější aktualizace nejsou v replice hned viditelné. Latence je způsobena operací opakování asynchronního transakčního protokolu.

> [!NOTE]
> Latence replikace v rámci oblasti je nízká a tato situace je vzácná. Pokud chcete monitorovat latenci replikace, přečtěte si téma [monitorování a řešení potíží s replikou jen pro čtení](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Připojení k replice jen pro čtení

Pokud pro databázi povolíte škálování pro čtení, `ApplicationIntent` možnost v připojovacím řetězci poskytovaná klientem určí, zda je připojení směrováno do repliky zápisu nebo do repliky jen pro čtení. Konkrétně, pokud `ApplicationIntent` je hodnota `ReadWrite` (výchozí hodnota), připojení bude Přesměrováno na repliku pro čtení i zápis. Jedná se o shodu s chováním, když není `ApplicationIntent` součástí připojovacího řetězce. Pokud `ApplicationIntent` je hodnota `ReadOnly` , připojení se směruje do repliky jen pro čtení.

Například následující připojovací řetězec připojí klienta k replice jen pro čtení (nahrazující položky v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazením lomených závorek):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Některý z následujících připojovacích řetězců připojí klienta k replice pro čtení i zápis (nahrazující položky v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazením lomených závorek):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Ověření, zda je připojení k replice jen pro čtení

Spuštěním následujícího dotazu v kontextu vaší databáze můžete ověřit, zda jste připojeni k replice jen pro čtení. Pokud jste připojeni k replice jen pro čtení, vrátí se READ_ONLY.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> V úrovních služeb Premium a Pro důležité obchodní informace je v daném okamžiku k dispozici pouze jedna z replik jen pro čtení. Škálovatelné rozšíření podporuje více replik jen pro čtení.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Monitorování a řešení potíží s replikami jen pro čtení

Pokud jste připojeni k replice jen pro čtení, zobrazení dynamické správy (zobrazení dynamické správy) odráží stav repliky a lze k nim zadat dotaz pro účely monitorování a řešení potíží. Databázový stroj nabízí více zobrazení, která zveřejňují širokou škálu monitorovaných dat. 

Běžně používaná zobrazení:

| Název | Účel |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Poskytuje metriky využití prostředků za poslední hodinu, včetně CPU, v/v v/v, a využití zápisu do protokolu vzhledem k omezením cíle služby.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Poskytuje agregované statistiky čekání pro instanci databázového stroje. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Poskytuje stav repliky a statistiku synchronizace. Velikost fronty znovu a rychlost opakování slouží jako indikátory latence dat v replice jen pro čtení. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Poskytuje čítače výkonu databázového stroje.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Poskytuje statistiku spouštění podle dotazů, jako je počet spuštění, použitý čas procesoru atd.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Poskytuje plány dotazů v mezipaměti. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Poskytuje text dotazu pro plán dotazů v mezipaměti.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Poskytuje dotaz v reálném čase během provádění dotazů.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Poskytuje poslední známý skutečný plán spuštění včetně statistik za běhu pro dotaz.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Poskytuje statistiku vstupně-výstupních operací úložiště, propustnosti a latence pro všechny soubory databáze. |

> [!NOTE]
> `sys.resource_stats`Zobrazení dynamické správy a `sys.elastic_pool_resource_stats` v logické hlavní databázi vrátí data o využití prostředků primární repliky.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Monitorování replik jen pro čtení s rozšířenými událostmi

Relaci rozšířené události nelze vytvořit, je-li připojena k replice určené jen pro čtení. V Azure SQL Database však definice relací [rozšířených událostí](xevent-db-diff-from-svr.md) , které byly vytvořeny a změněny v primární replice, replikovány do replik jen pro čtení, včetně geografických replik a zachytí události do replik jen pro čtení.

Relace rozšířené události v replice jen pro čtení, která je založena na definici relace z primární repliky, může být spuštěna a zastavena nezávisle na primární replice. Když je relace rozšířené události na primární replice Vyřazená, vynechává se taky u všech replik jen pro čtení.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Úroveň izolace transakce v replikách jen pro čtení

Dotazy, které jsou spouštěny v replikách jen pro čtení, jsou vždy mapovány na úroveň izolace transakce [snímku](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) . Izolace snímku používá správu verzí řádků, aby nedocházelo k blokování scénářů, kde čtenáři blokují zapisovače.

Ve výjimečných případech, pokud transakce izolace snímku přistupuje k metadatům objektů, které byly upraveny v jiné souběžné transakci, může docházet k chybě [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), v databázi%. * ls se nezdařila transakce izolace snímku, protože objekt, k němuž byl přístup proveden příkazem, byl od spuštění této transakce ZMĚNĚN příkazem DDL v jiné souběžné transakci. Tato akce je zakázaná, protože metadata nemají verze. Souběžná aktualizace metadat může vést k nekonzistenci, pokud je smíšená s izolací snímku. "

### <a name="long-running-queries-on-read-only-replicas"></a>Dlouhotrvající dotazy v replikách jen pro čtení

Dotazy, které běží na replikách jen pro čtení, musí mít přístup k metadatům pro objekty, na které se odkazuje v dotazu (tabulky, indexy, statistiky atd.). Ve výjimečných případech platí, že pokud se objekt metadat v primární replice změní, zatímco dotaz drží zámek na stejném objektu repliky jen pro čtení, může dotaz [blokovat](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) proces, který aplikuje změny z primární repliky do repliky jen pro čtení. Pokud by byl takový dotaz po dlouhou dobu spuštěný, mohl by být replika jen pro čtení výrazně nesynchronizovaná s primární replikou. 

Pokud dlouhotrvající dotaz na repliku, která je jen pro čtení, způsobuje tento typ blokování, bude automaticky ukončen a v relaci se zobrazí chyba 1219, "Vaše relace byla odpojena z důvodu operace DDL s vysokou prioritou".

> [!NOTE]
> Pokud se zobrazí chyba 3961 nebo chyba 1219 při spouštění dotazů proti replice jen pro čtení, opakujte dotaz.

> [!TIP]
> V úrovních služby Premium a Pro důležité obchodní informace jsou při připojení k replice jen pro čtení `redo_queue_size` `redo_rate` použity sloupce a v [Sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV k monitorování procesu synchronizace dat, který slouží jako indikátory latence dat v replice jen pro čtení.
> 

## <a name="enable-and-disable-read-scale-out"></a>Povolit a zakázat horizontální navýšení kapacity čtení

U úrovní služeb Premium, Pro důležité obchodní informace a škálování je ve výchozím nastavení povolené škálování čtení na více instancí. U úrovní služeb Basic, Standard a Pro obecné účely není možné povolit horizontální navýšení kapacity čtení. Pro databáze v rámci škálování s nulovými replikami jsou automaticky zakázané horizontální navýšení kapacity.

Můžete zakázat a znovu povolit horizontální navýšení kapacity pro čtení v izolovaných databázích a databázích elastických fondů na úrovni Premium nebo Pro důležité obchodní informace pomocí následujících metod.

> [!NOTE]
> U izolovaných databází a databází elastických fondů je k dispozici možnost zakázat horizontální navýšení kapacity čtení z důvodu zpětné kompatibility. U Pro důležité obchodní informace spravovaných instancí nelze zakázat horizontální navýšení kapacity čtení.

### <a name="azure-portal"></a>Azure Portal

V okně **Konfigurovat** databázi můžete spravovat nastavení škálování pro čtení.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Modul Azure Resource Manager bude i nadále přijímat opravy chyb, dokud nebude aspoň 2020. prosince.  Argumenty pro příkazy v modulu AZ Module a in Azure Resource Manager jsou v podstatě identické. Další informace o jejich kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Správa škálování čtení na více instancí v Azure PowerShell vyžaduje vydání Azure PowerShell verze z prosince 2016 nebo novější. Nejnovější verzi PowerShellu najdete v tématu [Azure PowerShell](/powershell/azure/install-az-ps).

Můžete zakázat nebo znovu povolit horizontální navýšení kapacity čtení v Azure PowerShell vyvoláním rutiny [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) a předáním požadované hodnoty ( `Enabled` nebo `Disabled` ) pro `-ReadScale` parametr.

Postup zakázání horizontálního navýšení kapacity pro čtení v existující databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení lomených závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Postup zakázání horizontálního navýšení kapacity pro čtení v nové databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení ostrých závorek):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Opětovné povolení horizontálního navýšení kapacity pro čtení v existující databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení lomených závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Chcete-li vytvořit databázi s vypnutým škálováním pro čtení nebo změnit nastavení pro existující databázi, použijte následující metodu s `readScale` vlastností nastavenou na `Enabled` nebo `Disabled` , jako v následujícím ukázkovém požadavku.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Další informace najdete v tématu [databáze – vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Použití `tempdb` databáze v replice jen pro čtení

`tempdb`Databáze na primární replice není replikována do replik jen pro čtení. Každá replika má svou vlastní `tempdb` databázi, která se vytvoří při vytvoření repliky. Tím zajistíte, aby `tempdb` bylo možné je aktualizovat a upravovat během provádění dotazu. Pokud vaše úloha jen pro čtení závisí na používání `tempdb` objektů, měli byste tyto objekty vytvořit jako součást skriptu dotazu.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Použití škálování pro čtení u geograficky replikovaných databází

Geograficky replikované sekundární databáze mají stejnou architekturu vysoké dostupnosti jako primární databáze. Pokud se připojujete k geograficky replikovaným sekundárním databázím s povoleným škálováním pro čtení, vaše relace s se `ApplicationIntent=ReadOnly` budou směrovat na jednu z replik s vysokou dostupností stejným způsobem, jakým jsou směrována na primární databázi s možností zápisu. Relace bez `ApplicationIntent=ReadOnly` Směrování budou směrovány do primární repliky geograficky replikovaného sekundárního umístění, které je také jen pro čtení. 

Tímto způsobem vytvoří geografickou repliku pro primární databázi pro čtení i zápis dvě repliky jen pro čtení, a to za celkem tři repliky jen pro čtení. Každá další geografická replika poskytuje další dvojici replik jen pro čtení. Geografické repliky je možné vytvořit v libovolné oblasti Azure, včetně oblasti primární databáze.

> [!NOTE]
> Mezi replikami geograficky replikované sekundární databáze neexistuje žádné automatické kruhové dotazování ani žádné jiné směrování s vyrovnáváním zatížení.

## <a name="next-steps"></a>Další kroky

- Informace o SQL Database nabídce škálování na úrovni služeb najdete v tématu [úroveň služby s škálovatelným škálováním](service-tier-hyperscale.md).
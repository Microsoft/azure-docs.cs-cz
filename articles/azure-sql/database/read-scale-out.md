---
title: Čtení dotazů na replikách
description: Azure SQL Database poskytuje možnost vyrovnávat zatížení úloh jen pro čtení pomocí kapacity replik jen pro čtení, které se nazývají škálování pro čtení.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
titleSuffix: Azure SQL Database & SQL Managed Instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: a043eb5eed8f5554e42a113a3d7963be94da8a49
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985563"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Použití replik jen pro čtení k vyrovnávání zatížení dotazovacích úloh jen pro čtení
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V rámci [architektury vysoké dostupnosti](high-availability-sla.md#premium-and-business-critical-service-tier-availability)se každá databáze na úrovních služby Premium a pro důležité obchodní informace automaticky zřídí s primární replikou a několika sekundárními replikami. Sekundární repliky se zřídí se stejnou výpočetní velikostí jako primární replika. Funkce *škálování čtení* na více instancí umožňuje vyrovnávat zatížení SQL Database úloh jen pro čtení pomocí kapacity jedné z replik jen pro čtení namísto sdílení repliky pro čtení i zápis. Tímto způsobem bude úloha jen pro čtení izolovaná od hlavní úlohy pro čtení a zápis a nebude mít vliv na její výkon. Tato funkce je určená pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako je například analýza. V úrovních služeb Premium a Pro důležité obchodní informace můžou aplikace získat výhody výkonu na základě této další kapacity bez dalších poplatků.

Funkce škálování pro čtení je k dispozici také v úrovni služby škálování na více instancí, pokud je vytvořena alespoň jedna sekundární replika. Více sekundárních replik lze použít, pokud úlohy jen pro čtení vyžadují více prostředků, než je k dispozici v jedné sekundární replice. Architektura vysoké dostupnosti pro úrovně služeb Basic, Standard a Pro obecné účely neobsahuje žádné repliky. Funkce škálování pro čtení na více instancí není v těchto úrovních služby k dispozici.

Následující diagram znázorňuje použití databáze Pro důležité obchodní informace.

![Repliky jen pro čtení](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkce škálování čtení na více instancí je ve výchozím nastavení povolená pro nové databáze Premium, Pro důležité obchodní informace a v databázi s měřítkem. V případě velkého rozsahu je pro nové databáze ve výchozím nastavení vytvořena jedna sekundární replika. Pokud je v připojovacím řetězci SQL nakonfigurován `ApplicationIntent=ReadOnly` , aplikace bude bránu přesměrována do repliky, která je jen pro čtení této databáze. Informace o použití `ApplicationIntent` vlastnosti naleznete v tématu [určení záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud chcete zajistit, aby se aplikace připojovala k primární replice bez ohledu na `ApplicationIntent` nastavení v připojovacím řetězci SQL, je nutné explicitně zakázat horizontální navýšení kapacity čtení při vytváření databáze nebo změny konfigurace. Pokud třeba upgradujete databázi z úrovně Standard nebo Pro obecné účely na úroveň Premium, Pro důležité obchodní informace nebo škálování a chcete zajistit, aby všechna vaše připojení pokračovala v přechodu na primární repliku, zakažte u čtení horizontální navýšení kapacity. Podrobnosti o tom, jak ho zakázat, najdete v tématu [povolení a zakázání horizontálního navýšení kapacity pro čtení](#enable-and-disable-read-scale-out).

> [!NOTE]
> Dotazy na úložiště dat, rozšířené události a funkce SQL profileru se v replikách jen pro čtení nepodporují.

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod repliky je, že repliky jsou vždycky v konzistentním stavu, ale v různých časových okamžicích může dojít k malé latenci mezi různými replikami. Škálování na více instancí podporuje konzistenci na úrovni relace. Znamená to, že pokud se relace jen pro čtení znovu připojí po chybě připojení způsobené nedostupností repliky, může být přesměrována do repliky, která není 100% v aktuálním stavu s replikou pro čtení i zápis. Podobně platí, že pokud aplikace zapisuje data pomocí relace čtení i zápisu a hned ji načte pomocí relace jen pro čtení, je možné, že nejnovější aktualizace nejsou hned viditelné na replice. Latence je způsobena operací opakování asynchronního transakčního protokolu.

> [!NOTE]
> Latence replikace v rámci oblasti je nízká a tato situace je vzácná.

## <a name="connect-to-a-read-only-replica"></a>Připojení k replice jen pro čtení

Pokud pro databázi povolíte škálování pro čtení, `ApplicationIntent` možnost v připojovacím řetězci poskytovaná klientem určí, zda je připojení směrováno do repliky zápisu nebo do repliky jen pro čtení. Konkrétně, pokud `ApplicationIntent` je hodnota `ReadWrite` (výchozí hodnota), připojení bude Přesměrováno na repliku pro čtení a zápis databáze. To je stejné jako stávající chování. Pokud `ApplicationIntent` je hodnota `ReadOnly` , připojení se směruje do repliky jen pro čtení.

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

Spuštěním následujícího dotazu můžete ověřit, zda jste připojeni k replice, která je jen pro čtení. Pokud jste připojeni k replice jen pro čtení, vrátí se READ_ONLY.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Relace jen pro čtení je v kterémkoli okamžiku přístupná pouze k jedné z replik AlwaysON.

## <a name="monitor-and-troubleshoot-a-read-only-replica"></a>Monitorování a řešení potíží s replikou jen pro čtení

Když jste připojeni k replice jen pro čtení, můžete k metrikám výkonu přistupovat pomocí `sys.dm_db_resource_stats` DMV. Pro přístup ke statistikám plánu dotazů použijte `sys.dm_exec_query_stats` `sys.dm_exec_query_plan` zobrazení dynamické správy a `sys.dm_exec_sql_text` .

> [!NOTE]
> DMV `sys.resource_stats` v logické hlavní databázi vrátí využití CPU a data úložiště primární repliky.

## <a name="enable-and-disable-read-scale-out"></a>Povolit a zakázat horizontální navýšení kapacity čtení

U úrovní služeb Premium, Pro důležité obchodní informace a škálování je ve výchozím nastavení povolené škálování čtení na více instancí. U úrovní služeb Basic, Standard a Pro obecné účely není možné povolit horizontální navýšení kapacity čtení. Pro databáze v rámci škálování s nulovými replikami jsou automaticky zakázané horizontální navýšení kapacity.

Můžete zakázat a znovu povolit horizontální navýšení kapacity pro čtení v izolovaných databázích a databázích elastických fondů na úrovni služby Premium nebo Pro důležité obchodní informace pomocí následujících metod.

> [!NOTE]
> Možnost zakázat horizontální navýšení kapacity čtení je k dispozici kvůli zpětné kompatibilitě.

### <a name="azure-portal"></a>portál Azure

V okně **Konfigurovat** databázi můžete spravovat nastavení škálování pro čtení.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager modul je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Modul Azure Resource Manager bude i nadále přijímat opravy chyb, dokud nebude aspoň 2020. prosince.  Argumenty pro příkazy v modulu AZ Module a in Azure Resource Manager jsou v podstatě identické. Další informace o jejich kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

Správa škálování čtení na více instancí v Azure PowerShell vyžaduje vydání Azure PowerShell verze z prosince 2016 nebo novější. Nejnovější verzi PowerShellu najdete v tématu [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

Další informace najdete v tématu [databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-a-read-only-replica"></a>Použití databáze TempDB v replice jen pro čtení

Databáze TempDB není replikována do replik jen pro čtení. Každá replika má svou vlastní verzi databáze TempDB, která se vytvoří při vytvoření repliky. Zajišťuje, aby se databáze TempDB aktualizovala a upravila během provádění dotazu. Pokud vaše úloha jen pro čtení závisí na použití objektů TempDB, měli byste tyto objekty vytvořit jako součást skriptu dotazu.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Použití škálování pro čtení u geograficky replikovaných databází

Pokud k vyrovnávání zatížení pro úlohy, která je geograficky replikované (například jako člen skupiny převzetí služeb při selhání), používáte škálování na více instancí, ujistěte se, že je na primární i geograficky replikovaných sekundárních databázích zapnuté škálování pro čtení. Tato konfigurace zajistí, že stejné prostředí pro vyrovnávání zatížení bude pokračovat, až se vaše aplikace připojí k nové primární úrovni po převzetí služeb při selhání. 

Pokud se připojujete k geograficky replikovaným sekundárním databázím s povoleným škálováním pro čtení, vaše relace se `ApplicationIntent=ReadOnly` budou směrovat na jednu z replik stejným způsobem jako připojení k primární databázi.  Relace bez `ApplicationIntent=ReadOnly` Směrování budou směrovány do primární repliky geograficky replikovaného sekundárního umístění, které je také jen pro čtení. Vzhledem k tomu, že geograficky replikovaná sekundární databáze má jiný koncový bod než primární databáze, historicky pro přístup k sekundární databázi nemusela být nastavena `ApplicationIntent=ReadOnly` . Aby se zajistila zpětná kompatibilita, `sys.geo_replication_links` DMV zobrazí `secondary_allow_connections=2` (jakékoli připojení klienta je povolené).

> [!NOTE]
> Kruhové dotazování nebo jakékoli jiné směrování s vyrovnáváním zatížení mezi místními replikami sekundární databáze není podporováno.

## <a name="next-steps"></a>Další kroky

Informace o SQL Database nabídce škálování na úrovni služeb najdete v tématu [úroveň služby s škálovatelným škálováním](service-tier-hyperscale.md).

---
title: Čtení dotazů na repliky
description: Azure SQL Database poskytuje možnost vyvažovat úlohy jen pro čtení pomocí kapacity replik jen pro čtení – s názvem Škálování pro čtení.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 06/03/2019
ms.openlocfilehash: 1a1b9907cd931716949d92d948a7d541fd2d5057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206941"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Použití replik jen pro čtení k vyrovnávání zatížení dotazovacích úloh jen pro čtení

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jako součást [architektury vysoké dostupnosti](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)je každá databáze ve vrstvě služby Premium a Business Critical automaticky zřízena primární replikou a několika sekundárními replikami. Sekundární repliky jsou zřízeny se stejnou velikostí výpočetních prostředků jako primární repliky. Funkce **Horizontální navýšení kapacity pro čtení** umožňuje načíst vyrovnávání úloh y SQL Database jen pro čtení pomocí kapacity jedné z replik jen pro čtení namísto sdílení repliky pro čtení a zápis. Úloha jen pro čtení díky tomu bude izolovaná od hlavní úlohy pro čtení i zápis a nebude mít vliv na její výkon. Tato funkce je určena pro aplikace, které obsahují logicky oddělené úlohy jen pro čtení, jako je například analýza. Na úrovních služeb Premium a Business Critical mohou aplikace získat výhody výkonu pomocí této dodatečné kapacity bez dalších nákladů.

Funkce **Horizontální navýšení kapacity pro čtení** je také k dispozici ve vrstvě služby Hyperscale, když je vytvořena alespoň jedna sekundární replika. Více sekundárních replik lze použít, pokud úlohy jen pro čtení vyžadují více prostředků, než je k dispozici na jedné sekundární replice. Architektura služeb vysoké dostupnosti úrovně základních, standardních a obecných služeb neobsahuje žádné repliky. Funkce **Horizontální navýšení kapacity pro čtení** není v těchto úrovních služby k dispozici.

Následující diagram znázorňuje použití kritické podnikové databáze.

![Repliky jen pro čtení](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkce Horizontální navýšení kapacity pro čtení je ve výchozím nastavení povolena v nových databázích Premium, Business Critical a Hyperscale. Pro hyperškálování je ve výchozím nastavení vytvořena jedna sekundární replika pro nové databáze. Pokud je připojovací `ApplicationIntent=ReadOnly`řetězec SQL nakonfigurován pomocí aplikace, bude bránou přesměrována na repliku této databáze jen pro čtení. Informace o použití této `ApplicationIntent` vlastnosti naleznete v [tématu Určení záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud chcete zajistit, aby se aplikace připojila k `ApplicationIntent` primární replice bez ohledu na nastavení v připojovacím řetězci SQL, musíte explicitně zakázat horizontální navýšení kapacity pro čtení při vytváření databáze nebo při změně její konfigurace. Pokud například upgradujete databázi z úrovně Standard nebo General Purpose na úroveň Premium, Business Critical nebo Hyperscale a chcete se ujistit, že všechna vaše připojení budou i nadále přecházet na primární repliku, zakažte horizontální navýšení kapacity pro čtení. Podrobnosti o tom, jak ji zakázat, naleznete [v tématu Povolení a zakázání horizontálního navýšení kapacity pro čtení](#enable-and-disable-read-scale-out).

> [!NOTE]
> Úložiště dat dotazu, rozšířené události a sql profiler funkce nejsou podporovány na repliky jen pro čtení.

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod replik je, že repliky jsou vždy v transakčním konzistentním stavu, ale v různých časových bodech může existovat některé malé latence mezi různými replikami. Čtení horizontálního navýšení kapacity podporuje konzistenci na úrovni relace. To znamená, že pokud se relace jen pro čtení znovu připojí po chybě připojení způsobené nedostupností repliky, může být přesměrována na repliku, která není 100 % aktuální s replikou pro čtení a zápis. Podobně pokud aplikace zapisuje data pomocí relace pro čtení a zápis a okamžitě ji přečte pomocí relace jen pro čtení, je možné, že nejnovější aktualizace nejsou okamžitě viditelné v replice. Latence je způsobena operace opakování protokolu asynchronní transakce.

> [!NOTE]
> Latence replikace v rámci oblasti jsou nízké a tato situace je vzácné.

## <a name="connect-to-a-read-only-replica"></a>Připojení k replice jen pro čtení

Pokud povolíte horizontální navýšení kapacity `ApplicationIntent` pro čtení pro databázi, možnost v připojovacím řetězci poskytované klientem určuje, zda je připojení směrováno do repliky zápisu nebo do repliky jen pro čtení. Konkrétně pokud `ApplicationIntent` je `ReadWrite` hodnota (výchozí hodnota), připojení bude přesměrováno na repliku databáze pro čtení a zápis. To je totožné s existující chování. Pokud `ApplicationIntent` je `ReadOnly`hodnota , připojení je směrováno do repliky jen pro čtení.

Například následující připojovací řetězec spojuje klienta s replikou jen pro čtení (nahrazení položek v úhlových závorkách se správnými hodnotami pro vaše prostředí a uvolněním úhlových závorek):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Některý z následujících připojovacích řetězců spojuje klienta s replikou pro čtení a zápis (nahrazení položek v úhlových závorkách se správnými hodnotami pro vaše prostředí a uvolněním úhlových závorek):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Ověření, zda je připojení k replice jen pro čtení

Spuštěním následujícího dotazu můžete ověřit, zda jste připojeni k replice jen pro čtení. Vrátí READ_ONLY při připojení k replice jen pro čtení.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> V daném okamžiku pouze jeden z AlwaysON repliky je přístupný relace jen pro čtení.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Sledování a odstraňování potíží s replikou jen pro čtení

Při připojení k replice jen pro čtení, můžete `sys.dm_db_resource_stats` přistupovat k metriky výkonu pomocí DMV. Chcete-li získat přístup `sys.dm_exec_query_stats`ke `sys.dm_exec_query_plan` `sys.dm_exec_sql_text` statistice plánu dotazů, použijte a dmvs.

> [!NOTE]
> DMV `sys.resource_stats` v logické hlavní databázi vrátí využití procesoru a data úložiště primární repliky.

## <a name="enable-and-disable-read-scale-out"></a>Povolení a zakázání horizontálního navýšení kapacity pro čtení

Škálování na čtení je ve výchozím nastavení povoleno na úrovních služeb Premium, Business Critical a Hyperscale. Čtení horizontálního navýšení kapacity nelze povolit v úrovních služeb Basic, Standard nebo General Purpose. Čtení horizontálního navýšení kapacity je automaticky zakázáno v databázích Hyperscale nakonfigurovaných s 0 replikami.

Škálování čtení můžete zakázat a znovu povolit v jednotlivých databázích a databázích elastického fondu na úrovni služby Premium nebo Business Critical pomocí následujících metod.

> [!NOTE]
> Možnost zakázat horizontální navýšení kapacity pro čtení je k dispozici pro zpětnou kompatibilitu.

### <a name="azure-portal"></a>portál Azure

Nastavení Horizontální navýšení kapacity pro čtení můžete spravovat v okně **Konfigurovat** databázi.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Správa škálování čtení v Azure PowerShellu vyžaduje vydání Azure PowerShellu z prosince 2016 nebo novější. Nejnovější verzi PowerShellu najdete v [tématu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Můžete zakázat nebo znovu povolit škálování pro čtení v Prostředí Azure PowerShell vyvoláte [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) `Disabled` rutina `-ReadScale` a předání matné hodnoty – `Enabled` nebo -- pro parametr.

Zakázání škálování čtení v existující databázi (nahrazení položek v úhlových závorkách správnými hodnotami pro vaše prostředí a uvolnění úhlových závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Zakázání škálování čtení v nové databázi (nahrazení položek v úhlových závorkách správnými hodnotami pro vaše prostředí a uvolnění úhlových závorek):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Opětovné povolení škálování čtení v existující databázi (nahrazení položek v úhlových závorkách správnými hodnotami pro vaše prostředí a uvolnění úhlových závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST API

Chcete-li vytvořit databázi se zakázaným škálováním čtení nebo změnit nastavení pro `readScale` existující databázi, použijte následující metodu s vlastností nastavenou na `Enabled` nebo `Disabled` jako v níže uvedeném ukázkovém požadavku.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Další informace naleznete v [tématu Databases - Create or Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Použití databáze TempDB v replice jen pro čtení

Databáze TempDB není replikována do replik jen pro čtení. Každá replika má vlastní verzi databáze TempDB, která je vytvořena při vytvoření repliky. Zajišťuje, že TempDB je aktualizovatelný a lze upravit během provádění dotazu. Pokud vaše úloha jen pro čtení závisí na použití objektů TempDB, měli byste tyto objekty vytvořit jako součást skriptu dotazu.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Použití horizontálního navýšení kapacity pro čtení s geograficky replikované databáze

Pokud používáte horizontální navýšení kapacity pro čtení k vyrovnávání zatížení úloh jen pro čtení v databázi, která je geograficky replikována (například jako člen skupiny s podporou převzetí služeb při selhání), ujistěte se, že je povoleno horizontální navýšení kapacity pro čtení v primární i geograficky replikované sekundární databáze. Tato konfigurace zajistí, že stejné prostředí vyrovnávání zatížení pokračuje, když se vaše aplikace připojí k nové primární po převzetí služeb při selhání. Pokud se připojujete k geograficky replikované sekundární databázi s povoleným měřítkem pro čtení, budou vaše relace `ApplicationIntent=ReadOnly` směrovány do jedné z replik stejným způsobem, jakým směrujeme připojení v primární databázi.  Relace bez `ApplicationIntent=ReadOnly` budou směrovány do primární replikace geograficky replikované sekundární, která je také jen pro čtení. Vzhledem k tomu, že geograficky replikovaná sekundární databáze má jiný koncový bod než primární `ApplicationIntent=ReadOnly`databáze, historicky pro přístup k sekundární nebylo nutné nastavit . Pro zajištění zpětné `sys.geo_replication_links` kompatibility `secondary_allow_connections=2` se zobrazí dmv (jakékoli připojení klienta je povoleno).

> [!NOTE]
> Kruhové dotazování nebo jiné směrování s vyrovnáváním zatížení mezi místními replikami sekundární databáze není podporováno.

## <a name="next-steps"></a>Další kroky

- Informace o nabídce hyperškálování databáze SQL naleznete v tématu [Vrstva služby Hyperscale](./sql-database-service-tier-hyperscale.md).

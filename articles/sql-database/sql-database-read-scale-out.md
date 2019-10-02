---
title: Azure SQL Database – čtení dotazů v replikách | Microsoft Docs
description: Azure SQL Database poskytuje možnost vyrovnávat zatížení úloh jen pro čtení pomocí kapacity replik jen pro čtení – s názvem čtení a škálováním na více instancí.
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
ms.openlocfilehash: 73c31a60fb14df00f50fefb35ca123298241c61d
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812371"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Použití replik jen pro čtení k vyrovnávání zatížení úloh dotazů jen pro čtení

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V rámci [architektury vysoké dostupnosti](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability)se každá databáze na úrovni služby Premium a pro důležité obchodní informace automaticky zřídí s primární replikou a několika sekundárními replikami. Sekundární repliky se zřídí se stejnou výpočetní velikostí jako primární replika. Funkce **škálování čtení** na více instancí umožňuje vyrovnávat zatížení SQL Database úloh jen pro čtení pomocí kapacity jedné z replik jen pro čtení namísto sdílení repliky pro čtení i zápis. Úloha jen pro čtení díky tomu bude izolovaná od hlavní úlohy pro čtení i zápis a nebude mít vliv na její výkon. Tato funkce je určená pro aplikace, které zahrnují logicky oddělené úlohy jen pro čtení, jako je například analýza. V úrovních služeb Premium a Pro důležité obchodní informace můžou aplikace získat výhody výkonu na základě této další kapacity bez dalších poplatků.

Funkce **škálování pro čtení** je k dispozici také v úrovni služby škálování na více instancí, pokud je vytvořena alespoň jedna sekundární replika. Více sekundárních replik lze použít, pokud úlohy jen pro čtení vyžadují více prostředků, než je k dispozici v jedné sekundární replice. Architektura vysoké dostupnosti pro úrovně služeb Basic, Standard a Pro obecné účely nezahrnuje žádné repliky. Funkce **škálování pro čtení** na více instancí není v těchto úrovních služby k dispozici.

Následující diagram znázorňuje použití databáze Pro důležité obchodní informace.

![Repliky jen pro čtení](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkce škálování čtení na více instancí je ve výchozím nastavení povolená pro nové databáze Premium, Pro důležité obchodní informace a v databázi s měřítkem. V případě velkého rozsahu je pro nové databáze ve výchozím nastavení vytvořena jedna sekundární replika. Pokud je připojovací řetězec SQL nakonfigurován pomocí `ApplicationIntent=ReadOnly`, aplikace bude bránu přesměrována do repliky určené jen pro čtení této databáze. Informace o tom, jak používat vlastnost `ApplicationIntent`, najdete v tématu [určení záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud chcete zajistit, aby se aplikace připojovala k primární replice bez ohledu na nastavení `ApplicationIntent` v připojovacím řetězci SQL, musíte explicitně zakázat horizontální navýšení kapacity čtení při vytváření databáze nebo při změně její konfigurace. Pokud například upgradujete databázi ze úrovně Standard nebo Pro obecné účely na úroveň Premium, Pro důležité obchodní informace nebo úrovně škálování a chcete zajistit, aby všechna vaše připojení pokračovala v přechodu na primární repliku, zakažte horizontální navýšení kapacity čtení. Podrobnosti o tom, jak ho zakázat, najdete v tématu [povolení a zakázání horizontálního navýšení kapacity pro čtení](#enable-and-disable-read-scale-out).

> [!NOTE]
> Dotazy na úložiště dat, rozšířené události, funkce SQL Profiler a audit nejsou podporovány v replikách jen pro čtení. 

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod repliky je, že repliky jsou vždycky v konzistentním stavu, ale v různých časových okamžicích může dojít k malé latenci mezi různými replikami. Škálování na více instancí podporuje konzistenci na úrovni relace. To znamená, že pokud se relace jen pro čtení znovu připojí po chybě připojení způsobené nedostupností repliky, může být přesměrována do repliky, která není 100%, s replikou pro čtení i zápis. Podobně platí, že pokud aplikace zapisuje data pomocí relace čtení i zápisu a hned ji načte pomocí relace jen pro čtení, je možné, že nejnovější aktualizace nejsou hned viditelné v replice. Latence je způsobena operací opakování asynchronního transakčního protokolu.

> [!NOTE]
> Latence replikace v rámci oblasti je nízká a tato situace je vzácná.

## <a name="connect-to-a-read-only-replica"></a>Připojení k replice jen pro čtení

Povolíte-li pro databázi škálování na více instancí, možnost `ApplicationIntent` v připojovacím řetězci, kterou poskytuje klient, určí, zda je připojení směrováno do repliky zápisu nebo do repliky jen pro čtení. Konkrétně Pokud je hodnota `ApplicationIntent` `ReadWrite` (výchozí hodnota), připojení bude Přesměrováno na repliku pro čtení a zápis databáze. To je stejné jako stávající chování. Pokud je hodnota `ApplicationIntent` `ReadOnly`, připojení se směruje do repliky jen pro čtení.

Například následující připojovací řetězec připojí klienta k replice jen pro čtení (nahrazující položky v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazením lomených závorek):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Některý z následujících připojovacích řetězců připojí klienta k replice pro čtení i zápis (nahrazující položky v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazením lomených závorek):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Ověření, zda je připojení k replice jen pro čtení

Spuštěním následujícího dotazu můžete ověřit, zda jste připojeni k replice, která je jen pro čtení. Vrátí READ_ONLY, když se připojí k replice jen pro čtení.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> V jednom okamžiku pouze jedna z replik AlwaysON je přístupná pro relace jen pro čtení.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Monitorování a řešení potíží s replikou jen pro čtení

Když jste připojeni k replice jen pro čtení, můžete k metrikám výkonu přistupovat pomocí `sys.dm_db_resource_stats` DMV. Pro přístup ke statistickým údajům o plánu dotazů použijte `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` a `sys.dm_exec_sql_text` zobrazení dynamické správy.

> [!NOTE]
> DMV `sys.resource_stats` v logické hlavní databázi vrátí využití CPU a data úložiště primární repliky.


## <a name="enable-and-disable-read-scale-out"></a>Povolit a zakázat horizontální navýšení kapacity čtení

Ve výchozím nastavení je škálování čtení na více instancí povolené na úrovních Premium, Pro důležité obchodní informace a škálování služby. U úrovní služeb Basic, Standard a Pro obecné účely není možné povolit horizontální navýšení kapacity čtení. Pro databáze v rámci škálování s nakonfigurovanou instancí s 0 je automatické škálování na více instancí zakázané. 

Můžete zakázat a znovu povolit horizontální navýšení kapacity pro čtení v izolovaných databázích a databázích elastických fondů na úrovni Premium nebo Pro důležité obchodní informace pomocí následujících metod.

> [!NOTE]
> Možnost zakázat horizontální navýšení kapacity čtení je k dispozici kvůli zpětné kompatibilitě.

### <a name="azure-portal"></a>Portál Azure

V okně **Konfigurovat** databázi můžete spravovat nastavení škálování pro čtení. 

### <a name="powershell"></a>PowerShell

Správa škálování čtení na více instancí v Azure PowerShell vyžaduje vydání Azure PowerShell verze z prosince 2016 nebo novější. Nejnovější verzi PowerShellu najdete v tématu [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Můžete zakázat nebo znovu povolit horizontální navýšení kapacity čtení v Azure PowerShell vyvoláním rutiny [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) a předáním požadované hodnoty – `Enabled` nebo `Disabled`--pro parametr `-ReadScale`. 

Postup zakázání horizontálního navýšení kapacity pro čtení v existující databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení lomených závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Postup zakázání horizontálního navýšení kapacity pro čtení v nové databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení ostrých závorek):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Opětovné povolení horizontálního navýšení kapacity pro čtení v existující databázi (výměna položek v lomených závorkách se správnými hodnotami pro vaše prostředí a vyřazení lomených závorek):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>Rozhraní REST API

Pokud chcete vytvořit databázi s vypnutým škálováním pro čtení nebo změnit nastavení pro existující databázi, použijte následující metodu s vlastností `readScale` nastavenou na `Enabled` nebo `Disabled` jako v níže uvedené ukázkové žádosti.

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

Další informace najdete v tématu [databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Použití databáze TempDB v replice jen pro čtení

Databáze TempDB není replikována do replik jen pro čtení. Každá replika má svou vlastní verzi databáze TempDB, která se vytvoří při vytvoření repliky. Zajišťuje, aby bylo možné databázi TempDB aktualizovatelné a upravitelné během provádění dotazu. Pokud vaše úloha jen pro čtení závisí na použití objektů TempDB, měli byste tyto objekty vytvořit jako součást skriptu dotazu. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Použití škálování pro čtení u geograficky replikovaných databází

Pokud k vyrovnávání zatížení u úloh, které jsou geograficky replikované (například jako člena skupiny převzetí služeb při selhání), používáte škálování na více instancí, ujistěte se, že je na primární i geograficky replikovaných sekundárních databázích zapnuté horizontální navýšení kapacity pro čtení. Tato konfigurace zajistí, že stejné prostředí pro vyrovnávání zatížení bude pokračovat, až se vaše aplikace připojí k nové primární úrovni po převzetí služeb při selhání. Pokud se připojujete k geograficky replikovaným sekundárním databázím s povoleným škálováním pro čtení, budou se vaše relace s `ApplicationIntent=ReadOnly` směrovat na jednu z replik stejným způsobem jako připojení k primární databázi.  Relace bez `ApplicationIntent=ReadOnly` budou směrovány do primární repliky geograficky replikovaného sekundárního umístění, které je také jen pro čtení. Vzhledem k tomu, že geograficky replikovaná sekundární databáze má jiný koncový bod než primární databáze, historicky pro přístup k sekundární databázi nevyžadovala nastavení `ApplicationIntent=ReadOnly`. Aby se zajistila zpětná kompatibilita, `sys.geo_replication_links` DMV ukazuje `secondary_allow_connections=2` (jakékoli připojení klienta je povolené).

> [!NOTE]
> Kruhové dotazování nebo jakékoli jiné směrování s vyrovnáváním zatížení mezi místními replikami sekundární databáze není podporováno.

## <a name="next-steps"></a>Další kroky

- Informace o SQL Database nabídce škálování na úrovni služeb najdete v tématu [úroveň služby s škálovatelným škálováním](./sql-database-service-tier-hyperscale.md).

---
title: Ručně iniciovat převzetí služeb při selhání ve spravované instanci SQL
description: Přečtěte si, jak ručně převzetí služeb při selhání primárních a sekundárních replik na spravované instanci SQL Azure.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: douglas, sstein
ms.date: 02/17/2021
ms.openlocfilehash: ecd97efbf12fb149037a94749bc899169f2c5a92
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096515"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Ruční převzetí služeb při selhání iniciované uživatelem ve službě SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, jak ručně převzetí služeb při selhání primárního uzlu na úrovních služby SQL Managed instance Pro obecné účely (GP) a Pro důležité obchodní informace (BC) a ruční převzetí služeb při selhání sekundárního uzlu repliky jen pro čtení jenom na úrovni služby BC.

## <a name="when-to-use-manual-failover"></a>Kdy použít ruční převzetí služeb při selhání

[Vysoká dostupnost](../database/high-availability-sla.md) je základní součástí platformy spravované instance SQL, která funguje transparentně pro vaše databázové aplikace. Převzetí služeb při selhání z primární na sekundární uzly v případě snížení nebo detekce chyb uzlů nebo během pravidelných měsíčních aktualizací softwaru je očekávaným výskytem pro všechny aplikace, které používají spravovanou instanci SQL v Azure.

V některých z následujících důvodů můžete zvážit spuštění [ručního převzetí služeb při selhání](../database/high-availability-sla.md#testing-application-fault-resiliency) na spravované instanci SQL:
- Před nasazením do produkčního prostředí otestujte aplikaci pro odolnost převzetí služeb při selhání.
- Testování komplexních systémů pro odolnost proti chybám při automatickém převzetí služeb při selhání
- Testování vlivu převzetí služeb při selhání na stávající relace databáze
- Ověřte, jestli se převzetí služeb při selhání změní na konečný výkon kvůli změnám v latenci sítě.
- V některých případech snížení výkonu dotazů může ruční převzetí služeb při potížích s výkonem snížit riziko.

> [!NOTE]
> Zajištění odolnosti před převzetím služeb při selhání před nasazením do produkčního prostředí vám pomůže zmírnit riziko chyb aplikací v produkčním prostředí a přispívat k dostupnosti aplikací pro vaše zákazníky.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Iniciovat ruční převzetí služeb při selhání na spravované instanci SQL

### <a name="azure-rbac-permissions-required"></a>Vyžaduje se oprávnění Azure RBAC.

Uživatel, který iniciuje převzetí služeb při selhání, bude muset mít jednu z následujících rolí Azure:

- Role vlastníka předplatného nebo
- Role přispěvatele spravované instance nebo
- Vlastní role s následujícím oprávněním:
  - `Microsoft.Sql/managedInstances/failover/action`

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Minimální verze AZ. SQL musí být [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0). Zvažte použití [Azure Cloud Shell](../../cloud-shell/overview.md) z Azure Portal, které mají vždy k dispozici nejnovější verzi prostředí PowerShell. 

Aby bylo možné nainstalovat požadované moduly Azure, použijte následující skript prostředí PowerShell. Kromě toho vyberte předplatné, ve kterém se nachází spravovaná instance, na kterou chcete převzetí služeb při selhání nakládat.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Pomocí příkazu PowerShellu [Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/invoke-azsqlinstancefailover) s následujícím příkladem můžete iniciovat převzetí služeb při selhání primárního uzlu, které platí pro úroveň služby BC a GP.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

Pro převzetí služeb při selhání čtení sekundárního uzlu, který se vztahuje pouze na úroveň služby BC, použijte následující příkaz PS.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>Pomocí rozhraní příkazového řádku

Ujistěte se, že jsou nainstalované nejnovější skripty rozhraní příkazového řádku.

Pomocí příkazu AZ SQL mi Failover CLI s následujícím příkladem můžete iniciovat převzetí služeb při selhání primárního uzlu, které platí pro úroveň služby BC a GP.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

Použijte následující příkaz rozhraní příkazového řádku pro převzetí služeb při selhání pro čtení sekundární uzel, které platí pouze pro úroveň služby BC.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>S využitím rozhraní REST API

Pokročilým uživatelům, kteří by museli potřebovat automatizovat převzetí služeb při selhání svých spravovaných instancí SQL pro účely implementace kanálu nepřetržitého testování nebo automatizovaného zmírnění výkonu, je možné tuto funkci provést prostřednictvím volání rozhraní API při inicializaci převzetí služeb při selhání. Podrobnosti najdete v tématu [spravované instance – převzetí služeb při selhání REST API](/rest/api/sql/managed%20instances%20-%20failover/failover) .

Pokud chcete spustit převzetí služeb při selhání pomocí REST API volání, nejdřív vygenerujte ověřovací token pomocí klienta rozhraní API podle vašeho výběru. Vygenerovaný ověřovací token se používá jako vlastnost Authorization v hlavičce požadavku rozhraní API a je povinný.

Následující kód je příkladem identifikátoru URI rozhraní API, který se má zavolat:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

V volání rozhraní API je potřeba předat tyto vlastnosti:

| **Vlastnost rozhraní API** | **Parametr** |
| --- | --- |
| subscriptionId | ID předplatného, ke kterému se nasazuje spravovaná instance |
| resourceGroupName | Skupina prostředků, která obsahuje spravovanou instanci |
| managedInstanceName | Název spravované instance |
| replicaType | Volitelné (Primární nebo ReadableSecondary). Tyto parametry reprezentují typ repliky, která se má převzít při selhání: primární nebo čitelný sekundární objekt. Pokud není zadaný, převzetí služeb při selhání se ve výchozím nastavení inicializuje na primární replice. |
| verze-api | Statická hodnota a v současné době musí být "2019-06-01-Preview". |

Odpověď rozhraní API bude jedna z následujících dvou možností:

- 202 přijato
- Jedna z chyb žádosti 400.

Stav operace se dá sledovat prostřednictvím revizí odpovědí rozhraní API v hlavičkách odpovědí. Další informace najdete v tématu [stav asynchronních operací Azure](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>Monitorování převzetí služeb při selhání

Pokud chcete monitorovat průběh převzetí služeb při selhání uživatelem iniciované instance BC, spusťte následující dotaz T-SQL ve vašem oblíbeném klientovi (například SSMS) na spravované instanci SQL. Přečte zobrazení systému sys.dm_hadr_fabric_replica_states a repliky sestav, které jsou k dispozici na instanci. Po zahájení ručního převzetí služeb při selhání aktualizujte stejný dotaz.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

Před zahájením převzetí služeb při selhání bude výstup označovat aktuální primární repliku ve vrstvě služby BC obsahující jednu primární a tři sekundární prostředí ve skupině dostupnosti AlwaysOn. Po provedení převzetí služeb při selhání bude nutné znovu spustit tento dotaz, aby označoval změnu primárního uzlu.

Nebudete moct zobrazit stejný výstup s úrovní služeb GP, jak je uvedeno výše pro BC. Je to proto, že úroveň služby GP je založená jenom na jednom uzlu. Můžete použít alternativní dotaz T-SQL ukazující čas spuštění procesu SQL na uzlu pro instanci vrstvy služby GP:

```T-SQL
SELECT sqlserver_start_time, sqlserver_start_time_ms_ticks FROM sys.dm_os_sys_info
```

Krátkodobá ztráta připojení z klienta během převzetí služeb při selhání, obvykle trvalá za minutu, bude znamenat provedení převzetí služeb při selhání bez ohledu na úroveň služby.

> [!NOTE]
> Dokončení procesu převzetí služeb při selhání (nejedná se o skutečnou krátkou nedostupnost) může v případě úloh s **vysokou mírou svítivosti** trvat v čase několik minut. Důvodem je to, že instance Engine se stará o všechny aktuální transakce na primárním a zachytit v sekundárním uzlu před převzetím služeb při selhání.

> [!IMPORTANT]
> Funkční omezení uživatelsky iniciované ruční převzetí služeb při selhání:
> - V rámci jedné spravované instance se každých **15 minut** může nacházet jedna (1) převzetí služeb při selhání.
> - Pro instance BC musí existovat kvorum replik pro požadavek převzetí služeb při selhání, které se má přijmout.
> - U instancí BC není možné určit, která čitelná sekundární replika má iniciovat převzetí služeb při selhání.
> - Převzetí služeb při selhání nebude povoleno, dokud nebude první úplné zálohování nové databáze dokončeno automatizovanými zálohovacími systémy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o vysoké dostupnosti spravované instance s [vysokou dostupností pro Azure SQL Managed instance](../database/high-availability-sla.md).
- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).

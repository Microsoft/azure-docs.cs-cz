---
title: Azure SQL Database – přečtěte si dotazy na replikách | Microsoft Docs
description: Databáze SQL Azure poskytuje schopnost načíst vyrovnávání jen pro čtení úlohy využití kapacity jen pro čtení replik - názvem čtení Škálováním na více systémů.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: sashan
ms.openlocfilehash: 7b504306e32f97a0392239f9e6adc6c460848580
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060004"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Použít jen pro čtení repliky načíst vyrovnávat zatížení dotazu jen pro čtení (preview)

**Čtení škálování** umožňuje načíst jen pro čtení zatížení serveru vyrovnávání Azure SQL Database pomocí kapacitu repliky jen pro čtení. 

## <a name="overview-of-read-scale-out"></a>Přehled čtení Škálováním na více systémů

Každou databázi v úrovni Premium ([na základě DTU nákupní model](sql-database-service-tiers-dtu.md)) nebo v kritické obchodní vrstvy ([nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md)) je automaticky zřízena několik AlwaysON repliky pro podporu smlouva SLA o dostupnosti. Tyto repliky jsou zřízené se stejnou úrovní výkonu jako repliky pro čtení a zápis používá standardní databázi připojení. **Čtení škálování** funkce umožňuje načíst vyrovnávat SQL Database jen pro čtení zatížení využití kapacity jednoho z repliky jen pro čtení místo sdílení repliky pro čtení a zápis. Tímto způsobem zatížení jen pro čtení bude izolovaná od hlavní úloh pro čtení a zápis a nebude mít vliv na jeho výkon. Tato funkce je určená pro aplikace, které zahrnují logicky oddělené jen pro čtení úlohy, jako jsou například analýzy a proto může získat výhody výkonu pomocí tuto dodatečnou kapacitu na Ne peníze navíc.

Pokud chcete používat funkce škálování pro čtení s danou databází, je potřeba explicitně povolit ho při vytváření databáze nebo později změnou jeho konfigurace pomocí prostředí PowerShell vyvoláním [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [ Nový-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) rutiny nebo prostřednictvím rozhraní REST API Azure Resource Manager [databáze - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate) metoda. 

Po povolení škálování pro čtení pro databázi aplikace připojení k databázi přesměrováni do repliky pro čtení a zápis nebo repliku jen pro čtení této databáze podle `ApplicationIntent` vlastnost nakonfigurované do aplikace připojovací řetězec. Informace o `ApplicationIntent` vlastnost, najdete v části [zadání záměru aplikace](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Pokud je zakázán pro čtení Škálováním na více systémů nebo nastavte vlastnost ReadScale v vrstvou nepodporované služby, všechna připojení jsou směrované repliky pro čtení a zápis, nezávisle `ApplicationIntent` vlastnost.

> [!NOTE]
> Verzi Preview úložiště dat dotazů a rozšířených událostí nejsou podporovány na replikách jen pro čtení.

## <a name="data-consistency"></a>Konzistence dat

Jednou z výhod Always ON je, že repliky jsou vždycky ve stavu transakční konzistence stavu, ale v různých okamžicích v čase může existovat určité malé zpoždění mezi jiné repliky. Čtení Škálováním na více systémů podporuje relace úroveň konzistence. Znamená to, když relace jen pro čtení znovu připojí po chybě připojení kvůli nedostupnosti repliky, může být přesměrován na repliku, která není 100 % aktuální s repliky pro čtení a zápis. Podobně pokud aplikace zapisuje data pomocí relace pro čtení a zápis a okamžitě přečte pomocí relaci jen pro čtení, je možné, že nejnovější aktualizace nejsou okamžitě viditelné. Je to proto znovu protokolu transakcí s replikami je asynchronní.

> [!NOTE]
> Jsou nízkou latenci replikace v rámci oblasti a navíc není obvyklé situace.


## <a name="connecting-to-a-read-only-replica"></a>Připojení k replice jen pro čtení

Když povolíte škálování pro čtení pro databázi, `ApplicationIntent` možnost v připojovacím řetězci klient poskytl stanoví, zda připojení se směruje na zápis repliku nebo na repliku jen pro čtení. Konkrétně Pokud `ApplicationIntent` hodnota je `ReadWrite` (výchozí hodnota), připojení se přesměruje na repliku databáze pro čtení a zápis. Toto je stejný jako stávající chování. Pokud `ApplicationIntent` hodnota je `ReadOnly`, připojení se směruje na čitelných replik.

Například následující připojovací řetězec připojení klienta k repliku jen pro čtení (nahraďte položky v lomených závorkách správné hodnoty pro vaše prostředí a vyřazení lomené závorky):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Některý z následujících připojovací řetězce klient připojí k repliky pro čtení a zápis (nahraďte položky v lomených závorkách správné hodnoty pro vaše prostředí a vyřazení lomené závorky):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Můžete ověřit, zda jste připojeni k repliku jen pro čtení spuštěním následujícího dotazu. Vrátí READ_ONLY při připojení k replice jen pro čtení.


```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```
> [!NOTE]
> V každém okamžiku pouze jeden z replik AlwaysON, je přístupné relací jen pro čtení.

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Povolení a zákaz čtení Škálováním na více systémů pomocí prostředí Azure PowerShell

Správa čtení Škálováním na více systémů v prostředí Azure PowerShell vyžaduje 2016 prosinec verzi prostředí Azure PowerShell nebo novější. V nejnovější verzi prostředí PowerShell [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Povolení nebo zakázání čtení Škálováním na více systémů v prostředí Azure PowerShell vyvoláním [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) rutiny a předejte požadovanou hodnotu – `Enabled` nebo `Disabled` – pro `-ReadScale` parametr. Alternativně můžete použít [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) vytvořte novou databázi s číst škálování povolené.

Například povolit čtení Škálováním na více systémů pro existující databázi (nahraďte položky v lomených závorkách správné hodnoty pro vaše prostředí a vyřazení lomené závorky):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Postup při zakázání čtení Škálováním na více systémů pro databázi (nahraďte položky v lomených závorkách správné hodnoty pro vaše prostředí a vyřazení lomené závorky):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Chcete-li vytvořit novou databázi s čtení horizontální povolena (nahraďte položky v lomených závorkách správné hodnoty pro vaše prostředí a vyřazení lomené závorky):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Povolení a zakázání čtení Škálováním na více systémů pomocí REST API služby Azure SQL Database

K vytvoření databáze pomocí čtení horizontální povoleno, nebo k povolení nebo zakázání čtení Škálováním na více systémů pro existující databázi, vytvořit nebo aktualizovat odpovídající entita databáze s `readScale` vlastnost nastavena na hodnotu `Enabled` nebo `Disabled` jako v následující ukázka požadavek.

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

Další informace najdete v tématu [databází - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate).

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Čtení Škálováním na více systémů pomocí geograficky replikované databáze

Pokud vaše jsou pomocí čtení horizontální načíst Vyrovnávání zatížení jen pro čtení v databázi, která je geograficky replikované (např. jako člen skupiny převzetí služeb při selhání), ujistěte se, že čtení Škálováním na více systémů je povolena na primárním serverem a geograficky replikované sekundární databáze. Tím bude zajištěno stejného efektu Vyrovnávání zatížení, když se aplikace připojí k nové primární po převzetí služeb při selhání. Pokud se připojujete k geograficky replikované sekundární databáze s měřítku pro čtení povolené, vaše relace s `ApplicationIntent=ReadOnly` , budou směrovány na jednu z replik stejným způsobem jako jsme směrovat připojení na primární databáze.  Relace bez `ApplicationIntent=ReadOnly` , budou směrovány na primární repliku geograficky replikované sekundární, což je také jen pro čtení. Protože geograficky replikované sekundární databáze má jiný koncový bod než primární databázi, v minulosti pro přístup k sekundární nebyl vyžadovala nastavit `ApplicationIntent=ReadOnly`. Pro zajištění zpětné kompatibility, `sys.geo_replication_links` DMV ukazuje `secondary_allow_connections=2` (jakékoli připojení klienta je povolena.).

> [!NOTE]
> Během ve verzi preview jsme nebude provádět pomocí kruhového dotazování nebo jiné zatížení vyrovnáváním směrování mezi místní repliky sekundární databázi. 


## <a name="next-steps"></a>Další postup

- Informace o použití prostředí PowerShell pro nastavení čtení Škálováním na více systémů najdete v tématu [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) rutiny.
- Informace o použití rozhraní REST API pro nastavení čtení Škálováním na více systémů najdete v tématu [databází - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate).

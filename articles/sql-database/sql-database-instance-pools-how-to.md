---
title: Průvodce s návody k instancí (náhled)
description: Tento článek popisuje, jak vytvořit a spravovat fondy instancí Azure SQL Database (náhled).
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: 4a27165d929cc9bc5f18e372f7f108887e466e43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299358"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Fondy instancí Azure SQL Database (preview) s návody

Tento článek obsahuje podrobnosti o tom, jak vytvořit a spravovat [fondy instancí](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Operace fondu instancí

V následující tabulce jsou uvedeny dostupné operace související s fondy instancí a jejich dostupnost na webu Azure Portal a PowerShellu.

|Příkaz|portál Azure|PowerShell|
|:---|:---|:---|
|Vytvoření fondu instancí|Ne|Ano|
|Aktualizovat fond instancí (omezený počet vlastností)|Ne |Ano |
|Kontrola využití a vlastností fondu instancí|Ne|Ano |
|Odstranit fond instancí|Ne|Ano|
|Vytvoření spravované instance ve fondu instancí|Ne|Ano|
|Aktualizovat využití prostředků spravované instance|Ano |Ano|
|Kontrola využití spravované instance a vlastností|Ano|Ano|
|Odstranění spravované instance z fondu|Ano|Ano|
|Vytvoření databáze ve spravované instanci umístěné ve fondu|Ano|Ano|
|Odstranění databáze ze spravované instance|Ano|Ano|

Dostupné [příkazy Prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Rutina |Popis |
|:---|:---|
|[Nový fond instance AzSql](/powershell/module/az.sql/new-azsqlinstancepool/) | Vytvoří fond instancí Azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Vrátí informace o fondu instancí Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Nastaví vlastnosti fondu instancí Azure SQL Database. |
|[Odebrat fond instance AzSql](/powershell/module/az.sql/remove-azsqlinstancepool/) | Odebere fond instancí Azure SQL Database. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Vrátí informace o využití fondu instancí Azure SQL. |


Pokud chcete používat PowerShell, [nainstalujte nejnovější verzi PowerShellu Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)a podle pokynů [nainstalujte modul Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Pro operace související s instancemi uvnitř fondů i s jedním instancí použijte standardní [příkazy spravované instance](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), ale vlastnost *název fondu instancí* musí být naplněna při použití těchto příkazů pro instanci ve fondu.

## <a name="how-to-deploy-managed-instances-into-pools"></a>Jak nasadit spravované instance do fondů

Proces nasazení instance do fondu se skládá z následujících dvou kroků:

1. Nasazení fondu jednorázových instancí. Jedná se o dlouho běžící operaci, kde doba trvání je stejná jako nasazení [jedné instance vytvořené v prázdné podsíti](sql-database-managed-instance.md#managed-instance-management-operations).

2. Opakované nasazení instance ve fondu instancí. Parametr fondu instancí musí být explicitně určen jako součást této operace. Jedná se o relativně rychlou operaci, která obvykle trvá až 5 minut.

Ve verzi Public Preview jsou oba kroky podporovány pouze pomocí šablon PowerShellu a Správce prostředků. Prostředí portálu Azure není momentálně dostupné.

Po nasazení spravované instance do fondu *můžete* pomocí portálu Azure změnit jeho vlastnosti na stránce cenové úrovně.


## <a name="create-an-instance-pool"></a>Vytvoření fondu instancí

Vytvoření fondu instancí:

1. [Vytvořte virtuální síť s podsítí](#create-a-virtual-network-with-a-subnet).
2. [Vytvořte fond instancí](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Vytvoření virtuální sítě s podsítí 

Pokud chcete umístit více fondů instancí do stejné virtuální sítě, přečtěte si následující články:

- [Určete velikost podsítě virtuální sítě pro spravovanou instanci Azure SQL Database](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Vytvořte novou virtuální síť a podsíť pomocí [šablony portálu Azure](sql-database-managed-instance-create-vnet-subnet.md) nebo postupujte podle pokynů pro [přípravu existující virtuální sítě](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Vytvoření fondu instancí 

Po dokončení předchozích kroků jste připraveni vytvořit fond instancí.

Následující omezení platí pro fondy instancí:

- Ve verzi Public Preview jsou k dispozici pouze obecné účely a gen5.
- Název fondu může obsahovat pouze malá písmena, čísla a pomlčku a nemůže začínat pomlčkou.
- Pokud chcete použít AHB (Azure Hybrid Benefit), použije se na úrovni fondu instancí. Typ licence můžete nastavit během vytváření fondu nebo jej aktualizovat kdykoli po vytvoření.

> [!IMPORTANT]
> Nasazení fondu instancí je dlouho běžící operace, která trvá přibližně 4,5 hodiny.

Chcete-li získat parametry sítě:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Vytvoření fondu instancí:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Vzhledem k tomu, že nasazení fondu instancí je dlouho běžící operace, musíte počkat, dokud nebude dokončena před spuštěním některého z následujících kroků v tomto článku.

## <a name="create-a-managed-instance-inside-the-pool"></a>Vytvoření spravované instance uvnitř fondu 

Po úspěšném nasazení fondu instancí je čas vytvořit instanci uvnitř.

Chcete-li vytvořit spravovanou instanci, proveďte následující příkaz:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Nasazení instance ve fondu trvá několik minut. Po vytvoření první instance lze vytvořit další instance:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Vytvoření databáze uvnitř instance 

Chcete-li vytvořit a spravovat databáze ve spravované instanci, která je uvnitř fondu, použijte příkazy jedné instance.

Vytvoření databáze uvnitř spravované instance:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Získání využití fondu instancí 
 
Získání seznamu instancí ve fondu:

```powershell
$instancePool | Get-AzSqlInstance
```


Jak získat využití prostředků fondu:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Chcete-li získat podrobný přehled o využití fondu a instancí uvnitř fondu:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Chcete-li v instanci uvést databáze:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Je limit 100 databází na fond (ne na instanci).


## <a name="scale-a-managed-instance-inside-a-pool"></a>Škálování spravované instance uvnitř fondu 


Po vyplnění spravované instance s databázemi, můžete sát omezení instance týkající se úložiště nebo výkonu. V takovém případě pokud využití fondu nebyla překročena, můžete škálovat instanci.
Škálování spravované instance uvnitř fondu je operace, která trvá několik minut. Předpokladem pro škálování je k dispozici virtuální jádra a úložiště na úrovni fondu instancí.

Aktualizace počtu virtuálních jader a velikosti úložiště:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aktualizace pouze velikosti úložiště:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Připojení ke spravované instanci uvnitř fondu

Chcete-li se připojit ke spravované instanci ve fondu, jsou vyžadovány následující dva kroky:

1. [Povolte veřejný koncový bod pro instanci](#enable-the-public-endpoint-for-the-instance).
2. [Přidejte příchozí pravidlo do skupiny zabezpečení sítě (NSG).](#add-an-inbound-rule-to-the-network-security-group)

Po dokončení obou kroků se můžete k instanci připojit pomocí veřejné adresy koncového bodu, portu a pověření zadaných během vytváření instance. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Povolení veřejného koncového bodu pro instanci

Povolení veřejného koncového bodu pro instanci lze provést prostřednictvím portálu Azure nebo pomocí následujícího příkazu PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Tento parametr lze nastavit také při vytváření instance.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Přidání příchozího pravidla do skupiny zabezpečení sítě 

Tento krok lze provést prostřednictvím portálu Azure nebo pomocí příkazů Prostředí PowerShell a lze provést kdykoli po podsíti je připraven pro spravovanou instanci.

Podrobnosti naleznete v tématu [Povolit veřejný provoz koncových bodů ve skupině zabezpečení sítě](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Přesunutí existující jedné instance do fondu instancí 
 
Přesunutí instancí do a z fondu je jedním z omezení náhledu ve veřejném náhledu. Řešení, které lze použít, závisí na obnovení databází v čase z instance mimo fond na instanci, která je již ve fondu. 

Obě instance musí být ve stejném předplatném a oblasti. Obnovení mezi oblastmi a mezi odběry není aktuálně podporováno.

Tento proces má období prostojů.

Přesunutí existujících databází:

1. Pozastavte úlohy spravované instance, ze které migrujete.
2. Vygenerujte skripty k vytvoření systémových databází a spouštějte je na instanci, která je uvnitř fondu instancí.
3. Proveďte obnovení bodu v čase každé databáze z jedné instance do instance ve fondu.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Najeďte svou aplikaci na novou instanci a pokračujte v jeho úlohách.

Pokud existuje více databází, opakujte proces pro každou databázi.


## <a name="next-steps"></a>Další kroky

- Funkce a seznam porovnání naleznete v tématu [běžné funkce SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [konfigurace virtuální sítě spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Rychlý start, který vytvoří spravovanou instanci a obnoví databázi ze záložního souboru, najdete [v tématu vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz využívající službu Migrace databáze Azure (DMS) pro migraci najdete v tématu [migrace spravovaných instancí pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pokročilé monitorování výkonu databáze spravovaných instancí s integrovanou inteligencí řešení potíží najdete v tématu [Monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách naleznete v [tématu SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/).

---
title: Nasazení spravované instance SQL do fondu instancí
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak vytvořit a spravovat fondy spravované instance Azure SQL (Preview).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793105"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Nasazení spravované instance Azure SQL do fondu instancí
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek poskytuje podrobné informace o tom, jak vytvořit [fond instancí](instance-pools-overview.md) a nasadit do něj spravovanou instanci Azure SQL. 

## <a name="instance-pool-operations"></a>Operace fondu instancí

V následující tabulce jsou uvedeny dostupné operace týkající se fondů instancí a jejich dostupnosti v Azure Portal a prostředí PowerShell.

|Příkaz|portál Azure|PowerShell|
|:---|:---|:---|
|Vytvoření fondu instancí|No|Yes|
|Aktualizace fondu instancí (omezený počet vlastností)|No |Yes |
|Ověření využití fondu instancí a vlastností|No|Yes |
|Odstranění fondu instancí|No|Yes|
|Vytvoření spravované instance v rámci fondu instancí|No|Yes|
|Aktualizace využití prostředků pro spravovanou instanci|Yes |Yes|
|Zkontroluje využití a vlastnosti spravované instance.|Yes|Yes|
|Odstranění spravované instance z fondu|Yes|Yes|
|Vytvoření databáze v instanci v rámci fondu|Yes|Yes|
|Odstranění databáze z spravované instance SQL|Yes|Yes|

Dostupné [Příkazy prostředí PowerShell](/powershell/module/az.sql/):

|Rutina |Popis |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Vytvoří fond spravovaných instancí SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Vrátí informace o fondu instancí. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Nastaví vlastnosti fondu instancí ve spravované instanci SQL. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Odebere fond instancí ve spravované instanci SQL. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Vrátí informace o využití fondu spravované instance SQL. |


Pokud chcete použít PowerShell, [nainstalujte nejnovější verzi prostředí PowerShell Core](/powershell/scripting/install/installing-powershell#powershell)a postupujte podle pokynů pro [instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps).

V případě operací souvisejících s instancemi v rámci fondů i s jednou instancí použijte [příkazy standardní spravované instance](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances), ale při použití těchto příkazů pro instanci ve fondu musí být naplněna vlastnost *název fondu instancí* .

## <a name="deployment-process"></a>Proces nasazení

Pokud chcete nasadit spravovanou instanci do fondu instancí, musíte nejdřív nasadit fond instancí, což je jednorázová dlouhotrvající operace, která je stejná jako při nasazení [jedné instance vytvořené v prázdné podsíti](sql-managed-instance-paas-overview.md#management-operations). Potom můžete nasadit spravovanou instanci do fondu, což je poměrně rychlá operace, která obvykle trvá až pět minut. Parametr fondu instancí se musí explicitně zadat jako součást této operace.

Ve verzi Public Preview se obě akce podporují jenom pomocí PowerShellu a Azure Resource Manager šablon. Prostředí Azure Portal není aktuálně k dispozici.

Po nasazení spravované instance do fondu *můžete* použít Azure Portal ke změně jejích vlastností na stránce s cenovou úrovní.

## <a name="create-a-virtual-network-with-a-subnet"></a>Vytvoření virtuální sítě s podsítí 

Chcete-li umístit více fondů instancí do stejné virtuální sítě, přečtěte si následující články:

- [Určete velikost podsítě virtuální sítě pro spravovanou instanci Azure SQL](vnet-subnet-determine-size.md).
- Vytvořte novou virtuální síť a podsíť pomocí [šablony Azure Portal](virtual-network-subnet-create-arm-template.md) nebo postupujte podle pokynů pro [přípravu existující virtuální sítě](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Vytvoření fondu instancí 

Po dokončení předchozích kroků jste připraveni vytvořit fond instancí.

Následující omezení platí pro fondy instancí:

- Ve verzi Public Preview jsou k dispozici pouze Pro obecné účely a Gen5.
- Název fondu může obsahovat jenom malá písmena, číslice a spojovníky a nemůže začínat spojovníkem.
- Pokud chcete použít Zvýhodněné hybridní využití Azure, použije se na úrovni fondu instancí. Typ licence můžete nastavit během vytváření fondu nebo ho aktualizovat kdykoli po vytvoření.

> [!IMPORTANT]
> Nasazení fondu instancí je dlouhodobě běžící operace, která trvá přibližně 4,5 hodin.

Postup získání parametrů sítě:

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
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Vzhledem k tomu, že nasazení fondu instancí je dlouhodobá operace, je třeba počkat na jeho dokončení, než spustíte některý z následujících kroků v tomto článku.

## <a name="create-a-managed-instance"></a>Vytvoření spravované instance

Po úspěšném nasazení fondu instancí je čas vytvořit spravovanou instanci uvnitř ní.

Chcete-li vytvořit spravovanou instanci, spusťte následující příkaz:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Nasazení instance do fondu trvá několik minut. Po vytvoření první instance je možné vytvořit další instance:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Vytvoření databáze 

Pokud chcete vytvářet a spravovat databáze ve spravované instanci, která je uvnitř fondu, použijte příkazy s jedinou instancí.

Vytvoření databáze uvnitř spravované instance:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Získat využití fondu 
 
Získání seznamu instancí v rámci fondu:

```powershell
$instancePool | Get-AzSqlInstance
```


Získání využití prostředků fondu:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


Získání podrobného přehledu využití fondu a instancí v rámci něj:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Výpis databází v instanci:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Pro každý fond je povolený limit 100 databází (ne na instanci).


## <a name="scale"></a>Měřítko 


Po naplnění spravované instance databázemi můžete vyhodnotit omezení pro instance týkající se úložiště nebo výkonu. V takovém případě, pokud se využití fondu nepřekročilo, můžete škálovat instanci.
Škálování spravované instance v rámci fondu je operace, která trvá několik minut. Předpoklad pro škálování je dostupný virtuální jádra a Storage na úrovni fondu instancí.

Aktualizace počtu virtuální jádra a velikosti úložiště:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aktualizace pouze velikosti úložiště:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Připojit 

Pokud se chcete připojit ke spravované instanci ve fondu, vyžadují se tyto dva kroky:

1. [Povolte pro instanci veřejný koncový bod](#enable-the-public-endpoint).
2. [Přidat příchozí pravidlo do skupiny zabezpečení sítě (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Po dokončení obou kroků se můžete připojit k instanci pomocí veřejné adresy koncového bodu, portu a přihlašovacích údajů zadaných během vytváření instance. 

### <a name="enable-the-public-endpoint"></a>Povolení veřejného koncového bodu

Povolení veřejného koncového bodu pro instanci lze provést prostřednictvím Azure Portal nebo pomocí následujícího příkazu prostředí PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Tento parametr lze nastavit také při vytváření instance.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Přidat příchozí pravidlo do skupiny zabezpečení sítě 

Tento krok se dá provést pomocí Azure Portal nebo pomocí příkazů PowerShellu a dá se provést kdykoli po přípravě podsítě na spravovanou instanci.

Podrobnosti najdete v tématu [povolení provozu veřejného koncového bodu ve skupině zabezpečení sítě](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Přesunutí existující jedné instance do fondu
 
Přesunutí instancí do fondu a ven je jedním z omezení verze Public Preview. Alternativní řešení využívá obnovení databází k určitému bodu v čase z instance mimo fond do instance, která je již ve fondu. 

Obě instance musí být ve stejném předplatném a oblasti. Obnovení mezi různými oblastmi a mezi předplatnými není v současné době podporováno.

Tento proces může mít dobu výpadku.

Přesunutí stávajících databází:

1. Pozastaví úlohy na spravované instanci, ze které migrujete.
2. Vygenerujte skripty pro vytváření systémových databází a proveďte je v instanci, která je uvnitř fondu instancí.
3. Proveďte obnovení jednotlivých databází z jedné instance do instance fondu v čase.

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

4. Nasměrujte aplikaci na novou instanci a obnovte její úlohy.

Pokud existuje více databází, opakujte tento postup pro každou databázi.


## <a name="next-steps"></a>Další kroky

- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](instance-create-quickstart.md).
- Kurz týkající se použití Azure Database Migration Service k migraci najdete v tématu [migrace spravované instance SQL pomocí Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Informace o pokročilém monitorování výkonu databáze spravované instance SQL s integrovanými funkcemi pro řešení potíží najdete v tématu [monitorování spravované instance Azure SQL pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [ceny za spravované instance SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
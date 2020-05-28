---
title: Nasazení spravované instance SQL do fondu instancí
titleSuffix: Azure SQL Managed Instance
description: Tento článek popisuje, jak vytvořit a spravovat fondy spravované instance Azure SQL (Preview).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab
ms.date: 09/05/2019
ms.openlocfilehash: c781e23b23f5dbaf8eba9efe4c27428ef35c7be1
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84113633"
---
# <a name="deploy-an-azure-sql-managed-instance-to-an-instance-pool"></a>Nasazení spravované instance Azure SQL do fondu instancí
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek poskytuje podrobné informace o tom, jak vytvořit [fondy instancí](instance-pools-overview.md) a nasadit na ni spravovanou instanci Azure SQL. 

## <a name="instance-pool-operations"></a>Operace fondu instancí

V následující tabulce jsou uvedeny dostupné operace týkající se fondů instancí a jejich dostupnosti v Azure Portal a prostředí PowerShell.

|Příkaz|portál Azure|PowerShell|
|:---|:---|:---|
|Vytvoření fondu instancí|Ne|Ano|
|Aktualizovat fond instancí (omezený počet vlastností)|Ne |Ano |
|Zkontroluje využití fondu instancí a vlastnosti.|Ne|Ano |
|Odstranit fond instancí|Ne|Ano|
|Vytvoření spravované instance SQL uvnitř fondu instancí|Ne|Ano|
|Aktualizovat využití prostředku spravované instance SQL|Ano |Ano|
|Zkontroluje využití a vlastnosti spravované instance SQL.|Ano|Ano|
|Odstranit spravovanou instanci SQL z fondu|Ano|Ano|
|Vytvoření databáze v instanci v rámci fondu|Ano|Ano|
|Odstranění databáze z spravované instance SQL|Ano|Ano|

Dostupné [Příkazy prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Rutina |Description |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Vytvoří fond spravovaných instancí SQL. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Vrátí informace o fondu instancí. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Nastaví vlastnosti fondu instancí ve spravované instanci SQL. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Odebere fond instancí ve spravované instanci SQL. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Vrátí informace o využití fondu spravované instance SQL. |


Pokud chcete použít PowerShell, [nainstalujte nejnovější verzi prostředí PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell)a postupujte podle pokynů pro [instalaci modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

V případě operací souvisejících s instancemi v rámci fondů i s jednou instancí použijte [příkazy standardní spravované instance](api-references-create-manage-instance.md#powershell-create-and-manage-managed-instances), ale při použití těchto příkazů pro instanci ve fondu musí být naplněna vlastnost *název fondu instancí* .

## <a name="deployment-process"></a>Proces nasazení

Chcete-li nasadit spravovanou instanci SQL do fondu instancí, je nutné nejprve nasadit fond instancí, což je jednorázová dlouhotrvající operace, která je stejná jako při nasazení [jedné instance vytvořené v prázdné podsíti](sql-managed-instance-paas-overview.md#management-operations). Potom můžete nasadit spravované instance SQL do fondu, což je poměrně rychlá operace, která obvykle trvá až pět minut. Parametr fondu instancí se musí explicitně zadat jako součást této operace.

Ve verzi Public Preview se obě akce podporují jenom pomocí PowerShellu a Správce prostředků šablon. Prostředí Azure Portal není aktuálně k dispozici.

Po nasazení spravované instance SQL do fondu *můžete* použít Azure Portal ke změně jejích vlastností na stránce s cenovou úrovní.

## <a name="create-virtual-network-with-a-subnet"></a>Vytvoření virtuální sítě s podsítí 

Chcete-li umístit více fondů instancí do stejné virtuální sítě, přečtěte si následující články:

- [Určete velikost podsítě virtuální sítě pro spravovanou instanci Azure SQL](vnet-subnet-determine-size.md).
- Vytvořte novou virtuální síť a podsíť pomocí [šablony Azure Portal](virtual-network-subnet-create-arm-template.md) nebo postupujte podle pokynů pro [přípravu existující virtuální sítě](vnet-existing-add-subnet.md).
 

## <a name="create-instance-pool"></a>Vytvoření fondu instancí 

Po dokončení předchozích kroků jste připraveni vytvořit fond instancí.

Následující omezení platí pro fondy instancí:

- Ve verzi Public Preview jsou k dispozici pouze Pro obecné účely a Gen5.
- Název fondu může obsahovat jenom malá písmena, číslice a spojovníky a nemůže začínat spojovníkem.
- Pokud chcete použít AHB (Zvýhodněné hybridní využití Azure), použije se na úrovni fondu instancí. Typ licence můžete nastavit během vytváření fondu nebo ho aktualizovat kdykoli po vytvoření.

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
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Vzhledem k tomu, že nasazení fondu instancí je dlouhodobá operace, je třeba počkat na jeho dokončení, než spustíte některý z následujících kroků v tomto článku.

## <a name="create-sql-managed-instance"></a>Vytvoření spravované instance SQL

Po úspěšném nasazení fondu instancí je čas vytvořit v něm spravovanou instanci SQL.

Chcete-li vytvořit spravovanou instanci SQL, spusťte následující příkaz:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Nasazení instance do fondu trvá několik minut. Po vytvoření první instance je možné vytvořit další instance:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Vytvoření databáze 

Chcete-li vytvářet a spravovat databáze ve spravované instanci SQL, která je uvnitř fondu, použijte příkazy s jedinou instancí.

Vytvoření databáze v rámci spravované instance SQL:

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


Po naplnění spravované instance SQL databázemi můžete vyhodnotit omezení pro instance týkající se úložiště nebo výkonu. V takovém případě, pokud se využití fondu nepřekročilo, můžete škálovat instanci.
Škálování spravované instance SQL uvnitř fondu je operace, která trvá několik minut. Předpoklad pro škálování je dostupný virtuální jádra a Storage na úrovni fondu instancí.

Aktualizace počtu virtuální jádra a velikosti úložiště:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Aktualizace pouze velikosti úložiště:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Připojit 

Pokud se chcete připojit ke spravované instanci SQL ve fondu, vyžadují se tyto dva kroky:

1. [Povolte pro instanci veřejný koncový bod](#enable-public-endpoint).
2. [Přidat příchozí pravidlo do skupiny zabezpečení sítě (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Po dokončení obou kroků se můžete připojit k instanci pomocí veřejné adresy koncového bodu, portu a přihlašovacích údajů zadaných během vytváření instance. 

### <a name="enable-public-endpoint"></a>Povolit veřejný koncový bod

Povolení veřejného koncového bodu pro instanci lze provést prostřednictvím Azure Portal nebo pomocí následujícího příkazu prostředí PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Tento parametr lze nastavit také při vytváření instance.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Přidat příchozí pravidlo do skupiny zabezpečení sítě 

Tento krok se dá provést pomocí Azure Portal nebo pomocí příkazů PowerShellu a dá se provést kdykoli po přípravě podsítě na spravovanou instanci.

Podrobnosti najdete v tématu [povolení provozu veřejného koncového bodu ve skupině zabezpečení sítě](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-existing-single-instance-to-pool"></a>Přesunout existující jednu instanci do fondu
 
Přesunutí instancí do fondu a ven je jedním z omezení verze Public Preview. Alternativní řešení využívá obnovení databází k určitému bodu v čase z instance mimo fond do instance, která je již ve fondu. 

Obě instance musí být ve stejném předplatném a oblasti. Obnovení mezi různými oblastmi a mezi předplatnými není v současné době podporováno.

Tento proces může mít dobu výpadku.

Přesunutí stávajících databází:

1. Pozastaví úlohy na spravované instanci SQL, ze které migrujete.
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

4. Nasměrujte svoji aplikaci na novou instanci a obnovte její úlohy.

Pokud existuje více databází, opakujte tento postup pro každou databázi.


## <a name="next-steps"></a>Další kroky

- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](../database/features-comparison.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě spravované instance SQL](connectivity-architecture-overview.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance SQL](instance-create-quickstart.md).
- Kurz pro migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migrace spravované instance SQL pomocí DMS](../../dms/tutorial-sql-server-to-managed-instance.md).
- Informace o pokročilém monitorování výkonu databáze spravované instance SQL s integrovanými funkcemi pro řešení potíží najdete v tématu [monitorování spravované instance Azure SQL pomocí Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [ceny za spravované instance SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

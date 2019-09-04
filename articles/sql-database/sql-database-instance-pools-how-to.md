---
title: Průvodce postupy pro fondy instancí Azure SQL Database (Preview) | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat fondy instancí Azure SQL Database (Preview).
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
ms.openlocfilehash: 5862a54c92de7395ce42865ae32d453e926048d8
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70294266"
---
# <a name="azure-sql-database-instance-pools-preview-how-to-guide"></a>Průvodce postupy pro Azure SQL Database fondy instancí (Preview)

Tento článek poskytuje podrobné informace o tom, jak vytvářet a spravovat [fondy instancí](sql-database-instance-pools.md).

## <a name="instance-pool-operations"></a>Operace fondu instancí

V následující tabulce jsou uvedeny dostupné operace týkající se fondů instancí a jejich dostupnosti v Azure Portal a prostředí PowerShell.

|Příkaz|portál Azure|PowerShell|
|:---|:---|:---|
|Vytvořit fond instancí|Ne|Ano|
|Aktualizovat fond instancí (omezený počet vlastností)|Ne |Ano |
|Zkontroluje využití fondu instancí a vlastnosti.|Ne|Ano |
|Odstranit fond instancí|Ne|Ano|
|Vytvoření spravované instance uvnitř fondu instancí|Ne|Ano|
|Aktualizace využití prostředků spravované instance|Ano |Ano|
|Zkontroluje využití a vlastnosti spravované instance.|Ano|Ano|
|Odstranit spravovanou instanci z fondu|Ano|Ano|
|Vytvoří databázi ve spravované instanci, která je umístěná ve fondu.|Ano|Ano|
|Odstraní databázi ze spravované instance.|Ano|Ano|

Dostupné [Příkazy prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.sql/)

|Rutiny |Popis |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Vytvoří fond instancí Azure SQL Database. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Vrátí informace o fondu instancí Azure SQL. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Nastaví vlastnosti fondu instancí Azure SQL Database. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Odebere fond instancí Azure SQL Database. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Vrátí informace o využití fondu instancí Azure SQL. |


Pokud chcete použít PowerShell, [nainstalujte nejnovější verzi prostředí PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core)a postupujte podle pokynů pro [instalaci modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

V případě operací souvisejících s instancemi v rámci fondů i s jednou instancí použijte [příkazy standardní spravované instance](sql-database-managed-instance-create-manage.md#powershell-create-and-manage-managed-instances), ale při použití těchto příkazů pro instanci ve fondu musí být naplněna vlastnost *název fondu instancí* .

## <a name="how-to-deploy-managed-instances-into-pools"></a>Jak nasadit spravované instance do fondů

Proces nasazení instance do fondu se skládá z následujících dvou kroků:

1. Nasazení fondu s jednorázovým instancí. Jedná se o dlouhodobou provozní operaci, kde doba trvání je stejná jako nasazení [jedné instance vytvořené v prázdné podsíti](sql-database-managed-instance.md#managed-instance-management-operations).

2. Opakované nasazení instance v fondu instancí. Parametr fondu instancí se musí explicitně zadat jako součást této operace. Jedná se o relativně rychlou operaci, která obvykle trvá až 5 minut.

Ve verzi Public Preview se oba kroky podporují jenom pomocí PowerShellu a Správce prostředků šablon. Prostředí Azure Portal není aktuálně k dispozici.

Po nasazení spravované instance do fondu *můžete* použít Azure Portal ke změně jejích vlastností na stránce s cenovou úrovní.


## <a name="create-an-instance-pool"></a>Vytvoření fondu instancí

Vytvoření fondu instancí:

1. [Vytvořte virtuální síť s podsítí](#create-a-virtual-network-with-a-subnet).
2. [Vytvořte fond instancí](#create-an-instance-pool).


### <a name="create-a-virtual-network-with-a-subnet"></a>Vytvoření virtuální sítě s podsítí 

Chcete-li umístit více fondů instancí do stejné virtuální sítě, přečtěte si následující články:

- [Určete velikost podsítě virtuální sítě pro Azure SQL Database spravovanou instanci](sql-database-managed-instance-determine-size-vnet-subnet.md).
- Vytvořte novou virtuální síť a podsíť pomocí [šablony Azure Portal](sql-database-managed-instance-create-vnet-subnet.md) nebo postupujte podle pokynů pro [přípravu existující virtuální sítě](sql-database-managed-instance-configure-vnet-subnet.md).
 


### <a name="create-an-instance-pool"></a>Vytvoření fondu instancí 

Po dokončení předchozích kroků jste připraveni vytvořit fond instancí.

Následující omezení platí pro fondy instancí:

- Ve verzi Public Preview jsou k dispozici pouze Pro obecné účely a Gen5.
- Název fondu může obsahovat jenom malá písmena, číslice a spojovníky a nemůže začínat spojovníkem.
- K získání ID podsítě použijte `Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork`.
- Pokud chcete použít AHB (Zvýhodněné hybridní využití Azure), použije se na úrovni fondu instancí. Typ licence můžete nastavit během vytváření fondu nebo ho aktualizovat kdykoli po vytvoření.

> [!IMPORTANT]
> Nasazení fondu instancí je dlouhodobě běžící operace, která trvá přibližně 4,5 hodin.

Vytvoření fondu instancí:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId "/subscriptions/subscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/miPoolVirtualNetwork/subnets/miPoolSubnet" `
  -LicenseType "LicenseIncluded" `
  -VCore 80 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Vzhledem k tomu, že nasazení fondu instancí je dlouhodobá operace, je třeba počkat na jeho dokončení, než spustíte některý z následujících kroků v tomto článku.

## <a name="create-a-managed-instance-inside-the-pool"></a>Vytvoření spravované instance v rámci fondu 

Po úspěšném nasazení fondu instancí je čas vytvořit instanci uvnitř ní.

Chcete-li vytvořit spravovanou instanci, spusťte následující příkaz:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 2 -StorageSizeInGB 256
```

Nasazení instance do fondu trvá několik minut. Po vytvoření první instance je možné vytvořit další instance:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-pool-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database-inside-an-instance"></a>Vytvoření databáze v instanci 

Pokud chcete vytvářet a spravovat databáze ve spravované instanci, která je uvnitř fondu, použijte příkazy s jedinou instancí.

Vytvoření databáze uvnitř spravované instance:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-instance-pool-usage"></a>Získat využití fondu instancí 
 
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


## <a name="scale-a-managed-instance-inside-a-pool"></a>Škálování spravované instance v rámci fondu 


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



## <a name="connect-to-a-managed-instance-inside-a-pool"></a>Připojení ke spravované instanci v rámci fondu

Pokud se chcete připojit ke spravované instanci ve fondu, vyžadují se tyto dva kroky:

1. [Povolte pro instanci veřejný koncový bod](#enable-the-public-endpoint-for-the-instance).
2. [Přidat příchozí pravidlo do skupiny zabezpečení sítě (NSG)](#add-an-inbound-rule-to-the-network-security-group).

Po dokončení obou kroků se můžete připojit k instanci pomocí veřejné adresy koncového bodu, portu a přihlašovacích údajů zadaných během vytváření instance. 

### <a name="enable-the-public-endpoint-for-the-instance"></a>Povolení veřejného koncového bodu pro instanci

Povolení veřejného koncového bodu pro instanci lze provést prostřednictvím Azure Portal nebo pomocí následujícího příkazu prostředí PowerShell:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Tento parametr lze nastavit také při vytváření instance.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Přidat příchozí pravidlo do skupiny zabezpečení sítě 

Tento krok se dá provést pomocí Azure Portal nebo pomocí příkazů PowerShellu a dá se provést kdykoli po přípravě podsítě na spravovanou instanci.

Podrobnosti najdete v tématu [povolení provozu veřejného koncového bodu ve skupině zabezpečení sítě](sql-database-managed-instance-public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-inside-an-instance-pool"></a>Přesunout existující jednu instanci v rámci fondu instancí 
 
Přesunutí instancí do fondu a ven je jedním z omezení verze Public Preview. Alternativní řešení, které se dá použít, spoléhá na obnovení databází k určitému bodu v čase z instance mimo fond na instanci, která už je ve fondu. 

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

4. Nasměrujte svoji aplikaci na novou instanci a obnovte její úlohy.

Pokud existuje více databází, opakujte tento postup pro každou databázi.


## <a name="next-steps"></a>Další postup

- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě VNet spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz pro migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migrace spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pro pokročilé monitorování výkonu databáze spravované instance s integrovanými funkcemi pro odstraňování potíží najdete informace v tématu [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [SQL Database ceny za Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).

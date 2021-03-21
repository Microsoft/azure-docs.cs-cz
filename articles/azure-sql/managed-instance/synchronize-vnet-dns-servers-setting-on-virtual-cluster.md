---
title: Synchronizovat nastavení serverů DNS virtuální sítě ve virtuálním clusteru spravované instance SQL
description: Přečtěte si, jak synchronizovat nastavení serverů DNS virtuální sítě na virtuálním clusteru spravované instance SQL.
services: sql-database
ms.service: sql-managed-instance
author: srdan-bozovic-msft
ms.author: srbozovi
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b95afe513dba2f1da9556b27ec17bcccc9fe88e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173547"
---
# <a name="synchronize-virtual-network-dns-servers-setting-on-sql-managed-instance-virtual-cluster"></a>Synchronizovat nastavení serverů DNS virtuální sítě ve virtuálním clusteru spravované instance SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje, kdy a jak synchronizovat nastavení serverů DNS virtuální sítě na virtuálním clusteru spravované instance SQL.

## <a name="when-to-synchronize-the-dns-setting"></a>Kdy synchronizovat nastavení DNS

Existuje několik scénářů (například Databázová pošta nebo servery odkazované na jiné instance SQL Serveru ve vašem cloudovém nebo hybridním prostředí), které vyžadují překlad názvů privátních hostitelů ze služby SQL Managed Instance. V takovém případě musíte nakonfigurovat vlastní DNS v rámci Azure. Podrobnosti najdete v tématu [Konfigurace vlastního DNS pro službu Azure SQL Managed Instance](custom-dns-configure.md).

Pokud se tato změna implementuje po vytvoření [virtuálního clusteru](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) , který je hostitelem spravované instance, budete muset synchronizovat nastavení serverů DNS ve virtuálním clusteru s konfigurací virtuální sítě.

> [!IMPORTANT]
> Synchronizace nastavení serverů DNS bude mít vliv na všechny spravované instance hostované ve virtuálním clusteru.

## <a name="how-to-synchronize-the-dns-setting"></a>Jak synchronizovat nastavení DNS

### <a name="azure-rbac-permissions-required"></a>Vyžaduje se oprávnění Azure RBAC.

Konfigurace serveru DNS pro synchronizaci uživatelů bude muset mít jednu z následujících rolí Azure:

- Role vlastníka předplatného nebo
- Role přispěvatele spravované instance nebo
- Vlastní role s následujícím oprávněním:
  - `Microsoft.Sql/virtualClusters/updateManagedInstanceDnsServers/action`

### <a name="use-azure-powershell"></a>Použití Azure Powershell

Načte virtuální síť, kde se nastavila aktualizace serverů DNS.

```PowerShell
$ResourceGroup = 'enter resource group of virtual network'
$VirtualNetworkName = 'enter virtual network name'
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroup $ResourceGroup -Name $VirtualNetworkName
```
K synchronizaci konfigurace serverů DNS pro všechny virtuální clustery v podsíti použijte příkaz PowerShellu [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) .

```PowerShell
Get-AzSqlVirtualCluster `
    | where SubnetId -match $virtualNetwork.Id `
    | select Id `
    | Invoke-AzResourceAction -Action updateManagedInstanceDnsServers -Force
```
### <a name="use-the-azure-cli"></a>Použití Azure CLI

Načte virtuální síť, kde se nastavila aktualizace serverů DNS.

```Azure CLI
resourceGroup="auto-failover-group"
virtualNetworkName="vnet-fog-eastus"
virtualNetwork=$(az network vnet show -g $resourceGroup -n $virtualNetworkName --query "id" -otsv)
```

Použijte příkaz Azure CLI [AZ Resource Invoke-Action](/cli/azure/resource#az_resource_invoke_action) pro synchronizaci konfigurace serverů DNS pro všechny virtuální clustery v podsíti.

```Azure CLI
az sql virtual-cluster list --query "[? contains(subnetId,'$virtualNetwork')].id" -o tsv \
    | az resource invoke-action --action updateManagedInstanceDnsServers --ids @-
```
## <a name="next-steps"></a>Další kroky

- Další informace o konfiguraci vlastního DNS [nakonfigurujte vlastní DNS pro spravovanou instanci SQL Azure](custom-dns-configure.md).
- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).

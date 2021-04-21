---
title: Konfigurace skupiny dostupnosti (& PowerShellu AZ CLI)
description: Pomocí PowerShellu nebo rozhraní příkazového řádku Azure můžete vytvořit cluster s podporou převzetí služeb při selhání systému Windows, naslouchací proces skupiny dostupnosti a interní nástroj pro vyrovnávání zatížení na virtuálním počítači s SQL Server v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: ffd4ec6eff94589abbc8af70ecf9c0f7dc168962
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766928"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Ke konfiguraci skupiny dostupnosti pro SQL Server na virtuálním počítači Azure použijte PowerShell nebo AZ CLI. 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak pomocí [PowerShellu](/powershell/scripting/install/installing-powershell) nebo rozhraní příkazového [řádku Azure](/cli/azure/sql/vm) nasadit cluster s podporou převzetí služeb při selhání s Windows, přidat SQL Server virtuální počítače do clusteru a vytvořit interní nástroj pro vyrovnávání zatížení a naslouchací proces pro skupinu dostupnosti Always On. 

Nasazení skupiny dostupnosti se pořád provádí ručně pomocí SQL Server Management Studio (SSMS) nebo jazyka Transact-SQL (T-SQL). 

V tomto článku se používá prostředí PowerShell a příkaz AZ CLI ke konfiguraci prostředí skupiny dostupnosti, je to také možné z [Azure Portal](availability-group-azure-portal-configure.md), pomocí [šablon Azure pro rychlý Start](availability-group-quickstart-template-configure.md)nebo [ručně](availability-group-manually-configure-tutorial.md) . 

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci skupiny dostupnosti Always On musíte mít následující požadavky: 

- [Předplatné Azure](https://azure.microsoft.com/free/)
- Skupina prostředků s řadičem domény. 
- Jeden nebo víc virtuálních počítačů připojených k doméně [v Azure se spuštěným SQL Server 2016 (nebo novější) Enterprise Edition](./create-sql-vm-portal.md) ve *stejné* skupině dostupnosti nebo v *různých* zónách dostupnosti, které jsou [zaregistrované pomocí rozšíření agenta SQL IaaS](sql-agent-extension-manually-register-single-vm.md).  
- Nejnovější verzi [PowerShellu](/powershell/scripting/install/installing-powershell) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli). 
- K dispozici jsou dvě (nepoužívané v žádné entitě) IP adresy. Jedna je určena pro interní nástroj pro vyrovnávání zatížení. Druhá je pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupina dostupnosti. Pokud používáte existující Nástroj pro vyrovnávání zatížení, budete pro naslouchací proces skupiny dostupnosti potřebovat jenom jednu dostupnou IP adresu. 

## <a name="permissions"></a>Oprávnění

Ke konfiguraci skupiny dostupnosti Always On pomocí Azure CLI potřebujete následující oprávnění: 

- Existující účet uživatele domény, který má v doméně oprávnění **vytvořit objekt počítače** . Například účet správce domény má obvykle dostatečná oprávnění (například: account@domain.com ). _Tento účet by měl být taky součástí místní skupiny správců na každém virtuálním počítači, aby se vytvořil cluster._
- Uživatelský účet domény, který řídí SQL Server. 
 
## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště 

Cluster potřebuje účet úložiště, který bude fungovat jako disk s kopií cloudu. Můžete použít libovolný existující účet úložiště, nebo můžete vytvořit nový účet úložiště. Pokud chcete použít existující účet úložiště, přeskočte dopředu k další části. 

Následující fragment kódu vytvoří účet úložiště: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> `az sql: 'vm' is not in the 'az sql' command group`Pokud používáte zastaralou verzi rozhraní příkazového řádku Azure CLI, může se zobrazit chyba. Stáhněte si [nejnovější verzi Azure CLI](/cli/azure/install-azure-cli-windows) , abyste mohli tuto chybu přeskočit.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definování metadat clusteru

Příkaz Azure CLI [AZ SQL VM Group](/cli/azure/sql/vm/group) Group spravuje metadata služby Windows Server failover cluster (WSFC), která je hostitelem skupiny dostupnosti. Metadata clusteru zahrnují doménu služby Active Directory, účty clusteru, účty úložiště, které se mají použít jako disk s kopií cloudu, a SQL Server verze. Pomocí [AZ SQL VM Group Create](/cli/azure/sql/vm/group#az_sql_vm_group_create) definujte metadata pro službu WSFC, aby při přidání prvního SQL Server virtuálního počítače se cluster vytvořil tak, jak je definovaný. 

Následující fragment kódu definuje metadata pro cluster:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Přidání virtuálních počítačů do clusteru

Přidáním prvního virtuálního počítače SQL Server do clusteru se vytvoří cluster. Pomocí příkazu [AZ SQL VM Add-to-Group](/cli/azure/sql/vm#az_sql-vm_add_to_group) se vytvoří cluster s dříve zadaným názvem, nainstaluje roli clusteru na SQL Server virtuální počítače a přidá je do clusteru. Další použití `az sql vm add-to-group` příkazu přidá do nově vytvořeného clusteru více SQL Server virtuálních počítačů. 

Následující fragment kódu vytvoří cluster a přidá do něj první SQL Server virtuální počítač: 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Tento příkaz slouží k přidání dalších SQL Server virtuálních počítačů do clusteru. Upravte pouze `-n` parametr pro SQL Server název virtuálního počítače. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Ověřit cluster 

Aby byl cluster s podporou převzetí služeb při selhání podporovaný společností Microsoft, musí projít ověřením clusteru. Připojte se k virtuálnímu počítači pomocí preferované metody, jako je například protokol RDP (Remote Desktop Protocol) (RDP), a ověřte, že cluster před pokračováním projde ověření. Pokud to neuděláte, zastavte cluster v nepodporovaném stavu. 

Cluster můžete ověřit pomocí Správce clusteru s podporou převzetí služeb při selhání (FCM) nebo následujícího příkazu PowerShellu:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Vytvořit skupinu dostupnosti

Ručně vytvořte skupinu dostupnosti obvyklým způsobem, a to pomocí [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShellu](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)nebo [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> V tuto chvíli nevytvářejte naslouchací proces, protože to *se provádí prostřednictvím* rozhraní příkazového řádku Azure v následujících oddílech.  

## <a name="create-internal-load-balancer"></a>Vytvořit interní nástroj pro vyrovnávání zatížení

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Naslouchací proces skupiny dostupnosti Always On vyžaduje interní instanci Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení poskytuje "plovoucí" IP adresu pro naslouchací proces skupiny dostupnosti, který umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud jsou virtuální počítače s SQL Server ve skupině dostupnosti součástí stejné sady dostupnosti, můžete použít základní nástroj pro vyrovnávání zatížení. V opačném případě je nutné použít standardní nástroj pro vyrovnávání zatížení.  

> [!NOTE]
> Interní nástroj pro vyrovnávání zatížení by měl být ve stejné virtuální síti jako instance virtuálních počítačů SQL Server. 

Následující fragment kódu vytvoří interní nástroj pro vyrovnávání zatížení:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> Prostředek veřejné IP adresy pro každý virtuální počítač SQL Server by měl mít standardní SKU, aby byl kompatibilní s nástrojem Load Balancer úrovně Standard. Pokud chcete zjistit SKU prostředku veřejné IP adresy vašeho virtuálního počítače, přejděte do **skupiny prostředků**, vyberte prostředek **veřejné IP adresy** pro požadovaný SQL Server virtuální počítač a vyhledejte hodnotu v části **SKU** v podokně **Přehled** .  

## <a name="create-listener"></a>Vytvořit naslouchací proces

Po ručním vytvoření skupiny dostupnosti můžete naslouchací proces vytvořit pomocí [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_create). 

*ID prostředku podsítě* je hodnota `/subnets/<subnetname>` PŘIPOJENá k ID prostředku prostředku virtuální sítě. Identifikace ID prostředku podsítě:
   1. V [Azure Portal](https://portal.azure.com)přejít do skupiny prostředků. 
   1. Vyberte prostředek virtuální sítě. 
   1. V podokně **Nastavení** vyberte **vlastnosti** . 
   1. Identifikujte ID prostředku pro virtuální síť a přidejte `/subnets/<subnetname>` na konec IT a vytvořte ID prostředku podsítě. Například:
      - Vaše ID prostředku virtuální sítě je: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Název vaší podsítě: `default`
      - Proto je ID prostředku podsítě: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Následující fragment kódu vytvoří naslouchací proces skupiny dostupnosti:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Změna počtu replik 
Když nasazujete skupinu dostupnosti pro SQL Server virtuálních počítačů hostovaných v Azure, připravujeme vrstvu složitosti. Prostředky jsou nyní spravovány poskytovatelem prostředků a skupinou virtuálních počítačů. V takovém případě, když přidáváte nebo odebíráte repliky ve skupině dostupnosti, je k dispozici další krok aktualizace metadat naslouchacího procesu s informacemi o SQL Serverch virtuálních počítačích. Pokud upravujete počet replik ve skupině dostupnosti, musíte taky pomocí příkazu [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener#az_sql_vm_group_ag_listener_update) aktualizovat naslouchací proces s metadaty SQL Server virtuálních počítačů. 


### <a name="add-a-replica"></a>Přidání repliky

Přidání nové repliky do skupiny dostupnosti:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Přidejte SQL Server virtuální počítač do skupiny clusterů:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Pomocí SQL Server Management Studio přidejte instanci SQL Server jako repliku ve skupině dostupnosti.
1. Přidejte do naslouchacího procesu metadata SQL Server virtuálních počítačů:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Přidejte SQL Server virtuální počítač do skupiny clusterů:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Pomocí SQL Server Management Studio přidejte instanci SQL Server jako repliku ve skupině dostupnosti.
1. Přidejte do naslouchacího procesu metadata SQL Server virtuálních počítačů:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Odebrání repliky

Odebrání repliky ze skupiny dostupnosti:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Odstraňte repliku ze skupiny dostupnosti pomocí SQL Server Management Studio. 
1. Z naslouchacího procesu odeberte metadata SQL Server virtuálních počítačů:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Odeberte virtuální počítač SQL Server z clusteru:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Odstraňte repliku ze skupiny dostupnosti pomocí SQL Server Management Studio. 
1. Z naslouchacího procesu odeberte metadata SQL Server virtuálních počítačů:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Odeberte virtuální počítač SQL Server z clusteru:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Odebrat naslouchací proces
Pokud později potřebujete odebrat naslouchací proces skupiny dostupnosti nakonfigurovaný pomocí Azure CLI, musíte projít rozšířením agenta SQL IaaS. Protože je naslouchací proces zaregistrován prostřednictvím rozšíření agenta SQL IaaS, stačí ho odstranit prostřednictvím SQL Server Management Studio nedostatečné. 

Nejlepším způsobem je odstranit ho pomocí rozšíření agenta SQL IaaS pomocí následujícího fragmentu kódu v Azure CLI. Tím se z rozšíření agenta SQL IaaS Odebere metadata naslouchacího procesu skupiny dostupnosti. Také fyzicky odstraní naslouchací proces ze skupiny dostupnosti. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Odebrat cluster

Odeberte všechny uzly z clusteru, abyste je zničili, a pak odeberte metadata clusteru z rozšíření agenta SQL IaaS. Můžete to provést pomocí Azure CLI nebo PowerShellu. 


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív odeberte všechny SQL Server virtuálních počítačů z clusteru: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Pokud se jedná o jediné virtuální počítače v clusteru, bude cluster zničen. Pokud v clusteru dojde k jiným virtuálním počítačům z SQL Server odebraných virtuálních počítačů, ostatní virtuální počítače se neodeberou a cluster nebude zničen. 

Dále odeberte metadata clusteru z rozšíření agenta SQL IaaS: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nejdřív odeberte všechny SQL Server virtuálních počítačů z clusteru. Tím dojde k fyzickému odebrání uzlů z clusteru a zničení clusteru: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Pokud se jedná o jediné virtuální počítače v clusteru, bude cluster zničen. Pokud v clusteru dojde k jiným virtuálním počítačům z SQL Server odebraných virtuálních počítačů, ostatní virtuální počítače se neodeberou a cluster nebude zničen. 

Dále odeberte metadata clusteru z rozšíření agenta SQL IaaS: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích: 

* [Přehled SQL Server virtuálních počítačů](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Nejčastější dotazy týkající se SQL Server virtuálních počítačů](frequently-asked-questions-faq.md)
* [Poznámky k verzi pro virtuální počítače s SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Přepínání modelů licencování pro SQL Server virtuální počítač](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Přehled skupin dostupnosti Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfigurace instance serveru pro skupiny dostupnosti Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Správa skupiny dostupnosti &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorování skupin dostupnosti &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Přehled příkazů jazyka Transact-SQL pro skupiny dostupnosti Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Přehled rutin PowerShellu pro skupiny dostupnosti Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)
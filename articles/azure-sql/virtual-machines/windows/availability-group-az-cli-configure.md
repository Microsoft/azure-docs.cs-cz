---
title: Konfigurace skupiny dostupnosti pomocí Azure CLI
description: Pomocí Azure CLI můžete vytvořit cluster s podporou převzetí služeb při selhání Windows, naslouchací proces skupiny dostupnosti a interní nástroj pro vyrovnávání zatížení na virtuálním počítači s SQL Server v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 23667e8a50d2ef3a7a31aeb165c0b5d43bcf3eca
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219625"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Použití rozhraní příkazového řádku Azure ke konfiguraci skupiny dostupnosti Always On pro SQL Server na virtuálním počítači Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek popisuje, jak pomocí rozhraní příkazového [řádku Azure](/cli/azure/sql/vm?view=azure-cli-latest/) nasadit cluster s podporou převzetí služeb při selhání systému Windows, přidat SQL Server virtuálních počítačů do clusteru a vytvořit interní nástroj pro vyrovnávání zatížení a naslouchací proces pro skupinu dostupnosti Always On. Nasazení skupiny dostupnosti Always On se pořád provádí ručně prostřednictvím SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Požadavky
K automatizaci nastavení skupiny dostupnosti Always On pomocí Azure CLI musíte mít následující požadavky: 
- [Předplatné Azure](https://azure.microsoft.com/free/).
- Skupina prostředků s řadičem domény. 
- Jeden nebo víc virtuálních počítačů připojených k doméně [v Azure se spuštěným SQL Server 2016 (nebo novější) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ve *stejné skupině dostupnosti nebo v různých zónách dostupnosti* , které jsou [zaregistrované u poskytovatele prostředků virtuálního počítače SQL](sql-vm-resource-provider-register.md).  
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) 
- K dispozici jsou dvě (nepoužívané v žádné entitě) IP adresy. Jedna je určena pro interní nástroj pro vyrovnávání zatížení. Druhá je pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupina dostupnosti. Pokud používáte existující Nástroj pro vyrovnávání zatížení, budete pro naslouchací proces skupiny dostupnosti potřebovat jenom jednu dostupnou IP adresu. 

## <a name="permissions"></a>Oprávnění
Ke konfiguraci skupiny dostupnosti Always On pomocí Azure CLI potřebujete následující oprávnění: 

- Existující účet uživatele domény, který má v doméně oprávnění **vytvořit objekt počítače** . Například účet správce domény má obvykle dostatečná oprávnění (například: account@domain.com ). _Tento účet by měl být taky součástí místní skupiny správců na každém virtuálním počítači, aby se vytvořil cluster._
- Uživatelský účet domény, který řídí SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Krok 1: vytvoření účtu úložiště jako určujícího cloudu
Cluster potřebuje účet úložiště, který bude fungovat jako disk s kopií cloudu. Můžete použít libovolný existující účet úložiště, nebo můžete vytvořit nový účet úložiště. Pokud chcete použít existující účet úložiště, přeskočte dopředu k další části. 

Následující fragment kódu vytvoří účet úložiště: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> `az sql: 'vm' is not in the 'az sql' command group`Pokud používáte zastaralou verzi rozhraní příkazového řádku Azure CLI, může se zobrazit chyba. Stáhněte si [nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) , abyste mohli tuto chybu přeskočit.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Krok 2: definování metadat clusteru Windows s podporou převzetí služeb při selhání
Příkaz Azure CLI [AZ SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) Group spravuje metadata služby Windows Server failover cluster (WSFC), která je hostitelem skupiny dostupnosti. Metadata clusteru zahrnují doménu služby Active Directory, účty clusteru, účty úložiště, které se mají použít jako disk s kopií cloudu, a SQL Server verze. Pomocí [AZ SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) definujte metadata pro službu WSFC, aby při přidání prvního SQL Server virtuálního počítače se cluster vytvořil tak, jak je definovaný. 

Následující fragment kódu definuje metadata pro cluster:
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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Krok 3: Přidání SQL Server virtuálních počítačů do clusteru
Přidáním prvního virtuálního počítače SQL Server do clusteru se vytvoří cluster. Pomocí příkazu [AZ SQL VM Add-to-Group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) se vytvoří cluster s dříve zadaným názvem, nainstaluje roli clusteru na SQL Server virtuální počítače a přidá je do clusteru. Další použití `az sql vm add-to-group` příkazu přidá do nově vytvořeného clusteru více SQL Server virtuálních počítačů. 

Následující fragment kódu vytvoří cluster a přidá do něj první SQL Server virtuální počítač: 

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

## <a name="step-4-create-the-availability-group"></a>Krok 4: Vytvoření skupiny dostupnosti
Ručně vytvořte skupinu dostupnosti obvyklým způsobem, a to pomocí [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShellu](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)nebo [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> V tuto chvíli nevytvářejte naslouchací proces, protože to *se provádí prostřednictvím* rozhraní příkazového řádku Azure v následujících oddílech.  

## <a name="step-5-create-the-internal-load-balancer"></a>Krok 5: vytvoření interního nástroje pro vyrovnávání zatížení

Naslouchací proces skupiny dostupnosti Always On vyžaduje interní instanci Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení poskytuje "plovoucí" IP adresu pro naslouchací proces skupiny dostupnosti, který umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud jsou virtuální počítače s SQL Server ve skupině dostupnosti součástí stejné sady dostupnosti, můžete použít základní nástroj pro vyrovnávání zatížení. V opačném případě je nutné použít standardní nástroj pro vyrovnávání zatížení.  

> [!NOTE]
> Interní nástroj pro vyrovnávání zatížení by měl být ve stejné virtuální síti jako instance virtuálních počítačů SQL Server. 

Následující fragment kódu vytvoří interní nástroj pro vyrovnávání zatížení:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Prostředek veřejné IP adresy pro každý virtuální počítač SQL Server by měl mít standardní SKU, aby byl kompatibilní s nástrojem Load Balancer úrovně Standard. Pokud chcete zjistit SKU prostředku veřejné IP adresy vašeho virtuálního počítače, přejděte do **skupiny prostředků**, vyberte prostředek **veřejné IP adresy** pro požadovaný SQL Server virtuální počítač a vyhledejte hodnotu v části **SKU** v podokně **Přehled** .  

## <a name="step-6-create-the-availability-group-listener"></a>Krok 6: vytvoření naslouchacího procesu skupiny dostupnosti
Po ručním vytvoření skupiny dostupnosti můžete naslouchací proces vytvořit pomocí [AZ SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*ID prostředku podsítě* je hodnota `/subnets/<subnetname>` PŘIPOJENá k ID prostředku prostředku virtuální sítě. Identifikace ID prostředku podsítě:
   1. V [Azure Portal](https://portal.azure.com)přejít do skupiny prostředků. 
   1. Vyberte prostředek virtuální sítě. 
   1. V podokně **Nastavení** vyberte **vlastnosti** . 
   1. Identifikujte ID prostředku pro virtuální síť a přidejte `/subnets/<subnetname>` na konec IT a vytvořte ID prostředku podsítě. Příklad:
      - Vaše ID prostředku virtuální sítě je:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Název vaší podsítě:`default`
      - Proto je ID prostředku podsítě:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Následující fragment kódu vytvoří naslouchací proces skupiny dostupnosti:

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

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Změna počtu replik ve skupině dostupnosti
Když nasazujete skupinu dostupnosti pro SQL Server virtuálních počítačů hostovaných v Azure, připravujeme vrstvu složitosti. Prostředky jsou nyní spravovány poskytovatelem prostředků a skupinou virtuálních počítačů. V takovém případě, když přidáváte nebo odebíráte repliky ve skupině dostupnosti, je k dispozici další krok aktualizace metadat naslouchacího procesu s informacemi o SQL Serverch virtuálních počítačích. Pokud upravujete počet replik ve skupině dostupnosti, musíte taky pomocí příkazu [AZ SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) aktualizovat naslouchací proces s metadaty SQL Server virtuálních počítačů. 


### <a name="add-a-replica"></a>Přidání repliky

Přidání nové repliky do skupiny dostupnosti:

1. Přidejte do clusteru SQL Server virtuální počítač:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
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

### <a name="remove-a-replica"></a>Odebrání repliky

Odebrání repliky ze skupiny dostupnosti:

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

## <a name="remove-the-availability-group-listener"></a>Odebrání naslouchacího procesu skupiny dostupnosti
Pokud později potřebujete odebrat naslouchací proces skupiny dostupnosti nakonfigurovaný pomocí Azure CLI, musíte projít poskytovatelem prostředků virtuálního počítače SQL. Vzhledem k tomu, že naslouchací proces je zaregistrován prostřednictvím poskytovatele prostředků virtuálního počítače SQL, stačí ho odstranit přes SQL Server Management Studio nedostatečné. 

Nejlepším způsobem je odstranit ho přes poskytovatele prostředků SQL VM pomocí následujícího fragmentu kódu v Azure CLI. Tím se odebere metadata naslouchacího procesu skupiny dostupnosti z poskytovatele prostředků virtuálního počítače SQL. Také fyzicky odstraní naslouchací proces ze skupiny dostupnosti. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

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

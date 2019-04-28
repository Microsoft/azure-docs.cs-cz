---
title: Pomocí Azure CLI virtuálního počítače SQL ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure
description: 'Pomocí Azure CLI vytvořit Cluster převzetí služeb při selhání Windows, naslouchacího procesu skupiny dostupnosti a interního nástroje pro vyrovnávání zatížení na virtuální počítač s SQL serverem v Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591294"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Pomocí Azure CLI virtuálního počítače SQL ke konfiguraci skupiny dostupnosti Always On pro SQL Server na Virtuálním počítači Azure
Tento článek popisuje způsob použití [příkazového řádku Azure SQL VM](/cli/azure/sql/vm?view=azure-cli-latest/) nasadit Windows Failover Cluster (WSFC) a přidejte virtuální počítače SQL serveru do clusteru, jakož i vytvořit interní nástroj pro vyrovnávání zatížení a naslouchacího procesu pro skupiny dostupnosti Always On.  Skutečné nasazení skupiny dostupnosti Always On se stále provádí ručně přes SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Požadavky
K automatizaci instalací skupiny dostupnosti Always On pomocí příkazového řádku virtuálního počítače SQL Azure, musí už máte splněné následující požadavky: 
- [Předplatného Azure](https://azure.microsoft.com/free/).
- Skupina prostředků s řadičem domény. 
- Jeden nebo více připojených k doméně [virtuálních počítačů v Azure spuštěné systému SQL Server 2016 (nebo vyšší) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) v *stejné skupiny dostupnosti nebo různých zón dostupnosti* jsou [zaregistrovaný u poskytovatele prostředků SQL VM](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli). 
- (Není používána entitu) k dispozici dvě IP adresy, jeden pro interní nástroj pro vyrovnávání zatížení a jeden pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupiny dostupnosti. Pokud se používá stávajícího nástroje pro vyrovnávání zatížení, je potřeba jenom jeden dostupnou IP adresu pro naslouchací proces skupiny dostupnosti. 

## <a name="permissions"></a>Oprávnění
Následující účet oprávnění je potřeba pro konfiguraci skupiny dostupnosti Always On pomocí příkazového řádku virtuálního počítače SQL Azure. 

- Existující účet uživatele domény, který má oprávnění k vytvoření objektu počítače v doméně.  Například účet správce domény obvykle má dostatečná oprávnění (ex: account@domain.com). _Tento účet také musí být součástí místní skupiny správců na každém virtuálním počítači k vytvoření clusteru._
- Uživatelský účet domény, který řídí službu systému SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Krok 1 – Vytvoření účtu úložiště jako disk s kopií cloudu
Cluster potřebuje účet úložiště tak, aby fungoval jako disk s kopií cloudu. Můžete použít jakýkoli existující účet úložiště, nebo můžete vytvořit nový účet úložiště. Pokud chcete použít existující účet úložiště, přeskočte k další části. 

Následující fragment kódu vytvoří účet úložiště: 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Může se zobrazit chyba `az sql: 'vm' is not in the 'az sql' command group` Pokud používáte zastaralou verzi rozhraní příkazového řádku Azure. Stáhněte si [nejnovější verzi Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) k vyřešení této chyby.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Krok 2: definování Metadata clusteru převzetí služeb při selhání Windows
Rozhraní příkazového řádku Azure SQL VM [skupiny virtuálních počítačů sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) příkaz skupina spravuje metadat služby Windows Failover Cluster (WSFC), který je hostitelem skupiny dostupnosti. Metadata Clusterové zahrnuje doménu AD, clusteru účty, účty úložiště, který se použije jako disk s kopií cloudu a verze systému SQL Server. Použití [vytvořte skupiny virtuálních počítačů sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) můžete definovat metadata služby WSFC tak, aby při přidání prvního virtuálního počítače SQL serveru, je cluster vytvořen, jak jsou definovány. 

Následující fragment kódu definuje metadat pro cluster:
```azurecli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Krok 3 – přidání do clusteru virtuálních počítačů s SQL serverem
Přidání prvního virtuálního počítače SQL serveru do clusteru vytvoří cluster. [Az sql skupiny virtuálních počítačů přidat na-](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) příkaz vytvoří cluster s názvem uvedeným dřív, nainstaluje role clusteru virtuální počítače SQL serveru a přidá je do clusteru. Následné použití `az sql vm add-to-group` příkaz přidá další virtuální počítače SQL serveru do nově vytvořený cluster. 

Následující fragment kódu vytvoří cluster a přidá do ní prvního virtuálního počítače SQL serveru: 

```azurecli
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
Tento příkaz slouží k přidání všechny ostatní virtuální počítače SQL serveru do clusteru, jen pro úpravy `-n` parametr pro název virtuálního počítače s SQL serverem. 

## <a name="step-4---create-availability-group"></a>Krok 4: vytvoření skupiny dostupnosti
Ruční vytvoření skupiny dostupnosti jako obvykle, buď pomocí [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), nebo [příkazů jazyka Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Proveďte **není** v tuto chvíli vytvořit naslouchací proces, protože to se provádí prostřednictvím rozhraní příkazového řádku Azure v následujících částech.  

## <a name="step-5---create-internal-load-balancer"></a>Krok 5: vytvoření interního nástroje Load Balancer

Always On naslouchací proces skupiny dostupnosti (AG) vyžaduje vnitřní Azure zatížení nástroje pro vyrovnávání (ILB). ILB zajišťující "plovoucí" IP adresu naslouchacího procesu AG, které umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud virtuální počítače SQL serveru ve skupině dostupnosti jsou součástí stejné skupiny dostupnosti, pak můžete použít základní nástroje pro vyrovnávání zatížení; v opačném případě budete muset použít standardní nástroje pro vyrovnávání zatížení.  **ILB musí být ve stejné virtuální síti jako instance virtuálního počítače s SQL serverem.** 

Následující fragment kódu vytvoří interního nástroje pro vyrovnávání zatížení:

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > Standardní SKU se kvůli kompatibilitě s Load balanceru úrovně Standard by měl mít prostředek veřejné IP pro každý virtuální počítač s SQL serverem. Určit SKU prostředek veřejné IP adresy Virtuálního počítače, přejděte na vaše **skupiny prostředků**vyberte vaše **veřejnou IP adresu** prostředek požadovaný virtuální počítač SQL Server a vyhledejte hodnotu v rámci **SKU**  z **přehled** podokně.  

## <a name="step-6---create-availability-group-listener"></a>Krok 6: vytvoření naslouchacího procesu skupiny dostupnosti
Po ruční vytváření skupiny dostupnosti můžete vytvořit naslouchací proces, pomocí [az sql vm-naslouchacího procesu ag](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- **ID prostředku podsítě** je hodnota `/subnets/<subnetname>` připojenou k prostředku virtuální sítě v ID prostředku. Pokud chcete zjistit ID prostředku podsítě, postupujte takto:
   1. Přejděte do vaší skupiny prostředků v [webu Azure portal](https://portal.azure.com). 
   1. Vyberte prostředek virtuální sítě. 
   1. Vyberte **vlastnosti** v **nastavení** podokně. 
   1. Určení ID prostředku pro virtuální síť a připojení `/subnets/<subnetname>`na konec objektu má vytvořit ID podsítě prostředku. Příklad:
        - Svoje ID prostředku virtuální sítě je: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Podsíť se `default`.
        - Proto je svoje ID prostředku podsítě: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Následující fragment kódu vytvoří naslouchací proces skupiny dostupnosti:

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Upravit počet replik ve skupině dostupnosti
Při nasazení skupiny dostupnosti hostovaných v Azure, virtuální počítače s SQL serverem v jako prostředky jsou teď spravované přes poskytovatele prostředků a tím je další úroveň složitosti `virtual machine group`. V důsledku toho při přidávání nebo odstraňování repliky do skupiny dostupnosti, je další krok aktualizace metadat naslouchací proces s informacemi o virtuální počítače SQL serveru. Proto při úpravě počet replik ve skupině dostupnosti, musíte taky použít [aktualizace naslouchacího procesu ag skupiny virtuálních počítačů sql az](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) příkaz k aktualizaci naslouchací proces s metadaty virtuální počítače SQL serveru. 


### <a name="add-a-replica"></a>Přidejte repliku

Chcete-li přidat novou repliku do skupiny dostupnosti, postupujte takto:

1. Přidání virtuálního počítače SQL serveru do clusteru:
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Slouží k přidání instance systému SQL Server jako repliky v rámci skupiny dostupnosti SQL Server Management Studio (SSMS).
1. Přidání metadat virtuálního počítače s SQL serverem k naslouchacímu procesu:
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Odstranění repliky

Odstranění repliky ze skupiny dostupnosti, postupujte takto:

1. Odeberte repliky ze skupiny dostupnosti pomocí SQL Server Management Studio (SSMS). 
1. Odebrání metadat virtuálního počítače s SQL serverem z naslouchací proces:
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Odebrání virtuálního počítače SQL serveru z clusteru:
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Odebrání naslouchacího procesu skupiny dostupnosti
Pokud později potřebujete odebrat naslouchacího procesu skupiny dostupnosti nakonfigurováno pomocí rozhraní příkazového řádku Azure, musíte projít přes poskytovatele prostředků virtuálního počítače s SQL. Protože naslouchací proces je zaregistrované prostřednictvím poskytovatele prostředků virtuálního počítače s SQL, pouhým odstraněním přes SQL Server Management Studio není dostatečná. Je ve skutečnosti je potřeba odstranit prostřednictvím poskytovatele prostředků virtuálního počítače SQL pomocí Azure CLI. To odebere metadata naslouchacího procesu AG od zprostředkovatele prostředků virtuálního počítače s SQL a fyzicky odstraní naslouchací proces skupiny dostupnosti. 

Následující fragment kódu odstraní naslouchacího procesu skupiny dostupnosti SQL, od obou tohoto poskytovatele prostředků SQL a ze skupiny dostupnosti: 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících článcích: 

* [Přehled virtuálního počítače s SQL serverem](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy k virtuálnímu počítači SQL serveru](virtual-machines-windows-sql-server-iaas-faq.md)
* [Zpráva k vydání verze virtuálního počítače s SQL serverem](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Přepínání licenční modely pro virtuální počítač s SQL serverem](virtual-machines-windows-sql-ahb.md)
* [Přehled skupin dostupnosti Always On &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfigurace skupin dostupnosti Always On instance serveru pro &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Správa skupiny dostupnosti &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitorování dostupnosti skupiny &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Přehled příkazů jazyka Transact-SQL pro skupin dostupnosti Always On &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Přehled rutin Powershellu pro skupiny dostupnosti AlwaysOn &#40;systému SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

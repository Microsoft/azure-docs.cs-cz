---
title: Konfigurace skupiny dostupnosti (Azure CLI)
description: Pomocí příkazového příkazového příkazu k vytvoření clusteru s podporou převzetí služeb při selhání systému Windows, naslouchací proces skupiny dostupnosti a interního nástroje pro vyrovnávání zatížení na virtuálním počítači SQL Server v Azure.
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
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022372"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Použití rozhraní příkazového příkazu k Azure ke konfiguraci skupiny dostupnosti always on pro SQL Server na virtuálním počítači Azure
Tento článek popisuje, jak pomocí [příkazového příkazového příkazu Azure](/cli/azure/sql/vm?view=azure-cli-latest/) nasadit cluster s podporou převzetí služeb při selhání systému Windows, přidat virtuální počítače SQL Server do clusteru a vytvořit interní nástroje pro vyrovnávání zatížení a naslouchací proces pro skupinu dostupnosti Always On. Nasazení skupiny dostupnosti vždy se stále provádí ručně prostřednictvím SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Požadavky
Chcete-li automatizovat nastavení skupiny dostupnosti vždy na pomocí azure cli, musíte mít následující požadavky: 
- Předplatné [Azure](https://azure.microsoft.com/free/).
- Skupina prostředků s řadičem domény. 
- Jeden nebo více virtuálních počítačů spojených s doménou [v Azure se spuštěnou edicí SQL Server 2016 (nebo novější) Enterprise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ve stejné skupině *dostupnosti nebo v různých zónách dostupnosti,* které byly [registrované u poskytovatele prostředků virtuálního počítače SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- [Azure CLI](/cli/azure/install-azure-cli). 
- Dvě dostupné (nepoužívané žádným subjektem) IP adresy. Jedním z nich je pro vnitřní vyrovnávání zatížení. Druhý je pro naslouchací proces skupiny dostupnosti ve stejné podsíti jako skupina dostupnosti. Pokud používáte existující vytápěč, potřebujete pouze jednu dostupnou IP adresu pro naslouchací proces skupiny dostupnosti. 

## <a name="permissions"></a>Oprávnění
Ke konfiguraci skupiny dostupnosti Always On pomocí rozhraní příkazového příkazu Azure potřebujete následující oprávnění účtu: 

- Existující uživatelský účet domény, který má oprávnění **Vytvořit objekt počítače** v doméně. Například účet správce domény má obvykle dostatečná account@domain.comoprávnění (například: ). _Tento účet by měl být také součástí skupiny místního správce na každém virtuálním počítači k vytvoření clusteru._
- Uživatelský účet domény, který řídí službu SQL Server. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Krok 1: Vytvoření účtu úložiště jako cloudového svědka
Cluster potřebuje účet úložiště, aby mohl fungovat jako cloudový důkaz. Můžete použít libovolný existující účet úložiště nebo můžete vytvořit nový účet úložiště. Pokud chcete použít existující účet úložiště, přeskočte na další část. 

Účet úložiště vytvoří následující fragment kódu: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Chyba se může `az sql: 'vm' is not in the 'az sql' command group` zobrazit, pokud používáte zastaralou verzi rozhraní příkazového příkazu k řešení Azure. Stáhněte si [nejnovější verzi rozhraní příkazového příkazu Konzumu Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) abyste se dostali přes tuto chybu.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Krok 2: Definování metadat clusteru s podporou převzetí služeb při selhání systému Windows
Skupina příkazů [skupiny AZ SQL VM skupiny](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) Azure CLI spravuje metadata služby WSFC (Cluster s podporou převzetí služeb při selhání) systému Windows Server, která je hostitelem skupiny dostupnosti. Metadata clusteru zahrnují doménu služby Active Directory, účty clusteru, účty úložiště, které mají být použity jako důkaz cloudu, a verzi serveru SQL Server. Pomocí [vytvoření skupiny vm sql az](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) definujte metadata pro WSFC tak, aby při přidání prvního virtuálního serveru SQL Server byl cluster vytvořen podle definice. 

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

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Krok 3: Přidání virtuálních připojení SQL Server do clusteru
Přidáním prvního virtuálního virtuálního serveru SQL Server do clusteru se vytvoří cluster. Příkaz [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) vytvoří cluster s dříve daným názvem, nainstaluje roli clusteru na virtuální chody SQL Server a přidá je do clusteru. Následné použití `az sql vm add-to-group` příkazu přidat další virtuální chodsql server do nově vytvořeného clusteru. 

Následující fragment kódu vytvoří cluster a přidá do něj první virtuální modul SQL Server: 

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
Pomocí tohoto příkazu můžete do clusteru přidat další virtuální servery SQL Server. Upravte `-n` pouze parametr pro název virtuálního soudu SQL Server. 

## <a name="step-4-create-the-availability-group"></a>Krok 4: Vytvoření skupiny dostupnosti
Ručně vytvořte skupinu dostupnosti obvyklým způsobem pomocí [sql server management studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)nebo [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Nevytvářejte* naslouchací proces v tomto okamžiku, protože se to provádí prostřednictvím příkazového příkazu k příkazu Azure v následujících částech.  

## <a name="step-5-create-the-internal-load-balancer"></a>Krok 5: Vytvoření interního systému vyrovnávání zatížení

Naslouchací proces skupiny dostupnosti always on vyžaduje interní instanci Azure Load Balancer. Interní vykladač zatížení poskytuje "plovoucí" IP adresu pro naslouchací proces skupiny dostupnosti, který umožňuje rychlejší převzetí služeb při selhání a opětovné připojení. Pokud jsou virtuální servery SQL Server ve skupině dostupnosti součástí stejné skupiny dostupnosti, můžete použít základní nástroje pro vyrovnávání zatížení. V opačném případě je třeba použít standardní vyrovnávání zatížení.  

> [!NOTE]
> Interní nástroj pro vyrovnávání zatížení by měl být ve stejné virtuální síti jako instance virtuálního počítače SQL Server. 

Následující fragment kódu vytvoří interní systém vyrovnávání zatížení:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Veřejný prostředek IP pro každý virtuální počítač SQL Server by měl mít standardní skladovou položku, která by byla kompatibilní se standardním nástroji pro vyrovnávání zatížení. Pokud chcete určit skladovou položku veřejného prostředku IP virtuálního počítače, přejděte na **Skupinu prostředků**, vyberte prostředek **veřejné IP adresy** pro požadovaný virtuální počítač SQL Server a vyhledejte hodnotu pod **skladovou položkou** v podokně **Přehled.**  

## <a name="step-6-create-the-availability-group-listener"></a>Krok 6: Vytvoření naslouchací proces skupiny dostupnosti
Po ručním vytvoření skupiny dostupnosti můžete vytvořit naslouchací proces pomocí [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*ID prostředku podsítě* je `/subnets/<subnetname>` hodnota připojená k ID prostředku prostředku virtuální sítě. Identifikace ID prostředku podsítě:
   1. Přejděte do skupiny prostředků na [webu Azure Portal](https://portal.azure.com). 
   1. Vyberte prostředek virtuální sítě. 
   1. V podokně **Nastavení** vyberte **Vlastnosti.** 
   1. Identifikujte ID prostředku `/subnets/<subnetname>` pro virtuální síť a připojte ho na konec, abyste vytvořili ID prostředku podsítě. Například:
      - ID vašeho prostředku virtuální sítě je:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Název podsítě je:`default`
      - ID prostředku podsítě je tedy:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


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
Při nasazování skupiny dostupnosti do virtuálních aplikací SQL Server hostovaných v Azure je další vrstva složitosti. Zprostředkovatel prostředků a skupina virtuálních strojů nyní spravují prostředky. V důsledku toho při přidávání nebo odebírání replik ve skupině dostupnosti, je další krok aktualizace metadat naslouchacího procesu s informacemi o virtuálních serverech SQL Server. Při úpravě počtu replik ve skupině dostupnosti, musíte také použít [az sql vm skupiny ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) příkaz aktualizovat naslouchací proces s metadaty virtuálních aplikací SQL Server. 


### <a name="add-a-replica"></a>Přidání repliky

Přidání nové repliky do skupiny dostupnosti:

1. Přidejte virtuální hod SQL Server do clusteru:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Pomocí aplikace SQL Server Management Studio přidejte instanci serveru SQL Server jako repliku v rámci skupiny dostupnosti.
1. Přidejte do naslouchací procesu metadata virtuálního soudu serveru SQL Server:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Odebrání repliky

Odebrání repliky ze skupiny dostupnosti:

1. Odeberte repliku ze skupiny dostupnosti pomocí aplikace SQL Server Management Studio. 
1. Odeberte metadata virtuálního soudu SQL Server z naslouchací proces:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Odebrání virtuálního serveru SQL Server z clusteru:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Odebrání naslouchací proces skupiny dostupnosti
Pokud později potřebujete odebrat naslouchací proces skupiny dostupnosti nakonfigurovaný pomocí příkazového příkazu k onomu Azure, musíte projít poskytovatelem prostředků virtuálního počítače SQL. Vzhledem k tomu, že naslouchací proces je registrován prostřednictvím zprostředkovatele prostředků virtuálního virtuálního provozu SQL, stačí jej odstranění prostřednictvím SQL Server Management Studio je nedostatečná. 

Nejlepší metodou je odstranit prostřednictvím zprostředkovatele prostředků virtuálního počítače SQL pomocí následujícího fragmentu kódu v nastavení příkazového příkazu Konzumu Azure. Tím odeberete metadata posluchače skupiny dostupnosti z poskytovatele prostředků virtuálního virtuálního připojení SQL. Také fyzicky odstraní naslouchací proces ze skupiny dostupnosti. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích: 

* [Přehled virtuálních měn SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Nejčastější dotazy pro virtuální servery SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Poznámky k verzi pro virtuální servery SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Přepínání licenčních modelů pro virtuální modul SQL Server](virtual-machines-windows-sql-ahb.md)
* [Přehled skupin dostupnosti Always On &#40;&#41;serveru SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfigurace instance serveru pro skupiny dostupnosti always on &#40;sql server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Správa skupiny dostupnosti &#40;&#41;serveru SQL Server](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Sledování skupin dostupnosti &#40;sql server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Přehled příkazů Transact-SQL pro skupiny dostupnosti always on &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Přehled rutin prostředí PowerShell pro skupiny dostupnosti always on &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

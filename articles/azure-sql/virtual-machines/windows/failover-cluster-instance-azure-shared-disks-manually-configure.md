---
title: Vytvoření FCI pomocí sdílených disků Azure (Preview)
description: Pomocí sdílených disků Azure vytvořte instanci clusteru s podporou převzetí služeb při selhání (FCI) s SQL Server v Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: 6e32f183709aca8a78f8448f2d6e6b63a77f2133
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272646"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Vytvoření FCI se sdílenými disky Azure (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek vysvětluje, jak vytvořit instanci clusteru s podporou převzetí služeb při selhání (FCI) pomocí sdílených disků Azure s SQL Server v Azure Virtual Machines (VM). 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech pro clustery](hadr-cluster-best-practices.md). 


## <a name="prerequisites"></a>Požadavky 

Před dokončením kroků v tomto článku byste už měli mít:

- Předplatné Azure. Začněte [zdarma](https://azure.microsoft.com/free/). 
- [Dva nebo více virtuálních počítačů se systémem Windows Azure](failover-cluster-instance-prepare-vm.md). Podporují se skupiny [dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md) a [skupiny umístění pro Proximity](../../../virtual-machines/windows/co-location.md#proximity-placement-groups) (PPGs). Pokud použijete PPG, musí všechny uzly existovat ve stejné skupině.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Nejnovější verze [prostředí PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0). 


## <a name="add-azure-shared-disk"></a>Přidat sdílený disk Azure
Nasaďte spravovaný SSD úrovně Premium disk s povolenou funkcí sdíleného disku. Nastavte, `maxShares` aby se **zarovnal počet uzlů clusteru** , aby bylo možné sdílet disk ve všech FCI uzlech. 

Pomocí následujícího postupu přidejte sdílený disk Azure: 


1. Následující skript uložte jako *SharedDiskConfig.jsna*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```


2. Spusťte *SharedDiskConfig.jsna* pomocí prostředí PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Pro každý virtuální počítač inicializujte připojené sdílené disky jako tabulku oddílů GUID (GPT) a naformátujte je jako nový technologický systém souborů (NTFS) spuštěním tohoto příkazu: 

   ```powershell
   $resourceGroup = "<your resource group name>"
       $location = "<region of your shared disk>"
       $ppgName = "<your proximity placement groups name>"
       $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
           -Name "<your VM node name>"
       $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
           -DiskName "<your shared disk name>"
       $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
           -CreateOption Attach -ManagedDiskId $dataDisk.Id `
           -Lun <available LUN  check disk setting of the VM>
    update-AzVm -VM $vm -ResourceGroupName $resourceGroup
   ```


## <a name="create-failover-cluster"></a>Vytvořit cluster pro převzetí služeb při selhání

Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:

- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání.
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.


# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání. Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání. Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Další informace najdete v tématu [cluster pro převzetí služeb při selhání: objekt sítě s clustery](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurace kvora

Nakonfigurujte řešení kvora, které nejlépe vyhovuje vašim obchodním potřebám. Můžete nakonfigurovat disk s kopií [clusteru](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), disk s [kopií cloudu](/windows-server/failover-clustering/deploy-cloud-witness)nebo [určující sdílenou složku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Další informace najdete v tématu [kvorum s SQL Servermi virtuálními počítači](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Ověřit cluster
Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, udělejte na jednom z těchto virtuálních počítačů následující:

1. V části **Správce serveru**vyberte **nástroje**a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **Akce**a pak vyberte **ověřit konfiguraci**.
1. Vyberte **Další**.
1. V části **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **Spustit pouze vybrané testy**. 
1. Vyberte **Další**.
1. V části **Výběr testu**vyberte všechny testy *s výjimkou* **úložiště** .

## <a name="test-cluster-failover"></a>Testovací převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte pravým tlačítkem na svůj cluster, vyberte **Další akce**  >  **přesunout základní prostředek clusteru**  >  **Vybrat uzel**a pak vyberte druhý uzel clusteru. Přesuňte základní prostředek clusteru do každého uzlu clusteru a pak ho přesuňte zpátky do primárního uzlu. Pokud můžete cluster úspěšně přesunout do každého uzlu, budete připraveni nainstalovat SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do ostatních uzlů":::

## <a name="create-sql-server-fci"></a>Vytvořit SQL Server FCI

Po nakonfigurování clusteru s podporou převzetí služeb při selhání a všech součástí clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokol RDP (Remote Desktop Protocol) (RDP).

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby všechny základní prostředky clusteru byly na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na adrese `C:\SQLServer_<version number>_Full` . 

1. Vyberte **Nastavení**.

1. V **SQL Server centra instalace**vyberte možnost **instalace**.

1. Vyberte **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server FCI.

Datové adresáře FCI musí být na sdílených discích Azure. 

1. Po dokončení pokynů v průvodci instalační program nainstaluje SQL Server FCI na první uzel.

1. Až instalační program nainstaluje FCI na první uzel, připojte se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**a pak vyberte možnost **instalace**.

1. Vyberte **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie, která obsahuje SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste některou z těchto imagí nepoužili, nainstalujte nástroje SQL Server samostatně. Další informace najdete v tématu [stažení SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >

## <a name="register-with-the-sql-vm-rp"></a>Zaregistrujte se pomocí SQL VM RP.

Pokud chcete spravovat SQL Server virtuální počítač z portálu, zaregistrujte ho pomocí poskytovatele prostředků virtuálního počítače SQL (RP) v [režimu zjednodušené správy](sql-vm-resource-provider-register.md#lightweight-management-mode), v současné době se ale jedná o jediný režim podporovaný FCI a SQL Server na virtuálních počítačích Azure. 


Zaregistrujte SQL Server virtuální počítač v jednoduchém režimu pomocí prostředí PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurace možností připojení 

Pokud chcete směrovat provoz odpovídající aktuálnímu primárnímu uzlu, nakonfigurujte možnost připojení, která je vhodná pro vaše prostředí. Můžete vytvořit [Nástroj pro vyrovnávání zatížení Azure](hadr-vnn-azure-load-balancer-configure.md) , nebo pokud používáte SQL Server 2019 CU2 + a Windows Server 2016 (nebo novější), můžete místo toho zobrazit náhled funkce [názvu distribuované sítě](hadr-distributed-network-name-dnn-configure.md) . 

## <a name="limitations"></a>Omezení

- Podporuje se jenom registrace u poskytovatele prostředků virtuálního počítače SQL v [režimu zjednodušené správy](sql-vm-resource-provider-register.md#management-modes) .

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, nakonfigurujte připojení k vašemu FCI pomocí [názvu virtuální sítě a nástroje pro vyrovnávání zatížení Azure](hadr-vnn-azure-load-balancer-configure.md) nebo [názvu DISTRIBUOVANÉ sítě (DNN)](hadr-distributed-network-name-dnn-configure.md). 

Pokud se pro vás nejedná o vhodné řešení úložiště FCI, doporučujeme místo toho vytvořit FCI pomocí [prémiových sdílených složek](failover-cluster-instance-premium-file-share-manually-configure.md) nebo [prostory úložiště s přímým přístupem](failover-cluster-instance-storage-spaces-direct-manually-configure.md) . 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech konfigurace clusteru](hadr-cluster-best-practices.md).

Další informace najdete tady: 
- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

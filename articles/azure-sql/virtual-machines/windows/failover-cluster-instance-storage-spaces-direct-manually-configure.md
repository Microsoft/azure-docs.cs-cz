---
title: Vytvoření FCI pomocí Prostory úložiště s přímým přístupem
description: Pomocí Prostory úložiště s přímým přístupem můžete vytvořit instanci clusteru s podporou převzetí služeb při selhání (FCI) s SQL Server na virtuálních počítačích Azure.
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
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 3cc579615a69b659bc1a4736984f0b3dcd6edb6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272521"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Vytvoření FCI s využitím Prostory úložiště s přímým přístupem (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek vysvětluje, jak vytvořit instanci clusteru s podporou převzetí služeb při selhání (FCI) pomocí [prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) s SQL Server v Azure Virtual Machines (virtuálních počítačích). Prostory úložiště s přímým přístupem se chová jako softwarová síť síti vSAN (software), která synchronizuje úložiště (datové disky) mezi uzly (virtuální počítače Azure) v clusteru Windows. 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech pro clustery](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Přehled 

[Prostory úložiště s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) podporuje dva typy architektury: sblížené a konvergované. Nakonvergovaná infrastruktura umístí úložiště na stejné servery, které hostují Clusterovou aplikaci, aby bylo úložiště v každém uzlu SQL Server FCI. 

Následující diagram znázorňuje kompletní řešení, které využívá Prostory úložiště s přímým přístupem s SQL Server na virtuálních počítačích Azure: 

![Diagram kompletního řešení s využitím Prostory úložiště s přímým přístupem prosblíženého](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Předchozí diagram znázorňuje následující prostředky ve stejné skupině prostředků:

- Dva virtuální počítače v clusteru s podporou převzetí služeb při selhání systému Windows Server. Když je virtuální počítač v clusteru s podporou převzetí služeb při selhání, nazývá se to taky *uzel clusteru* nebo *uzel*.
- Každý virtuální počítač má dva nebo více datových disků.
- Prostory úložiště s přímým přístupem synchronizuje data na datových discích a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje sdílený svazek clusteru (CSV) do clusteru s podporou převzetí služeb při selhání.
- Role clusteru SQL Server FCI používá sdílený svazek clusteru pro datové jednotky.
- Nástroj pro vyrovnávání zatížení Azure, který bude obsahovat IP adresu pro SQL Server FCI.
- Skupina dostupnosti Azure obsahuje všechny prostředky.

   > [!NOTE]
   > Celé řešení můžete vytvořit v Azure ze šablony. Příklad šablony je k dispozici na stránce [šablony Azure pro rychlý Start](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) pro GitHub. Tento příklad není navržený nebo testován pro žádnou konkrétní úlohu. Můžete spustit šablonu a vytvořit SQL Server FCI s úložištěm Prostory úložiště s přímým přístupem připojeným k vaší doméně. Můžete vyhodnotit šablonu a upravit ji pro vaše účely.


## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste už měli mít:

- Předplatné Azure. Začněte [zdarma](https://azure.microsoft.com/free/). 
- [Dva nebo více připravených virtuálních počítačů Windows Azure](failover-cluster-instance-prepare-vm.md) ve [skupině dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Nejnovější verze [prostředí PowerShell](/powershell/azure/install-az-ps?view=azps-4.2.0). 


## <a name="add-the-windows-cluster-feature"></a>Přidání funkce cluster Windows

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokol RDP (Remote Desktop Protocol) (RDP) s účtem domény, který je členem místních správců a který má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro zbytek konfigurace.

1. Přidejte Clustering s podporou převzetí služeb při selhání do každého virtuálního počítače.

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání z uživatelského rozhraní, udělejte na obou virtuálních počítačích tento postup:

   1. V **Správce serveru**vyberte **Spravovat**a pak vyberte **Přidat role a funkce**.
   1. V průvodci **přidáním rolí a funkcí** vyberte **Další** , dokud se nedostanete k **vybraným funkcím**.
   1. V **možnosti vybrat funkce**vyberte **Clustering s podporou převzetí služeb při selhání**. Zahrňte všechny požadované funkce a nástroje pro správu. 
   1. Vyberte **Přidat funkce**.
   1. Vyberte **Další**a potom vyberte **Dokončit** a nainstalujte funkce.

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z virtuálních počítačů:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Další informace o dalších krocích najdete v pokynech v oddílu "krok 3: Konfigurace Prostory úložiště s přímým přístupem" v [řešení s použitím prostory úložiště s přímým přístupem v systému Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Ověřit cluster

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, udělejte na jednom z těchto virtuálních počítačů následující:

1. V části **Správce serveru**vyberte **nástroje**a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **Akce**a pak vyberte **ověřit konfiguraci**.
1. Vyberte **Další**.
1. V části **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **Spustit pouze vybrané testy**. 
1. Vyberte **Další**.
1. V části **Výběr testu**vyberte všechny testy s výjimkou **úložiště**, jak je znázorněno zde:

   ![Výběr testů pro ověření clusteru](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Vyberte **Další**.
1. V části **potvrzení**vyberte **Další**.

    Průvodce **ověřením konfigurace** spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.


## <a name="create-failover-cluster"></a>Vytvořit cluster pro převzetí služeb při selhání

Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:

- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání.
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 – 2016](#tab/windows2012)

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2012 přes Windows Server 2016. Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2019.  Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Další informace najdete v tématu [cluster pro převzetí služeb při selhání: objekt sítě s clustery](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Konfigurace kvora

Nakonfigurujte řešení kvora, které nejlépe vyhovuje vašim obchodním potřebám. Můžete nakonfigurovat disk s kopií [clusteru](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), disk s [kopií cloudu](/windows-server/failover-clustering/deploy-cloud-witness)nebo [určující sdílenou složku](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Další informace najdete v tématu [kvorum s SQL Servermi virtuálními počítači](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Přidání úložiště

Disky pro Prostory úložiště s přímým přístupem musí být prázdné. Nemohou obsahovat oddíly nebo jiná data. Chcete-li vyčistit disky, postupujte podle pokynů v části [nasazení prostory úložiště s přímým přístupem](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Povolte prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Následující skript prostředí PowerShell umožňuje Prostory úložiště s přímým přístupem:  

   ```powershell
   Enable-ClusterS2D
   ```

   V **Správce clusteru s podporou převzetí služeb při selhání**se teď můžete podívat na fond úložiště.

1. [Vytvořte svazek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Prostory úložiště s přímým přístupem automaticky vytvoří fond úložiště, když ho povolíte. Teď jste připraveni vytvořit svazek. Rutina PowerShellu `New-Volume` automatizuje proces vytváření svazků. Tento proces zahrnuje formátování, přidání svazku do clusteru a vytvoření SDÍLENÉHO svazku clusteru. Tento příklad vytvoří soubor CSV 800 GB:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po spuštění předchozího příkazu je svazek 800 GB připojený jako prostředek clusteru. Svazek je na adrese `C:\ClusterStorage\Volume1\` .

   Na tomto snímku obrazovky se zobrazuje CSV s Prostory úložiště s přímým přístupem:

   ![Snímek obrazovky sdílený svazek clusteru s Prostory úložiště s přímým přístupem](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testovací převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte pravým tlačítkem na svůj cluster, vyberte **Další akce**  >  **přesunout základní prostředek clusteru**  >  **Vybrat uzel**a pak vyberte druhý uzel clusteru. Přesuňte základní prostředek clusteru do každého uzlu clusteru a pak ho přesuňte zpátky do primárního uzlu. Pokud můžete cluster úspěšně přesunout do každého uzlu, budete připraveni nainstalovat SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do ostatních uzlů":::

## <a name="create-sql-server-fci"></a>Vytvořit SQL Server FCI

Po nakonfigurování clusteru s podporou převzetí služeb při selhání a všech součástí clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby všechny základní prostředky clusteru byly na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na adrese `C:\SQLServer_<version number>_Full` . Vyberte **Nastavení**.

1. V **SQL Server centra instalace**vyberte možnost **instalace**.

1. Vyberte **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být v clusterovém úložišti. V případě Prostory úložiště s přímým přístupem se nejedná o sdílený disk, ale přípojný bod na svazku na každém serveru. Prostory úložiště s přímým přístupem synchronizuje svazek mezi oběma uzly. Svazek se prezentuje clusteru jako CSV. Pro datové adresáře použijte přípojný bod sdíleného svazku clusteru.

   ![Datové adresáře](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Po dokončení pokynů v průvodci instalační program nainstaluje do prvního uzlu SQL Server FCI.

1. Až instalační program nainstaluje FCI na první uzel, připojte se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**. Vyberte možnost **instalace**.

1. Vyberte **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie, která obsahuje SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste některou z těchto imagí nepoužili, nainstalujte nástroje SQL Server samostatně. Další informace najdete v tématu [stažení SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).
   >


## <a name="register-with-the-sql-vm-rp"></a>Zaregistrujte se pomocí SQL VM RP.

Pokud chcete spravovat SQL Server virtuální počítač z portálu, zaregistrujte ho pomocí poskytovatele prostředků virtuálního počítače SQL (RP) v [režimu zjednodušené správy](sql-vm-resource-provider-register.md#lightweight-management-mode), v současné době jde o jediný režim, který se v FCI a SQL Server na virtuálních počítačích Azure podporuje. 


Zaregistrujte SQL Server virtuální počítač v jednoduchém režimu pomocí prostředí PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurace možností připojení 

Pokud chcete směrovat provoz odpovídající aktuálnímu primárnímu uzlu, nakonfigurujte možnost připojení, která je vhodná pro vaše prostředí. Můžete vytvořit [Nástroj pro vyrovnávání zatížení Azure](hadr-vnn-azure-load-balancer-configure.md) , nebo pokud používáte SQL Server 2019 a Windows Server 2016 (nebo novější), můžete místo toho zobrazit náhled funkce [názvu distribuované sítě](hadr-distributed-network-name-dnn-configure.md) . 

## <a name="limitations"></a>Omezení

- Virtuální počítače Azure podporují službu Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) na Windows serveru 2019 s úložištěm v CSV a [standardním nástrojem pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md).
- Disky, které jsou připojené jako disky formátované systémem souborů NTFS, se dají používat s Prostory úložiště s přímým přístupem jenom v případě, že při přidávání úložiště do clusteru není zaškrtnuté políčko nárok na disk nebo když se nevymaže. 
- Podporuje se jenom registrace u poskytovatele prostředků virtuálního počítače SQL v [režimu zjednodušené správy](sql-vm-resource-provider-register.md#management-modes) .

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, nakonfigurujte připojení k vašemu FCI pomocí [názvu virtuální sítě a nástroje pro vyrovnávání zatížení Azure](hadr-vnn-azure-load-balancer-configure.md) nebo [názvu DISTRIBUOVANÉ sítě (DNN)](hadr-distributed-network-name-dnn-configure.md). 

Pokud Prostory úložiště s přímým přístupem není pro vás vhodné řešení úložiště pro FCI, zvažte místo toho vytvoření FCI pomocí [sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) nebo [sdílených složek prémiových souborů](failover-cluster-instance-premium-file-share-manually-configure.md) . 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech konfigurace clusteru](hadr-cluster-best-practices.md). 

Další informace najdete v těchto tématech: 
- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

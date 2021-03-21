---
title: Vytvoření FCI se sdílením souborů úrovně Premium
description: K vytvoření instance clusteru s podporou převzetí služeb při selhání (FCI) s SQL Server na virtuálních počítačích Azure použijte prémiovou sdílenou složku (PFS).
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 80fe9f03f2c57eab8527e553153f3e65315a54bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034842"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Vytvoření FCI se souborovou sdílenou složkou Premium (SQL Server na virtuálních počítačích Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Tento článek vysvětluje, jak vytvořit instanci clusteru s podporou převzetí služeb při selhání (FCI) s SQL Server v Azure Virtual Machines (VM) pomocí [sdílené složky Premium](../../../storage/files/storage-how-to-create-file-share.md).

Soubory úrovně Premium jsou Prostory úložiště s přímým přístupem (SSD) se záložními sdílenými složkami, které jsou plně podporované pro použití s instancemi clusteru s podporou převzetí služeb při selhání pro SQL Server 2012 nebo novější v systému Windows Server 2012 nebo novějším. Prémiové sdílené složky poskytují větší flexibilitu, což vám umožní změnit velikost sdílené složky a škálovat ji bez výpadků.

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech pro clustery](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Předpoklady

Před dokončením kroků v tomto článku byste už měli mít:

- Předplatné Azure.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- [Dva nebo více připravených virtuálních počítačů Windows Azure](failover-cluster-instance-prepare-vm.md) v [sadě dostupnosti](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) nebo různých [zónách dostupnosti](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- [Prémiová sdílená složka](../../../storage/files/storage-how-to-create-file-share.md) , která se má použít jako clusterovaná jednotka, na základě kvóty úložiště vaší databáze pro datové soubory.
- Nejnovější verze [prostředí PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Připojit prémiovou sdílenou složku

1. Přihlaste se na [Azure Portal](https://portal.azure.com). a přejít na účet úložiště.
1. V části **Souborová služba** klikněte na **sdílené složky** a potom vyberte sdílenou složku Premium, kterou chcete použít pro své úložiště SQL.
1. Výběrem **připojit** otevřete připojovací řetězec pro sdílenou složku.
1. V rozevíracím seznamu vyberte písmeno jednotky, které chcete použít, a potom zkopírujte oba bloky kódu do poznámkového bloku.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Kopírování příkazů PowerShellu z portálu pro připojení ke sdílené složce":::

1. Pomocí protokol RDP (Remote Desktop Protocol) (RDP) se připojte k virtuálnímu počítači s SQL Server pomocí účtu, který bude služba SQL Server FCI používat pro účet služby.
1. Otevřete konzolu příkazového řádku PowerShellu pro správu.
1. Spusťte příkazy, které jste předtím uložili při práci na portálu.
1. Do sdílené složky zadejte buď pomocí Průzkumníka souborů, nebo pomocí dialogového okna **Spustit** (vyberte Windows + R). Použijte síťovou cestu `\\storageaccountname.file.core.windows.net\filesharename` . Například `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`.

1. Vytvořte alespoň jednu složku pro nově připojenou sdílenou složku, do které chcete umístit datové soubory SQL.
1. Tento postup opakujte na každém virtuálním počítači s SQL Server, který se bude podílet na clusteru.

  > [!IMPORTANT]
  > - Zvažte použití samostatné sdílené složky pro záložní soubory k uložení vstupních/výstupních operací za sekundu (IOPS) a kapacity prostoru této sdílené složky pro data a soubory protokolů. Pro záložní soubory můžete použít buď prémiovou, nebo standardní souborovou sdílenou složku.
  > - Pokud používáte systém Windows 2012 R2 nebo starší, připojte ke sdílené složce, kterou chcete použít jako určující sdílenou složku, stejným způsobem. 
  > 


## <a name="add-windows-cluster-feature"></a>Přidat funkci clusteru Windows

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP pomocí účtu domény, který je členem místní skupiny Administrators a který má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro zbytek konfigurace.

1. [Přidejte Clustering s podporou převzetí služeb při selhání do každého virtuálního počítače](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání z uživatelského rozhraní, udělejte na obou virtuálních počítačích tento postup:
   1. V **Správce serveru** vyberte **Spravovat** a pak vyberte **Přidat role a funkce**.
   1. V průvodci **přidáním rolí a funkcí** vyberte **Další** , dokud se nedostanete k **vybraným funkcím**.
   1. V **možnosti vybrat funkce** vyberte **Clustering s podporou převzetí služeb při selhání**. Zahrňte všechny požadované funkce a nástroje pro správu. 
   1. Vyberte **Přidat funkce**.
   1. Vyberte **Další** a potom vyberte **Dokončit** a nainstalujte funkce.

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z virtuálních počítačů:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

## <a name="validate-cluster"></a>Ověřit cluster

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, udělejte na jednom z těchto virtuálních počítačů následující:

1. V části **Správce serveru** vyberte **nástroje** a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání** vyberte **Akce** a pak vyberte **ověřit konfiguraci**.
1. Vyberte **Další**.
1. V části **Vybrat servery nebo cluster** zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování** vyberte **Spustit pouze vybrané testy**. 
1. Vyberte **Další**.
1. V části **Výběr testu** vyberte všechny testy s výjimkou **úložiště** a **prostory úložiště s přímým přístupem**, jak je znázorněno zde:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Výběr testů pro ověření clusteru":::

1. Vyberte **Další**.
1. V části **potvrzení** vyberte **Další**.

Průvodce **ověřením konfigurace** spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>Testovací převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. V **Správce clusteru s podporou převzetí služeb při selhání** klikněte pravým tlačítkem na svůj cluster, vyberte **Další akce**  >  **přesunout základní prostředek clusteru**  >  **Vybrat uzel** a pak vyberte druhý uzel clusteru. Přesuňte základní prostředek clusteru do každého uzlu clusteru a pak ho přesuňte zpátky do primárního uzlu. Pokud můžete cluster úspěšně přesunout do každého uzlu, budete připraveni nainstalovat SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do ostatních uzlů":::


## <a name="create-sql-server-fci"></a>Vytvořit SQL Server FCI

Po dokončení konfigurace clusteru s podporou převzetí služeb při selhání můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání** zajistěte, aby všechny základní prostředky clusteru byly na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na adrese `C:\SQLServer_<version number>_Full` . 

1. Vyberte **Nastavení**.

1. V **instalačním centru SQL Server** vyberte možnost **instalace**.

1. Vyberte **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání** a pak postupujte podle pokynů v průvodci a nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být ve sdílené souborové složce Premium. Zadejte úplnou cestu ke sdílené složce v tomto formátu: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Zobrazí se upozornění s oznámením, že jste jako datový adresář zadali souborový server. Toto upozornění je očekávané. Ujistěte se, že uživatelský účet, který jste použili pro přístup k virtuálnímu počítači přes protokol RDP při trvalém sdílení souborů, je stejný účet, který služba SQL Server používá, aby se předešlo možným chybám.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Použít sdílení souborů jako datové adresáře SQL":::

1. Po dokončení kroků v průvodci instalační program nainstaluje SQL Server FCI na první uzel.

1. Až instalační program nainstaluje FCI na první uzel, připojte se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server** a pak vyberte možnost **instalace**.

1. Vyberte **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie s SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste některou z těchto imagí nepoužili, nainstalujte nástroje SQL Server samostatně. Další informace najdete v tématu [stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Opakujte tyto kroky na všech ostatních uzlech, které chcete přidat do instance clusteru převzetí služeb při selhání SQL Server. 

## <a name="register-with-the-sql-vm-rp"></a>Zaregistrujte se pomocí SQL VM RP.

Pokud chcete spravovat SQL Server virtuální počítač z portálu, zaregistrujte ho pomocí rozšíření SQL IaaS Agent (RP) v [režimu zjednodušené správy](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), v současné době se jedná o jediný režim, který podporuje FCI a SQL Server na virtuálních počítačích Azure. 

Registrace virtuálního počítače s SQL Server v jednoduchém režimu pomocí prostředí PowerShell (-LicenseType může být `PAYG` nebo `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Konfigurace možností připojení 

Pokud chcete směrovat provoz odpovídající aktuálnímu primárnímu uzlu, nakonfigurujte možnost připojení, která je vhodná pro vaše prostředí. Můžete vytvořit [Nástroj pro vyrovnávání zatížení Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) , nebo pokud používáte SQL Server 2019 CU2 (nebo novější) a Windows Server 2016 (nebo novější), můžete místo toho použít funkci [názvu distribuované sítě](failover-cluster-instance-distributed-network-name-dnn-configure.md) . 

Další podrobnosti o možnostech připojení clusteru najdete v tématu [Směrování hadr připojení k SQL Server na virtuálních počítačích Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Omezení

- Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) není podporován na Windows serveru 2016 a starších verzích. 
- FILESTREAM není podporován pro cluster s podporou převzetí služeb při selhání se sdílenou složkou Premium. Pokud chcete použít FILESTREAM, nasaďte cluster místo toho pomocí [prostory úložiště s přímým přístupem](failover-cluster-instance-storage-spaces-direct-manually-configure.md) nebo [sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) .
- Podporuje se jenom registrování s rozšířením agenta SQL IaaS v [režimu zjednodušené správy](sql-server-iaas-agent-extension-automate-management.md#management-modes) . 

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, nakonfigurujte připojení k vašemu FCI pomocí [názvu virtuální sítě a nástroje pro vyrovnávání zatížení Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) nebo [názvu DISTRIBUOVANÉ sítě (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Pokud soubory úrovně Premium pro vás nejsou odpovídajícím řešením úložiště FCI, zvažte vytvoření FCI pomocí [sdílených disků Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) nebo místo toho [prostory úložiště s přímým přístupem](failover-cluster-instance-storage-spaces-direct-manually-configure.md) . 

Další informace najdete v tématu Přehled [FCI s SQL Server na virtuálních počítačích Azure](failover-cluster-instance-overview.md) a [osvědčených postupech konfigurace clusteru](hadr-cluster-best-practices.md). 

Další informace naleznete v tématu: 
- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
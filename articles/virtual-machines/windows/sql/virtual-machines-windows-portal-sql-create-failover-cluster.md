---
title: SQL Server FCI – Azure Virtual Machines | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 08549935c7a0651709a08bef61624e4e436d4aad
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084098"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurace SQL Server instance clusteru s podporou převzetí služeb při selhání na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání (FCI) na virtuálních počítačích Azure v modelu Azure Resource Manager. Toto řešení používá [Windows Server 2016 Datacenter edition prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako softwarovou virtuální síť SAN, která synchronizuje úložiště (datové disky) mezi uzly (virtuální počítače Azure) v clusteru Windows. Prostory úložiště s přímým přístupem byla v systému Windows Server 2016 nová.

Následující diagram znázorňuje kompletní řešení na virtuálních počítačích Azure:

![Kompletní řešení](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Tento diagram znázorňuje:

- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání systému Windows Server. Když je virtuální počítač v clusteru s podporou převzetí služeb při selhání, nazývá se to taky *uzel clusteru* nebo *uzel*.
- Každý virtuální počítač má dva nebo více datových disků.
- Prostory úložiště s přímým přístupem synchronizuje data na datových discích a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje sdílený svazek clusteru (CSV) do clusteru s podporou převzetí služeb při selhání.
- Role clusteru SQL Server FCI používá sdílený svazek clusteru pro datové jednotky.
- Nástroj pro vyrovnávání zatížení Azure, který bude obsahovat IP adresu pro SQL Server FCI.
- Skupina dostupnosti Azure obsahuje všechny prostředky.

>[!NOTE]
>Všechny prostředky Azure v diagramu jsou ve stejné skupině prostředků.

Podrobnosti o Prostory úložiště s přímým přístupem najdete v tématu [Windows Server 2016 Datacenter edition prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Prostory úložiště s přímým přístupem podporuje dva typy architektury: sblížené a nakonvergované na technologii Hyper-v. Architektura v tomto dokumentu je konvergovaná na základě technologie Hyper-v. Infrastruktura sblížená pomocí technologie Hyper-v umístí úložiště na stejné servery, které hostují Clusterovou aplikaci. V této architektuře je úložiště na každém uzlu SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licencování a ceny

Na virtuálních počítačích Azure můžete licencovat SQL Server s využitím imagí (PAYG) s průběžnými platbami (BYOL) a imagemi virtuálních počítačů s vlastní licencí (). Typ obrázku, který zvolíte, bude mít vliv na to, jak se vám bude účtovat.

Díky licencování s průběžnými platbami se instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Server na virtuálních počítačích Azure za všechny uzly FCI, včetně pasivních uzlů. Další informace najdete v tématu [SQL Server Enterprise Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Pokud máte smlouva Enterprise se Software Assurance, můžete pro každý aktivní uzel použít jeden bezplatný pasivní uzel FCI. Pokud chcete tuto výhodu využít v Azure, použijte image virtuálních počítačů BYOL a stejnou licenci používejte v aktivních i pasivních uzlech FCI. Další informace najdete v tématu [smlouva Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat BYOL s průběžnými platbami a licencováním pro SQL Server na virtuálních počítačích Azure, přečtěte si téma Začínáme s virtuálními počítači [SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licenčních SQL Server najdete v tématu [ceny](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Ukázková šablona Azure

Celé řešení můžete vytvořit v Azure ze šablony. Příklad šablony je k dispozici v [šablonách rychlého](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)startu pro Azure na GitHubu. Tento příklad není navržený nebo testován pro žádnou konkrétní úlohu. Můžete spustit šablonu a vytvořit SQL Server FCI s úložištěm Prostory úložiště s přímým přístupem připojeným k vaší doméně. Můžete vyhodnotit šablonu a upravit ji pro vaše účely.

## <a name="before-you-begin"></a>Než začnete

K dispozici je několik věcí, které potřebujete znát a které jsou ještě před začátkem.

### <a name="what-to-know"></a>Co potřebujete
Měli byste mít provozní znalosti těchto technologií:

- [Technologie clusterů Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedním z věcí, o které je potřeba vědět, je to, že na clusteru s podporou převzetí služeb při selhání hosta virtuálního počítače Azure IaaS doporučujeme jednu síťovou kartu na jeden server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v clusteru hostů virtuálních počítačů Azure IaaS vyžaduje další síťové adaptéry a podsítě, které nejsou potřebné. Sestava ověření clusteru vás upozorní, že uzly jsou dosažitelné jenom v jedné síti. Toto upozornění můžete ignorovat u clusterů s podporou převzetí služeb při selhání hosta virtuálních počítačů Azure IaaS.

Měli byste mít také obecné porozumění těmto technologiím:

- [Řešení konvergovaná pomocí technologie Hyper-v Prostory úložiště s přímým přístupem v systému Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Skupiny prostředků Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době se SQL Server instance clusterů s podporou převzetí služeb při selhání na virtuálních počítačích Azure podporují jenom s režimem [zjednodušené](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) správy [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Odinstalujte úplné rozšíření z virtuálních počítačů, které jsou součástí clusteru s podporou převzetí služeb při selhání, a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL v jednoduchém režimu.
>
> Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a Správa portálu pro pokročilé. Tyto funkce nebudou fungovat pro SQL Server virtuální počítače po přeinstalaci agenta v režimu zjednodušené správy.

### <a name="what-to-have"></a>Co má

Před dokončením kroků v tomto článku byste už měli mít:

- Microsoft Azure předplatné.
- Doména Windows na virtuálních počítačích Azure.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Virtuální síť Azure a podsíť s dostatkem adresního prostoru IP adres pro tyto součásti:
   - Oba virtuální počítače.
   - IP adresa clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- Služba DNS konfigurovaná na síti Azure odkazuje na řadiče domény.

V rámci těchto požadavků můžete začít vytvářet cluster s podporou převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého předplatného.

1. [Vytvořte skupinu dostupnosti Azure](../tutorial-availability-sets.md).

   Skupina dostupnosti seskupuje virtuální počítače napříč doménami selhání a aktualizačními doménami. Zajišťuje, aby vaše aplikace neovlivnila jednotlivé body selhání, například síťový přepínač nebo jednotka napájení stojanu serverů.

   Pokud jste ještě nevytvořili skupinu prostředků pro virtuální počítače, udělejte to při vytváření sady dostupnosti Azure. Pokud k vytvoření skupiny dostupnosti používáte Azure Portal, proveďte tyto kroky:

   1. V Azure Portal vyberte **vytvořit prostředek** a otevřete Azure Marketplace. Vyhledejte **skupinu dostupnosti**.
   1. Vyberte **skupinu dostupnosti**.
   1. Vyberte **Vytvořit**.
   1. V části **vytvořit skupinu dostupnosti**zadejte tyto hodnoty:
      - **Name (název**): název skupiny dostupnosti.
      - **Předplatné**: vaše předplatné Azure.
      - **Skupina prostředků**: Pokud chcete použít existující skupinu, klikněte na **Vybrat existující** a potom vyberte skupinu ze seznamu. V opačném případě vyberte **vytvořit novou** a zadejte název skupiny.
      - **Umístění**: Nastavte umístění, kde plánujete vytvořit virtuální počítače.
      - **Doména selhání**: použijte výchozí (**3**).
      - **Aktualizovat domény**: použijte výchozí (**5**).
   1. Vyberte **vytvořit** a vytvořte skupinu dostupnosti.

1. Vytvořte virtuální počítače ve skupině dostupnosti.

   Zřizování dvou SQL Server virtuálních počítačů ve skupině dostupnosti Azure. Pokyny najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umístit oba virtuální počítače:

   - Ve stejné skupině prostředků Azure jako vaše skupina dostupnosti.
   - Ve stejné síti jako řadič domény.
   - V podsíti, která má dostatek adresního prostoru IP adres pro virtuální počítače i všechny FCIs, které můžete nakonec použít na clusteru.
   - V sadě dostupnosti Azure.

      >[!IMPORTANT]
      >Po vytvoření virtuálního počítače už skupinu dostupnosti nemůžete nastavit ani změnit.

   Vyberte obrázek z Azure Marketplace. Můžete použít Azure Marketplace image, která zahrnuje Windows Server a SQL Server, nebo použít jednu z nich, která obsahuje jenom Windows Server. Podrobnosti najdete v tématu [přehled SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Oficiální SQL Server Image v galerii Azure zahrnují nainstalovanou instanci SQL Server, SQL Server instalační software a požadovaný klíč.

   Vyberte správnou bitovou kopii na základě toho, jak chcete platit SQL Server licenci:

   - **Licencování s platbami za použití**. Náklady na tyto image za sekundu zahrnují SQL Server licencování:
      - **SQL Server 2016 Enterprise v systému Windows Server 2016 Datacenter**
      - **SQL Server 2016 standard v systému Windows Server 2016 Datacenter**
      - **SQL Server 2016 vývojář v systému Windows Server 2016 Datacenter**

   - **Používání vlastní licence (BYOL)**

      - **BYOL SQL Server 2016 Enterprise v systému Windows Server 2016 Datacenter**
      - **BYOL SQL Server 2016 standard v systému Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Po vytvoření virtuálního počítače odeberte předem nainstalovanou samostatnou SQL Server instanci. Po nastavení clusteru s podporou převzetí služeb při selhání a Prostory úložiště s přímým přístupem můžete k vytvoření SQL Server FCI použít předem nainstalovanou SQL Server média.

   Alternativně můžete použít Azure Marketplace image, které obsahují jenom operační systém. Vyberte bitovou kopii **Windows Server 2016 Datacenter** a nainstalujte SQL Server FCI po nastavení clusteru s podporou převzetí služeb při selhání a prostory úložiště s přímým přístupem. Tato image neobsahuje instalační médium SQL Server. Instalační médium SQL Server umístit do umístění, kde ho můžete spustit pro každý server.

1. Až Azure vytvoří vaše virtuální počítače, připojí se ke každému z nich pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači pomocí protokolu RDP, zobrazí se výzva, jestli chcete, aby byl počítač v síti zjistitelný. Vyberte **Ano**.

1. Pokud používáte jednu z imagí virtuálních počítačů založených na SQL Server, odeberte instanci SQL Server.

   1. V části **programy a funkce**klikněte pravým tlačítkem na **Microsoft SQL Server 2016 (64 bitů)** a vyberte **Odinstalovat nebo změnit**.
   1. Vyberte **Odebrat**.
   1. Vyberte výchozí instanci.
   1. Odeberte všechny funkce ve **službě databázového stroje**. Neodstraňujte **sdílené funkce**. Uvidíte něco podobného jako na následujícím snímku obrazovky:

      ![Výběr funkcí](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Vyberte **Další**a pak vyberte **Odebrat**.

1. <a name="ports"></a>Otevřete porty brány firewall.

   Na každém virtuálním počítači otevřete tyto porty na bráně Windows Firewall:

   | Účel | Port TCP | Poznámky:
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance SQL Server. Pokud jste použili image z Galerie, tento port se automaticky otevře.
   | Sonda stavu | 59999 | Libovolný otevřený port TCP. V pozdějším kroku nakonfigurujte [sondu stavu](#probe) nástroje pro vyrovnávání zatížení a cluster tak, aby používal tento port.  

1. Přidejte úložiště do virtuálního počítače. Podrobné informace najdete v tématu věnovaném [Přidání úložiště](../disks-types.md).

   Oba virtuální počítače potřebují aspoň dva datové disky.

   Připojte nezpracované disky, nikoli disky formátované systémem souborů NTFS.
      >[!NOTE]
      >Pokud připojíte disky formátované systémem souborů NTFS, můžete povolit Prostory úložiště s přímým přístupem pouze bez kontroly způsobilosti disku.  

   Ke každému virtuálnímu počítači připojte minimálně dvě SSD Premium. Doporučujeme aspoň P30 (1 TB) disků.

   Nastavte ukládání hostitelů do mezipaměti **jen pro čtení**.

   Kapacita úložiště, kterou používáte v produkčním prostředí, závisí na vašich úlohách. Hodnoty popsané v tomto článku jsou k disukázce a testování.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nastavit cluster s podporou převzetí služeb při selhání.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Krok 2: konfigurace clusteru s podporou převzetí služeb při selhání Windows serveru pomocí Prostory úložiště s přímým přístupem

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání pomocí Prostory úložiště s přímým přístupem. V tomto kroku provedete tyto kroky:

1. Přidejte funkci Clustering s podporou převzetí služeb při selhání Windows serveru.
1. Ověřte cluster.
1. Vytvořte cluster s podporou převzetí služeb při selhání.
1. Vytvořte disk s kopií cloudu.
1. Přidejte úložiště.

### <a name="add-windows-server-failover-clustering"></a>Přidat Clustering s podporou převzetí služeb při selhání Windows serveru

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP pomocí účtu domény, který je členem místní skupiny Administrators a který má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro zbytek konfigurace.

1. [Přidejte Clustering s podporou převzetí služeb při selhání do každého virtuálního počítače](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání z uživatelského rozhraní, proveďte tyto kroky na obou virtuálních počítačích:
   1. V **Správce serveru**vyberte **Spravovat**a pak vyberte **Přidat role a funkce**.
   1. V **Průvodci přidáním rolí a funkcí**vyberte **Další** , dokud se nedostanete k **vybraným funkcím**.
   1. V **možnosti vybrat funkce**vyberte **Clustering s podporou převzetí služeb při selhání**. Zahrňte všechny požadované funkce a nástroje pro správu. Vyberte **Přidat funkce**.
   1. Vyberte **Další**a potom vyberte **Dokončit** a nainstalujte funkce.

   Pokud chcete nainstalovat Clustering s podporou převzetí služeb při selhání pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z virtuálních počítačů:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Další informace o dalších krocích najdete v pokynech v části Krok 3 [řešení sblíženého pomocí technologie Hyper-v prostory úložiště s přímým přístupem v systému Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Ověřit cluster

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, proveďte následující kroky na jednom z těchto virtuálních počítačů:

1. V části **Správce serveru**vyberte **nástroje**a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **Akce**a pak vyberte **ověřit konfiguraci**.
1. Vyberte **Next** (Další).
1. V části **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **Spustit pouze vybrané testy**. Vyberte **Next** (Další).
1. V části **Výběr testu**vyberte všechny testy s výjimkou **úložiště**, jak je znázorněno zde:

   ![Výběr testů pro ověření clusteru](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Vyberte **Next** (Další).
1. V části **potvrzení**vyberte **Další**.

Průvodce ověřením konfigurace spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 s Windows serverem 2016

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2008 přes Windows Server 2016. Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2019. Další informace najdete v tématu [cluster pro převzetí služeb při selhání: objekt sítě s clustery](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Vytvoření určujícího cloudu

Disk s kopií cloudu je nový typ určujícího disku kvora clusteru, který je uložený v objektu BLOB služby Azure Storage. Tím se eliminuje nutnost samostatného virtuálního počítače, který je hostitelem sdílené složky s kopií clusteru.

1. [Vytvořte určující Cloud pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů BLOB.

1. Uložte přístupové klíče a adresu URL kontejneru.

1. Nakonfigurujte určující disk kvora clusteru s podporou převzetí služeb při selhání Viz [Konfigurace určujícího disku kvora v uživatelském rozhraní](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Přidání úložiště

Disky pro Prostory úložiště s přímým přístupem musí být prázdné. Nemohou obsahovat oddíly nebo jiná data. Chcete-li vyčistit disky, postupujte podle [kroků v této příručce](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Povolit prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)

   Následující skript prostředí PowerShell umožňuje Prostory úložiště s přímým přístupem:  

   ```powershell
   Enable-ClusterS2D
   ```

   V **Správce clusteru s podporou převzetí služeb při selhání**se teď můžete podívat na fond úložiště.

1. [Vytvořte svazek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Prostory úložiště s přímým přístupem automaticky vytvoří fond úložiště, když ho povolíte. Teď jste připraveni vytvořit svazek. Rutina PowerShellu `New-Volume` automatizuje proces vytváření svazků. Tento proces zahrnuje formátování, přidání svazku do clusteru a vytváření sdílený svazek clusteru (CSV). Tento příklad vytvoří soubor CSV 800 GB:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po dokončení tohoto příkazu je svazek 800 GB připojen jako prostředek clusteru. Svazek je na `C:\ClusterStorage\Volume1\`.

   Tento snímek obrazovky ukazuje sdílený svazek clusteru s Prostory úložiště s přímým přístupem:

   ![sdílený svazek clusteru](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: testování převzetí služeb při selhání clusteru se selháním

V **Správce clusteru s podporou převzetí služeb při selhání**ověřte, zda můžete přesunout prostředek úložiště do jiného uzlu clusteru. Pokud se ke clusteru s podporou převzetí služeb při selhání můžete připojit pomocí **Správce clusteru s podporou převzetí služeb při selhání** a přesunout úložiště z jednoho uzlu na druhý, budete připraveni nakonfigurovat FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Krok 4: vytvoření FCI SQL Server

Po nakonfigurování clusteru s podporou převzetí služeb při selhání a všech součástí clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby všechny základní prostředky clusteru byly na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na `C:\SQLServer_<version number>_Full`. Vyberte **Nastavení**.

1. V **SQL Server centra instalace**vyberte možnost **instalace**.

1. Vyberte **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být v clusterovém úložišti. V případě Prostory úložiště s přímým přístupem se nejedná o sdílený disk, ale přípojný bod na svazku na každém serveru. Prostory úložiště s přímým přístupem synchronizuje svazek mezi oběma uzly. Svazek se prezentuje clusteru jako sdílený svazek clusteru. Pro datové adresáře použijte přípojný bod sdíleného svazku clusteru.

   ![Datové adresáře](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po dokončení pokynů v průvodci instalační program nainstaluje SQL Server FCI na první uzel.

1. Až instalační program nainstaluje FCI na první uzel, připojte se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**. Vyberte možnost **instalace**.

1. Vyberte **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie, která obsahuje SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste některou z těchto imagí nepoužili, nainstalujte nástroje SQL Server samostatně. Viz [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Krok 5: Vytvoření nástroje pro vyrovnávání zatížení Azure

Ve virtuálních počítačích Azure používají clustery Nástroj pro vyrovnávání zatížení k uchování IP adresy, která musí být na jednom uzlu clusteru. V tomto řešení má nástroj pro vyrovnávání zatížení uloženou IP adresu pro SQL Server FCI.

Další informace najdete v tématu [Vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření nástroje pro vyrovnávání zatížení v Azure Portal

Vytvoření nástroje pro vyrovnávání zatížení:

1. V Azure Portal přejdete do skupiny prostředků, která obsahuje virtuální počítače.

1. Vyberte **Přidat**. Vyhledejte **Load Balancer**Azure Marketplace. Vyberte **Load Balancer**.

1. Vyberte **Vytvořit**.

1. Nakonfigurujte Nástroj pro vyrovnávání zatížení pomocí:

   - **Předplatné**: vaše předplatné Azure.
   - **Skupina prostředků**: Skupina prostředků, která obsahuje vaše virtuální počítače.
   - **Název**: název, který identifikuje Nástroj pro vyrovnávání zatížení.
   - **Oblast**: umístění Azure, které obsahuje vaše virtuální počítače.
   - **Zadejte**: buď Public, nebo Private. K privátnímu nástroji pro vyrovnávání zatížení se dá v rámci virtuální sítě dostat. Většina aplikací Azure může používat privátní Nástroj pro vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes Internet, použijte veřejný Nástroj pro vyrovnávání zatížení.
   - **SKU**: Standard.
   - **Virtuální síť**: stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: statické. 
   - **Privátní IP adresa**: IP adresa, kterou jste přiřadili SQL Server síťovému prostředku clusteru FCI.

 Na následujícím snímku obrazovky vidíte uživatelské rozhraní **pro vytvoření nástroje pro vyrovnávání zatížení** :

   ![Nastavení nástroje pro vyrovnávání zatížení](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurace fondu back-endu nástroje pro vyrovnávání zatížení

1. Vraťte se do skupiny prostředků Azure, která obsahuje virtuální počítače, a Najděte nový nástroj pro vyrovnávání zatížení. Možná budete muset aktualizovat zobrazení skupiny prostředků. Vyberte nástroj pro vyrovnávání zatížení.

1. Vyberte **back-end fondy**a pak vyberte **Přidat**.

1. Přidružte back-end fond ke skupině dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové konfigurace sítě IP**vyberte **virtuální počítač** a zvolte virtuální počítače, které se budou podílet jako uzly clusteru. Nezapomeňte zahrnout všechny virtuální počítače, které budou hostovat FCI.

1. Výběrem **OK** vytvořte fond back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurace sondy stavu nástroje pro vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení vyberte **sondy stavu**.

1. Vyberte **Přidat**.

1. V okně **Přidat sondu stavu** <a name="probe"> </a>nastavte parametry sondy stavu.

   - **Name (název**): název pro sondu stavu.
   - **Protokol**: TCP.
   - **Port**: nastavte na port, který jste vytvořili v bráně firewall pro sondu stavu v [tomto kroku](#ports). V tomto článku příklad používá port TCP `59999`.
   - **Interval**: 5 sekund.
   - **Prahová hodnota špatného stavu**: 2 po sobě jdoucích selhání.

1. Vyberte **OK**.

### <a name="set-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení vyberte **pravidla vyrovnávání zatížení**.

1. Vyberte **Přidat**.

1. Nastavte parametry pravidla vyrovnávání zatížení:

   - **Name (název**): název pro pravidla vyrovnávání zatížení.
   - **IP adresa front-endu**: IP adresa pro prostředek sítě clusteru SQL Server FCI
   - **Port**: port TCP SQL Server FCI. Výchozí port instance je 1433.
   - **Back-end port**: používá stejný port jako hodnota **portu** , když povolíte **plovoucí IP adresu (přímé vrácení serveru)** .
   - **Back-end fond**: název back-endu, který jste nakonfigurovali dříve.
   - **Sonda stavu**: sonda stavu, kterou jste nakonfigurovali dříve.
   - **Trvalost relace**: žádné.
   - **Časový limit nečinnosti (minuty)** : 4.
   - **Plovoucí IP adresa (přímá návrat ze serveru)** : povoleno.

1. Vyberte **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Krok 6: konfigurace clusteru pro test

Nastavte parametr portu testu clusteru v prostředí PowerShell.

Pokud chcete nastavit parametr portu sondy clusteru, aktualizujte proměnné v následujícím skriptu pomocí hodnot z vašeho prostředí. Odebere ze skriptu lomené závorky (`<` a `>`).

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Následující seznam popisuje hodnoty, které je třeba aktualizovat:

   - `<Cluster Network Name>`: název clusteru s podporou převzetí služeb při selhání Windows serveru pro síť. V **Správce clusteru s podporou převzetí služeb při selhání** > **sítě**klikněte pravým tlačítkem myši na síť a vyberte **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** .

   - `<SQL Server FCI IP Address Resource Name>`: název prostředku IP adresy SQL Server FCI. V **Správce clusteru s podporou převzetí služeb při selhání** > **role**v části role SQL Server FCI v části **název serveru**klikněte pravým tlačítkem na prostředek IP adresy a vyberte **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** . 

   - `<ILBIP>`: IP adresa interního nástroje. Tato adresa je nakonfigurovaná v Azure Portal jako front-end adresa interního nástroje. To je taky SQL Server FCI IP adresa. Můžete ji najít v **Správce clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste `<SQL Server FCI IP Address Resource Name>`našli.  

   - `<nnnnn>`: port testu, který jste nakonfigurovali v testu stavu nástroje pro vyrovnávání zatížení. Nepoužívaný port TCP je platný.

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být adresa všesměrového vysílání IP protokolu TCP: `255.255.255.255`.

Po nastavení sondy clusteru můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: testování převzetí služeb při selhání FCI

Testovací převzetí služeb při selhání pro FCI k ověření funkčnosti clusteru. Proveďte následující kroky:

1. Připojte se k jednomu z SQL Server uzlů clusteru FCI pomocí protokolu RDP.

1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, že uzel je vlastníkem role SQL Server FCI.

1. Klikněte pravým tlačítkem na roli SQL Server FCI.

1. Vyberte **přesunout**a pak vyberte **nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejít do režimu offline. Prostředky pak přesunete a přejdete do režimu online na druhém uzlu.

### <a name="test-connectivity"></a>Test připojení

Pokud chcete otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k názvu SQL Server FCI.

>[!NOTE]
>Pokud potřebujete, můžete [si stáhnout SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Omezení

Virtuální počítače Azure podporují službu Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) na Windows serveru 2019 s úložištěm na sdílených svazcích clusteru (CSV) a [standardním nástrojem pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md).

Na virtuálních počítačích Azure není služba MSDTC podporovaná na Windows serveru 2016 nebo starším, protože:

- Clusterový prostředek MSDTC nejde nakonfigurovat tak, aby používal sdílené úložiště. Pokud v systému Windows Server 2016 vytvoříte prostředek MSDTC, nezobrazí se žádné sdílené úložiště dostupné pro použití, a to i v případě, že je úložiště k dispozici. Tento problém byl opravený v systému Windows Server 2019.
- Nástroj pro vyrovnávání zatížení úrovně Basic nezpracovává porty RPC.

## <a name="see-also"></a>Viz také

[Nastavení Prostory úložiště s přímým přístupem pomocí vzdálené plochy (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Řešení sblížené pomocí technologie Hyper-v Prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Přehled Prostory úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Podpora SQL Server Prostory úložiště s přímým přístupem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)

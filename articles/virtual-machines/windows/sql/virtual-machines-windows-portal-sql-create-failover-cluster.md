---
title: Systému SQL Server FCI – virtuální počítače Azure | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit Instance clusteru převzetí služeb při selhání SQL serveru na virtuálních počítačích Azure.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 13/22/2018
ms.author: mikeray
ms.openlocfilehash: faa849fc53aa15a47e850a20531c4fa30544f750
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurace Instance clusteru převzetí služeb při selhání systému SQL Server na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit SQL Server převzetí služeb při selhání clusteru Instance (FCI) na virtuálních počítačích Azure v modelu Resource Manager. Toto řešení používá [Windows Server 2016 Datacenter edition prostory úložiště – přímé \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako softwarová virtuální síť SAN, synchronizuje úložiště (datových disků) mezi uzly (virtuální počítače Azure) v clusteru se systémem Windows. S2D je nového v systému Windows Server 2016.

Následující diagram znázorňuje kompletního řešení na virtuálních počítačích Azure:

![Skupiny dostupnosti](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Na předchozím obrázku uvádí:

- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání systému Windows. Pokud virtuální počítač v clusteru s podporou převzetí služeb při selhání se také označuje jako *uzlu clusteru*, nebo *uzly*.
- Každý virtuální počítač má dva nebo více datových disků.
- S2D synchronizuje data na datový disk a uvede synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje sdílený svazek clusteru (CSV) do clusteru převzetí služeb při selhání.
- Role clusteru SQL serveru FCI používá sdílený svazek clusteru pro datové jednotky.
- K Azure pro vyrovnávání zatížení pro uložení IP adresu pro SQL Server FCI.
- Nastavení Azure dostupnosti obsahuje všechny prostředky.

   >[!NOTE]
   >Všechny prostředky Azure jsou v diagramu jsou ve stejné skupině prostředků.

Podrobnosti o S2D najdete v tématu [Windows Server 2016 Datacenter edition prostory úložiště – přímé \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D podporuje dva typy architektury - sblížené a hyperkonvergované. Architektura v tomto dokumentu je hyperkonvergované. V infrastruktuře hyperkonvergované umístí úložiště na stejné servery, které jsou hostiteli clusterové aplikace. V této architektuře úložiště je na každém uzlu SQL serveru FCI.

## <a name="licensing-and-pricing"></a>Licencování a ceny

Ve virtuálních počítačích Azure můžete licencí systému SQL Server pomocí platím průběžně (srážek daně ze MZDY) nebo přineste si vlastní licenci Image virtuálních počítačů (BYOL). Typ obrázku, který zvolíte ovlivňuje, jak vám účtovat.

S licencování srážek daně ze MZDY instance clusteru převzetí služeb při selhání (FCI) systému SQL Server na virtuálních počítačích Azure budou vám účtovány poplatky pro všechny uzly FCI, včetně pasivní uzly. Další informace najdete v tématu [SQL Server Enterprise ceny služeb Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Zákazníci s Enterprise Agreement pomocí programu Software Assurance mít právo používat jeden volné pasivní uzel FCI pro každou aktivní uzel. Chcete-li využít tuto výhodu v Azure, použít Image virtuálních počítačů BYOL a pak použijte stejné licence na aktivní a pasivní uzly FCI. Další informace najdete v tématu [smlouva Enterprise Agreement](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Můžete porovnat srážek daně ze MZDY a BYOL licencování pro SQL Server na virtuálních počítačích Azure najdete v části [začít s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licencování SQL serveru najdete v tématu [cenová](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Příklad šablony Azure

Celé řešení v Azure můžete vytvořit ze šablony. Příklad šablony je k dispozici v Githubu [šablon Azure rychlý Start](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). V tomto příkladu není určená nebo testována pro všechny konkrétní úlohu. Když spustíte šablonu, kterou chcete vytvořit SQL Server FCI s S2D úložiště připojené k vaší doméně. Můžete vyhodnotit šablony a upravit pro vaše záměry.

## <a name="before-you-begin"></a>Než začnete

Existuje několik věcí, které je nutné znát a několik věcí, které budete potřebovat na místě před můžete pokračovat.

### <a name="what-to-know"></a>Co potřebujete vědět
Musí mít provozní znalosti o technologiích následující:

- [Technologie clusteru systému Windows](http://technet.microsoft.com/library/hh831579.aspx)
-  [Instance clusteru převzetí služeb při selhání SQL serveru](http://msdn.microsoft.com/library/ms189134.aspx).

Kromě toho musí mít obecné znalosti následujících technologií:

- [Konvergované Hyper řešení pomocí prostory úložiště – přímé v systému Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Skupiny prostředků Azure.](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Co je potřeba mít

Než budete postupovat podle pokynů v tomto článku, byste již měli mít:

- Předplatné Microsoft Azure.
- Domény systému Windows na virtuálních počítačích Azure.
- Účet s oprávněním k vytváření objektů ve virtuálním počítači Azure.
- Virtuální síti Azure a podsítě s IP Adresou dostatečný Adresní prostor pro následující součásti:
   - Virtuální počítače.
   - IP adresa clusteru převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- DNS nakonfigurovaný v síti Azure, odkazující na řadiče domény.

Tyto požadavky splněny můžete pokračovat s vytvářením clusteru převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [portál Azure](http://portal.azure.com) s vaším předplatným.

1. [Vytvořit skupinu dostupnosti Azure](../tutorial-availability-sets.md).

   Dostupnost v rámci domén selhání nastavit skupiny virtuálních počítačů a aktualizaci domény. Skupiny dostupnosti je zajištěno, že vaše aplikace není ovlivněn jediný bod selhání, jako je síťový přepínač nebo jednotka power rack serverů.

   Pokud jste dosud nevytvořili skupinu prostředků pro virtuální počítače, když vytvoříte skupinu dostupnosti Azure ho proveďte. Pokud používáte portál Azure k vytvoření skupiny dostupnosti, proveďte následující kroky:

   - Na portálu Azure klikněte na tlačítko **+** otevřete Azure Marketplace. Vyhledejte **sadu dostupnosti**.
   - Klikněte na tlačítko **sadu dostupnosti**.
   - Klikněte na možnost **Vytvořit**.
   - Na **vytvořit skupinu dostupnosti** okno, nastavte následující hodnoty:
      - **Název**: název sady dostupnosti.
      - **Předplatné**: Azure vaše předplatné.
      - **Skupina prostředků**: Pokud chcete použít existující skupinu, klikněte na tlačítko **použít existující** a vyberte skupinu z rozevíracího seznamu. Jinak vyberte **vytvořit nový** a zadejte název pro skupinu.
      - **Umístění**: nastavte umístění, kde chcete vytvořit virtuální počítače.
      - **Poruch domén**: použijte výchozí (3).
      - **Aktualizovat domén**: použijte výchozí (5).
   - Klikněte na tlačítko **vytvořit** vytvoření dostupnost sady.

1. Vytvoření virtuálních počítačů v sadě dostupnosti.

   Zřídit dva virtuální počítače systému SQL Server v sadě Azure dostupnosti. Pokyny najdete v tématu [zřízení virtuálního počítače s SQL serverem na portálu Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Umístíte virtuální počítače:

   - Ve službě Azure stejné skupině prostředků, kterou vaše dostupnosti je v.
   - Ve stejné síti jako řadiče domény.
   - V podsíti s dostatkem místa IP adresu pro virtuální počítače a všech instancích Fci, které může nakonec použijete na tomto clusteru.
   - V sadě Azure dostupnosti.   

      >[!IMPORTANT]
      >Nelze nastavit nebo změnit dostupnosti nastavit po vytvoření virtuálního počítače.

   Vyberte bitovou kopii z Azure Marketplace. Můžete použít na trhu bitové kopie s, který zahrnuje Windows Server a SQL Server nebo jenom Windows Server. Podrobnosti najdete v tématu [přehled systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficiální imagí SQL serveru v galerii Azure zahrnují nainstalovaná instance systému SQL Server, plus instalace softwaru SQL Server a vyžadovaný klíč.

   Vyberte bitovou kopii správné podle způsob platit za licenci systému SQL Server:

   - **Platba za použití licencování**: náklady za sekundu na těchto bitových kopií zahrnuje licencování SQL serveru:
      - **SQL Server 2016 Enterprise na Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard na Windows Server Datacenter 2016**
      - **SQL Server 2016 vývojáře v systému Windows Server Datacenter 2016**

   - **Převést – vaše – vlastní – licence (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise na Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard na Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Po vytvoření virtuálního počítače odeberte předem nainstalovaná samostatnou instanci serveru SQL. Chcete-li vytvořit SQL Server FCI po konfiguraci clusteru převzetí služeb při selhání a S2D použijete předem nainstalovaná média systému SQL Server.

   Alternativně můžete použít Image Azure Marketplace s pouze operační systém. Vyberte **Windows Server 2016 Datacenter** bitovou kopii a nainstalujte SQL Server FCI po konfiguraci clusteru převzetí služeb při selhání a S2D. Tento image neobsahuje instalačního média systému SQL Server. Umístíte na instalačním médiu v umístění, kde můžete spouštět instalace systému SQL Server pro každý server.

1. Jakmile Azure vytvoří virtuální počítače, připojení všem virtuálním počítačům pomocí protokolu RDP.

   Když poprvé připojíte k virtuálnímu počítači pomocí protokolu RDP, počítač požádá, pokud chcete povolit tento počítač zjistitelné v síti. Klikněte na **Ano**.

1. Pokud použijete jednu z imagí virtuálního počítače se systémem SQL Server, odeberte instance systému SQL Server.

   - V **programy a funkce**, klikněte pravým tlačítkem na **Microsoft SQL Server 2016 (64 bitů)** a klikněte na tlačítko **odinstalovat nebo změnit**.
   - Klikněte na tlačítko **odebrat**.
   - Vyberte výchozí instanci.
   - Odeberte všechny funkce v části **služby databázového stroje**. Neodebírejte **sdílené součásti**. Viz následující obrázek:

      ![Odebrat funkce](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klikněte na tlačítko **Další**a potom klikněte na **odebrat**.

1. <a name="ports"></a>Otevřete porty brány firewall.

   Na každém virtuálním počítači otevřete následující porty v bráně Windows Firewall.

   | Účel | TCP Port | Poznámky
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance systému SQL Server. Pokud jste použili bitovou kopii z galerie, se automaticky otevře tento port.
   | Test stavu | 59999 | Žádné otevřete TCP port. Později, nakonfigurujte pro vyrovnávání zatížení [test stavu](#probe) a cluster na tento port použít.  

1. Přidání úložiště do virtuálního počítače. Podrobné informace najdete v tématu [přidejte úložiště](../premium-storage.md).

   Virtuální počítače nutné aspoň dva datových disků.

   Připojte nezpracovaná disky - není NTFS naformátovaný disky.
      >[!NOTE]
      >Pokud připojíte naformátovaném systémem souborů NTFS disků, můžete povolit jenom S2D s žádná kontrola způsobilosti disku.  

   Pro každý virtuální počítač připojte minimálně dvě Storage úrovně Premium (SSD disky). Doporučujeme alespoň P30 disky (1 TB).

   Sada hostitele ukládání do mezipaměti ke **jen pro čtení**.

   Kapacita úložiště, které můžete použít v produkčním prostředí závisí na velikosti pracovní zátěže. Hodnoty popsané v tomto článku jsou pro demonstrační a testování.

1. [Přidejte virtuální počítače k existující doméně](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po virtuální počítače jsou vytvoření a konfiguraci, můžete nakonfigurovat cluster převzetí služeb při selhání.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2: Konfigurace clusteru převzetí služeb při selhání systému Windows s S2D

Dalším krokem je konfigurace clusteru převzetí služeb při selhání s S2D. V tomto kroku provedete následující dílčích kroků:

1. Přidejte funkci Clustering převzetí služeb při selhání systému Windows
1. Ověření clusteru
1. Vytvoření clusteru převzetí služeb při selhání
1. Vytvoření určujícího prvku cloudu
1. Přidání úložiště

### <a name="add-windows-failover-clustering-feature"></a>Přidejte funkci Clustering převzetí služeb při selhání systému Windows

1. Pokud chcete začít, připojte k první virtuální počítač s RDP pomocí účtu domény, který je členem místní skupiny administrators a má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro ostatní konfigurace.

1. [Přidejte funkci Clustering převzetí služeb při selhání na každém virtuálním počítači](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Instalace funkce Clustering převzetí služeb při selhání z uživatelského rozhraní, proveďte následující kroky na virtuální počítače.
   - V **správce serveru**, klikněte na tlačítko **spravovat**a potom klikněte na **přidat role a funkce**.
   - V **Průvodce přidáním rolí a funkcí**, klikněte na tlačítko **Další** až na **vybrat funkce**.
   - V **vybrat funkce**, klikněte na tlačítko **Clustering převzetí služeb při selhání**. Zahrnout všechny požadované funkce a nástroje pro správu. Klikněte na tlačítko **přidat funkce**.
   - Klikněte na tlačítko **Další** a pak klikněte na **Dokončit** nainstalovat funkce.

   K instalaci funkce Clustering převzetí služeb při selhání v prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Pro referenci další kroky, postupujte podle pokynů v kroku 3 [konvergované Hyper řešení pomocí prostory úložiště – přímé v systému Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Ověření clusteru

Tato příručka označuje pokyny v části [ověření clusteru](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Ověření clusteru v uživatelském rozhraní nebo pomocí prostředí PowerShell.

Pro ověření clusteru s uživatelským rozhraním, proveďte následující kroky z jednoho z virtuálních počítačů.

1. V **správce serveru**, klikněte na tlačítko **nástroje**, pak klikněte na tlačítko **Správce clusteru převzetí služeb při selhání**.
1. V **Správce clusteru převzetí služeb při selhání**, klikněte na tlačítko **akce**, pak klikněte na tlačítko **ověřit konfiguraci...** .
1. Klikněte na **Další**.
1. Na **vybrat servery nebo Cluster**, zadejte název virtuální počítače.
1. Na **testování možnosti**, zvolte **spustit jenom testy, vyberte**. Klikněte na **Další**.
1. Na **testování výběr**, zahrnout všechny testy s výjimkou **úložiště**. Viz následující obrázek:

   ![Ověřit testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klikněte na **Další**.
1. Na **potvrzení**, klikněte na tlačítko **Další**.

**Průvodce ověřením konfigurace** spustí testy pro ověření.

Pro ověření clusteru v prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru, vytvořte cluster převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru převzetí služeb při selhání

Tato příručka označuje [vytvořit cluster převzetí služeb při selhání](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Pokud chcete vytvořit cluster převzetí služeb při selhání, potřebujete:
- Názvy virtuálních počítačů, které se uzly clusteru.
- Název pro cluster převzetí služeb při selhání
- IP adresu pro převzetí služeb při selhání clusteru. Můžete použít IP adresu, která nepoužívá stejnou virtuální síť Azure a podsíť jako uzly clusteru.

Následující PowerShell vytváří cluster s podporou převzetí služeb při selhání. Skript aktualizace s názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresu z virtuální sítě Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Vytvoření určujícího cloudu

Cloud s kopií clusteru je nový typ určující disk kvora clusteru, která je uložená v objektu Blob úložiště Azure. To eliminuje nutnost samostatné virtuálního počítače hostování určující sdílenou složku.

1. [Vytvoření určujícího cloudu pro převzetí služeb při selhání clusteru](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů blob.

1. Uložte přístupové klíče a adresy URL kontejneru.

1. Nakonfigurujte určující disk kvora clusteru cluster převzetí služeb při selhání. V tématu, [nakonfigurovat určující disk kvora v uživatelském rozhraní]. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) v uživatelském rozhraní.

### <a name="add-storage"></a>Přidání úložiště

Disky pro S2D musí být prázdné a bez oddílů nebo jiná data. Pro čištění disky podle [kroky v této příručce](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Prostory úložiště povolení přímé \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Následující prostředí PowerShell umožňuje prostory úložiště – přímé.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   V **Správce clusteru převzetí služeb při selhání**, se zobrazí fondu úložiště.

1. [Vytvoření svazku](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jedna z funkcí S2D je, že automaticky vytvoří fond úložiště při jejím povolením. Nyní jste připraveni k vytvoření svazku. Prostředí PowerShell `New-Volume` automatizuje proces vytvoření svazku, včetně formátování, přidání do clusteru a vytvoření sdíleného svazku clusteru (CSV). Následující příklad vytvoří 800 gigabajt (GB) sdílených svazků clusteru.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po dokončení tohoto příkazu je připojen 800 GB svazek jako prostředku clusteru. Svazek je v `C:\ClusterStorage\Volume1\`.

   Následující diagram znázorňuje sdílený svazek clusteru s S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Testování převzetí služeb při selhání clusteru převzetí služeb při selhání

Ve Správci clusteru převzetí služeb při selhání ověřte, že prostředků úložiště můžete přesunout do jiného uzlu clusteru. Pokud se můžete připojit ke clusteru převzetí služeb při selhání s **Správce clusteru převzetí služeb při selhání** a přesunout úložiště z jednoho uzlu do druhého, jste připraveni ke konfiguraci FCI.

## <a name="step-4-create-sql-server-fci"></a>Krok 4: Vytvoření systému SQL Server FCI

Po nakonfigurování clusteru převzetí služeb při selhání a všechny součásti clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte k první virtuální počítač s RDP.

1. V **Správce clusteru převzetí služeb při selhání**, zkontrolujte, zda jsou všechny základní prostředky clusteru na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky k tomuto virtuálnímu počítači.

1. Vyhledejte na instalačním médiu. Pokud virtuální počítač používá jednu z imagí Azure Marketplace, se nachází na médium nástroje `C:\SQLServer_<version number>_Full`. Klikněte na tlačítko **instalační program**.

1. V **centrum instalace SQL serveru**, klikněte na tlačítko **instalace**.

1. Klikněte na tlačítko **nová instalace SQL serveru převzetí služeb při selhání clusteru**. Postupujte podle pokynů v průvodci a nainstalujte SQL Server FCI.

   FCI datové adresáře musí být v úložišti clusteru. S S2D není sdíleného disku, ale přípojného bodu do svazku na každém serveru. S2D synchronizuje svazku mezi obou uzlů. Svazek je zobrazen do clusteru jako sdílený svazek clusteru. Použijte přípojného bodu sdíleného svazku clusteru pro data adresáře.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po dokončení průvodce se instalační program nainstaluje SQL Server FCI na prvním uzlu.

1. Poté, co instalační program úspěšně nainstaluje FCI na prvním uzlu, připojte ve druhém uzlu pomocí protokolu RDP.

1. Otevřete **centrum instalace SQL serveru**. Klikněte na tlačítko **instalace**.

1. Klikněte na tlačítko **přidat uzel do clusteru s podporou převzetí služeb při selhání systému SQL Server**. Postupujte podle pokynů v průvodci k instalaci systému SQL server a přidejte tento server FCI.

   >[!NOTE]
   >Pokud jste použili bitovou kopii Galerie Azure Marketplace s SQL serverem, nástroje SQL Server byly součástí bitovou kopii. Pokud jste nepoužili tuto bitovou kopii, nainstalujte nástroje SQL Server samostatně. V tématu [stáhnout SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5: Vytvoření pro vyrovnávání zatížení Azure

Na virtuálních počítačích Azure použijte clustery Vyrovnávání zatížení pro uložení IP adresu, která musí být v jednom uzlu clusteru současně. Nástroje pro vyrovnávání zatížení v tomto řešení obsahuje IP adresu pro SQL Server FCI.

[Vytvoření a konfigurace pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvořit nástroj pro vyrovnávání zatížení na portálu Azure

Pokud chcete vytvořit pro vyrovnávání zatížení:

1. Na portálu Azure přejděte do skupiny prostředků s virtuálními počítači.

1. Klikněte na tlačítko **+ přidat**. Vyhledávání na webu Marketplace pro **nástroj pro vyrovnávání zatížení**. Klikněte na tlačítko **nástroj pro vyrovnávání zatížení**.

1. Klikněte na možnost **Vytvořit**.

1. Konfigurace vyrovnávání zatížení:

   - **Název**: název, který identifikuje nástroje pro vyrovnávání zatížení.
   - **Typ**: nástroje pro vyrovnávání zatížení může být veřejné nebo soukromé. Nástroj pro vyrovnávání zatížení privátní je přístupná z stejnou virtuální síť. Většina Azure aplikace můžete použít nástroj pro vyrovnávání zatížení privátní. Pokud aplikace potřebuje přístup k systému SQL Server přímo přes Internet, použijte nástroj pro vyrovnávání zatížení veřejné.
   - **Virtuální síť**: stejné síti jako virtuální počítače.
   - **Podsíť**: stejné podsíti jako virtuální počítače.
   - **Privátní IP adresa**: stejnou IP adresu, který jste přiřadili k síťovému prostředku clusteru SQL serveru FCI.
   - **Předplatné**: Azure vaše předplatné.
   - **Skupina prostředků**: použijte stejné skupině prostředků jako virtuální počítače.
   - **Umístění**: použití stejného umístění Azure jako virtuální počítače.
   Viz následující obrázek:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Nakonfigurujte fond back-end pro vyrovnávání zatížení.

1. Vraťte se na skupiny prostředků Azure s virtuálními počítači a vyhledejte nové nástroje pro vyrovnávání zatížení. Možná budete muset aktualizovat zobrazení ve skupině prostředků. Klikněte na nástroje pro vyrovnávání zatížení.

1. V okně nástroje pro vyrovnávání zatížení, klikněte na **back-endové fondy**.

1. Klikněte na tlačítko **+ přidat** přidat fond back-end.

1. Zadejte název pro fond back-end.

1. Klikněte na tlačítko **přidat virtuální počítač**.

1. Na **vyberte virtuální počítače** okně klikněte na tlačítko **zvolit skupinu dostupnosti**.

1. Vyberte, že dostupnost nastavit, že jste umístili virtuální počítače v systému SQL Server.

1. Na **vyberte virtuální počítače** okně klikněte na tlačítko **zvolit virtuální počítače**.

   Portálu Azure by měl vypadat jako na následujícím obrázku:

   ![CreateLoadBalancerBackEnd](./media/virtual-machines-windows-portal-sql-create-failover-cluster/33-load-balancer-back-end.png)

1. Klikněte na tlačítko **vyberte** na **vyberte virtuální počítače** okno.

1. Klikněte na tlačítko **OK** dvakrát.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurace stavu sondu nástroje pro vyrovnávání zatížení.

1. V okně nástroje pro vyrovnávání zatížení, klikněte na **testy stavu**.

1. Klikněte na tlačítko **+ přidat**.

1. Na **test stavu přidat** okně <a name="probe"> </a>nastavit stav testu parametry:

   - **Název**: název pro kontrolu stavu.
   - **Protokol**: TCP.
   - **Port**: nastavte na dostupný port TCP. Tento port vyžaduje k portu brány firewall otevřít. Použití [stejný port](#ports) nastavení pro test stavu v bráně firewall.
   - **Interval**: 5 sekund.
   - **Prahová hodnota špatného stavu**: 2 po sobě jdoucích selhání.

1. Klikněte na tlačítko OK.

### <a name="set-load-balancing-rules"></a>Nastavit pravidla Vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení, klikněte na **pravidla Vyrovnávání zatížení**.

1. Klikněte na tlačítko **+ přidat**.

1. Nastavte parametry pravidla Vyrovnávání zatížení:

   - **Název**: název pravidla Vyrovnávání zatížení.
   - **Adresa IP front-endu**: používat IP adresu pro SQL Server FCI clusteru síťovému prostředku.
   - **Port**: pro port TCP systému SQL Server FCI nastaveno. Výchozí instance port je 1433.
   - **Back-endový port**: Tato hodnota používá stejný port jako **Port** hodnotu, pokud povolíte **plovoucí IP adresa (přímá odpověď ze serveru)**.
   - **Fond back-end**: použijte název fondu back-end, který jste nakonfigurovali dříve.
   - **Test stavu**: použijte Test stavu, který jste nakonfigurovali dříve.
   - **Trvalost relace**: žádné.
   - **Časový limit (v minutách) nečinnosti**: 4.
   - **Plovoucí IP adresa (přímá odpověď ze serveru)**: povoleno

1. Klikněte na **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6: Konfigurace clusteru pro test paměti

Nastavte parametr port testu clusteru v prostředí PowerShell.

Pokud chcete nastavit parametr port testu clusteru, aktualizujte proměnné ve následující skript s hodnotami ze svého prostředí. Odebrat lomené závorky `<>` ze skriptu. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

V předchozím skript nastavte hodnoty pro vaše prostředí. Následující seznam popisuje hodnoty:

   - `<Cluster Network Name>`: Název sítě Windows Server Failover Cluster. V **Správce clusteru převzetí služeb při selhání** > **sítě**, klikněte pravým tlačítkem na síť a klikněte na **vlastnosti**. Probíhá správnou hodnotu **název** na **Obecné** kartě. 

   - `<SQL Server FCI IP Address Resource Name>`: Název prostředku SQL Server FCI IP adresy. V **Správce clusteru převzetí služeb při selhání** > **role**, role serveru SQL Server FCI pod **název serveru**, klikněte pravým tlačítkem na prostředek IP adresy a klikněte na tlačítko **Vlastnosti**. Probíhá správnou hodnotu **název** na **Obecné** kartě. 

   - `<ILBIP>`: ILB IP adresa. Tato adresa je nakonfigurovaná na portálu Azure jako adresa front-endu ILB. Toto je také SQL serveru FCI IP adresu. Najdete ho v **Správce clusteru převzetí služeb při selhání** na stejné stránce vlastností, kde jste vyhledali `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Je port testu, který jste nakonfigurovali v test stavu nástroje pro vyrovnávání zatížení. Všechny nepoužívaný port TCP je platný. 

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být adresa všesměrového vysílání TCP: `255.255.255.255`.

Po nastavení testu clusteru, zobrazí se všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Testování převzetí služeb při selhání FCI

Testovací převzetí služeb při selhání FCI k ověření fungování clusteru. Proveďte následující kroky:

1. Připojení s jedním z uzlů clusteru SQL serveru FCI pomocí protokolu RDP.

1. Otevřete **Správce clusteru převzetí služeb při selhání**. Klikněte na tlačítko **role**. Všimněte si, který uzel vlastní roli SQL serveru FCI.

1. Klikněte pravým tlačítkem na roli SQL serveru FCI.

1. Klikněte na tlačítko **přesunout** a klikněte na tlačítko **nejvhodnějšího uzlu**.

**Správce clusteru převzetí služeb při selhání** ukazuje přechodu do offline režimu role a její prostředky. Prostředky pak přesuňte a režimu online na druhém uzlu.

### <a name="test-connectivity"></a>Test připojení

Chcete-li otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k názvu SQL serveru FCI.

>[!NOTE]
>Pokud třeba, můžete [stáhnout SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Omezení
Na virtuálních počítačích Azure nepodporuje Microsoft koordinátoru DTC (Distributed Transaction) v instancích Fci, protože RPC port není podporována pro vyrovnávání zatížení.

## <a name="see-also"></a>Viz také

[Instalační program S2D pomocí vzdálené plochy (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Konvergované Hyper řešení s prostory úložiště direct](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Prostory úložiště – přímé přehled](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Podpora systému SQL Server pro S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)

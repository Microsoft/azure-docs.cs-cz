---
title: SQL Server FCI – virtuální počítače Azure | Dokumentace Microsoftu
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
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 8e107c1721d5623239a694eba39b32e8a2a6089d
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42055490"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurace Instance clusteru převzetí služeb při selhání SQL serveru na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit SQL Server převzetí služeb při selhání clusteru Instance (FCI) na virtuálních počítačích Azure v modelu Resource Manager. Toto řešení používá [systému Windows Server 2016 Datacenter edition prostorů úložiště s přímým \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako softwarovou virtuální síť SAN, který synchronizuje mezi uzly (virtuální počítače Azure) v úložišti (datové disky) Windows Cluster. S2D je nového ve Windows serveru 2016.

Následující diagram ukazuje kompletní řešení na virtuálních počítačích Azure:

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Předchozí diagram znázorňuje:

- Dva virtuální počítače Azure v clusteru převzetí služeb při selhání s Windows. Když je virtuální počítač v clusteru převzetí služeb při selhání se také nazývá *uzlu clusteru*, nebo *uzly*.
- Každý virtuální počítač má dvě nebo více datových disků.
- S2D synchronizuje data na datový disk a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje sdíleného svazku clusteru (CSV) do clusteru převzetí služeb při selhání.
- Role clusteru SQL serveru FCI používá sdílený svazek clusteru pro datové jednotky.
- Azure load balancer pro uložení IP adresu pro SQL Server FCI.
- Skupině dostupnosti Azure obsahuje všechny prostředky.

   >[!NOTE]
   >Všechny prostředky Azure jsou v diagramu jsou ve stejné skupině prostředků.

Podrobnosti o S2D najdete v tématu [systému Windows Server 2016 Datacenter edition prostory úložiště – přímé \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D podporuje dva typy architektur - konvergované a konvergované na hyper v. Architektura v tomto dokumentu je konvergované na hyper v. Infrastrukturu konvergované na hyper v umístí na stejných serverů, které hostují aplikaci v clusteru úložiště. V této architektuře je úložiště na každý uzel SQL serveru FCI.

## <a name="licensing-and-pricing"></a>Licencování a ceny

Ve službě Azure Virtual Machines můžete licence SQL serveru pomocí průběžných plateb (PAYG) nebo používání vlastní licence Image virtuálních počítačů (BYOL). Typ image, kterou zvolíte ovlivňuje, jak se vám účtuje.

Instance clusteru převzetí služeb při selhání (FCI) systému SQL Server na virtuálních počítačích Azure s průběžnými PLATBAMI licencování, neúčtují poplatky za pro všechny uzly FCI, včetně pasivní uzly. Další informace najdete v tématu [ceník funkce SQL Server Enterprise Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Zákazníci se smlouvou Enterprise s programem Software Assurance mají právo používat jeden bezplatný pasivní uzel FCI pro každý aktivní uzel. Abyste mohli využívat tuto výhodu v Azure, používat Image virtuálních počítačů BYOL a potom použijte stejné licence na aktivními a pasivními uzly FCI. Další informace najdete v tématu [smlouvy Enterprise](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Můžete porovnat průběžné platby a BYOL licencování pro SQL Server na virtuálních počítačích Azure najdete v článku [Začínáme s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Kompletní informace o licencování SQL serveru najdete v tématu [ceny](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Příklad šablony Azure

Celé řešení v Azure můžete vytvořit ze šablony. Příklad šablony je k dispozici na Githubu [šablony pro rychlý start Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). V tomto příkladu není určená nebo testování pro jakékoli konkrétní úlohy. Můžete spustit šablonu pro vytváření SQL Server FCI s S2D úložiště připojené k vaší doméně. Můžete vyhodnotit šablony a upravit pro vaše záměry.

## <a name="before-you-begin"></a>Než začnete

Existuje několik věcí, které potřebujete znát a několik věcí, které budete potřebovat na místě, než budete pokračovat.

### <a name="what-to-know"></a>Co potřebujete vědět
Měli byste provozní znalost následujících technologií:

- [Technologie clusteru Windows](http://technet.microsoft.com/library/hh831579.aspx)
- [Instance clusteru převzetí služeb při selhání SQL serveru](http://msdn.microsoft.com/library/ms189134.aspx).

Také byste měli mít obecné principy systému následující technologie:

- [Konvergované na Hyper v řešení využívající prostory úložiště – přímé ve Windows serveru 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Skupiny prostředků Azure](../../../azure-resource-manager/resource-group-portal.md)

> [!IMPORTANT]
> V tuto chvíli [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) není podporována pro FCI Server SQL v Azure. Doporučujeme odinstalovat rozšíření z virtuálních počítačů, které jsou součástí FCI. Toto rozšíření podporuje funkce, jako je automatické zálohování a opravy a některé funkce portálu pro SQL. Tato funkce nebude fungovat pro virtuální počítače s SQL po daný agent nebude odinstalován.

### <a name="what-to-have"></a>Co je nutné mít

Než budete postupovat podle pokynů tohoto článku, byste už měli mít:

- Předplatné Microsoft Azure.
- Doména Windows na virtuálních počítačích Azure.
- Účet s oprávněním k vytvoření objektů ve virtuálním počítači Azure.
- Virtuální síť Azure a podsítě s IP Adresou dostatečný Adresní prostor pro následující komponenty:
   - Oba virtuální počítače.
   - IP adresa clusteru převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- DNS nakonfigurovaný v síti Azure, přejdete na řadičích domény.

Tyto požadavky můžete pokračovat s vytvářením clusteru převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [webu Azure portal](http://portal.azure.com) s vaším předplatným.

1. [Vytvoření skupiny dostupnosti Azure](../tutorial-availability-sets.md).

   Dostupnost nastavit skupiny virtuálních počítačů napříč doménami selhání a aktualizačními doménami. Skupina dostupnosti zajišťuje, že vaší aplikace neprojeví jednotlivé body selhání, třeba chybný síťový přepínač nebo napájecí jednotka racku serverů.

   Pokud jste ještě nevytvořili skupinu prostředků pro vaše virtuální počítače, postup při vytvoření sady dostupnosti Azure. Pokud používáte na webu Azure portal k vytvoření sady dostupnosti, proveďte následující kroky:

   - Na webu Azure Portal, klikněte na tlačítko **+** otevřít na webu Azure Marketplace. Vyhledejte **dostupnosti**.
   - Klikněte na tlačítko **dostupnosti**.
   - Klikněte na možnost **Vytvořit**.
   - Na **vytvořit skupinu dostupnosti** okno, nastavte následující hodnoty:
      - **Název**: název sady dostupnosti.
      - **Předplatné**: vašeho předplatného Azure.
      - **Skupina prostředků**: Pokud chcete použít existující skupinu, klikněte na tlačítko **použít existující** a vyberte skupinu z rozevíracího seznamu. V opačném případě zvolte **vytvořit nový** a zadejte název pro skupinu.
      - **Umístění**: nastavit umístění, kde plánujete vytvoření virtuálních počítačů.
      - **Domény selhání**: používají výchozí hodnoty (3).
      - **Aktualizační domény**: použít výchozí nastavení (5).
   - Klikněte na tlačítko **vytvořit** vytvoření dostupnost sady.

1. Vytvoření virtuálních počítačů ve skupině dostupnosti.

   Zřízení dva virtuální počítače systému SQL Server ve skupině dostupnosti Azure. Pokyny najdete v tématu [zřízení virtuálního počítače s SQL serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umístíte oba virtuální počítače:

   - Ve stejné skupině prostředků, kterou vaše skupiny dostupnosti probíhá.
   - Ve stejné síti jako řadiče domény.
   - V podsíti s dostatečný Adresní prostor IP adres pro virtuální počítače a všech instancích Fci, které můžete případně použít na tomto clusteru.
   - Ve skupině dostupnosti Azure.   

      >[!IMPORTANT]
      >Nelze nastavit nebo změna skupiny dostupnosti po vytvoření virtuálního počítače.

   Vyberte image z Azure Marketplace. Tržiště můžete použít obraz, který obsahuje Windows Server a SQL Server nebo jenom Windows Server. Podrobnosti najdete v tématu [přehled SQL serveru na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficiální Image SQL serveru v galerii Azure zahrnují nainstalovaná instance systému SQL Server, a instalace softwaru SQL Server a vyžaduje klíč.

   Vyberte správnou image podle způsobu platit za licenci systému SQL Server:

   - **Platba za použití licencování**: náklady za sekundu z těchto imagí zahrnuje licencování SQL serveru:
      - **SQL Server 2016 Enterprise v datacentru, Windows Server 2016**
      - **SQL Server 2016 Standard v datacentru, Windows Server 2016**
      - **SQL Server 2016 Developer v systému Windows Server Datacenter 2016**

   - **Přineste si – vlastní licence (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise v datacentru, Windows Server 2016**
      - **{BYOL} SQL Server 2016 Standard v datacentru, Windows Server 2016**

   >[!IMPORTANT]
   >Po vytvoření virtuálního počítače odeberte předinstalované samostatná instance SQL serveru. Předinstalované média systému SQL Server použije k vytvoření SQL Server FCI, po konfiguraci clusteru převzetí služeb při selhání a S2D.

   Alternativně můžete použít Image Azure Marketplace s pouze operační systém. Zvolte **systému Windows Server 2016 Datacenter** obrázků a nainstalujte SQL Server FCI po konfiguraci clusteru převzetí služeb při selhání a S2D. Tato image neobsahuje instalačního média systému SQL Server. Umístíte instalačním médiu na místě, kde můžete spouštět instalace systému SQL Server pro každý server.

1. Po navázání partnerského Azure vytvářet virtuální počítače, připojte jednotlivým virtuálním počítačům pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači pomocí RDP, počítač zeptá, jestli chcete povolit tento počítač zjistitelné v síti. Klikněte na **Ano**.

1. Pokud používáte některou k imagí virtuálních počítačů založené na SQL serveru, odeberte instanci systému SQL Server.

   - V **programy a funkce**, klikněte pravým tlačítkem na **Microsoft SQL Server 2016 (64-bit)** a klikněte na tlačítko **odinstalovat nebo změnit**.
   - Klikněte na tlačítko **odebrat**.
   - Vyberte výchozí instanci.
   - Odebrat všechny funkce v rámci **služby databázového stroje**. Neodebírejte **sdílené součásti**. Viz následující obrázek:

      ![Odebrat funkce](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klikněte na tlačítko **Další**a potom klikněte na tlačítko **odebrat**.

1. <a name="ports"></a>Otevřete porty brány firewall.

   Na každý virtuální počítač otevřete následující porty v bráně Windows Firewall.

   | Účel | TCP Port | Poznámky
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance systému SQL Server. Pokud jste použili image z galerie, se automaticky otevře tento port.
   | Sonda stavu | 59999 | Některé otevřete TCP port. V pozdějším kroku, nakonfigurujte nástroj pro vyrovnávání zatížení [sondu stavu](#probe) a cluster používat tento port.  

1. Přidání úložiště k virtuálnímu počítači. Podrobné informace najdete v tématu [přidat úložiště](../premium-storage.md).

   Oba virtuální počítače, potřebujete alespoň dva datové disky.

   Připojit holé disky – ne systému souborů NTFS ve formátu disky.
      >[!NOTE]
      >Pokud připojíte disky formátované systémem souborů NTFS, lze povolit pouze S2D s není žádná kontrola nároku disku.  

   Připojení minimálně dva Storage úrovně Premium (SSD disků) do všech virtuálních počítačů. Doporučujeme aspoň P30 (1 TB) disky.

   Ukládání do mezipaměti hostitele sady **jen pro čtení**.

   Kapacitu úložiště, které můžete používat v produkčním prostředí závisí na velikosti pracovní zátěže. S hodnotami popsanými v tomto článku jsou pro ukázkové a testování.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů, můžete nakonfigurovat cluster převzetí služeb při selhání.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2: Konfigurace převzetí služeb při selhání clusteru Windows s S2D

Dalším krokem je konfigurace clusteru převzetí služeb při selhání s S2D. V tomto kroku provedete následující dílčí kroky:

1. Přidejte funkci Clustering převzetí služeb při selhání s Windows
1. Ověření clusteru
1. Vytvoření clusteru převzetí služeb při selhání
1. Vytvořte disk s kopií cloudu
1. Přidání úložiště

### <a name="add-windows-failover-clustering-feature"></a>Přidejte funkci Clustering převzetí služeb při selhání s Windows

1. Pokud chcete začít, napojení na prvním virtuálním počítači protokol RDP pomocí účtu domény, který je členem skupiny místních správců a má oprávnění k vytváření objektů ve službě Active Directory. Pro zbývající konfiguraci pomocí tohoto účtu.

1. [Přidejte funkci Clustering převzetí služeb při selhání na každý virtuální počítač](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   K instalaci funkce Clustering převzetí služeb při selhání z uživatelského rozhraní, proveďte následující kroky na obou virtuálních počítačích.
   - V **správce serveru**, klikněte na tlačítko **spravovat**a potom klikněte na tlačítko **přidat role a funkce**.
   - V **Průvodce přidání rolí a funkcí**, klikněte na tlačítko **Další** dokud nezískáte **vybrat funkce**.
   - V **vybrat funkce**, klikněte na tlačítko **Clustering převzetí služeb při selhání**. Zahrnout všechny požadované součásti a nástroje pro správu. Klikněte na tlačítko **přidat funkce**.
   - Klikněte na tlačítko **Další** a potom klikněte na tlačítko **Dokončit** nainstalovat funkce.

   K instalaci funkce Clustering převzetí služeb při selhání pomocí prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce v jednom z virtuálních počítačů.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Pro srovnání dalších kroků, postupujte podle pokynů v kroku 3 [konvergované na Hyper v řešení využívající prostory úložiště – přímé ve Windows serveru 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Ověření clusteru

Tato příručka odkazuje na pokyny v části [ověření clusteru](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Ověření clusteru v uživatelském rozhraní nebo Powershellu.

Pro ověření clusteru pomocí uživatelského rozhraní, proveďte následující kroky z některého z virtuálních počítačů.

1. V **správce serveru**, klikněte na tlačítko **nástroje**, pak klikněte na tlačítko **Správce clusteru převzetí služeb při selhání**.
1. V **Správce clusteru převzetí služeb při selhání**, klikněte na tlačítko **akce**, pak klikněte na tlačítko **ověřit konfiguraci...** .
1. Klikněte na **Další**.
1. Na **vyberte servery nebo Cluster**, zadejte oba virtuální počítače.
1. Na **možnosti testování**, zvolte **spouštění testů pouze vyberu**. Klikněte na **Další**.
1. Na **Výběr testu**, zahrnují všechny testy kromě **úložiště**. Viz následující obrázek:

   ![Ověřit testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klikněte na **Další**.
1. Na **potvrzení**, klikněte na tlačítko **Další**.

**Průvodce ověřením konfigurace** spustí testy pro ověření.

Pro ověření clusteru pomocí prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce v jednom z virtuálních počítačů.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru převzetí služeb při selhání

Tato příručka odkazuje na [vytvoření clusteru převzetí služeb při selhání](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

K vytvoření clusteru převzetí služeb při selhání, budete potřebovat:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název pro cluster převzetí služeb při selhání
- IP adresu pro převzetí služeb při selhání clusteru. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíť jako uzly clusteru.

Následující příkaz Powershellu vytvoří cluster převzetí služeb při selhání. Tento skript aktualizace s názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresu z virtuální sítě Azure:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Vytvořte disk s kopií cloudu

Disk s kopií cloudu je nový typ určujícího disku kvora clusteru, která je uložena v objektu Blob služby Azure Storage. To odstraní potřebu samostatných virtuálních počítačů hostování sdílenou složku s kopií clusteru.

1. [Vytvoření cloudové kopie clusteru pro převzetí služeb při selhání clusteru](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů blob.

1. Uložte přístupové klávesy a adresa URL kontejneru.

1. Konfigurace určujícího prvku kvora clusteru převzetí služeb při selhání. Zobrazit, [konfigurace určujícího prvku kvora v uživatelském rozhraní](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) v uživatelském rozhraní.

### <a name="add-storage"></a>Přidání úložiště

Disky pro S2D musí být prázdné a bez oddílů nebo jiná data. K vyčištění disků podle [kroky v tomto průvodci](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Povolit Store prostory přímo \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Následující příkaz Powershellu povolí prostory úložiště s přímým přístupem.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   V **Správce clusteru převzetí služeb při selhání**, uvidíte teď fond úložiště.

1. [Vytvoření svazku](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jednou z funkcí S2D je, že ji automaticky vytvoří fond úložiště Pokud je povolená. Nyní jste připraveni vytvořit svazek. Rutiny Powershellu `New-Volume` automatizuje proces vytváření svazku, včetně formátování, přidání do clusteru a vytvoření sdíleného svazku clusteru (CSV). Následující příklad vytvoří 800 gigabajt (GB) sdíleného svazku clusteru.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po dokončení tohoto příkazu, 800 GB dat je připojený jako prostředku clusteru. Svazek je na `C:\ClusterStorage\Volume1\`.

   Následující diagram znázorňuje sdíleného svazku clusteru s S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Test převzetí služeb při selhání clusteru převzetí služeb při selhání

V modulu Správce clusteru převzetí služeb při selhání ověřte, že můžete přesunout prostředek úložiště do jiného uzlu clusteru. Pokud se můžete připojit ke clusteru převzetí služeb při selhání s **Správce clusteru převzetí služeb při selhání** a přesunout úložiště z jednoho uzlu, jste připraveni ke konfiguraci FCI.

## <a name="step-4-create-sql-server-fci"></a>Krok 4: Vytvoření serveru SQL Server FCI

Po nakonfigurování clusteru převzetí služeb při selhání a všechny součásti clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte se k první virtuální počítač pomocí protokolu RDP.

1. V **Správce clusteru převzetí služeb při selhání**, ujistěte se, že jsou všechny základní prostředky clusteru na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky k tomuto virtuálnímu počítači.

1. Vyhledejte instalačního média. Pokud virtuální počítač používá jednu z imagí Azure Marketplace, se nachází na médiu `C:\SQLServer_<version number>_Full`. Klikněte na tlačítko **nastavení**.

1. V **centrum instalace systému SQL Server**, klikněte na tlačítko **instalace**.

1. Klikněte na tlačítko **nová instalace SQL serveru převzetí služeb při selhání clusteru**. Postupujte podle pokynů průvodce a nainstalujte SQL Server FCI.

   FCI datové adresáře musí být na clusteru úložiště. Pomocí S2D není sdílený disk, ale přípojného bodu do svazku na každém serveru. S2D synchronizuje svazku mezi oběma uzly. Svazek prezentována clusteru jako sdílený svazek clusteru. Přípojný bod sdíleného svazku clusteru můžete použijte pro datové adresáře.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po dokončení průvodce se instalační program nainstaluje SQL Server FCI na prvním uzlu.

1. Po navázání partnerského instalační program úspěšně nainstaluje FCI na prvním uzlu, připojte přes RDP ve druhém uzlu.

1. Otevřít **centrum instalace systému SQL Server**. Klikněte na tlačítko **instalace**.

1. Klikněte na tlačítko **přidat uzel do clusteru převzetí služeb při selhání systému SQL Server**. Postupujte podle pokynů v průvodci k instalaci systému SQL server a přidejte tento server FCI.

   >[!NOTE]
   >Pokud jste použili image Galerie Azure Marketplace se systémem SQL Server, nástroje SQL Server byly součástí image. Pokud jste nepoužili tuto bitovou kopii, nainstalujte nástroje SQL Server samostatně. Zobrazit [stáhnout SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5: Vytvoření nástroje pro vyrovnávání zatížení Azure

Na Azure virtual machines clustery používají nástroj pro vyrovnávání zatížení pro uložení IP adresu, která musí být v jednom uzlu clusteru současně. Nástroje pro vyrovnávání zatížení v tomto řešení, obsahuje IP adresu pro SQL Server FCI.

[Vytvoření a konfigurace služby Azure load balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření nástroje pro vyrovnávání zatížení na webu Azure Portal

Pokud chcete vytvořit nástroj pro vyrovnávání zatížení:

1. Na webu Azure Portal přejděte do skupiny prostředků s virtuálními počítači.

1. Klikněte na tlačítko **+ Přidat**. Na webu Marketplace vyhledáme **nástroj pro vyrovnávání zatížení**. Klikněte na tlačítko **nástroj pro vyrovnávání zatížení**.

1. Klikněte na možnost **Vytvořit**.

1. Konfigurace vyrovnávání zatížení:

   - **Název**: název, který identifikuje nástroje pro vyrovnávání zatížení.
   - **Typ**: nástroje pro vyrovnávání zatížení může být veřejné nebo soukromé. Nástroj pro vyrovnávání zatížení privátní lze přistupovat z v rámci stejné virtuální síti. Většinu služeb Azure aplikace můžete použít nástroj pro vyrovnávání zatížení privátní. Pokud vaše aplikace potřebuje přístup k systému SQL Server přímo přes Internet, použijte nástroj pro vyrovnávání zatížení veřejnou.
   - **Virtuální síť**: stejné síti jako virtuální počítače.
   - **Podsíť**: stejné podsíti jako virtuální počítače.
   - **Privátní IP adresa**: stejnou IP adresu, který jste přiřadili k síťovému prostředku clusteru SQL serveru FCI.
   - **Předplatné**: vašeho předplatného Azure.
   - **Skupina prostředků**: použijte stejnou skupinu prostředků jako virtuální počítače.
   - **Umístění**: použití stejného umístění Azure jako virtuální počítače.
   Viz následující obrázek:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurace back-endového fondu nástroje pro vyrovnávání zatížení

1. Vraťte se do skupiny prostředků Azure s virtuálními počítači a vyhledejte nové nástroje pro vyrovnávání zatížení. Bude pravděpodobně nutné aktualizovat zobrazení ve skupině prostředků. Klikněte na nástroj pro vyrovnávání zatížení.

1. Klikněte na tlačítko **back-endové fondy** a klikněte na tlačítko **+ přidat** přidat back-endový fond.

1. Back-endového fondu přidružte skupinu dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové konfigurace protokolu IP sítě**, zkontrolujte **VIRTUÁLNÍHO počítače** a vyberte virtuální počítače, které se bude účastnit jako uzly clusteru. Nezapomeňte zahrnout všechny virtuální počítače, které budou hostovat FCI. 

1. Klikněte na tlačítko **OK** vytvořit back-endový fond.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurace sondy stavu nástroje pro vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení, klikněte na **sondy stavu**.

1. Klikněte na tlačítko **+ Přidat**.

1. Na **přidat sondu stavu** okně <a name="probe"> </a>nastavit stav testu parametry:

   - **Název**: název sondy stavu.
   - **Protokol**: TCP.
   - **Port**: nastavte na dostupný port TCP. Tento port vyžaduje port brány firewall otevřít. Použití [stejný port](#ports) jste nastavili pro sondu stavu na bránu firewall.
   - **Interval**: 5 sekund.
   - **Prahová hodnota špatného stavu**: 2 po sobě jdoucích selhání.

1. Klikněte na tlačítko OK.

### <a name="set-load-balancing-rules"></a>Nastavte pravidla Vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení, klikněte na **pravidla Vyrovnávání zatížení**.

1. Klikněte na tlačítko **+ Přidat**.

1. Nastavte pravidla parametrů služby Vyrovnávání zatížení:

   - **Název**: název pravidla Vyrovnávání zatížení.
   - **Front-endovou IP adresu**: použijte IP adresu pro síťový prostředek clusteru SQL serveru FCI.
   - **Port**: nastavte pro port TCP systému SQL Server FCI. Výchozí instanci port je 1433.
   - **Back-endový port**: tuto hodnotu nepoužívá stejný port jako **Port** hodnotu, pokud povolíte **plovoucí IP (přímá odpověď ze serveru vrácené)**.
   - **Back-endový fond**: použijte název fondu back-end, který jste nakonfigurovali v předchozích krocích.
   - **Sonda stavu**: použijte sondu stavu, který jste nakonfigurovali v předchozích krocích.
   - **Trvalost relace**: žádné.
   - **Časový limit (minuty) nečinnosti**: 4.
   - **Plovoucí IP adresa (přímá odpověď ze serveru vrácené)**: povoleno

1. Klikněte na **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6: Konfigurace clusteru pro test paměti

Nastavte parametr port sondy clusteru v prostředí PowerShell.

Pokud chcete nastavit parametr port sondy clusteru, aktualizujte proměnné v následujícím skriptu s hodnotami ze svého prostředí. Odebrat lomené závorky `<>` ze skriptu. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

V předchozím skriptu nastavte hodnoty pro vaše prostředí. Následující seznam popisuje hodnoty:

   - `<Cluster Network Name>`: Název sítě Windows Server Failover Cluster. V **Správce clusteru převzetí služeb při selhání** > **sítě**, klikněte pravým tlačítkem na síť a klikněte na tlačítko **vlastnosti**. Probíhá správnou hodnotu **název** na **Obecné** kartu. 

   - `<SQL Server FCI IP Address Resource Name>`: Název prostředku SQL Server FCI IP adresy. V **Správce clusteru převzetí služeb při selhání** > **role**, v rámci role SQL serveru FCI pod **název serveru**, klikněte pravým tlačítkem myši klikněte na prostředek IP adresy a klikněte na tlačítko **Vlastnosti**. Probíhá správnou hodnotu **název** na **Obecné** kartu. 

   - `<ILBIP>`: ILB IP adresu. Tato adresa je nakonfigurovaný na portálu Azure portal jako front-endových adres ILB. Je také SQL serveru FCI IP adresu. Najdete ho v **Správce clusteru převzetí služeb při selhání** na stejné stránce vlastností, kde je umístěn `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Je portu sondy, které jste nakonfigurovali v sondy stavu nástroje pro vyrovnávání zatížení. Žádné nevyužité port TCP je platný. 

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být adresa všesměrového vysílání TCP: `255.255.255.255`.

Jakmile nastavíte cluster testu můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Test převzetí služeb při selhání FCI

Testovací převzetí služeb při selhání FCI k ověření clusteru funkce. Proveďte následující kroky:

1. Připojte se k jednomu z uzlů clusteru SQL serveru FCI pomocí protokolu RDP.

1. Otevřít **Správce clusteru převzetí služeb při selhání**. Klikněte na tlačítko **role**. Všimněte si, který uzel vlastní roli SQL serveru FCI.

1. Klikněte pravým tlačítkem na roli SQL serveru FCI.

1. Klikněte na tlačítko **přesunout** a klikněte na tlačítko **nejlepšího možného uzlu**.

**Správce clusteru převzetí služeb při selhání** ukazuje role a její prostředky přejdou do režimu offline. Prostředky pak přesuňte a převede do online režimu v jiném uzlu.

### <a name="test-connectivity"></a>Test připojení

K otestování připojení, připojte se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřít **SQL Server Management Studio** a připojte se k názvu SQL serveru FCI.

>[!NOTE]
>Pokud třeba, můžete [stáhnout SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Omezení

Virtuální počítače Azure podporují Microsoft distribuované transakce koordinátor (MSDTC) 2019 Windows serveru s úložištěm na sdílené svazky clusteru (CSV) a [load balanceru úrovně standard](../../../load-balancer/load-balancer-standard-overview.md).

Na virtuálních počítačích Azure služby MSDTC nepodporuje ve Windows serveru 2016 a starší protože:

- Prostředků clusteru služby MSDTC nelze nakonfigurovat na používání sdíleného úložiště. S Windows serverem 2016 Pokud vytvoříte prostředek služby MSDTC, nezobrazí se žádné sdílené úložiště, které jsou k dispozici pro použití, i v případě, že je úložiště. Tento problém chyba byla opravena v systému Windows Server 2019.
- Nástroje pro vyrovnávání zatížení základní nezpracovává porty RPC.

## <a name="see-also"></a>Viz také

[Instalační program S2D pomocí vzdálené plochy (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyperkonvergované řešení s prostory úložiště s přímým přístupem](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Přímé prostor úložiště – přehled](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Podpora systému SQL Server pro S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)

---
title: SQL Server FCI – Azure Virtual Machines | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit instanci clusteru s podporou převzetí služeb při selhání SQL Server v Azure Virtual Machines.
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
ms.openlocfilehash: 92623377daa80efe08b260745fa1d9443366cb8a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300588"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurace SQL Server instance clusteru s podporou převzetí služeb při selhání v Azure Virtual Machines

Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server převzetí služeb při selhání (FCI) na virtuálních počítačích Azure v modelu Správce prostředků. V tomto řešení se používá [Windows Server 2016 Datacenter edition Prostory úložiště s přímým přístupem \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako softwarová virtuální síť SAN, která synchronizuje úložiště (datové disky) mezi uzly (virtuální počítače Azure) v clusteru Windows. S2D je novinkou ve Windows serveru 2016.

Následující diagram znázorňuje kompletní řešení na virtuálních počítačích Azure:

![Skupina dostupnosti](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Předchozí diagram znázorňuje:

- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání systému Windows. Když je virtuální počítač v clusteru s podporou převzetí služeb při selhání, nazývá se taky *uzel clusteru*nebo *uzly*.
- Každý virtuální počítač má dva nebo více datových disků.
- S2D synchronizuje data na datovém disku a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje sdílený svazek clusteru (CSV) do clusteru s podporou převzetí služeb při selhání.
- Role clusteru SQL Server FCI používá sdílený svazek clusteru pro datové jednotky.
- Nástroj pro vyrovnávání zatížení Azure, který bude obsahovat IP adresu pro SQL Server FCI.
- Skupina dostupnosti Azure obsahuje všechny prostředky.

   >[!NOTE]
   >Všechny prostředky Azure jsou v diagramu ve stejné skupině prostředků.

Podrobnosti o S2D najdete v tématu [Windows Server 2016 Datacenter edition Prostory úložiště s přímým přístupem \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D podporuje dva typy architektur – sblížené a nakonvergované na technologii Hyper-v. Architektura v tomto dokumentu je konvergovaná na základě technologie Hyper-v. Infrastruktura sblížená pomocí technologie Hyper-v umístí úložiště na stejné servery, které hostují Clusterovou aplikaci. V této architektuře je úložiště na každém uzlu SQL Server FCI.

## <a name="licensing-and-pricing"></a>Licencování a ceny

V Azure Virtual Machines můžete SQL Server licence pomocí PAYG (průběžné platby za použití) nebo Přineste vlastní licenci virtuálních počítačů (BYOL). Typ obrázku, který zvolíte, bude mít vliv na to, jak se vám budou účtovat poplatky.

Díky licencování PAYG je instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Server v Azure Virtual Machines zaúčtována za všechny uzly FCI, včetně pasivních uzlů. Další informace najdete v tématu [SQL Server Enterprise Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Zákazníci s smlouva Enterprise se Software Assurance mají právo používat jeden bezplatný pasivní uzel FCI pro každý aktivní uzel. Pokud chcete tuto výhodu využít v Azure, použijte image virtuálních počítačů BYOL a pak použijte stejnou licenci v aktivních i pasivních uzlech FCI. Další informace najdete v tématu [smlouva Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat PAYG a BYOL licencování pro SQL Server v Virtual Machines Azure, přečtěte si téma Začínáme [s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licenčních SQL Server najdete v tématu [ceny](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Ukázková šablona Azure

V Azure můžete vytvořit celé řešení ze šablony. Příklad šablony je k dispozici v [šablonách rychlého](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad)startu pro Azure na GitHubu. Tento příklad není navržený ani testován pro žádnou konkrétní úlohu. Můžete spustit šablonu a vytvořit SQL Server FCI s úložištěm S2D připojeným k vaší doméně. Můžete vyhodnotit šablonu a upravit ji pro vaše účely.

## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat, je potřeba, abyste věděli a několik věcí, které potřebujete.

### <a name="what-to-know"></a>Co potřebujete
Měli byste mít praktické znalosti následujících technologií:

- [Technologie clusterů Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Důležitým rozdílem je to, že na clusteru s podporou převzetí služeb při selhání hosta virtuálního počítače Azure IaaS doporučujeme jednu síťovou kartu na jeden server (uzel clusteru) a jednu podsíť. Sítě Azure má fyzickou redundanci, díky níž je zbytečné používat další síťové adaptéry a podsítě na hostovaném clusteru ve virtuálním počítači Azure IaaS. I když ověřovací zpráva clusteru vydá varování, že uzly jsou dosažitelné pouze v jedné síti, můžete toto varování bezpečně ignorovat ve všech hostovaných clusterech ve virtuálních počítačích Azure IaaS. 

Kromě toho byste měli mít obecné informace o těchto technologiích:

- [Řešení sblížené pomocí technologie Hyper-v Prostory úložiště s přímým přístupem ve Windows serveru 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Skupiny prostředků Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době se SQL Server instance clusterů s podporou převzetí služeb při selhání na virtuálních počítačích Azure podporují jenom s režimem [zjednodušené](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) správy [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Odinstalujte úplné rozšíření z virtuálních počítačů, které jsou součástí clusteru s podporou převzetí služeb při selhání, a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL v režimu @no__t 0. Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a Správa portálu. Po přeinstalaci agenta v režimu zjednodušené správy nebudou tyto funkce fungovat pro virtuální počítače SQL.

### <a name="what-to-have"></a>Co má

Než budete postupovat podle pokynů v tomto článku, měli byste už mít následující:

- Microsoft Azure předplatné.
- Doména Windows na virtuálních počítačích Azure.
- Účet s oprávněním k vytváření objektů na virtuálním počítači Azure.
- Virtuální síť Azure a podsíť s dostatečným adresním prostorem IP adres pro následující komponenty:
   - Oba virtuální počítače.
   - IP adresa clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- Služba DNS konfigurovaná na síti Azure odkazuje na řadiče domény.

V rámci těchto požadavků můžete pokračovat v vytváření clusteru s podporou převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého předplatného.

1. [Vytvořte skupinu dostupnosti Azure](../tutorial-availability-sets.md).

   Skupina dostupnosti seskupuje virtuální počítače napříč doménami selhání a aktualizačními doménami. V rámci skupiny dostupnosti se ujistěte, že vaše aplikace není ovlivněná jedním bodem selhání, jako je síťový přepínač nebo jednotka napájení stojanu serverů.

   Pokud jste skupinu prostředků pro virtuální počítače nevytvořili, udělejte to při vytváření sady dostupnosti Azure. Pokud k vytvoření skupiny dostupnosti používáte Azure Portal, proveďte následující kroky:

   - V Azure Portal otevřete Azure Marketplace kliknutím na **+** . Vyhledejte **skupinu dostupnosti**.
   - Klikněte na možnost **Skupina dostupnosti**.
   - Klikněte na **Vytvořit**.
   - V okně **vytvořit skupinu dostupnosti** nastavte následující hodnoty:
      - **Name (název**): název skupiny dostupnosti.
      - **Předplatné**: vaše předplatné Azure.
      - **Skupina prostředků**: Pokud chcete použít existující skupinu, klikněte na **použít existující** a vyberte skupinu z rozevíracího seznamu. V opačném případě vyberte **vytvořit novou** a zadejte název skupiny.
      - **Umístění**: Nastavte umístění, kde plánujete vytvořit virtuální počítače.
      - **Doména selhání**: použijte výchozí (3).
      - **Aktualizovat domény**: použijte výchozí (5).
   - Kliknutím na **vytvořit** vytvořte skupinu dostupnosti.

1. Vytvořte virtuální počítače ve skupině dostupnosti.

   Zřizování dvou SQL Server virtuálních počítačů ve skupině dostupnosti Azure. Pokyny najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umístit oba virtuální počítače:

   - Ve stejné skupině prostředků Azure, ve které je vaše skupina dostupnosti.
   - Ve stejné síti jako řadič domény.
   - V podsíti s dostatečným adresním prostorem IP adres pro oba virtuální počítače a všemi FCIs, které můžete nakonec použít v tomto clusteru.
   - V sadě dostupnosti Azure.   

      >[!IMPORTANT]
      >Po vytvoření virtuálního počítače nemůžete nastavit nebo změnit skupinu dostupnosti.

   Vyberte obrázek z Azure Marketplace. Můžete použít image na webu Marketplace, která zahrnuje Windows Server a SQL Server, nebo jenom Windows Server. Podrobnosti najdete v tématu [přehled SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)

   Oficiální SQL Server Image v galerii Azure zahrnují nainstalovanou instanci SQL Server a navíc SQL Server instalační software a požadovaný klíč.

   Vyberte správnou bitovou kopii podle toho, jak chcete platit SQL Server licenci:

   - **Licencování za použití**: cena za sekundu těchto imagí zahrnuje SQL Server licencování:
      - **SQL Server 2016 Enterprise v systému Windows Server Datacenter 2016**
      - **SQL Server 2016 standard v systému Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer v systému Windows Server Datacenter 2016**

   - **Používání vlastní licence (BYOL)**

      - **BYOL SQL Server 2016 Enterprise v systému Windows Server Datacenter 2016**
      - **BYOL SQL Server 2016 standard v systému Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Po vytvoření virtuálního počítače odeberte předem nainstalovanou samostatnou SQL Server instanci. Po nakonfigurování clusteru s podporou převzetí služeb při selhání a S2D budete pomocí předinstalovaného SQL Server média vytvářet SQL Server FCI.

   Alternativně můžete použít Azure Marketplace image jenom s operačním systémem. Vyberte bitovou kopii **Windows Server 2016 Datacenter** a po nakonfigurování clusteru s podporou převzetí služeb při selhání a S2D nainstalujte SQL Server FCI. Tato bitová kopie neobsahuje SQL Server instalačních médií. Instalační médium umístěte do umístění, kde můžete spustit instalaci SQL Server pro každý server.

1. Až Azure vytvoří vaše virtuální počítače, připojte se ke každému virtuálnímu počítači pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači s protokolem RDP, počítač se zeptá, jestli chcete, aby tento počítač mohl být zjistitelný v síti. Klikněte na **Ano**.

1. Pokud používáte jednu z imagí virtuálních počítačů založených na SQL Server, odeberte instanci SQL Server.

   - V části **programy a funkce**klikněte pravým tlačítkem na **Microsoft SQL Server 2016 (64 bitů)** a klikněte na **Odinstalovat nebo změnit**.
   - Klikněte na **Odebrat**.
   - Vyberte výchozí instanci.
   - Odeberte všechny funkce ve **službě databázového stroje**. Neodstraňujte **sdílené funkce**. Podívejte se na následující obrázek:

      ![Odebrat funkce](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klikněte na **Další**a potom na **Odebrat**.

1. <a name="ports"></a>Otevřete porty brány firewall.

   Na každém virtuálním počítači otevřete následující porty na bráně Windows Firewall.

   | Účel | Port TCP | Poznámky
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance SQL Server. Pokud jste použili image z Galerie, tento port se automaticky otevře.
   | Sonda stavu | 59999 | Libovolný otevřený port TCP. V pozdějším kroku nakonfigurujte [sondu stavu](#probe) nástroje pro vyrovnávání zatížení a cluster tak, aby používal tento port.  

1. Přidejte úložiště do virtuálního počítače. Podrobné informace najdete v tématu věnovaném [Přidání úložiště](../disks-types.md).

   Oba virtuální počítače potřebují aspoň dva datové disky.

   Připojit nezpracované disky – neformátované disky NTFS
      >[!NOTE]
      >Pokud připojíte disky formátované systémem souborů NTFS, můžete povolit pouze S2D bez kontroly způsobilosti disku.  

   Ke každému virtuálnímu počítači připojte minimálně dvě SSD Premium. Doporučujeme aspoň P30 (1 TB) disků.

   Nastavte ukládání hostitelů do mezipaměti **jen pro čtení**.

   Kapacita úložiště, kterou používáte v produkčním prostředí, závisí na vašich úlohách. Hodnoty popsané v tomto článku jsou k disukázce a testování.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nakonfigurovat cluster s podporou převzetí služeb při selhání.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Krok 2: konfigurace clusteru s podporou převzetí služeb při selhání Windows pomocí S2D

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání pomocí S2D. V tomto kroku provedete následující kroky:

1. Přidat funkci clusteringu s podporou převzetí služeb při selhání systému Windows
1. Ověřit cluster
1. Vytvoření clusteru s podporou převzetí služeb při selhání
1. Vytvoření určujícího cloudu
1. Přidat úložiště

### <a name="add-windows-failover-clustering-feature"></a>Přidat funkci clusteringu s podporou převzetí služeb při selhání systému Windows

1. Začněte tím, že se připojíte k prvnímu virtuálnímu počítači pomocí protokolu RDP pomocí účtu domény, který je členem místní skupiny Administrators, a má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro zbytek konfigurace.

1. [Přidejte funkci clusteringu s podporou převzetí služeb při selhání do každého virtuálního počítače](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Chcete-li nainstalovat funkci clusteringu s podporou převzetí služeb při selhání z uživatelského rozhraní, proveďte následující kroky na obou virtuálních počítačích.
   - V **Správce serveru**klikněte na **Spravovat**a pak klikněte na **Přidat role a funkce**.
   - V **Průvodci přidáním rolí a funkcí**klikněte na **Další** , dokud se nedostanete k **vybraným funkcím**.
   - V nabídce **Vybrat funkce**klikněte na **Clustering s podporou převzetí služeb při selhání**. Zahrňte všechny požadované funkce a nástroje pro správu. Klikněte na **Přidat funkce**.
   - Klikněte na tlačítko **Další** a potom kliknutím na tlačítko **Dokončit** instalaci funkcí nainstalujte.

   Pokud chcete nainstalovat funkci clusteringu s podporou převzetí služeb při selhání pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z těchto virtuálních počítačů.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Další kroky najdete v následujících krocích v části Krok 3 [řešení sblíženého s technologií Hyper-v prostory úložiště s přímým přístupem v systému Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Ověřit cluster

Tato příručka odkazuje na pokyny v části [ověřit cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, proveďte následující kroky z jednoho z těchto virtuálních počítačů.

1. V **Správce serveru**klikněte na **nástroje**a pak na **Správce clusteru s podporou převzetí služeb při selhání**.
1. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte na **Akce**a pak na **ověřit konfiguraci...** .
1. Klikněte na **Další**.
1. Na stránce **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V **možnostech testování**zvolte **Spustit pouze vybrané testy**. Klikněte na **Další**.
1. Do **výběru testů**Zahrňte všechny testy s výjimkou **úložiště**. Podívejte se na následující obrázek:

   ![Ověřit testy](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klikněte na **Další**.
1. Po **potvrzení**klikněte na **Další**.

**Průvodce ověřením konfigurace** spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z těchto virtuálních počítačů.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

Tato příručka obsahuje postup [Vytvoření clusteru s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2008-2016"></a>Windows Server 2008-2016

Následující PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro **Windows Server 2008-2016**. Aktualizujte skript pomocí názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Následující PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2019.  Další informace najdete v [clusteru s podporou převzetí služeb při selhání na blogu: objekt sítě s clustery](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Aktualizujte skript pomocí názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Vytvoření určujícího cloudu

Disk s kopií cloudu je nový typ určujícího disku kvora clusteru, který je uložený v Azure Storage Blob. Tím se odstraní nutnost samostatného virtuálního počítače, který hostuje sdílenou složku s kopií clusteru.

1. [Vytvořte určující Cloud pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů BLOB.

1. Uložte přístupové klíče a adresu URL kontejneru.

1. Nakonfigurujte určující disk kvora clusteru s podporou převzetí služeb při selhání Přečtěte si téma [Konfigurace určujícího disku kvora v uživatelském rozhraní](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) v uživatelském rozhraní.

### <a name="add-storage"></a>Přidat úložiště

Disky pro S2D musí být prázdné a nesmí obsahovat oddíly nebo jiná data. Chcete-li vyčistit disky, postupujte podle [kroků v této příručce](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Povolit prostory úložiště s přímým přístupem \(S2D @ no__t-2](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Následující prostředí PowerShell umožňuje prostory úložiště s přímým přístupem.  

   ```powershell
   Enable-ClusterS2D
   ```

   V **Správce clusteru s podporou převzetí služeb při selhání**se teď můžete podívat na fond úložiště.

1. [Vytvořte svazek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Jednou z funkcí S2D je to, že když ho povolíte, automaticky vytvoří fond úložiště. Nyní jste připraveni vytvořit svazek. Prostředí PowerShell rutiny `New-Volume` automatizuje proces vytváření svazků, včetně formátování, přidání do clusteru a vytvoření sdíleného svazku clusteru (CSV). Následující příklad vytvoří soubor CSV 800 gigabajt (GB).

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po dokončení tohoto příkazu se svazek 800 GB připevní jako prostředek clusteru. Svazek je na `C:\ClusterStorage\Volume1\`.

   Následující diagram znázorňuje sdílený svazek clusteru s S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: testování převzetí služeb při selhání clusteru se selháním

V Správce clusteru s podporou převzetí služeb při selhání ověřte, zda můžete přesunout prostředek úložiště do jiného uzlu clusteru. Pokud se ke clusteru s podporou převzetí služeb při selhání můžete připojit pomocí **Správce clusteru s podporou převzetí služeb při selhání** a přesunout úložiště z jednoho uzlu do druhého, budete připraveni nakonfigurovat FCI.

## <a name="step-4-create-sql-server-fci"></a>Krok 4: vytvoření SQL Server FCI

Po nakonfigurování clusteru s podporou převzetí služeb při selhání a všech součástí clusteru včetně úložiště můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby byly všechny základní prostředky clusteru v prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na `C:\SQLServer_<version number>_Full`. Klikněte na tlačítko **nastavit**.

1. V **instalačním centru SQL Server**klikněte na možnost **instalace**.

1. Klikněte na **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání** Podle pokynů v průvodci nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být v clusterovém úložišti. S S2D se nejedná o sdílený disk, ale přípojný bod na svazku na každém serveru. S2D synchronizuje svazek mezi oběma uzly. Svazek se prezentuje clusteru jako sdílený svazek clusteru. Pro datové adresáře použijte přípojný bod sdíleného svazku clusteru.

   ![Dataředitelé](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po dokončení průvodce Instalační program nainstaluje SQL Server FCI na první uzel.

1. Po úspěšné instalaci nainstaluje FCI do prvního uzlu, připojí se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**. Klikněte na možnost **instalace**.

1. Klikněte na **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte tento server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie s SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste tuto bitovou kopii nepoužili, nainstalujte nástroje SQL Server samostatně. Viz [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Krok 5: Vytvoření nástroje pro vyrovnávání zatížení Azure

Ve virtuálních počítačích Azure používají clustery Nástroj pro vyrovnávání zatížení k uchování IP adresy, která musí být na jednom uzlu clusteru. V tomto řešení má nástroj pro vyrovnávání zatížení uloženou IP adresu pro SQL Server FCI.

[Vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření nástroje pro vyrovnávání zatížení v Azure Portal

Vytvoření nástroje pro vyrovnávání zatížení:

1. V Azure Portal do skupiny prostředků použijte virtuální počítače.

1. Klikněte na tlačítko **+ Přidat**. Vyhledejte **Load Balancer**na webu Marketplace. Klikněte na **Load Balancer**.

1. Klikněte na **Vytvořit**.

1. Nakonfigurujte Nástroj pro vyrovnávání zatížení pomocí:

   - **Předplatné**: vaše předplatné Azure.
   - **Skupina prostředků**: použijte stejnou skupinu prostředků jako virtuální počítače.
   - **Název**: název, který identifikuje Nástroj pro vyrovnávání zatížení.
   - **Oblast**: použijte stejné umístění Azure jako virtuální počítače.
   - **Typ**: Nástroj pro vyrovnávání zatížení může být buď veřejný, nebo soukromý. Privátní Nástroj pro vyrovnávání zatížení je možné použít v rámci stejné virtuální sítě. Většina aplikací Azure může používat privátní Nástroj pro vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes Internet, použijte veřejný Nástroj pro vyrovnávání zatížení.
   - **SKU**: SKU pro váš nástroj pro vyrovnávání zatížení by měla být standard. 
   - **Virtual Network**: stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: přiřazení IP adresy musí být statické. 
   - **Privátní IP adresa**: stejná IP adresa, kterou jste přiřadili SQL Server síťovému prostředku clusteru FCI.
   Podívejte se na následující obrázek:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurace fondu back-endu nástroje pro vyrovnávání zatížení

1. Vraťte se do skupiny prostředků Azure s virtuálními počítači a Najděte nový nástroj pro vyrovnávání zatížení. Možná budete muset aktualizovat zobrazení skupiny prostředků. Klikněte na nástroj pro vyrovnávání zatížení.

1. Klikněte na **back-endové fondy** a kliknutím na **+ Přidat** přidejte back-end fond.

1. Přidružte back-end fond ke skupině dostupnosti, která obsahuje virtuální počítače.

1. V části **cílové konfigurace sítě IP**vyberte **virtuální počítač** a vyberte virtuální počítače, které se budou podílet jako uzly clusteru. Nezapomeňte zahrnout všechny virtuální počítače, které budou hostovat FCI. 

1. Kliknutím na tlačítko **OK** vytvořte fond back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurace sondy stavu nástroje pro vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení klikněte na **sondy stavu**.

1. Klikněte na tlačítko **+ Přidat**.

1. V okně **Přidat sondu stavu** <a name="probe"> </a>nastavte parametry sondy stavu:

   - **Name (název**): název pro sondu stavu.
   - **Protokol**: TCP.
   - **Port**: nastavte na port, který jste vytvořili v bráně firewall pro sondu stavu v [tomto kroku](#ports). V tomto článku příklad používá port TCP `59999`.
   - **Interval**: 5 sekund.
   - **Prahová hodnota špatného stavu**: 2 po sobě jdoucích selhání.

1. Klikněte na tlačítko OK.

### <a name="set-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. V okně nástroje pro vyrovnávání zatížení klikněte na **pravidla vyrovnávání zatížení**.

1. Klikněte na tlačítko **+ Přidat**.

1. Nastavte parametry pravidel vyrovnávání zatížení:

   - **Name (název**): název pro pravidla vyrovnávání zatížení.
   - **IP adresa front-endu**: použijte IP adresu pro prostředek sítě SQL Server FCI clusteru.
   - **Port**: sada pro port TCP SQL Server FCI. Výchozí port instance je 1433.
   - **Port back-endu**: Tato hodnota používá stejný port jako hodnota **portu** , když povolíte **plovoucí IP adresu (přímé vrácení serveru)** .
   - **Back-end fond**: použijte název back-end fondu, který jste nakonfigurovali dříve.
   - **Sonda stavu**: použijte test stavu, který jste nakonfigurovali dříve.
   - **Trvalost relace**: žádné.
   - **Časový limit nečinnosti (minuty)** : 4.
   - **Plovoucí IP adresa (přímá návrat ze serveru)** : povoleno

1. Klikněte na **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Krok 6: konfigurace clusteru pro test paměti

Nastavte parametr portu testu clusteru v prostředí PowerShell.

Pokud chcete nastavit parametr portu sondy clusteru, aktualizujte proměnné v následujícím skriptu pomocí hodnot z vašeho prostředí. Odebrat lomené závorky `<>` ze skriptu 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

V předchozím skriptu nastavte hodnoty pro vaše prostředí. Následující seznam popisuje tyto hodnoty:

   - `<Cluster Network Name>`: název clusteru převzetí služeb při selhání Windows serveru pro síť. V **Správce clusteru s podporou převzetí služeb při selhání** > **sítě**klikněte pravým tlačítkem myši na síť a klikněte na **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** . 

   - `<SQL Server FCI IP Address Resource Name>`: název prostředku IP adresy SQL Server FCI. V **Správce clusteru s podporou převzetí služeb při selhání** **role** >  v části role SQL Server FCI v části **název serveru**klikněte pravým tlačítkem na prostředek IP adresy a klikněte na **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** . 

   - `<ILBIP>`: IP adresa interního nástroje. Tato adresa je nakonfigurovaná v Azure Portal jako front-end adresa interního nástroje. To je taky SQL Server FCI IP adresa. Můžete ji najít v **Správce clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste našli `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: je port testu, který jste nakonfigurovali v testu stavu nástroje pro vyrovnávání zatížení. Nepoužívaný port TCP je platný. 

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být adresa všesměrového vysílání IP protokolu TCP: `255.255.255.255`.

Po nastavení sondy clusteru můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: testování převzetí služeb při selhání FCI

Testovací převzetí služeb při selhání pro FCI k ověření funkčnosti clusteru. Proveďte následující kroky:

1. Připojte se k jednomu z uzlů SQL Server FCI clusteru pomocí protokolu RDP.

1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Klikněte na **role**. Všimněte si, že uzel je vlastníkem role SQL Server FCI.

1. Klikněte pravým tlačítkem na roli SQL Server FCI.

1. Klikněte na **přesunout** a klikněte na **nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje role a prostředky, které přecházejí do režimu offline. Prostředky pak přesunete a přejdete do režimu online na druhém uzlu.

### <a name="test-connectivity"></a>Test připojení

Pokud chcete otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k názvu SQL Server FCI.

>[!NOTE]
>V případě potřeby si můžete [stáhnout SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Omezení

Azure Virtual Machines podporuje Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) na Windows serveru 2019 s úložištěm na sdílených svazcích clusteru (CSV) a [standardním nástrojem pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md).

Ve virtuálních počítačích Azure není služba MSDTC podporovaná na Windows serveru 2016 a starších verzích z těchto důvodů:

- Clusterový prostředek MSDTC nejde nakonfigurovat tak, aby používal sdílené úložiště. Pokud v systému Windows Server 2016 vytvoříte prostředek MSDTC, nezobrazí se žádné sdílené úložiště, které by bylo možné použít, i když je úložiště tam. Tento problém byl opravený v systému Windows Server 2019.
- Nástroj pro vyrovnávání zatížení Basic nezpracovává porty RPC.

## <a name="see-also"></a>Viz také

[Nastavení S2D pomocí vzdálené plochy (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Řešení sblížené pomocí technologie Hyper-v prostorech úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Přehled prostorů úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Podpora SQL Server pro S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)

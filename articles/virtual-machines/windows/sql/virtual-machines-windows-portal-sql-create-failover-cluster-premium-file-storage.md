---
title: SQL Server FCI s využitím sdílené složky Premium – Azure Virtual Machines
description: Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání pomocí sdílené složky Premium na Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 39f04005776f3b451ad7c64c76f9aa5d8c4a7768
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330096"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurace SQL Server instance clusteru s podporou převzetí služeb při selhání se službou Premium na Azure Virtual Machines

Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání (FCI) na virtuálních počítačích Azure pomocí [souborové sdílené složky Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Soubory úrovně Premium jsou sdílené složky s nepřetržitou latencí ve formátu SSD, které jsou plně podporované pro použití s instancí clusteru s podporou převzetí služeb při selhání pro SQL Server 2012 a novější v systému Windows Server 2012 a novějším. Prémiové sdílené složky poskytují větší flexibilitu, což vám umožní změnit velikost sdílené složky a škálovat ji bez výpadků. 


## <a name="before-you-begin"></a>Než začnete

Než budete pokračovat, je potřeba, abyste věděli a několik věcí, které potřebujete.

Měli byste mít praktické znalosti následujících technologií:

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Důležitým rozdílem je to, že na clusteru s podporou převzetí služeb při selhání virtuálního počítače Azure IaaS doporučujeme jednu síťovou kartu na jeden server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v clusteru hostů virtuálních počítačů Azure IaaS vyžaduje další síťové adaptéry a podsítě, které nejsou potřebné. I když sestava ověření clusteru vydá upozornění, že uzly jsou dosažitelné jenom v jedné síti, můžete toto upozornění bezpečně ignorovat na clusterech s podporou převzetí služeb při selhání virtuálních počítačů Azure IaaS. 

Kromě toho byste měli mít obecné informace o těchto technologiích:

- [Azure Premium – sdílení souborů](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Skupiny prostředků Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době se SQL Server instance clusterů s podporou převzetí služeb při selhání na virtuálních počítačích Azure podporují jenom s režimem [zjednodušené](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) správy [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Odinstalujte úplné rozšíření z virtuálních počítačů, které jsou součástí clusteru s podporou převzetí služeb při selhání, a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL v režimu @no__t 0. Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a Správa portálu. Po přeinstalaci agenta v režimu zjednodušené správy nebudou tyto funkce fungovat pro virtuální počítače SQL.

### <a name="workload-consideration"></a>Aspekt úloh

Soubory úrovně Premium poskytují IOPS a celou kapacitu, která bude vyhovovat potřebám řady úloh. Pro úlohy náročné na v/v je ale vhodné [SQL Server FCI s prostory úložiště s přímým přístupem](virtual-machines-windows-portal-sql-create-failover-cluster.md) na základě spravovaných disků Premium nebo extrémně-discích.  

Zkontrolujte aktivitu IOPS vašeho aktuálního prostředí a před zahájením nasazení nebo migrace ověřte, že soubory prémií budou poskytnout IOPS, které potřebujete. Použijte čítače disku sledování výkonu systému Windows a monitorovat celkový počet vstupně-výstupních operací za sekundu (přenosy disku/s) a propustnost (v bajtech disku/s) požadované pro soubory SQL Server dat, protokolů a dočasné databáze. Mnohé úlohy mají v/v vstupně-výstupní operace, takže je dobré kontrolovat během těžkých dob používání a poznamenat maximální IOPS a také průměrnou IOPS. Sdílené soubory úrovně Premium poskytují IOPS na základě velikosti sdílené složky. Prémiové soubory také poskytují bezplatné rozmístění, kde můžete zvýšit počet vstupně-výstupních operací na trojnásobek základní částky po dobu až jedné hodiny. 

Další informace o výkonu sdílené složky Premium najdete v tématu [úrovně výkonu sdílení souborů](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#file-share-performance-tiers). 

### <a name="licensing-and-pricing"></a>Licencování a ceny

V Azure Virtual Machines můžete SQL Server licence pomocí PAYG (průběžné platby) nebo Přineste vlastní licence virtuálních počítačů (BYOL). Typ obrázku, který zvolíte, bude mít vliv na to, jak se vám budou účtovat poplatky.

Díky licencování PAYG je instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Server v Azure Virtual Machines zaúčtována za všechny uzly FCI, včetně pasivních uzlů. Další informace najdete v tématu [SQL Server Enterprise Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Zákazníci s smlouva Enterprise se Software Assurance mají právo používat jeden bezplatný pasivní uzel FCI pro každý aktivní uzel. Pokud chcete tuto výhodu využít v Azure, použijte image virtuálních počítačů BYOL a pak použijte stejnou licenci v aktivních i pasivních uzlech FCI. Další informace najdete v tématu [smlouva Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat PAYG a BYOL licencování pro SQL Server v Virtual Machines Azure, přečtěte si téma Začínáme [s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licenčních SQL Server najdete v tématu [ceny](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="limitations"></a>Omezení

- FILESTREAM není podporován pro cluster s podporou převzetí služeb při selhání se sdílenou složkou Premium. Pokud chcete použít FILESTREAM, nasaďte cluster pomocí [prostory úložiště s přímým přístupem](virtual-machines-windows-portal-sql-create-failover-cluster.md). 

## <a name="prerequisites"></a>Předpoklady 

Než budete postupovat podle pokynů v tomto článku, měli byste už mít následující:

- Microsoft Azure předplatné.
- Doména Windows na virtuálních počítačích Azure.
- Účet s oprávněním k vytváření objektů na virtuálním počítači Azure.
- Virtuální síť Azure a podsíť s dostatečným adresním prostorem IP adres pro následující komponenty:
   - Oba virtuální počítače.
   - IP adresa clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- Služba DNS konfigurovaná na síti Azure odkazuje na řadiče domény.
- [Prémiová sdílená složka](../../../storage/files/storage-how-to-create-premium-fileshare.md) na základě kvóty úložiště vaší databáze pro vaše datové soubory. 
- Sdílená složka pro zálohy, která se liší od úrovně Premium, která se používá pro vaše datové soubory. Tato sdílená složka může být buď Standard, nebo Premium. 

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

   >[!IMPORTANT]
   > Po vytvoření virtuálního počítače odeberte předem nainstalovanou samostatnou SQL Server instanci. Po nakonfigurování clusteru s podporou převzetí služeb při selhání a sdílení souborů Premium jako úložiště použijete předinstalované SQL Server médium k vytvoření SQL Server FCI. 

   Alternativně můžete použít Azure Marketplace image jenom s operačním systémem. Vyberte bitovou kopii **Windows Server 2016 Datacenter** a nainstalujte SQL Server FCI po nakonfigurování clusteru s podporou převzetí služeb při selhání a sdílení souborů Premium jako úložiště. Tato bitová kopie neobsahuje SQL Server instalačních médií. Instalační médium umístěte do umístění, kde můžete spustit instalaci SQL Server pro každý server. 

1. Až Azure vytvoří vaše virtuální počítače, připojte se ke každému virtuálnímu počítači pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači s protokolem RDP, počítač se zeptá, jestli chcete, aby tento počítač mohl být zjistitelný v síti. Klikněte na **Ano**.

1. Pokud používáte jednu z imagí virtuálních počítačů založených na SQL Server, odeberte instanci SQL Server.

   - V části **programy a funkce**klikněte pravým tlačítkem na **Microsoft SQL Server 201_ (64 bitů)** a klikněte na **Odinstalovat nebo změnit**.
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
   | Sdílená složka | 445 | Port používaný službou Sdílení souborů. 

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nakonfigurovat prémiovou sdílenou složku.

## <a name="step-2-mount-premium-file-share"></a>Krok 2: připojení ke sdílené složce prémiových souborů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a pokračujte na svůj účet úložiště.
1. V části **Souborová služba** klikněte na **sdílené složky** a vyberte prémiovou sdílenou složku, kterou chcete použít pro své úložiště SQL. 
1. Výběrem **připojit** otevřete připojovací řetězec pro sdílenou složku. 
1. V rozevíracím seznamu vyberte písmeno jednotky, které chcete použít, a potom zkopírujte oba bloky kódu do poznámkového bloku.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="Kopírování příkazů PowerShellu z portálu pro připojení ke sdílené složce":::

1. Protokol RDP do virtuálního počítače s SQL Server pomocí účtu, který bude služba SQL Server FCI používat pro účet služby. 
1. Spusťte konzolu Command PowerShellu pro správu. 
1. Spusťte příkazy z portálu, který jste předtím uložili. 
1. Přejděte ke sdílené složce pomocí Průzkumníka souborů nebo dialogového okna **Spustit** (klávesa Windows + r) pomocí síťové cesty `\\storageaccountname.file.core.windows.net\filesharename`. Příklad: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Vytvořte alespoň jednu složku pro nově připojenou sdílenou složku, do které chcete umístit datové soubory SQL. 
1. Tento postup opakujte na každém virtuálním počítači s SQL Server, který se bude podílet na clusteru. 

  > [!IMPORTANT]
  > Zvažte použití samostatné sdílené složky pro záložní soubory k uložení IOPS a velikosti kapacity této sdílené složky pro data a soubor protokolu. Pro záložní soubory můžete použít buď prémiovou, nebo standardní souborovou sdílenou složku.

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Krok 3: konfigurace clusteru s podporou převzetí služeb při selhání pomocí sdílené složky 

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání. V tomto kroku provedete následující kroky:

1. Přidat funkci clusteringu s podporou převzetí služeb při selhání systému Windows
1. Ověřit cluster
1. Vytvoření clusteru s podporou převzetí služeb při selhání
1. Vytvoření určujícího cloudu


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

### <a name="validate-the-cluster"></a>Ověřit cluster

Tato příručka odkazuje na pokyny v části [ověřit cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, proveďte následující kroky z jednoho z těchto virtuálních počítačů.

1. V **Správce serveru**klikněte na **nástroje**a pak na **Správce clusteru s podporou převzetí služeb při selhání**.
1. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte na **Akce**a pak na **ověřit konfiguraci...** .
1. Klikněte na **Další**.
1. Na stránce **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V **možnostech testování**zvolte **Spustit pouze vybrané testy**. Klikněte na **Další**.
1. Do **výběru testů**Zahrňte všechny testy s výjimkou **úložiště** a **prostory úložiště s přímým přístupem**. Podívejte se na následující obrázek:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="Testy pro ověření clusteru":::

1. Klikněte na **Další**.
1. Po **potvrzení**klikněte na **Další**.

**Průvodce ověřením konfigurace** spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace správce PowerShellu na jednom z těchto virtuálních počítačů.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání


Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

Následující PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro **Windows Server 2012-2016**. Aktualizujte skript pomocí názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure:

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


## <a name="step-4-test-cluster-failover"></a>Krok 4: testování převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. V Správce clusteru s podporou převzetí služeb při selhání klikněte pravým tlačítkem na svůj cluster > **Další akce** > **přesunout základní prostředek clusteru** > **vyberte uzel** a vyberte druhý uzel clusteru. Přesuňte základní prostředek clusteru do každého uzlu clusteru a pak ho přesuňte zpátky do primárního uzlu. Pokud je možné úspěšně přesunout cluster do každého uzlu, budete připraveni nainstalovat SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do ostatních uzlů":::

## <a name="step-5-create-sql-server-fci"></a>Krok 5: vytvoření SQL Server FCI

Po nakonfigurování clusteru s podporou převzetí služeb při selhání můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby byly všechny základní prostředky clusteru v prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na `C:\SQLServer_<version number>_Full`. Klikněte na tlačítko **nastavit**.

1. V **instalačním centru SQL Server**klikněte na možnost **instalace**.

1. Klikněte na **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání** Podle pokynů v průvodci nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být ve sdílené souborové složce Premium. Zadejte úplnou cestu ke sdílené složce, ve formě `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Zobrazí se upozornění s oznámením, že jste jako datový adresář zadali souborový server. To se očekává. Ujistěte se, že stejný účet, na který jste zachovali sdílenou složku, je stejný účet, který používá služba SQL Server, aby se předešlo možným chybám. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="Použít sdílení souborů jako datové adresáře SQL":::

1. Po dokončení průvodce Instalační program nainstaluje SQL Server FCI na první uzel.

1. Po úspěšné instalaci nainstaluje FCI do prvního uzlu, připojí se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**. Klikněte na možnost **instalace**.

1. Klikněte na **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte tento server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie s SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste tuto bitovou kopii nepoužili, nainstalujte nástroje SQL Server samostatně. Viz [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Krok 6: Vytvoření nástroje pro vyrovnávání zatížení Azure

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

### <a name="set-load-balancing-rules"></a>Nastavit pravidla vyrovnávání zatížení

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

## <a name="step-7-configure-cluster-for-probe"></a>Krok 7: konfigurace clusteru pro test paměti

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

   - `<SQL Server FCI IP Address Resource Name>`: název prostředku IP adresy SQL Server FCI. V **Správce clusteru s podporou převzetí služeb při selhání** **rolích** >  v části role SQL Server FCI v části **název serveru**klikněte pravým tlačítkem na prostředek IP adresy a klikněte na **vlastnosti**. Správná hodnota je pod **názvem** na kartě **Obecné** . 

   - `<ILBIP>`: IP adresa interního nástroje. Tato adresa je nakonfigurovaná v Azure Portal jako front-end adresa interního nástroje. To je taky SQL Server FCI IP adresa. Můžete ji najít v **Správce clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste našli `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: je port testu, který jste nakonfigurovali v testu stavu nástroje pro vyrovnávání zatížení. Nepoužívaný port TCP je platný. 

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být adresa všesměrového vysílání IP protokolu TCP: `255.255.255.255`.

Po nastavení sondy clusteru můžete zobrazit všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Krok 8: testování převzetí služeb při selhání FCI

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

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

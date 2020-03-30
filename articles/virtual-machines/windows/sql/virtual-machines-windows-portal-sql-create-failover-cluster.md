---
title: SQL Server FCI – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak vytvořit instanci clusteru sql server převzetí služeb při selhání na virtuálních počítačích Azure.
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
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249799"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurace instance clusteru s podporou převzetí služeb při selhání serveru SQL Server na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit instanci clusteru sql server převzetí služeb při selhání (FCI) na virtuálních počítačích Azure v modelu Azure Resource Manager. Toto řešení používá [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) jako softwarovou virtuální san, která synchronizuje úložiště (datové disky) mezi uzly (virtuální počítače Azure) v clusteru Windows. Storage Spaces Direct byl v systému Windows Server 2016 nový.

Následující diagram znázorňuje kompletní řešení na virtuálních počítačích Azure:

![Kompletní řešení](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Tento diagram znázorňuje:

- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání windows serveru. Pokud je virtuální počítač v clusteru s podporou převzetí služeb při selhání, nazývá se také uzel nebo *uzel* *clusteru* .
- Každý virtuální počítač má dva nebo více datových disků.
- Storage Spaces Direct synchronizuje data na datových discích a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje clusteru sdíleného svazku (CSV) clusteru s podporou převzetí služeb při selhání.
- Role clusteru SQL Server FCI používá csv pro datové jednotky.
- Azure nástroje pro vyrovnávání zatížení pro uložení IP adresy pro SQL Server FCI.
- Azure dostupnost sada obsahuje všechny prostředky.

>[!NOTE]
>Všechny prostředky Azure v diagramu jsou ve stejné skupině prostředků.

Podrobnosti o službě Storage Spaces Direct naleznete v [tématu Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Storage Spaces Direct podporuje dva typy architektur: konvergované a hyperkonvergované. Architektura v tomto dokumentu je hyper-konvergovaná. Hyperkonvergovaná infrastruktura umístí úložiště na stejné servery, které jsou hostiteli clusterované aplikace. V této architektuře je úložiště na každém uzlu FCI SERVERU SQL Server.

## <a name="licensing-and-pricing"></a>Licencování a stanovení cen

Na virtuálních počítačích Azure můžete licencovat SQL Server pomocí bitových kopií virtuálních počítačů s průběžnými platbami (PAYG) nebo s vlastní licencí (BYOL). Typ obrázku, který zvolíte, ovlivňuje způsob účtování.

S průběžnými licencemi se instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Serveru na virtuálních počítačích Azure účtuje poplatky za všechny uzly FCI, včetně pasivních uzlů. Další informace naleznete v tématu [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Pokud máte smlouvu Enterprise With Software Assurance, můžete pro každý aktivní uzel použít jeden bezplatný pasivní uzel FCI. Chcete-li tuto výhodu využít v Azure, použijte image virtuálního počítače BYOL a použijte stejnou licenci na aktivních i pasivních uzlech FCI. Další informace naleznete v tématu [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat průběžně platby a licencování BYOL pro SQL Server na virtuálních počítačích Azure, přečtěte si informace [o tom, jak začít s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licencování serveru SQL Server naleznete v [tématu Pricing](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Příklad šablony Azure

Celé toto řešení můžete vytvořit v Azure ze šablony. Příklad šablony je k dispozici v [šablonách GitHub Azure Quickstart Templates](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Tento příklad není navržen ani testován pro konkrétní úlohy. Šablonu můžete spustit a vytvořit rozhraní SQL Server FCI s úložištěm Storage Spaces Direct připojeném k vaší doméně. Šablonu můžete vyhodnotit a upravit pro své účely.

## <a name="before-you-begin"></a>Než začnete

Existuje několik věcí, které potřebujete vědět a mít na místě, než začnete.

### <a name="what-to-know"></a>Co je třeba vědět
Měli byste mít provozní znalosti těchto technologií:

- [Technologie clusteru Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instance clusteru s podporou převzetí služeb při selhání serveru SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedna věc, kterou je třeba si uvědomit, je, že v clusteru azure IaaS virtuálního počítače s podporou převzetí služeb při selhání doporučujeme jednu síťovou konto na server (uzel clusteru) a jednu podsíť. Azure networking má fyzickou redundanci, díky čemuž jsou další síťové karty a podsítě v clusteru hosta virtuálního počítače Azure IaaS zbytečné. Sestava ověření clusteru vás upozorní, že uzly jsou dostupné pouze v jedné síti. Toto upozornění můžete ignorovat v clusterech s podporou převzetí služeb při selhání virtuálního počítače Azure IaaS.

Měli byste také mít obecné znalosti těchto technologií:

- [Hyperkonvergovaná řešení, která používají storage spaces direct v systému Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Skupiny prostředků Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době jsou podporovány instance clusteru SQL Server s podporou převzetí služeb při selhání na virtuálních počítačích Azure pouze s [režimem zjednodušené správy](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Chcete-li přejít z režimu úplného rozšíření na zjednodušený, odstraňte prostředek **virtuálního počítače SQL** pro odpovídající virtuální počítače a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL v zjednodušeném režimu. Při odstranění prostředku **virtuálního počítače SQL** pomocí portálu Azure **zrušte zaškrtnutí políčka vedle správného virtuálního počítače**. Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a pokročilá správa portálu. Tyto funkce nebudou fungovat pro virtuální aplikace SQL po přeinstalaci agenta v režimu zjednodušené správy.

### <a name="what-to-have"></a>Co mít

Před dokončením kroků v tomto článku byste již měli mít:

- Předplatné Microsoft Azure.
- Doména Windows na virtuálních počítačích Azure.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Virtuální síť Azure a podsíť s dostatkem adresního prostoru IP pro tyto součásti:
   - Oba virtuální počítače.
   - Adresa IP clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- DNS nakonfigurované v síti Azure, ukazující na řadiče domény.

S těmito požadavky na místě můžete začít vytvářet clusteru s podporou převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí předplatného.

1. [Vytvořte sadu dostupnosti Azure](../tutorial-availability-sets.md).

   Sada dostupnosti seskupuje virtuální počítače mezi doménami selhání a aktualizačními doménami. Zajišťuje, že vaše aplikace není ovlivněna jedinými body selhání, jako je síťový přepínač nebo napájecí jednotka racku serverů.

   Pokud jste nevytvořili skupinu prostředků pro vaše virtuální počítače, udělejte to při vytváření azure dostupnosti. Pokud k vytvoření sady dostupnosti používáte portál Azure, postupujte takto:

   1. Na webu Azure Portal vyberte **Vytvořit prostředek** a otevřete Azure Marketplace. Vyhledejte **sadu dostupnosti**.
   1. Vyberte **možnost Sada dostupnosti**.
   1. Vyberte **Vytvořit**.
   1. V části **Vytvořit sadu dostupnosti**zadejte tyto hodnoty:
      - **Název**: Název pro sadu dostupnosti.
      - **Předplatné:** Vaše předplatné Azure.
      - **Skupina prostředků**: Pokud chcete použít existující skupinu, klikněte na **Vybrat existující** a vyberte skupinu ze seznamu. V opačném případě vyberte **Vytvořit nový** a zadejte název skupiny.
      - **Umístění**: Nastavte umístění, kde chcete vytvořit virtuální počítače.
      - **Domény selhání**: Použijte výchozí nastavení (**3**).
      - **Aktualizovat domény**: Použít výchozí (**5**).
   1. Chcete-li vytvořit sadu dostupnosti, vyberte **vytvořit.**

1. Vytvořte virtuální počítače v sadě dostupnosti.

   Zřídit dva virtuální počítače SQL Serveru v azure dostupnosti. Pokyny najdete [v tématu Zřízení virtuálního počítače SQL Serveru na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Umístění obou virtuálních počítačů:

   - Ve stejné skupině prostředků Azure jako vaše sada dostupnosti.
   - Ve stejné síti jako řadič domény.
   - V podsíti, která má dostatek adresního prostoru IP pro virtuální počítače a všechny fcis, které můžete nakonec použít v clusteru.
   - V azure dostupnosti.

      >[!IMPORTANT]
      >Po vytvoření virtuálního počítače nelze nastavit ani změnit sadu dostupnosti.

   Vyberte si image z Azure Marketplace. Můžete použít bitovou kopii Azure Marketplace, která zahrnuje Windows Server a SQL Server, nebo použít bitku, která obsahuje pouze Windows Server. Podrobnosti najdete [v tématu Přehled SQL Serveru na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Oficiální image SERVERU SQL Server v Galerii Azure zahrnují nainstalovanou instanci SERVERU SQL Server, instalační software serveru SQL Server a požadovaný klíč.

   Vyberte správnou bitovou kopii podle toho, jak chcete zaplatit za licenci serveru SQL Server:

   - **Licencování za využití**. Náklady na jednotlivé obrázky zahrnují licencování serveru SQL Server:
      - **SQL Server 2016 Enterprise v datovém centru Windows Serveru 2016**
      - **SQL Server 2016 Standard v datovém centru Windows Serveru 2016**
      - **Vývojář SQL Serveru 2016 v datovém centru Windows Serveru 2016**

   - **Přineste si vlastní licenci (BYOL)**

      - **(BYOL) SQL Server 2016 Enterprise v datovém centru Windows Serveru 2016**
      - **(BYOL) SQL Server 2016 Standard v datovém centru Windows Serveru 2016**

   >[!IMPORTANT]
   >Po vytvoření virtuálního počítače odeberte předinstanci samostatného serveru SQL Server. Po nastavení clusteru s podporou převzetí služeb při selhání a rozhraní Storage Spaces Direct použijete předinstalované médium SERVERU SQL Server k vytvoření rozhraní SQL Server FCI.

   Případně můžete použít image Azure Marketplace, které obsahují pouze operační systém. Zvolte bitovou kopii **datového centra Windows Serveru 2016** a po nastavení clusteru s podporou převzetí služeb při selhání a rozhraní Storage Spaces Direct nainstalujte sql server FCI. Tato bitová kopie neobsahuje instalační médium serveru SQL Server. Umístěte instalační médium serveru SQL Server do umístění, kde jej můžete spustit pro každý server.

1. Po Azure vytvoří vaše virtuální počítače, připojte se ke každému z nich pomocí RDP.

   Při prvním připojení k virtuálnímu počítači pomocí rdp, výzva se zobrazí výzva, pokud chcete povolit počítač, aby bylo zjistitelné v síti. Vyberte **ano**.

1. Pokud používáte jednu z bitových kopií virtuálních strojů založených na serveru SQL Server, odeberte instanci serveru SQL Server.

   1. V **části Programy a funkce**klepněte pravým tlačítkem myši na **položku Microsoft SQL Server 2016 (64bit)** a vyberte možnost **Odinstalovat nebo změnit**.
   1. Vyberte **Odebrat**.
   1. Vyberte výchozí instanci.
   1. Odebrat všechny funkce v části **Služby databázového stroje**. Neodstraňujte **sdílené funkce**. Uvidíte něco jako následující snímek obrazovky:

      ![Vybrat funkce](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Vyberte **Další**a pak vyberte **Odebrat**.

1. <a name="ports"></a>Otevřete porty brány firewall.

   Na každém virtuálním počítači otevřete tyto porty v bráně Windows Firewall:

   | Účel | Port TCP | Poznámky
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance serveru SQL Server. Pokud jste použili obrázek z galerie, tento port se automaticky otevře.
   | Sonda stavu | 59999 | Jakýkoli otevřený port TCP. V pozdějším kroku nakonfigurujte [sondu stavu](#probe) vykladače zatížení a cluster pro použití tohoto portu.  

1. Přidejte úložiště do virtuálního počítače. Podrobné informace naleznete v tématu [přidání úložiště](../disks-types.md).

   Oba virtuální počítače potřebují alespoň dva datové disky.

   Připojte nezpracované disky, nikoli disky ve formátu NTFS.
      >[!NOTE]
      >Pokud připojíte disky ve formátu NTFS, můžete povolit funkci Storage Spaces Direct pouze bez kontroly způsobilosti disku.  

   Připojte ke každému virtuálnímu virtuálnímu účtu minimálně dvě prémiová ssd disponála. Doporučujeme alespoň Disky P30 (1 TB).

   Nastavte ukládání hostitelů do mezipaměti jen **pro čtení**.

   Kapacita úložiště, kterou používáte v produkčním prostředí, závisí na vaší pracovní zátěži. Hodnoty popsané v tomto článku jsou pro demonstraci a testování.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nastavit cluster s podporou převzetí služeb při selhání.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Krok 2: Konfigurace clusteru s podporou převzetí služeb při selhání systému Windows Server s přímým prostorem úložiště

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání pomocí technologie Storage Spaces Direct. V tomto kroku provedete tyto dílčí kroky:

1. Přidejte funkci Clustering s podporou převzetí služeb při selhání systému Windows Server.
1. Ověřte cluster.
1. Vytvořte cluster s podporou převzetí služeb při selhání.
1. Vytvořte cloud důkaz.
1. Přidejte úložiště.

### <a name="add-windows-server-failover-clustering"></a>Přidání clusteringu s podporou převzetí služeb při selhání systému Windows Server

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP pomocí účtu domény, který je členem místních správců a který má oprávnění k vytváření objektů ve službě Active Directory. Tento účet použijte pro zbytek konfigurace.

1. [Přidejte clustering s podporou převzetí služeb při selhání do každého virtuálního počítače](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Chcete-li nainstalovat clustering s podporou převzetí služeb při selhání z uhlavního počítače, postupujte na obou virtuálních počítačích takto:
   1. Ve **Správci serveru**vyberte **Spravovat**a pak **vyberte Přidat role a funkce**.
   1. V **Průvodci přidáním rolí a funkcí**vyberte **možnost Další,** dokud se nedostanete k **možnosti Vybrat funkce**.
   1. V **povolte Select Features**možnost **Clustering s podporou převzetí služeb při selhání**. Zahrňte všechny požadované funkce a nástroje pro správu. Vyberte **Přidat funkce**.
   1. Vyberte **Další**a pak vyberte **Dokončit,** chcete-li nainstalovat funkce.

   Chcete-li nainstalovat clustering s podporou převzetí služeb při selhání pomocí prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Další informace o dalších krocích naleznete v pokynech v kroku 3 [hyperkonvergovaného řešení pomocí technologie Storage Spaces Direct v systému Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Ověření clusteru

Ověřte cluster v unovém prostředí nebo pomocí prostředí PowerShell.

Chcete-li ověřit cluster pomocí ui, postupujte takto na jednom z virtuálních počítačů:

1. V části **Správce serveru**vyberte **Nástroje**a potom vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **akce**a pak vyberte **Ověřit konfiguraci**.
1. Vyberte **další**.
1. V **části Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **možnost Spustit pouze testy, které vyberu**. Vyberte **další**.
1. V části **Test Selection**vyberte všechny testy s výjimkou **úložiště**, jak je znázorněno zde:

   ![Výběr ověřovacích testů clusteru](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Vyberte **další**.
1. V části **Potvrzení**vyberte **Další**.

Průvodce ověřením konfigurace spustí ověřovací testy.

Chcete-li ověřit cluster pomocí prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

Chcete-li vytvořit cluster s podporou převzetí služeb při selhání, potřebujete:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- Adresa IP clusteru s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 až Windows Server 2016

Následující skript prostředí PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro systém Windows Server 2008 až Windows Server 2016. Aktualizujte skript názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresou z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Následující skript prostředí PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2019. Další informace naleznete v tématu [Cluster s podporou převzetí služeb při selhání: Síťový objekt clusteru](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aktualizujte skript názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresou z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Vytvoření cloudového svědka

Cloud Witness je nový typ clusteru kvora důkaz, který je uložen v objektu blob úložiště Azure. Tím se odebere potřeba samostatného virtuálního virtuálního aplikace, který hostuje sdílenou složku s kopií clusteru.

1. [Vytvořte cloudovou důkaz pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů blob.

1. Uložte přístupové klíče a adresu URL kontejneru.

1. Nakonfigurujte důkaz toho, že je kvor clusteru s podporou převzetí služeb při selhání. Viz [Konfigurace svědka kvora v uživatelském rozhraní](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Přidání úložiště

Disky pro prostory úložiště direct musí být prázdné. Nemohou obsahovat oddíly nebo jiná data. Chcete-li disky vyčistit, postupujte podle [pokynů v této příručce](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Povolte přímé prostory úložiště](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Následující skript prostředí PowerShell umožňuje storage spaces direct:  

   ```powershell
   Enable-ClusterS2D
   ```

   Ve **Správci clusterů s podporou převzetí služeb při selhání**se nyní zobrazí fond úložiště.

1. [Vytvořte svazek](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Storage Spaces Direct automaticky vytvoří fond úložiště, když ho povolíte. Nyní jste připraveni vytvořit svazek. Rutina prostředí PowerShell `New-Volume` automatizuje proces vytváření svazku. Tento proces zahrnuje formátování, přidání svazku do clusteru a vytvoření sdíleného svazku clusteru (CSV). Tento příklad vytvoří CSV o velikosti 800 gb(GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Po dokončení tohoto příkazu je svazek 800 GB připojen jako prostředek clusteru. Hlasitost je `C:\ClusterStorage\Volume1\`na .

   Tento snímek obrazovky ukazuje sdílený svazek clusteru s přímým prostorem úložiště:

   ![Sdílený svazek clusteru](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Krok 3: Otestujte převzetí služeb při selhání clusteru s podporou převzetí služeb při selhání

Ve **Správci clusterů s podporou převzetí služeb při selhání**ověřte, zda můžete prostředek úložiště přesunout do jiného uzlu clusteru. Pokud se můžete připojit ke clusteru s podporou převzetí služeb při selhání pomocí **Správce clusteru s podporou převzetí služeb při selhání** a přesunout úložiště z jednoho uzlu do druhého, můžete nakonfigurovat FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Krok 4: Vytvoření SQL Server FCI

Po konfiguraci clusteru s podporou převzetí služeb při selhání a všech součástí clusteru, včetně úložiště, můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí programu RDP.

1. Ve **Správci clusterů s podporou převzetí služeb při selhání**zkontrolujte, zda jsou všechny prostředky základního clusteru na prvním virtuálním počítači. V případě potřeby přesuňte všechny prostředky do tohoto virtuálního počítače.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z ibi Azure `C:\SQLServer_<version number>_Full`Marketplace, médium se nachází na adrese . Vyberte **instalační program**.

1. V **Centru instalace serveru SQL Server**vyberte možnost **Instalace**.

1. Vyberte **novou instalaci clusteru s podporou převzetí služeb při selhání serveru SQL Server**. Podle pokynů průvodce nainstalujte rozhraní FCI serveru SQL Server.

   Datové adresáře FCI musí být v clusterovaném úložišti. S storage Spaces Direct se nejedná o sdílený disk, ale o přípojný bod na svazek na každém serveru. Storage Spaces Direct synchronizuje svazek mezi oběma uzly. Svazek je clusteru prezentován jako sdílený svazek clusteru. Pro datové adresáře použijte přípojné místo CSV.

   ![Datové adresáře](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Po dokončení pokynů v průvodci instalační program nainstaluje sql server FCI do prvního uzlu.

1. Po instalaci fci na první uzel se připojte k druhému uzlu pomocí programu RDP.

1. Otevřete **Centrum instalace serveru SQL Server**. Vyberte **možnost Instalace**.

1. Vyberte **Přidat uzel do clusteru**s podporou převzetí služeb při selhání serveru SQL Server . Podle pokynů průvodce nainstalujte sql server a přidejte server do fci.

   >[!NOTE]
   >Pokud jste použili image galerie Azure Marketplace, která obsahuje SQL Server, nástroje SQL Server byly součástí image. Pokud jste nepoužili jednu z těchto bitových kopií, nainstalujte nástroje serveru SQL Server samostatně. Viz [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Krok 5: Vytvoření azure balancer

Ve virtuálních počítačích Azure clustery používají nástroj pro vyrovnávání zatížení k uložení IP adresy, která musí být v jednom uzlu clusteru najednou. V tomto řešení je v nástroji pro vyrovnávání zatížení adresa IP pro SQL Server FCI.

Další informace najdete [v tématu Vytvoření a konfigurace azure balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření správce zatížení na webu Azure Portal

Vytvoření vykladače zatížení:

1. Na webu Azure Portal přejděte do skupiny prostředků, která obsahuje virtuální počítače.

1. Vyberte **Přidat**. Vyhledejte azure marketplace pro **vyrovnávání zatížení**. Vyberte **možnost Vyrovnávání zatížení**.

1. Vyberte **Vytvořit**.

1. Nakonfigurujte provyrovnávání zatížení pomocí:

   - **Předplatné:** Vaše předplatné Azure.
   - **Skupina prostředků**: Skupina prostředků, která obsahuje vaše virtuální počítače.
   - **Název**: Název, který identifikuje balancer.
   - **Oblast:** Umístění Azure, které obsahuje vaše virtuální počítače.
   - **Typ**: Veřejné nebo soukromé. K soukromému nástroju pro vyrovnávání zatížení lze přistupovat z virtuální sítě. Většina aplikací Azure můžete použít soukromý vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes internet, použijte veřejný vyrovnávání zatížení.
   - **Skladová položka**: standardní.
   - **Virtuální síť**: Stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: Statické. 
   - **Privátní IP adresa**: Adresa IP, kterou jste přiřadili síťovému prostředku clusteru SQL Server FCI.

 Následující snímek obrazovky ukazuje vytvořit rozhraní **pro vyrovnávání zatížení:**

   ![Nastavení provyrovnávání zatížení](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurace back-endového fondu vykladače zatížení

1. Vraťte se do skupiny prostředků Azure, která obsahuje virtuální počítače a vyhledejte nový nástroj pro vyrovnávání zatížení. Je možné, že bude nutné aktualizovat zobrazení ve skupině prostředků. Vyberte vykladač zatížení.

1. Vyberte **Back-endové fondy**a pak vyberte **Přidat**.

1. Přidružte back-endový fond k množině dostupnosti, která obsahuje virtuální servery.

1. V části **Cílové konfigurace IP adres sítě**vyberte VIRTUAL **MACHINE** a zvolte virtuální počítače, které se budou účastnit jako uzly clusteru. Nezapomeňte zahrnout všechny virtuální počítače, které budou hostovat FCI.

1. Chcete-li vytvořit back-endový fond, vyberte **ok.**

### <a name="configure-a-load-balancer-health-probe"></a>Nakonfigurovat sondu stavu nástroje pro vyrovnávání zatížení

1. Na noži pro vyrovnávání zatížení vyberte **možnost Zdravotní sondy**.

1. Vyberte **Přidat**.

1. V okně **Přidat sondu** <a name="probe"> </a>stavu nastavte parametry sondy stavu.

   - **Název**: Název sondy stavu.
   - **Protokol**: TCP.
   - **Port**: Nastavte port, který jste vytvořili v bráně firewall pro sondu stavu v [tomto kroku](#ports). V tomto článku příklad používá `59999`port TCP .
   - **Interval**: 5 sekund.
   - **Práh není v pořádku:** 2 po sobě jdoucí selhání.

1. Vyberte **OK**.

### <a name="set-load-balancing-rules"></a>Nastavení pravidel vyrovnávání zatížení

1. Na noži pro vyrovnávání zatížení vyberte **pravidla vyrovnávání zatížení**.

1. Vyberte **Přidat**.

1. Nastavte parametry pravidla vyrovnávání zatížení:

   - **Název**: Název pravidel vyrovnávání zatížení.
   - **Front-endOVÁ ADRESA IP**: Adresa IP síťového prostředku clusteru SQL Server FCI.
   - **Port**: Port SQL Server FCI TCP port. Výchozí port instance je 1433.
   - **Back-endový port**: Používá stejný port jako hodnota **portu,** když povolíte **plovoucí IP (přímý návrat serveru).**
   - **Back-endový fond**: Název back-endového fondu, který jste nakonfigurovali dříve.
   - **Sonda stavu**: Sonda stavu, kterou jste nakonfigurovali dříve.
   - **Trvalosti relace**: Žádná.
   - **Časový limit nečinnosti (minuty)**: 4.
   - **Plovoucí IP (přímý návrat serveru)**: Povoleno.

1. Vyberte **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Krok 6: Konfigurace clusteru pro sondu

Nastavte parametr portu sondy clusteru v prostředí PowerShell.

Chcete-li nastavit parametr portu sondy clusteru, aktualizujte proměnné v následujícím skriptu hodnotami z vašeho prostředí. Odstraňte úhlové`<` závorky ( a `>`) ze skriptu.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Následující seznam popisuje hodnoty, které je třeba aktualizovat:

   - `<Cluster Network Name>`: Název clusteru s podporou převzetí služeb při selhání systému Windows Server pro síť. V > **sítích** **Správce clusteru s podporou převzetí služeb při selhání**klepněte pravým tlačítkem myši na síť a vyberte příkaz **Vlastnosti**. Správná hodnota je v části **Název** na kartě **Obecné.**

   - `<SQL Server FCI IP Address Resource Name>`: Název prostředku IP adresy SQL Server FCI. V > **rolích** **správce clusteru s podporou převzetí služeb při selhání**klikněte v části SQL Server FCI v části Název **serveru**pravým tlačítkem myši na prostředek adresy IP a vyberte **příkaz Vlastnosti**. Správná hodnota je v části **Název** na kartě **Obecné.** 

   - `<ILBIP>`: IP adresa ILB. Tato adresa se na webu Azure portal nakonfiguruje jako front-endovou adresu ILB. Toto je také IP adresa FCI serveru SQL Server. Najdete ji ve **Správci clusteru s podporou převzetí služeb při selhání** na stejné stránce vlastností, kde jste našli . `<SQL Server FCI IP Address Resource Name>`  

   - `<nnnnn>`: Port sondy, který jste nakonfigurovali v sondě stavu vyrovnávání zatížení. Všechny nepoužívané porty TCP jsou platné.

>[!IMPORTANT]
>Maska podsítě pro parametr clusteru musí být `255.255.255.255`adresa všesměrového vysílání PROTOKOLU TCP: .

Po nastavení sondy clusteru uvidíte všechny parametry clusteru v prostředí PowerShell. Spusťte tento skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Krok 7: Testování převzetí služeb při selhání FCI

Otestujte převzetí služeb při selhání fci k ověření funkčnosti clusteru. Proveďte následující kroky:

1. Připojte se k jednomu z uzlů clusteru SQL Server FCI pomocí rdp.

1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, který uzel vlastní roli FCI serveru SQL Server.

1. Klikněte pravým tlačítkem myši na roli FCI serveru SQL Server.

1. Vyberte **Přesunout**a pak vyberte **Nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejdou do offline. Prostředky pak přesunout a přejít do režimu online na jiném uzlu.

### <a name="test-connectivity"></a>Test připojení

Chcete-li otestovat připojení, přihlaste se k jinému virtuálnímu počítači ve stejné virtuální síti. Otevřete **SQL Server Management Studio** a připojte se k názvu FCI serveru SQL Server.

>[!NOTE]
>V případě potřeby si můžete [stáhnout sql server management studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Omezení

Virtuální počítače Azure podporují Microsoft Distributed Transaction Coordinator (MSDTC) na Windows Serveru 2019 s úložištěm na clusterovaných sdílených svazcích (CSV) a [standardním nástrojem pro vyrovnávání zatížení](../../../load-balancer/load-balancer-standard-overview.md).

Na virtuálních počítačích Azure není koordinátor MSDTC na Windows Serveru 2016 nebo starším, protože:

- Prostředek msdtc clusterovaného nelze nakonfigurovat tak, aby používal sdílené úložiště. Pokud v systému Windows Server 2016 vytvoříte prostředek msdtc, nezobrazí se žádné sdílené úložiště, které je k dispozici pro použití, a to ani v případě, že je úložiště k dispozici. Tento problém byl vyřešen v systému Windows Server 2019.
- Základní nástroj pro vyrovnávání zatížení nezpracovává porty RPC.

## <a name="see-also"></a>Viz také

[Nastavení prostorů úložiště direct se vzdálenou plochou (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyperkonvergované řešení s rozhraním Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Přehled Prostorů úložiště s přímým přístupem](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Podpora serveru SQL Server pro rozhraní Storage Spaces Direct](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)

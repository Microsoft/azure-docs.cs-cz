---
title: SQL Server FCI s využitím sdílené složky Premium – Azure Virtual Machines
description: Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání pomocí sdílené složky Premium na virtuálních počítačích Azure.
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
ms.openlocfilehash: 10a3c2bf421c7182dca00dfcbf7c3f559141a745
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084081"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurace SQL Server instance clusteru s podporou převzetí služeb při selhání se službou Premium na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit instanci clusteru SQL Server s podporou převzetí služeb při selhání (FCI) na virtuálních počítačích Azure pomocí [sdílené složky Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Soubory úrovně Premium mají trvalé sdílené složky s nízkou latencí, které jsou plně podporované pro použití s instancemi clusteru s podporou převzetí služeb při selhání pro SQL Server 2012 nebo novější v systému Windows Server 2012 nebo novějším. Prémiové sdílené složky poskytují větší flexibilitu, což vám umožní změnit velikost sdílené složky a škálovat ji bez výpadků.


## <a name="before-you-begin"></a>Než začnete

K dispozici je několik věcí, které potřebujete znát a které jsou ještě před začátkem.

Měli byste mít provozní znalosti těchto technologií:

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedním z věcí, o které je potřeba vědět, je to, že na clusteru s podporou převzetí služeb při selhání virtuálních počítačů Azure IaaS doporučujeme jednu síťovou kartu na server (uzel clusteru) a jednu podsíť. Sítě Azure mají fyzickou redundanci, která v clusteru hostů virtuálních počítačů Azure IaaS vyžaduje další síťové adaptéry a podsítě, které nejsou potřebné. Sestava ověření clusteru vás upozorní, že uzly jsou dosažitelné jenom v jedné síti. Toto upozornění můžete ignorovat u clusterů s podporou převzetí služeb při selhání virtuálních počítačů Azure IaaS.

Měli byste mít také obecné porozumění těmto technologiím:

- [Azure Premium – sdílení souborů](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Skupiny prostředků Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době se SQL Server instance clusterů s podporou převzetí služeb při selhání na virtuálních počítačích Azure podporují jenom s režimem [zjednodušené](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) správy [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Pokud chcete změnit režim úplného rozšíření na zjednodušený režim, odstraňte prostředek **virtuálního počítače SQL** pro odpovídající virtuální počítače a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL ve [zjednodušeném](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) režimu. Když odstraníte prostředek **virtuálního počítače SQL** pomocí Azure Portal, zrušte zaškrtnutí políčka u správného virtuálního počítače.
>
> Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a Správa portálu pro pokročilé. Tyto funkce nebudou fungovat pro SQL Server virtuální počítače po přeinstalaci agenta v režimu [zjednodušené](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) správy.

Soubory úrovně Premium poskytují IOPS a celou kapacitu, která bude vyhovovat potřebám řady úloh. Pro úlohy náročné na v/v zvažte [SQL Server instance clusterů s podporou převzetí služeb při selhání pomocí prostory úložiště s přímým přístupem](virtual-machines-windows-portal-sql-create-failover-cluster.md)na základě spravovaných disků Premium nebo Ultra disks.  

Zkontrolujte aktivitu IOPS vašeho prostředí a ověřte, že sdílené složky Premium budou před zahájením nasazení nebo migrace vyžadovat, aby počet IOPS za sekundu poskytoval. Pomocí čítačů disku nástroje sledování výkonu systému Windows monitorujte celkový počet vstupně-výstupních operací za sekundu a propustnost (za sekundu bajtů disku/s), které se vyžadují pro soubory SQL Server dat, protokolů a dočasné databáze.

Řada úloh má v/v vstupně-výstupní operace, takže je dobré kontrolovat během těžkých období využití a poznamenat si maximální počet vstupně-výstupních operací a průměrných vstupně-výstupních operací. Soubory úrovně Premium poskytují IOPS na základě velikosti sdílené složky. Prémiové sdílené složky také poskytují bezplatné rozpínání, které vám umožní navýšit nárůst počtu vstupně-výstupních operací na trojnásobek základní částky po dobu až jedné hodiny.

Další informace o výkonu sdílené složky Premium najdete v tématu [úrovně výkonu sdílení souborů](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers).

### <a name="licensing-and-pricing"></a>Licencování a ceny

Na virtuálních počítačích Azure můžete licencovat SQL Server pomocí imagí virtuálních počítačů s průběžnými platbami (PAYG) nebo s vlastními licencemi (BYOL). Typ obrázku, který zvolíte, bude mít vliv na to, jak se vám bude účtovat.

Díky licencování s průběžnými platbami se instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Server na virtuálních počítačích Azure za všechny uzly FCI, včetně pasivních uzlů. Další informace najdete v tématu [SQL Server Enterprise Virtual Machines ceny](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Pokud máte smlouva Enterprise se Software Assurance, můžete pro každý aktivní uzel použít jeden bezplatný pasivní uzel FCI. Pokud chcete tuto výhodu využít v Azure, použijte image virtuálních počítačů BYOL a stejnou licenci používejte v aktivních i pasivních uzlech FCI. Další informace najdete v tématu [smlouva Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat BYOL s průběžnými platbami a licencováním pro SQL Server na virtuálních počítačích Azure, přečtěte si téma Začínáme s virtuálními počítači [SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licenčních SQL Server najdete v tématu [ceny](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>FileStream

FILESTREAM není podporován pro cluster s podporou převzetí služeb při selhání se sdílenou složkou Premium. Pokud chcete použít FILESTREAM, nasaďte cluster pomocí [prostory úložiště s přímým přístupem](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste už měli mít:

- Microsoft Azure předplatné.
- Doména Windows na virtuálních počítačích Azure.
- Účet, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Virtuální síť Azure a podsíť s dostatkem adresního prostoru IP adres pro tyto součásti:
   - Dva virtuální počítače.
   - IP adresa clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- Služba DNS konfigurovaná na síti Azure odkazuje na řadiče domény.
- [Prémiová sdílená složka](../../../storage/files/storage-how-to-create-premium-fileshare.md) na základě kvóty úložiště vaší databáze pro vaše datové soubory.
- Sdílená složka pro zálohy, která se liší od sdílené složky Premium používané pro vaše datové soubory. Tato sdílená složka může být buď Standard, nebo Premium.

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
   - V podsíti, která má dostatečný adresní prostor IP adres pro virtuální počítače a všechny FCIs, které můžete nakonec použít v clusteru.
   - V sadě dostupnosti Azure.

      >[!IMPORTANT]
      >Po vytvoření virtuálního počítače už skupinu dostupnosti nemůžete nastavit ani změnit.

   Vyberte obrázek z Azure Marketplace. Můžete použít Azure Marketplace image, která zahrnuje Windows Server a SQL Server, nebo použít jednu z nich, která obsahuje jenom Windows Server. Podrobnosti najdete v tématu [přehled SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   Oficiální SQL Server Image v galerii Azure zahrnují nainstalovanou instanci SQL Server, SQL Server instalační software a požadovaný klíč.

   >[!IMPORTANT]
   > Po vytvoření virtuálního počítače odeberte předem nainstalovanou samostatnou SQL Server instanci. Po nastavení clusteru s podporou převzetí služeb při selhání a sdílené složky Premium jako úložiště použijete předem nainstalovanou SQL Server médium k vytvoření SQL Server FCI.

   Alternativně můžete použít Azure Marketplace image, které obsahují jenom operační systém. Vyberte bitovou kopii **Windows Server 2016 Datacenter** a nainstalujte SQL Server FCI po nastavení clusteru s podporou převzetí služeb při selhání a sdílení souborů Premium jako úložiště. Tato image neobsahuje instalační médium SQL Server. Instalační médium SQL Server umístit do umístění, kde ho můžete spustit pro každý server.

1. Až Azure vytvoří vaše virtuální počítače, připojí se ke každému z nich pomocí protokolu RDP.

   Když se poprvé připojíte k virtuálnímu počítači pomocí protokolu RDP, zobrazí se výzva, jestli chcete, aby byl počítač v síti zjistitelný. Vyberte **Ano**.

1. Pokud používáte jednu z imagí virtuálních počítačů založených na SQL Server, odeberte instanci SQL Server.

   1. V části **programy a funkce**klikněte pravým tlačítkem na **Microsoft SQL Server 201_ (64 bitů)** a vyberte **Odinstalovat nebo změnit**.
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
   | Sdílená složka | 445 | Port používaný službou Sdílení souborů.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nakonfigurovat prémiovou sdílenou složku.

## <a name="step-2-mount-the-premium-file-share"></a>Krok 2: připojení sdílené složky prémiových souborů

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a pokračujte na svůj účet úložiště.
1. V části **Souborová služba** klikněte na **sdílené složky** a vyberte prémiovou sdílenou složku, kterou chcete použít pro své úložiště SQL.
1. Výběrem **připojit** otevřete připojovací řetězec pro sdílenou složku.
1. V rozevíracím seznamu vyberte písmeno jednotky, které chcete použít, a potom zkopírujte oba bloky kódu do poznámkového bloku.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Kopírování příkazů PowerShellu z portálu pro připojení ke sdílené složce":::

1. Pomocí protokolu RDP se připojte k SQL Server virtuálnímu počítači pomocí účtu, který bude služba SQL Server FCI používat pro účet služby.
1. Otevřete konzolu příkazového řádku PowerShellu pro správu.
1. Spusťte příkazy, které jste předtím uložili při práci na portálu.
1. Do sdílené složky se dostanete pomocí Průzkumníka souborů nebo dialogového okna **Spustit** (klávesa s logem Windows + r). Použijte síťovou cestu `\\storageaccountname.file.core.windows.net\filesharename`. Například `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`.

1. Vytvořte alespoň jednu složku pro nově připojenou sdílenou složku, do které chcete umístit datové soubory SQL.
1. Tento postup opakujte na každém virtuálním počítači s SQL Server, který se bude podílet na clusteru.

  > [!IMPORTANT]
  > Zvažte použití samostatné sdílené složky pro záložní soubory k uložení IOPS a kapacity prostoru této sdílené složky pro data a soubory protokolů. Pro záložní soubory můžete použít buď prémiovou, nebo standardní souborovou sdílenou složku.

## <a name="step-3-configure-the-failover-cluster-with-the-file-share"></a>Krok 3: konfigurace clusteru s podporou převzetí služeb při selhání se sdílenou složkou

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání. V tomto kroku provedete následující kroky:

1. Přidejte funkci Clustering s podporou převzetí služeb při selhání Windows serveru.
1. Ověřte cluster.
1. Vytvořte cluster s podporou převzetí služeb při selhání.
1. Vytvořte disk s kopií cloudu.


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

### <a name="validate-the-cluster"></a>Ověřit cluster

Ověřte cluster v uživatelském rozhraní nebo pomocí PowerShellu.

Pokud chcete cluster ověřit pomocí uživatelského rozhraní, proveďte následující kroky na jednom z těchto virtuálních počítačů:

1. V části **Správce serveru**vyberte **nástroje**a pak vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **Akce**a pak vyberte **ověřit konfiguraci**.
1. Vyberte **Next** (Další).
1. V části **Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **Spustit pouze vybrané testy**. Vyberte **Next** (Další).
1. V části **Výběr testu**vyberte všechny testy s výjimkou **úložiště** a **prostory úložiště s přímým přístupem**, jak je znázorněno zde:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Výběr testů pro ověření clusteru":::

1. Vyberte **Next** (Další).
1. V části **potvrzení**vyberte **Další**.

**Průvodce ověřením konfigurace** spustí ověřovací testy.

Pokud chcete cluster ověřit pomocí PowerShellu, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

Pokud chcete vytvořit cluster s podporou převzetí služeb při selhání, budete potřebovat:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- IP adresa pro cluster s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 s Windows serverem 2016

Následující skript PowerShellu vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2012 přes Windows Server 2016. Aktualizujte skript s použitím názvů uzlů (názvy virtuálních počítačů) a dostupné IP adresy z virtuální sítě Azure.

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


## <a name="step-4-test-cluster-failover"></a>Krok 4: testování převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. V **Správce clusteru s podporou převzetí služeb při selhání**klikněte pravým tlačítkem na svůj cluster a vyberte **Další akce** > **přesunout základní prostředek clusteru** > **vyberte uzel**a pak vyberte druhý uzel clusteru. Přesuňte základní prostředek clusteru do každého uzlu clusteru a pak ho přesuňte zpátky do primárního uzlu. Pokud můžete cluster úspěšně přesunout do každého uzlu, budete připraveni nainstalovat SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do ostatních uzlů":::

## <a name="step-5-create-the-sql-server-fci"></a>Krok 5: vytvoření FCI SQL Server

Po dokončení konfigurace clusteru s podporou převzetí služeb při selhání můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí protokolu RDP.

1. V **Správce clusteru s podporou převzetí služeb při selhání**zajistěte, aby všechny základní prostředky clusteru byly na prvním virtuálním počítači. Pokud potřebujete, přesuňte všechny prostředky na tento virtuální počítač.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z Azure Marketplace imagí, médium se nachází na `C:\SQLServer_<version number>_Full`. Vyberte **Nastavení**.

1. V **instalačním centru SQL Server**vyberte možnost **instalace**.

1. Vyberte **nový SQL Server instalace clusteru s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server FCI.

   Datové adresáře FCI musí být ve sdílené souborové složce Premium. Zadejte úplnou cestu ke sdílené složce v tomto formátu: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Zobrazí se upozornění s oznámením, že jste jako datový adresář zadali souborový server. Toto upozornění je očekávané. Ujistěte se, že účet, pomocí kterého sdílená složka trvala, je stejný účet, který služba SQL Server používá, aby se předešlo možným chybám.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Použít sdílení souborů jako datové adresáře SQL":::

1. Po dokončení kroků v průvodci instalační program nainstaluje SQL Server FCI na první uzel.

1. Až instalační program nainstaluje FCI na první uzel, připojte se k druhému uzlu pomocí protokolu RDP.

1. Otevřete **Centrum instalace SQL Server**. Vyberte možnost **instalace**.

1. Vyberte **přidat uzel do clusteru SQL Server s podporou převzetí služeb při selhání**. Podle pokynů v průvodci nainstalujte SQL Server a přidejte server do FCI.

   >[!NOTE]
   >Pokud jste použili Azure Marketplace image galerie s SQL Server, SQL Server nástroje byly součástí bitové kopie. Pokud jste některou z těchto imagí nepoužili, nainstalujte nástroje SQL Server samostatně. Viz [stáhnout SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Krok 6: Vytvoření nástroje pro vyrovnávání zatížení Azure

Ve virtuálních počítačích Azure používají clustery Nástroj pro vyrovnávání zatížení k uchování IP adresy, která musí být na jednom uzlu clusteru. V tomto řešení má nástroj pro vyrovnávání zatížení uloženou IP adresu pro SQL Server FCI.

Další informace najdete v tématu [Vytvoření a konfigurace nástroje pro vyrovnávání zatížení Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření nástroje pro vyrovnávání zatížení v Azure Portal

Vytvoření nástroje pro vyrovnávání zatížení:

1. V Azure Portal přejdete do skupiny prostředků, která obsahuje virtuální počítače.

1. Vyberte **Přidat**. Vyhledejte **Load Balancer**Azure Marketplace. Vyberte **Load Balancer**.

1. Vyberte **Vytvořit**.

1. Nástroj pro vyrovnávání zatížení nastavte pomocí následujících hodnot:

   - **Předplatné**: vaše předplatné Azure.
   - **Skupina prostředků**: Skupina prostředků, která obsahuje vaše virtuální počítače.
   - **Název**: název, který identifikuje Nástroj pro vyrovnávání zatížení.
   - **Oblast**: umístění Azure, které obsahuje vaše virtuální počítače.
   - **Zadejte**: buď Public, nebo Private. K privátnímu nástroji pro vyrovnávání zatížení se dá v rámci virtuální sítě dostat. Většina aplikací Azure může používat privátní Nástroj pro vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes Internet, použijte veřejný Nástroj pro vyrovnávání zatížení.
   - **SKU**: Standard.
   - **Virtuální síť**: stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: statické. 
   - **Privátní IP adresa**: IP adresa, kterou jste přiřadili SQL Server síťovému prostředku clusteru FCI.

   Následující obrázek ukazuje uživatelské rozhraní **pro vytvoření nástroje pro vyrovnávání zatížení** :

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

1. V okně **Přidat sondu stavu** <a name="probe"> </a>nastavte následující parametry sondy stavu.

   - **Name (název**): název pro sondu stavu.
   - **Protokol**: TCP.
   - **Port**: port, který jste vytvořili v bráně firewall pro sondu stavu v [tomto kroku](#ports). V tomto článku příklad používá port TCP `59999`.
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Krok 7: konfigurace clusteru pro test

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

## <a name="step-8-test-fci-failover"></a>Krok 8: testování převzetí služeb při selhání FCI

Testovací převzetí služeb při selhání pro FCI k ověření funkčnosti clusteru. Proveďte následující kroky:

1. Připojte se k jednomu z SQL Server uzlů clusteru FCI pomocí protokolu RDP.

1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, že uzel je vlastníkem role SQL Server FCI.

1. Klikněte pravým tlačítkem na roli SQL Server FCI.

1. Vyberte **přesunout**a pak vyberte **nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejít do režimu offline. Prostředky se pak přesunou a vrátí zpátky do režimu online v druhém uzlu.

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

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

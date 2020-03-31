---
title: SQL Server FCI s prémiovým sdílením souborů – virtuální počítače Azure
description: Tento článek vysvětluje, jak vytvořit instanci clusteru sql server převzetí služeb při selhání pomocí sdílené složky premium na virtuálních počítačích Azure.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303218"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurace instance clusteru s podporou převzetí služeb při selhání serveru SQL Server s prémiovou sdílenou složkou na virtuálních počítačích Azure

Tento článek vysvětluje, jak vytvořit instanci clusteru sql server převzetí služeb při selhání (FCI) na virtuálních počítačích Azure pomocí [sdílené složky premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Sdílené složky Premium jsou sdílené složky s podporou SSD a konzistentní s nízkou latencí, které jsou plně podporovány pro použití s instancemi clusteru s podporou převzetí služeb při selhání pro SQL Server 2012 nebo novější v systému Windows Server 2012 nebo novějším. Prémiové sdílené složky poskytují větší flexibilitu, což umožňuje měnit velikost a škálovat sdílenou složku bez prostojů.


## <a name="before-you-begin"></a>Než začnete

Existuje několik věcí, které potřebujete vědět a mít na místě, než začnete.

Měli byste mít provozní znalosti těchto technologií:

- [Technologie clusteru Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instance clusteru s podporou převzetí služeb při selhání serveru SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Jedna věc, kterou je třeba si uvědomit, je, že v clusteru s podporou převzetí služeb při selhání virtuálního počítače Azure IaaS doporučujeme jednu síť ovou síť na server (uzel clusteru) a jednu podsíť. Azure networking má fyzickou redundanci, díky které další síťové karty a podsítě zbytečné v clusteru hosta virtuálního počítače Azure IaaS. Sestava ověření clusteru vás upozorní, že uzly jsou dostupné pouze v jedné síti. Toto upozornění můžete ignorovat v clusterech s podporou převzetí služeb při selhání virtuálního počítače Azure IaaS.

Měli byste také mít obecné znalosti těchto technologií:

- [Sdílená složka Azure Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Skupiny prostředků Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> V současné době jsou podporovány instance clusteru SQL Server s podporou převzetí služeb při selhání na virtuálních počítačích Azure pouze s [režimem zjednodušené správy](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) [rozšíření agenta SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md). Chcete-li přejít z režimu úplného rozšíření na zjednodušený, odstraňte prostředek **virtuálního počítače SQL** pro odpovídající virtuální počítače a pak je zaregistrujte u poskytovatele prostředků virtuálního počítače SQL v zjednodušeném režimu. Při odstranění prostředku **virtuálního počítače SQL** pomocí portálu Azure **zrušte zaškrtnutí políčka vedle správného virtuálního počítače**. Úplné rozšíření podporuje funkce, jako je automatické zálohování, opravy a pokročilá správa portálu. Tyto funkce nebudou fungovat pro virtuální aplikace SQL po přeinstalaci agenta v režimu zjednodušené správy.

Sdílené složky Premium poskytují viops a navšech kapacitách, které budou splňovat potřeby mnoha úloh. U úloh náročných na iO zvažte [instance clusteru sql server s podporou převzetí služeb při selhání s rozhraním Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md)na základě spravovaných disků premium nebo ultra disků.  

Zkontrolujte aktivitu VOPS ve vašem prostředí a ověřte, zda sdílené složky premium budou poskytovat viopy, které potřebujete před zahájením nasazení nebo migrace. Pomocí diskových čítačů Sledování výkonu systému Windows můžete sledovat celkový počet vstupně-sad (Diskové přenosy za sekundu) a propustnost (Počet bajtů disku za sekundu) požadovaný chod souborů SQL Server Data, Log a Temp DB.

Mnoho úloh má prasknutí vi, takže je vhodné zkontrolovat během období náročné ho použití a poznamenejte si maximální vipony a průměrné viOPS. Sdílené složky Premium poskytují viops na základě velikosti sdílené složky. Prémiové sdílené složky také poskytují bezplatné roztržení, které vám umožní roztrhnout vaše IO, abyste ztrojnásobili základní částku až na jednu hodinu.

Další informace o výkonu sdílené složky premium najdete v [tématu Úrovně výkonu sdílení souborů](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licencování a stanovení cen

Na virtuálních počítačích Azure můžete licencovat SQL Server pomocí bitových kopií virtuálních počítačů s průběžnými platbami (PAYG) nebo s vlastní licencí (BYOL). Typ obrázku, který zvolíte, ovlivňuje způsob účtování.

S průběžnými licencemi se instance clusteru s podporou převzetí služeb při selhání (FCI) SQL Serveru na virtuálních počítačích Azure účtuje poplatky za všechny uzly FCI, včetně pasivních uzlů. Další informace naleznete v tématu [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Pokud máte smlouvu Enterprise With Software Assurance, můžete pro každý aktivní uzel použít jeden bezplatný pasivní uzel FCI. Chcete-li tuto výhodu využít v Azure, použijte image virtuálního počítače BYOL a použijte stejnou licenci na aktivních i pasivních uzlech FCI. Další informace naleznete v tématu [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Pokud chcete porovnat průběžně platby a licencování BYOL pro SQL Server na virtuálních počítačích Azure, přečtěte si informace [o tom, jak začít s virtuálními počítači SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Úplné informace o licencování serveru SQL Server naleznete v [tématu Pricing](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>FileStream

Filestream není podporován pro cluster s podporou převzetí služeb při selhání s prémiovou sdílenou složkou. Chcete-li použít filestream, nasaďte cluster pomocí [storage spaces direct](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Požadavky

Před dokončením kroků v tomto článku byste již měli mít:

- Předplatné Microsoft Azure.
- Doména Windows na virtuálních počítačích Azure.
- Uživatelský účet domény, který má oprávnění k vytváření objektů na virtuálních počítačích Azure i ve službě Active Directory.
- Uživatelský účet domény pro spuštění služby SQL Server, se kterým se můžete přihlásit k virtuálnímu počítači při připojování sdílené složky.  
- Virtuální síť Azure a podsíť s dostatkem adresního prostoru IP pro tyto součásti:
   - Dva virtuální počítače.
   - Adresa IP clusteru s podporou převzetí služeb při selhání.
   - IP adresa pro každý FCI.
- DNS nakonfigurované v síti Azure, ukazující na řadiče domény.
- [Sdílená složka premium,](../../../storage/files/storage-how-to-create-premium-fileshare.md) která má být použita jako clusterovaná jednotka na základě kvóty úložiště databáze pro datové soubory.
- Pokud jste na Windows Server 2012 R2 a starší, budete potřebovat jinou sdílenou složku, která se použije jako důkaz ní sdílení souborů, protože cloudové svědky jsou podporovány pro Windows 2016 a novější. Můžete použít jinou sdílenou složku Azure, nebo můžete použít sdílenou složku na samostatném virtuálním počítači. Pokud budete používat jinou sdílenou složku Azure, můžete ji připojit stejným procesem jako pro sdílenou složku premium použitou pro clusterovožději jednotku. 

S těmito požadavky na místě můžete začít vytvářet clusteru s podporou převzetí služeb při selhání. Prvním krokem je vytvoření virtuálních počítačů.

## <a name="step-1-create-the-virtual-machines"></a>Krok 1: Vytvoření virtuálních počítačů

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí předplatného.

1. [Vytvořte sadu dostupnosti Azure](../tutorial-availability-sets.md).

   Sada dostupnosti seskupuje virtuální počítače mezi doménami selhání a aktualizačními doménami. Zajišťuje, že vaše aplikace není ovlivněna jedním bodem selhání, jako je síťový přepínač nebo napájecí jednotka racku serverů.

   Pokud jste nevytvořili skupinu prostředků pro vaše virtuální počítače, udělejte to při vytváření azure dostupnosti. Pokud k vytvoření sady dostupnosti používáte portál Azure, postupujte takto:

   1. Na webu Azure Portal vyberte **Vytvořit prostředek** pro otevření Azure Marketplace. Vyhledejte **sadu dostupnosti**.
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

   >[!IMPORTANT]
   > Po vytvoření virtuálního počítače odeberte předinstanci samostatného serveru SQL Server. Předinstalované médium serveru SQL Server použijete k vytvoření fci serveru SQL Server po nastavení clusteru s podporou převzetí služeb při selhání a sdílené složky premium jako úložiště.

   Případně můžete použít image Azure Marketplace, které obsahují pouze operační systém. Zvolte bitovou kopii **datového centra Windows Serveru 2016** a nainstalujte SQL Server FCI po nastavení clusteru s podporou převzetí služeb při selhání a sdílené složky premium jako úložiště. Tato bitová kopie neobsahuje instalační médium serveru SQL Server. Umístěte instalační médium serveru SQL Server do umístění, kde jej můžete spustit pro každý server.

1. Po Azure vytvoří vaše virtuální počítače, připojte se ke každému z nich pomocí RDP.

   Při prvním připojení k virtuálnímu počítači pomocí rdp, výzva se zobrazí výzva, pokud chcete povolit počítač, aby bylo zjistitelné v síti. Vyberte **ano**.

1. Pokud používáte jednu z bitových kopií virtuálních strojů založených na serveru SQL Server, odeberte instanci serveru SQL Server.

   1. V **části Programy a funkce**klepněte pravým tlačítkem myši na **položku Microsoft SQL Server 201_ (64bit)** a vyberte možnost **Odinstalovat nebo změnit**.
   1. Vyberte **Odebrat**.
   1. Vyberte výchozí instanci.
   1. Odebrat všechny funkce v části **Služby databázového stroje**. Neodstraňujte **sdílené funkce**. Uvidíte něco jako následující snímek obrazovky:

        ![Vybrat funkce](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Vyberte **Další**a pak vyberte **Odebrat**.

1. <span id="ports"> </span> Otevřete porty brány firewall.  

   Na každém virtuálním počítači otevřete tyto porty v bráně Windows Firewall:

   | Účel | Port TCP | Poznámky
   | ------ | ------ | ------
   | SQL Server | 1433 | Normální port pro výchozí instance serveru SQL Server. Pokud jste použili obrázek z galerie, tento port se automaticky otevře.
   | Sonda stavu | 59999 | Jakýkoli otevřený port TCP. V pozdějším kroku nakonfigurujte [sondu stavu](#probe) vykladače zatížení a cluster pro použití tohoto portu.
   | Sdílená složka | 445 | Port používaný službou sdílení souborů.

1. [Přidejte virtuální počítače do již existující domény](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Po vytvoření a konfiguraci virtuálních počítačů můžete nakonfigurovat sdílenou složku premium.

## <a name="step-2-mount-the-premium-file-share"></a>Krok 2: Připojení sdílené složky premium

1. Přihlaste se k [portálu Azure a](https://portal.azure.com) přejděte na svůj účet úložiště.
1. Přejděte na **Sdílené složky** v části **Souborová služba** a vyberte sdílenou složku premium, kterou chcete použít pro úložiště SQL.
1. Výběrem **možnosti Připojit** se zvede připojovací řetězec pro sdílenou složku.
1. V rozevíracím seznamu vyberte písmeno jednotky, které chcete použít, a zkopírujte oba bloky kódu do poznámkového bloku.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Kopírování obou příkazů Prostředí PowerShell z portálu pro připojení sdílené složky":::

1. Pomocí rdp pro připojení k virtuálnímu počítači SQL Server s účtem, který sql server FCI bude používat pro účet služby.
1. Otevřete konzolu příkazů prostředí PowerShell pro správu.
1. Spusťte příkazy, které jste uložili dříve při práci na portálu.
1. Přejděte na sdílenou složku pomocí Průzkumníka souborů nebo dialogového okna **Spustit** (klávesa s logem Windows + r). Použijte síťovou `\\storageaccountname.file.core.windows.net\filesharename`cestu . Například `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`.

1. Vytvořte alespoň jednu složku v nově připojené sdílené složce souborů a umístěte do ní datové soubory SQL.
1. Opakujte tyto kroky na každém virtuálním počítači SQL Server, který se bude účastnit clusteru.

  > [!IMPORTANT]
  > - Zvažte použití samostatné sdílené složky pro záložní soubory k uložení videa VOPS a kapacity místa této sdílené složky pro datové soubory a soubory protokolu. Pro záložní soubory můžete použít prémiovou nebo standardní sdílenou složku.
  > - Pokud používáte systém Windows 2012 R2 a starší, připevněte sdílenou složku, kterou budete používat jako důkaz sdílení souborů, postupujte stejným způsobem. 

## <a name="step-3-configure-the-failover-cluster"></a>Krok 3: Konfigurace clusteru s podporou převzetí služeb při selhání

Dalším krokem je konfigurace clusteru s podporou převzetí služeb při selhání. V tomto kroku provedete následující dílčí kroky:

1. Přidejte funkci Clustering s podporou převzetí služeb při selhání systému Windows Server.
1. Ověřte cluster.
1. Vytvořte cluster s podporou převzetí služeb při selhání.
1. Vytvořte cloud ovou (pro Windows Server 2016 a novější) nebo důkaz ní sdílení souborů (pro Windows Server 2012 R2 a starší).


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

### <a name="validate-the-cluster"></a>Ověření clusteru

Ověřte cluster v unovém prostředí nebo pomocí prostředí PowerShell.

Chcete-li ověřit cluster pomocí ui, postupujte takto na jednom z virtuálních počítačů:

1. V části **Správce serveru**vyberte **Nástroje**a potom vyberte **Správce clusteru s podporou převzetí služeb při selhání**.
1. V části **Správce clusteru s podporou převzetí služeb při selhání**vyberte **akce**a pak vyberte **Ověřit konfiguraci**.
1. Vyberte **další**.
1. V **části Vybrat servery nebo cluster**zadejte názvy obou virtuálních počítačů.
1. V části **Možnosti testování**vyberte **možnost Spustit pouze testy, které vyberu**. Vyberte **další**.
1. V části **Test Selection**vyberte všechny testy s výjimkou **úložiště** a **prostory úložiště Direct**, jak je znázorněno zde:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Výběr ověřovacích testů clusteru":::

1. Vyberte **další**.
1. V části **Potvrzení**vyberte **Další**.

**Průvodce ověřením konfigurace** spustí ověřovací testy.

Chcete-li ověřit cluster pomocí prostředí PowerShell, spusťte následující skript z relace prostředí PowerShell správce na jednom z virtuálních počítačů:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Po ověření clusteru vytvořte cluster s podporou převzetí služeb při selhání.

### <a name="create-the-failover-cluster"></a>Vytvoření clusteru s podporou převzetí služeb při selhání

Chcete-li vytvořit cluster s podporou převzetí služeb při selhání, potřebujete:
- Názvy virtuálních počítačů, které se stanou uzly clusteru.
- Název clusteru s podporou převzetí služeb při selhání
- Adresa IP clusteru s podporou převzetí služeb při selhání. Můžete použít IP adresu, která se nepoužívá ve stejné virtuální síti Azure a podsíti jako uzly clusteru.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 až Windows Server 2016

Následující skript prostředí PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2012 až Windows Server 2016. Aktualizujte skript názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresou z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Následující skript prostředí PowerShell vytvoří cluster s podporou převzetí služeb při selhání pro Windows Server 2019. Další informace naleznete v tématu [Cluster s podporou převzetí služeb při selhání: Síťový objekt clusteru](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Aktualizujte skript názvy uzlů (názvy virtuálních počítačů) a dostupnou IP adresou z virtuální sítě Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Vytvoření cloudového svědka (Win 2016 +)

Pokud jste na Windows Serveru 2016 a vyšší, budete muset vytvořit Cloud Witness. Cloud Witness je nový typ clusteru kvora důkaz, který je uložen v objektu blob úložiště Azure. Tím se odebere potřeba samostatného virtuálního virtuálního aplikace, který hostuje sdílenou složku s kopií clusteru, nebo použití samostatné sdílené složky.

1. [Vytvořte cloudovou důkaz pro cluster s podporou převzetí služeb při selhání](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Vytvořte kontejner objektů blob.

1. Uložte přístupové klíče a adresu URL kontejneru.

### <a name="configure-quorum"></a>Konfigurace kvora 

Pro Windows Server 2016 a vyšší, nakonfigurujte cluster tak, aby používal cloud ovou důkaz, kterou jste právě vytvořili. Postupujte podle všech kroků [Konfigurace svědka kvora v uživatelském rozhraní](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

U systému Windows Server 2012 R2 a starší postupujte stejným způsobem v [části Konfigurace důkaz kvora v uživatelském rozhraní,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) ale na stránce **Vybrat kvora** vyberte možnost **Konfigurovat sdílenou složku.** Zadejte sdílenou složku, kterou jste přidělili jako důkaz ní, ať už jste ji nakonfigurovali na samostatném virtuálním počítači nebo připojili z Azure. 


## <a name="step-4-test-cluster-failover"></a>Krok 4: Testování převzetí služeb při selhání clusteru

Otestujte převzetí služeb při selhání clusteru. Ve **Správci clusteru s podporou převzetí služeb při selhání**klepněte pravým tlačítkem myši na cluster a vyberte možnost Další **akce** > **Přesunout základní prostředek** > clusteru**Vyberte uzel**a vyberte druhý uzel clusteru. Přesuňte prostředek základního clusteru do všech uzlů clusteru a potom jej přesuňte zpět do primárního uzlu. Pokud můžete úspěšně přesunout cluster u každého uzlu, jste připraveni k instalaci SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testování převzetí služeb při selhání clusteru přesunutím základního prostředku do jiných uzlů":::

## <a name="step-5-create-the-sql-server-fci"></a>Krok 5: Vytvoření SQL Server FCI

Po konfiguraci clusteru s podporou převzetí služeb při selhání můžete vytvořit SQL Server FCI.

1. Připojte se k prvnímu virtuálnímu počítači pomocí programu RDP.

1. Ve **Správci clusterů s podporou převzetí služeb při selhání**zkontrolujte, zda jsou všechny prostředky základního clusteru na prvním virtuálním počítači. Pokud potřebujete, přesuňte všechny prostředky do tohoto virtuálního počítače.

1. Vyhledejte instalační médium. Pokud virtuální počítač používá jednu z ibi Azure `C:\SQLServer_<version number>_Full`Marketplace, médium se nachází na adrese . Vyberte **instalační program**.

1. V **Centru instalace serveru SQL Server**vyberte možnost **Instalace**.

1. Vyberte **novou instalaci clusteru s podporou převzetí služeb při selhání serveru SQL Server**. Podle pokynů průvodce nainstalujte rozhraní FCI serveru SQL Server.

   Fci datové adresáře musí být na premium sdílení souborů. Zadejte úplnou cestu sdílené položky `\\storageaccountname.file.core.windows.net\filesharename\foldername`v tomto formuláři: . Zobrazí se upozornění s upozorněním, že jste jako datový adresář zadali souborový server. Toto upozornění se očekává. Ujistěte se, že uživatelský účet, který rdp'd do virtuálního účtu s při trvalé sdílení souborů je stejný účet, který používá služba SQL Server, aby se zabránilo možným chybám.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Použití sdílené složky jako datových adresářů SQL":::

1. Po dokončení kroků v průvodci instalační program nainstaluje sql server FCI do prvního uzlu.

1. Po instalaci fci na první uzel se připojte k druhému uzlu pomocí programu RDP.

1. Otevřete **Centrum instalace serveru SQL Server**. Vyberte **možnost Instalace**.

1. Vyberte **Přidat uzel do clusteru**s podporou převzetí služeb při selhání serveru SQL Server . Podle pokynů průvodce nainstalujte sql server a přidejte server do fci.

   >[!NOTE]
   >Pokud jste použili image galerie Azure Marketplace s SQL Server, sql server nástroje byly součástí image. Pokud jste nepoužili jednu z těchto bitových kopií, nainstalujte nástroje serveru SQL Server samostatně. Viz [Download SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Krok 6: Vytvoření azure balancer

Ve virtuálních počítačích Azure clustery používají nástroj pro vyrovnávání zatížení k uložení IP adresy, která musí být v jednom uzlu clusteru najednou. V tomto řešení je v nástroji pro vyrovnávání zatížení adresa IP pro SQL Server FCI.

Další informace najdete [v tématu Vytvoření a konfigurace azure balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Vytvoření správce zatížení na webu Azure Portal

Vytvoření vykladače zatížení:

1. Na webu Azure Portal přejděte do skupiny prostředků, která obsahuje virtuální počítače.

1. Vyberte **Přidat**. Vyhledejte azure marketplace pro **vyrovnávání zatížení**. Vyberte **možnost Vyrovnávání zatížení**.

1. Vyberte **Vytvořit**.

1. Nastavte provyčnost zatížení pomocí následujících hodnot:

   - **Předplatné:** Vaše předplatné Azure.
   - **Skupina prostředků**: Skupina prostředků, která obsahuje vaše virtuální počítače.
   - **Název**: Název, který identifikuje balancer.
   - **Oblast:** Umístění Azure, které obsahuje vaše virtuální počítače.
   - **Typ**: Veřejné nebo soukromé. K soukromému nástroju pro vyrovnávání zatížení lze přistupovat z virtuální sítě. Většina aplikací Azure můžete použít soukromý vyrovnávání zatížení. Pokud vaše aplikace potřebuje přístup k SQL Server přímo přes internet, použijte veřejný vyrovnávání zatížení.
   - **Skladová položka**: standardní.
   - **Virtuální síť**: Stejná síť jako virtuální počítače.
   - **Přiřazení IP adresy**: Statické. 
   - **Privátní IP adresa**: Adresa IP, kterou jste přiřadili síťovému prostředku clusteru SQL Server FCI.

   Následující obrázek znázorňuje vytvoření ui **systému pro vyrovnávání zatížení:**

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

1. V okně **Přidat sondu** <span id="probe"> </span> stavu nastavte následující parametry sondy stavu.

   - **Název**: Název sondy stavu.
   - **Protokol**: TCP.
   - **Port**: Port, který jste vytvořili v bráně firewall pro sondu stavu v [tomto kroku](#ports). V tomto článku příklad používá `59999`port TCP .
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Krok 7: Konfigurace clusteru pro sondu

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

## <a name="step-8-test-fci-failover"></a>Krok 8: Testování převzetí služeb při selhání FCI

Otestujte převzetí služeb při selhání fci k ověření funkčnosti clusteru. Proveďte následující kroky:

1. Připojte se k jednomu z uzlů clusteru SQL Server FCI pomocí rdp.

1. Otevřete **Správce clusteru s podporou převzetí služeb při selhání**. Vyberte **role**. Všimněte si, který uzel vlastní roli FCI serveru SQL Server.

1. Klikněte pravým tlačítkem myši na roli FCI serveru SQL Server.

1. Vyberte **Přesunout**a pak vyberte **Nejlepší možný uzel**.

**Správce clusteru s podporou převzetí služeb při selhání** zobrazuje roli a její prostředky přejdou do offline. Prostředky pak přesunout a vrátit se do režimu online v jiném uzlu.

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

- [Technologie clusteru Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instance clusteru s podporou převzetí služeb při selhání serveru SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

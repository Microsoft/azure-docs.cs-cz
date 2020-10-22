---
title: Nastavení zotavení po havárii SAP NetWeaver pomocí Azure Site Recovery
description: Přečtěte si, jak nastavit zotavení po havárii pro SAP NetWeaver pomocí Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 69f0a20bdcba23d947e3d1b573c1a359da245161
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369416"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Nastavení zotavení po havárii pro nasazení aplikace NetWeaver SAP ve více vrstvách

Většina nasazení SAP ve velkých a středních velikostech používá určitou formu řešení pro zotavení po havárii. Důležitost robustních a testovatelné řešení pro zotavení po havárii se zvýšila, protože další základní obchodní procesy se přesunou do aplikací, jako je SAP. Azure Site Recovery testován a integrován s aplikacemi SAP. Site Recovery překračuje možnosti většiny místních řešení pro zotavení po havárii a nižší celkové náklady na vlastnictví než konkurenční řešení.

Pomocí Site Recovery můžete:
* Zapněte ochranu komponent SAP NetWeaver a non-NetWeaver produkčních aplikací, které se spouštějí místně pomocí replikace součástí do Azure.
* Zapněte ochranu aplikací SAP NetWeaver a jiných než NetWeaver produkčních aplikací, které běží na Azure, replikací součástí do jiného datového centra Azure.
* Zjednodušení migrace na cloud pomocí Site Recovery migrací nasazení SAP do Azure
* Zjednodušte upgrady, testování a vytváření prototypů projektů SAP vytvořením produkčního klonování na vyžádání pro testování aplikací SAP.

Nasazení aplikací SAP NetWeaver můžete chránit pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek popisuje osvědčené postupy pro ochranu nasazení SAP NetWeaver v Azure při replikaci do jiného datového centra Azure pomocí Site Recovery. Článek popisuje podporované scénáře a konfigurace a postup testování převzetí služeb při selhání (přechody k zotavení po havárii) a skutečné převzetí služeb při selhání.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že víte, jak provádět následující úlohy:

* [Replikace virtuálního počítače do Azure](./azure-to-azure-tutorial-enable-replication.md)
* [Návrh sítě pro obnovení](./azure-to-azure-about-networking.md)
* [Provedení testovacího převzetí služeb při selhání do Azure](./azure-to-azure-tutorial-dr-drill.md)
* [Provedení převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace instance SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Podporované scénáře

Site Recovery můžete použít k implementaci řešení zotavení po havárii v následujících scénářích:
* Máte systémy SAP běžící v jednom datovém centru Azure a budete je replikovat do jiného datového centra Azure (zotavení po havárii z Azure do Azure). 
   Další informace najdete v tématu [Architektura replikace z Azure do Azure](./azure-to-azure-architecture.md).
* Máte systémy SAP spuštěné v místním prostředí VMware (nebo fyzické) servery. Také provádíte replikaci systémů SAP na lokalitu pro zotavení po havárii v datacentru Azure (zotavení po havárii z VMware do Azure). 
   Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [Architektura replikace z VMware do Azure](./vmware-azure-architecture.md).
* Máte systémy SAP spuštěné v místním prostředí Hyper-V. Také provádíte replikaci systémů SAP do lokality pro zotavení po havárii v datacentru Azure (zotavení po havárii z Hyper-V do Azure).
   Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [Architektura replikace Hyper-V do Azure](./hyper-v-azure-architecture.md).

V tomto článku používáme scénář zotavení po havárii z **Azure do Azure** . Scénář zobrazuje možnosti zotavení po havárii SAP Site Recovery. Vzhledem k tomu, že Site Recovery replikace není specifická pro aplikaci, předpokládá se postup, který je popsán, i pro jiné scénáře.

### <a name="required-foundation-services"></a>Požadované služby Foundation
Ve scénáři, který probereme v tomto článku, se nasadí následující základní služby:
* Azure ExpressRoute nebo Azure VPN Gateway
* Aspoň jeden Azure Active Directory řadič domény a server DNS běžící v Azure

Před nasazením Site Recovery doporučujeme tuto infrastrukturu vytvořit.

## <a name="reference-sap-application-deployment"></a>Referenční nasazení aplikace SAP

Tato referenční architektura provozuje SAP NetWeaver v prostředí Windows v Azure s vysokou dostupností. Tato architektura se nasazuje s konkrétními velikostmi virtuálních počítačů, které můžete změnit, aby vyhovovaly potřebám vaší organizace.

![Diagram typického modelu nasazení SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

V případě zotavení po havárii musíte být schopni převzít služby při selhání do sekundární oblasti. Každá vrstva používá jinou strategii pro poskytování ochrany při zotavení po havárii.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Virtuální počítače, na kterých běží fondy webového dispečera SAP

Komponenta webového dispečera funguje jako nástroj pro vyrovnávání zatížení pro přenos dat SAP mezi aplikační servery SAP. Pro zajištění vysoké dostupnosti součásti webového dispečera Azure Load Balancer implementuje nastavení paralelního webového dispečera. Webový dispečer používá konfiguraci kruhového dotazování pro distribuci přenosů HTTP (S) mezi dostupnými webovými odesláními ve fondu vyrovnávání.

#### <a name="vms-running-application-servers-pools"></a>Virtuální počítače spouštějící fondy aplikačních serverů
Transakce SMLG spravuje přihlašovací skupiny pro aplikační servery ABAP. Používá funkci Vyrovnávání zatížení v rámci serveru zpráv centrálních služeb k distribuci úloh mezi fondy aplikačních serverů SAP pro provoz SAPGUIs a RFC. Tuto správu můžete replikovat pomocí Site Recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Virtuální počítače s clustery služby SAP Central Services
Tato referenční architektura spouští centrální služby na virtuálních počítačích v aplikační vrstvě. Centrální služby jsou potenciální jediný bod selhání v jednom virtuálním počítači. Typické nasazení a vysoká dostupnost nejsou požadavky.

K implementaci řešení s vysokou dostupností můžete použít buď cluster sdíleného disku, nebo cluster Shared File. Pokud chcete nakonfigurovat virtuální počítače pro cluster sdíleného disku, použijte cluster Windows serveru s podporou převzetí služeb při selhání. Doporučujeme, abyste jako určující disk kvora používali disk s kopií cloudu.

 > [!NOTE]
 > Vzhledem k tomu, že Site Recovery nereplikuje disk s kopií cloudu, doporučujeme nasadit v oblasti zotavení po havárii cloudovou kopii clusteru.

Pro podporu prostředí clusteru s podporou převzetí služeb při selhání používá služba [Datakeeping Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) funkci sdíleného svazku clusteru. Ve funkci s tím, že cluster DataKeeper replikuje nezávislé disky vlastněné uzly clusteru. Vzhledem k tomu, že Azure nativně nepodporuje sdílené disky, vyžaduje řešení, která poskytuje.

Clustering můžete zpracovat také implementací clusteru sdílených souborů. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nedávno upravil vzor nasazení centrální služby pro přístup k globálním adresářům/sapmnt pomocí cesty UNC. Pořád doporučujeme, abyste zajistili vysokou dostupnost sdílení/sapmnt UNC. Můžete kontrolovat instanci služby Central Services. Použijte cluster Windows serveru s podporou převzetí služeb při selhání se souborovým serverem se škálováním na více systémů (SOFS) a funkcí Prostory úložiště s přímým přístupem (S2D) ve Windows serveru 2016.

 > [!NOTE]
 > Site Recovery aktuálně podporuje pouze replikaci bodů konzistentních vzhledem k selháním virtuálních počítačů, které používají prostory úložiště s přímým přístupem, a pasivní uzel s DataKeeper.


## <a name="more-disaster-recovery-considerations"></a>Další informace o zotavení po havárii

Pomocí Site Recovery můžete orchestrovat převzetí služeb při selhání úplného nasazení SAP napříč oblastmi Azure.
Postup nastavení zotavení po havárii najdete v následujících krocích:

1. Replikace virtuálních počítačů
1. Návrh sítě pro obnovení
1. Replikace řadiče domény
1. Replikovat základní vrstvu dat
1. Provedení testovacího převzetí služeb při selhání
1. Provedení převzetí služeb při selhání

Následuje doporučení pro zotavení po havárii jednotlivých vrstev používaných v tomto příkladu.

 **Úrovně SAP** | **Doporučení**
 --- | ---
**Fond webového dispečera SAP** |  Replikace pomocí Site Recovery 
**Fond aplikačních serverů SAP** |  Replikace pomocí Site Recovery 
**Cluster služby SAP Central Services** |  Replikace pomocí Site Recovery 
**Virtuální počítače služby Active Directory** |  Použití replikace Active Directory 
**Servery služby SQL Database** |  Použít SQL Server vždy při replikaci

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete zahájit replikaci všech virtuálních počítačů aplikace SAP do datacentra pro zotavení po havárii v Azure, postupujte podle pokynů v části [replikace virtuálního počítače do Azure](./azure-to-azure-tutorial-enable-replication.md).

* Pokyny k ochraně služby Active Directory a DNS najdete v článku [jak chránit službu Active Directory a DNS](site-recovery-active-directory.md).

* Pokyny k ochraně databázových vrstev běžících na SQL Server najdete v tématu [jak chránit SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Konfigurace sítě

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou má virtuální počítač převzít. Pokud chcete nastavit IP adresu, přečtěte **Compute and Network settings**si  >  **síťové rozhraní karta**výpočty a nastavení sítě.

![Snímek obrazovky, který ukazuje, jak nastavit privátní IP adresu v podokně Site Recovery síťového rozhraní](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

Plán obnovení podporuje sekvencování různých vrstev v vícevrstvé aplikaci během převzetí služeb při selhání. Sekvence pomáhá udržovat konzistenci aplikací. Při vytváření plánu obnovení pro vícevrstvé webové aplikace proveďte kroky popsané v tématu [Vytvoření plánu obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

1. Vytvořte plán obnovení přidáním virtuálních počítačů aplikační server, webový dispečer a SAP Central Services.
1. Vyberte **přizpůsobit** pro seskupení virtuálních počítačů. Ve výchozím nastavení jsou všechny virtuální počítače součástí skupiny 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení
Aby vaše aplikace fungovaly správně, možná budete muset provést některé operace na virtuálních počítačích Azure. Tyto operace proveďte po převzetí služeb při selhání nebo během testovacího převzetí služeb při selhání. Můžete také automatizovat některé operace po převzetí služeb při selhání. Například aktualizujte položku DNS a změňte vazby a připojení přidáním odpovídajících skriptů do plánu obnovení.

Nejčastěji používané Site Recovery skripty můžete nasadit do svého účtu Azure Automation tak, že vyberete **nasadit do Azure**. Když použijete publikovaný skript, postupujte podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript před akcemi do skupiny 1, který převezme služby při selhání skupinou dostupnosti SQL Server. Použijte skript ASR-SQL-FailoverAG, který je publikovaný ve vzorových skriptech. Postupujte podle pokynů ve skriptu a patřičně proveďte požadované změny ve skriptu.
1. Přidejte skript po akci, který připojí nástroj pro vyrovnávání zatížení do virtuálních počítačů s podporou převzetí služeb při selhání webové vrstvy (skupina 1). Použijte skript ASR-AddSingleLoadBalancer publikovaný ve vzorových skriptech. Postupujte podle pokynů ve skriptu a podle potřeby proveďte požadované změny ve skriptu.

![Plán obnovení SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. V Azure Portal vyberte svůj trezor Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
1. Vyberte **Testovací převzetí služeb při selhání**.
1. Pokud chcete spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
1. Když je sekundární prostředí nahoru, proveďte ověření.
1. Po dokončení ověřování vyčistěte prostředí převzetí služeb při selhání výběrem možnosti **vyčistit testovací převzetí služeb**při selhání.

Další informace najdete v tématu [testování převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. V Azure Portal vyberte svůj trezor Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
1. Vyberte **Převzetí služeb při selhání**.
1. Chcete-li spustit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o vytváření řešení pro zotavení po havárii pro nasazení SAP NetWeaver pomocí Site Recovery. Přečtěte si dokument white paper o podpoře ke stažení v dokumentu [SAP NetWeaver: vytvoření řešení zotavení po havárii pomocí Site Recovery](https://aka.ms/asr_sap). Dokument white paper obsahuje doporučení pro různé architektury SAP. Můžete si prohlédnout podporované aplikace a typy virtuálních počítačů pro SAP v Azure. K dispozici jsou také možnosti plánu pro testování řešení zotavení po havárii.
* Přečtěte si další informace o [replikaci dalších úloh](site-recovery-workload.md) pomocí Site Recovery.
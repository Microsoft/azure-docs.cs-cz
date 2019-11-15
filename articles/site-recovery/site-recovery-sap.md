---
title: Nastavení zotavení po havárii SAP NetWeaver pomocí Azure Site Recovery
description: Tento článek popisuje, jak nastavit zotavení po havárii pro nasazení aplikací SAP NetWeaver pomocí Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: ca3126c983d62cb28c543215b86ab9709a4736d8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083794"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Nastavení zotavení po havárii pro nasazení aplikace NetWeaver SAP ve více vrstvách

Většina nasazení SAP ve velkých a středních velikostech používá určitou formu řešení pro zotavení po havárii. Důležitost robustních a testovatelné řešení pro zotavení po havárii se zvýšila, protože další základní obchodní procesy se přesunou do aplikací, jako je SAP. Azure Site Recovery testován a integrován s aplikacemi SAP. Site Recovery překračuje možnosti většiny místních řešení pro zotavení po havárii a nižší celkové náklady na vlastnictví než konkurenční řešení.

Pomocí Site Recovery můžete:
* **Zapněte ochranu komponent SAP NetWeaver a non-NetWeaver produkčních aplikací, které se spouštějí místně** pomocí replikace součástí do Azure.
* **Zapněte ochranu aplikací SAP NetWeaver a jiných než NetWeaver produkčních aplikací, které běží na Azure,** replikací součástí do jiného datového centra Azure.
* **Zjednodušte migraci do cloudu** pomocí Site Recovery k migraci nasazení SAP do Azure.
* **Zjednodušte upgrady, testování a vytváření prototypů projektů SAP** vytvořením produkčního klonování na vyžádání pro testování aplikací SAP.

Tento článek popisuje, jak chránit nasazení aplikací SAP NetWeaver pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčenými postupy pro ochranu nasazení NetWeaver SAP v Azure pomocí replikace do jiného datového centra Azure pomocí Site Recovery. Popisuje podporované scénáře a konfigurace a postup testování převzetí služeb při selhání (cvičení zotavení po havárii) a skutečné převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že víte, jak provádět následující úlohy:

* [Replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Návrh sítě pro obnovení](site-recovery-azure-to-azure-networking-guidance.md)
* [Provedení testovacího převzetí služeb při selhání do Azure](azure-to-azure-walkthrough-test-failover.md)
* [Provedení převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Podporované scénáře
Site Recovery můžete použít k implementaci řešení zotavení po havárii v následujících scénářích:
* Systémy SAP běžící v jednom datovém centru Azure, které se replikují do jiného datacentra Azure (zotavení po havárii z Azure do Azure). Další informace najdete v tématu [Architektura replikace z Azure do Azure](https://aka.ms/asr-a2a-architecture).
* Systémy SAP běžící na místních serverech VMware (nebo fyzických), které se replikují do lokality pro zotavení po havárii v datacentru Azure (zotavení po havárii z VMware do Azure). Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [Architektura replikace z VMware do Azure](https://aka.ms/asr-v2a-architecture).
* Systémy SAP běžící v místním prostředí Hyper-V, které se replikují na lokalitu pro zotavení po havárii v datacentru Azure (zotavení po havárii z Hyper-V do Azure). Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [Architektura replikace Hyper-V do Azure](https://aka.ms/asr-h2a-architecture).

V tomto článku se používá scénář zotavení po havárii z **Azure do Azure** , který předvádí možnosti zotavení po havárii Site Recovery SAP. Vzhledem k tomu, že Site Recovery replikace není specifická pro aplikaci, předpokládá se postup, který je popsán, i pro jiné scénáře.

### <a name="required-foundation-services"></a>Požadované služby Foundation
Ve scénáři, který probereme v tomto článku, se nasadí následující základní služby:
* Azure ExpressRoute nebo Azure VPN Gateway
* Aspoň jeden řadič domény služby Active Directory a server DNS běžící v Azure

Před nasazením Site Recovery doporučujeme tuto infrastrukturu vytvořit.

## <a name="reference-sap-application-deployment"></a>Referenční nasazení aplikace SAP

Tato referenční architektura ukazuje spuštění SAP NetWeaver v prostředí Windows v Azure s vysokou dostupností.  Tato architektura se nasazuje s konkrétními velikostmi virtuálních počítačů, které se dají změnit, aby vyhovovaly potřebám vaší organizace.

![Diagram typického modelu nasazení SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Požadavky na zotavení po havárii

Pro zotavení po havárii (DR) musíte být schopni převzetí služeb při selhání do sekundární oblasti. Každá úroveň používá k zajištění ochrany při zotavení po havárii jinou strategii.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuální počítače s fondem webového dispečera SAP 
Komponenta Web Dispatchera slouží jako nástroj pro vyrovnávání zatížení pro provoz SAP mezi aplikačními servery SAP. Pro zajištění vysoké dostupnosti pro komponentu webového dispečera Azure Load Balancer slouží k implementaci nastavení paralelního webového dispečera v konfiguraci kruhového dotazování pro distribuci přenosů HTTP (S) mezi dostupnými webovými odinstalačními soubory ve fondu vyrovnávání. Tato akce bude replikována pomocí Azure Site Recovery (ASR) a skripty služby Automation budou použity ke konfiguraci nástroje pro vyrovnávání zatížení v oblasti zotavení po havárii. 

#### <a name="vms-running-application-servers-pool"></a>Virtuální počítače s fondem aplikačních serverů
Ke správě skupin přihlášení pro ABAP aplikační servery, je použít SMLG transakce. Jak rozdělit zatížení mezi fond serverů pro aplikace SAP pro SAPGUIs a v dokumentu RFC využívá funkce v rámci zprávy serveru z centrální služby Vyrovnávání zatížení provozu. Tato akce bude replikována pomocí Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>Virtuální počítače se spuštěným clusterem SAP Central Services
Tato referenční architektura centrální služby běží na virtuálních počítačích v aplikační vrstvě. Centrální služby je možné jediný bod selhání (SPOF) při nasazení na jeden virtuální počítač – typické nasazení při vysoké dostupnosti není povinné.<br>

K implementaci řešení vysoké dostupnosti lze použít buď cluster sdíleného disku, nebo cluster sdílené složky. Pokud chcete nakonfigurovat virtuální počítače pro cluster sdíleného disku, použijte cluster Windows serveru s podporou převzetí služeb při selhání. Disk s kopií cloudu se doporučuje jako určující disk kvora. 
 > [!NOTE]
 > Azure Site Recovery nereplikuje sdílené složky v cloudu. proto se doporučuje nasadit v oblasti zotavení po havárii cloudovou kopii clusteru.

Pro podporu prostředí clusteru převzetí služeb při selhání [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) provádí funkci clusteru sdíleného svazku to replikací nezávislých disků, které jsou ve vlastnictví uzlů clusteru. Azure nativně nepodporuje sdílené disky a proto vyžaduje řešení poskytovaných SIOS. 

Dalším způsobem, jak zvládnout clustering, je implementovat cluster sdílené složky. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) naposledy upravovaných model nasazení centrální služby pro přístup k /sapmnt globální adresáře prostřednictvím cesty UNC. Přesto se však doporučuje zajistit vysokou dostupnost sdílení/sapmnt UNC. To se dá udělat na instanci centrální služby pomocí clusteru s podporou převzetí služeb při selhání Windows serveru se souborovým serverem se škálováním na více systémů (SOFS) a funkcí Prostory úložiště s přímým přístupem (S2D) ve Windows serveru 2016. 
 > [!NOTE]
 > V současné době Azure Site Recovery podporovat jenom replikaci bodů s konzistentním selháním pro virtuální počítače s využitím prostorů úložiště s přímým přístupem a pasivního uzlu s datakeep


## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Azure Site Recovery můžete použít k orchestraci převzetí služeb při selhání celého nasazení SAP napříč oblastmi Azure.
Níže jsou uvedené kroky pro nastavení zotavení po havárii. 

1. Replikace virtuálních počítačů 
2. Návrh sítě pro obnovení
3.  Replikace řadiče domény
4.  Replikovat základní vrstvu dat 
5.  Provedení testovacího převzetí služeb při selhání 
6.  Provedení převzetí služeb při selhání 

Níže je uvedené doporučení pro zotavení po havárii jednotlivých vrstev používaných v tomto příkladu. 

 **Úrovně SAP** | **Doporučení**
 --- | ---
**Fond webového dispečera SAP** |  Replikace pomocí Site Recovery 
**Fond aplikačních serverů SAP** |  Replikace pomocí Site Recovery 
**Cluster služby SAP Central Services** |  Replikace pomocí Site Recovery 
**Virtuální počítače služby Active Directory** |  Replikace služby Active Directory 
**Servery SQL Database** |  Replikace SQL Always On

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete zahájit replikaci všech virtuálních počítačů aplikace SAP do datacentra pro zotavení po havárii v Azure, postupujte podle pokynů v části [replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md).


* Pokyny k ochraně služby Active Directory a DNS najdete v tématu [ochrana služby Active Directory a dokumentu DNS](site-recovery-active-directory.md) .

* Pokyny k ochraně databázových vrstev běžících na SQL serveru najdete v tématu [ochrana SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou má virtuální počítač převzít. IP adresu nastavíte tak, že přejdete na **Nastavení výpočty a síť** > **síťovou kartu**.

![Snímek obrazovky, který ukazuje, jak nastavit privátní IP adresu v podokně Site Recovery síťového rozhraní](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různých vrstev v vícevrstvé aplikaci během převzetí služeb při selhání. Sekvence pomáhá udržovat konzistenci aplikací. Při vytváření plánu obnovení pro vícevrstvé webové aplikace proveďte kroky popsané v tématu [Vytvoření plánu obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

1.  Vytvořte plán obnovení přidáním virtuálních počítačů aplikační server, webový dispečer a SAP Central Services.
2.  Pokud chcete seskupit virtuální počítače, klikněte na přizpůsobit. Ve výchozím nastavení jsou všechny virtuální počítače součástí skupiny 1.



### <a name="add-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení
Aby vaše aplikace fungovaly správně, možná budete muset provést některé operace na virtuálních počítačích Azure po převzetí služeb při selhání nebo během testovacího převzetí služeb při selhání. Některé operace po převzetí služeb při selhání můžete automatizovat. Můžete například aktualizovat položku DNS a změnit vazby a připojení přidáním odpovídajících skriptů do plánu obnovení.


Nejčastěji používané Azure Site Recovery skripty můžete nasadit do svého účtu Automation kliknutím na tlačítko nasadit do Azure níže. Při použití publikovaného skriptu se ujistěte, že budete postupovat podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript pre-Action do skupiny 1 pro převzetí služeb při selhání skupiny dostupnosti SQL. Použijte skript ASR-SQL-FailoverAG, který je publikovaný ve vzorových skriptech. Ujistěte se, že dodržujete pokyny ve skriptu a patřičně proveďte požadované změny ve skriptu.
2. Přidejte skript akce post pro připojení nástroje pro vyrovnávání zatížení ve virtuálních počítačích převzetí služeb při selhání webové vrstvy (skupina 1). Použijte skript ASR-AddSingleLoadBalancer, který je publikovaný ve vzorových skriptech. Ujistěte se, že dodržujete pokyny ve skriptu a patřičně proveďte požadované změny ve skriptu.

![Plán obnovení SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1.  V Azure Portal vyberte svůj trezor Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **Testovací převzetí služeb při selhání**.
4.  Pokud chcete spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5.  Když je sekundární prostředí nahoru, proveďte ověření.
6.  Po dokončení ověření pro vyčištění prostředí s podporou převzetí služeb při selhání vyberte **vyčistit testovací převzetí služeb**při selhání.

Další informace najdete v tématu [testování převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1.  V Azure Portal vyberte svůj trezor Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **Převzetí služeb při selhání**.
4.  Chcete-li spustit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další kroky
* Další informace o tom, jak vytvořit řešení zotavení po havárii pro nasazení SAP NetWeaver pomocí Site Recovery, najdete v dokumentu White paper o podpoře ke stažení v dokumentu [SAP NetWeaver: sestavování řešení zotavení po havárii pomocí Azure Site Recovery](https://aka.ms/asr_sap). Dokument white paper popisuje doporučení pro různé architektury SAP, uvádí podporované aplikace a typy virtuálních počítačů pro SAP v Azure a popisuje možnosti testovacího plánu pro řešení zotavení po havárii.
* Přečtěte si další informace o [replikaci dalších úloh](site-recovery-workload.md) pomocí Site Recovery.

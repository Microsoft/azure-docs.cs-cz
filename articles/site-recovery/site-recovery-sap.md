---
title: Ochrana nasazení vícevrstvé SAP NetWeaver aplikace pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak chránit nasazení aplikací SAP NetWeaver pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: 95e5c53da2556293fc676fa5b1db9b4585038300
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921427"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Ochrana nasazení vícevrstvé aplikace SAP NetWeaver pomocí Site Recovery

Většina velké a střední velikosti nasazení SAP použít nějaký způsob řešení zotavení po havárii. Důležitost řešení zotavení po havárii robustní a testovatelných zvýšilo jako další klíčové obchodní procesy, které jsou přesunuty do aplikací, jako je SAP. Azure Site Recovery prošla testováním a integrovaná s aplikacemi SAP. Site Recovery překračuje možnosti většiny řešení pro zotavení po havárii místních a při snížení celkových nákladů na vlastnictví (TCO) než konkurenční řešení.

Pomocí služby Site Recovery můžete:
* **Povolit ochranu SAP NetWeaver a jiných NetWeaver produkčních aplikací, které běží místně** pomocí replikace komponent do Azure.
* **Povolit ochranu SAP NetWeaver a jiných NetWeaver produkčních aplikací, které běží na Azure** pomocí replikace komponent do jiného datového centra Azure.
* **Zjednodušení migrace na cloud** pomocí Site Recovery migrací nasazení SAP do Azure.
* **Zjednodušení SAP upgradem projektu, testování a vytváření prototypů** tak, že vytvoříte produkční klonovat na vyžádání pro účely testování aplikací SAP.

Tento článek popisuje, jak chránit nasazení aplikací SAP NetWeaver pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčenými postupy pro ochranu třívrstvé nasazení SAP NetWeaver v Azure, ve které se replikují do jiného datového centra Azure s využitím Site Recovery. Popisuje Podporované scénáře a konfiguracích a jak provést testovací převzetí služeb při selhání (zotavení po havárii) a skutečné převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že budete vědět, jak provádět následující úlohy:

* [Replikace virtuálních počítačů do Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Návrh sítě pro zotavení](site-recovery-azure-to-azure-networking-guidance.md)
* [Provést testovací převzetí služeb do Azure](azure-to-azure-walkthrough-test-failover.md)
* [Proveďte převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Podporované scénáře
Site Recovery můžete použít k implementaci řešení pro zotavení po havárii v následujících scénářích:
* SAP systémy s operačním systémem v jednom datacentru Azure, které se replikují do jiného datového centra Azure (zotavení po havárii Azure do Azure). Další informace najdete v tématu [architektury pro replikaci Azure do Azure](https://aka.ms/asr-a2a-architecture).
* SAP systémy s operačním systémem ve VMware (nebo fyzická) serverů s místními této replikace na lokalitu pro zotavení po havárii v datacentru Azure (zotavení po havárii VMware do Azure). Tento scénář vyžaduje některé další komponenty. Další informace najdete v tématu [architektury pro replikaci VMware do Azure](https://aka.ms/asr-v2a-architecture).
* SAP systémy s operačním systémem Hyper-V v místním, které se replikují do lokality pro zotavení po havárii v datacentru Azure (zotavení po havárii Hyper-V do Azure). Tento scénář vyžaduje některé další komponenty. Další informace najdete v tématu [architektury pro replikaci Hyper-V do Azure](https://aka.ms/asr-h2a-architecture).

V tomto článku používáme **Azure do Azure** scénář zotavení po havárii předvést možnosti zotavení po havárii SAP služby Site Recovery. Protože replikace Site Recovery není specifická pro aplikaci, očekává se proces, který je popsán platí také pro jiné scénáře.

### <a name="required-foundation-services"></a>Požadované foundation services
Ve scénáři, které si popíšeme v tomto článku jsou nasazeny následující služby foundation:
* Azure ExpressRoute nebo Azure VPN Gateway
* Nejméně jeden řadič domény služby Active Directory a DNS server, který běží v Azure

Doporučujeme vytvořit tuto infrastrukturu ještě před nasazením Site Recovery.

## <a name="reference-sap-application-deployment"></a>Nasazení aplikace SAP odkaz

Tato referenční architektura ukazuje spuštění SAP NetWeaver v prostředí s Windows v Azure s vysokou dostupností.  Tato architektura se nasazuje s velikostí konkrétního virtuálního počítače (VM), které můžete změnit tak, aby vyhovovaly potřebám vaší organizace.

![Diagram typický vzor nasazení SAP](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Pro zotavení po havárii (DR) musíte být schopni převzetí služeb při selhání do sekundární oblasti. Každá úroveň používá k zajištění ochrany při zotavení po havárii jinou strategii.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuální počítače se systémem SAP Web Dispatchera fondu 
Komponenta Web Dispatchera slouží jako nástroj pro vyrovnávání zatížení pro provoz SAP mezi aplikačními servery SAP. K dosažení vysoké dostupnosti pro komponentu Web Dispatchera, nástroje pro vyrovnávání zatížení Azure slouží k implementaci paralelní instalaci Web Dispatchera v konfiguraci kruhové dotazování pro distribuci provozu HTTP (S) mezi dostupné webové dispečerů ve fondu nástroje pro vyrovnávání. To se budou replikovat pomocí Azure Site Recovery (ASR) a skripty pro automatizaci se použije ke konfiguraci nástroje pro vyrovnávání zatížení v oblasti pro zotavení po havárii. 

####<a name="vms-running-application-servers-pool"></a>Virtuální počítače, servery fondu aplikací
Ke správě skupin přihlášení pro ABAP aplikační servery, je použít SMLG transakce. Jak rozdělit zatížení mezi fond serverů pro aplikace SAP pro SAPGUIs a v dokumentu RFC využívá funkce v rámci zprávy serveru z centrální služby Vyrovnávání zatížení provozu. To se budou replikovat pomocí Azure Site Recovery 

####<a name="vms-running-sap-central-services-cluster"></a>Virtuální počítače se systémem SAP Central Services clusteru
Tato referenční architektura centrální služby běží na virtuálních počítačích v aplikační vrstvě. Centrální služby je možné jediný bod selhání (SPOF) při nasazení na jeden virtuální počítač – typické nasazení při vysoké dostupnosti není povinné.<br>

Při implementaci řešení vysoké dostupnosti, je možné sdílený disk clusteru nebo clusteru sdílené složky souboru. Ke konfiguraci virtuálních počítačů pro cluster sdíleného disku použijte Cluster převzetí služeb při selhání Windows serveru. Disk s kopií cloudu se doporučuje jako určující disk kvora. 
 > [!NOTE]
 > Azure Site Recovery se nereplikují disk s kopií cloudu proto se doporučuje nasazení cloudové kopie clusteru v oblasti pro zotavení po havárii.

Pro podporu prostředí clusteru převzetí služeb při selhání [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) provádí funkci clusteru sdíleného svazku to replikací nezávislých disků, které jsou ve vlastnictví uzlů clusteru. Azure nativně nepodporuje sdílené disky a proto vyžaduje řešení poskytovaných SIOS. 

Jiný způsob, jak zpracovat clustering je implementace cluster souborových sdílené složky. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) naposledy upravovaných model nasazení centrální služby pro přístup k /sapmnt globální adresáře prostřednictvím cesty UNC. Ale přesto doporučujeme Ujistěte se, že sdílené složky UNC /sapmnt s vysokou dostupností. To můžete udělat v instanci centrální služby s použitím clusteru převzetí služeb při selhání Windows serveru se škálování na souborový Server (SOFS) a funkci prostory úložiště – přímé (S2D) ve Windows serveru 2016. 
 > [!NOTE]
 > Aktuálně Azure Site Recovery podporu pouze havárií bod konzistentní vzhledem k replikaci virtuálních počítačů pomocí prostorů úložiště s přímým přístupem 


## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Azure Site Recovery můžete orchestrovat převzetí přes úplné nasazení SAP napříč oblastmi Azure.
Dále jsou uvedené kroky pro nastavení zotavení po havárii 

1. Replikace virtuálních počítačů 
2. Návrh sítě pro zotavení
3.  Replikace řadiče domény
4.  Replikace dat základní úrovni 
5.  Provést testovací převzetí služeb 
6.  Služeb při selhání 

Níže je doporučení pro zotavení po havárii pro každou vrstvu použitý v tomto příkladu. 

 **Úrovně SAP** | **Doporučení**
 --- | ---
**SAP Web Dispatchera fondu** |  Replikovat pomocí Site recovery 
**Fond serverů aplikace SAP** |  Replikovat pomocí Site recovery 
**Cluster se službou SAP Central Services** |  Replikovat pomocí Site recovery 
**Virtuální počítače služby Active directory** |  Replikace služby Active directory 
**Servery SQL database** |  SQL AlwaysOn replikace

##<a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Ke spuštění replikace všech SAP aplikací virtuálních počítačů do datacentra pro zotavení po havárii Azure, postupujte podle pokynů v [replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md).


* Pokyny k ochraně služby Active Directory a DNS, najdete v tématu [ochrana služby Active Directory a DNS](site-recovery-active-directory.md) dokumentu.

* Informace o ochraně databázová vrstva spouštěný na SQL serveru najdete v tématu [chránit SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

Pokud používáte statické IP adresy, můžete zadat IP adresu, která má virtuální počítač má provést. Pokud chcete nastavit IP adresu, přejděte na **výpočty a síť nastavení** > **síťové karty**.

![Snímek obrazovky, který ukazuje, jak nastavit privátní IP adresu v podokně karta rozhraní sítě Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Vytváří se plán obnovení
Plán obnovení podporuje sekvencování různé úrovně v vícevrstvou aplikaci při selhání. Klasifikace se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvou webovou aplikaci, dokončete kroky popsané v tématu [vytvořit plán obnovení s využitím Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupiny převzetí služeb při selhání

1.  Vytvořte plán obnovení tak, že přidáte aplikační server, web dispatchera a SAP Central services virtuálních počítačů.
2.  Klikněte na "Vlastní" seskupení virtuálních počítačů. Ve výchozím nastavení všechny virtuální počítače jsou součástí "Skupina 1".



### <a name="add-scripts-to-the-recovery-plan"></a>Přidat skripty do plánu obnovení
Pro vaše aplikace fungovala správně může být nutné udělat některé operace na virtuálních počítačích Azure po převzetí služeb nebo při selhání testu. Můžete automatizovat některé operace post-převzetí služeb při selhání. Například můžete aktualizovat položku DNS a měnit vazby a připojení přidáním příslušných skriptů do plánu obnovení.


Nejčastěji používané skriptů Azure Site Recovery můžete nasadit do účtu Automation, kliknutím na tlačítko 'Nasadit do Azure' níže. Při použití všechny publikované skripty, zajistěte, aby že postupujte podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidáte skript akce před "Skupina 1" do skupiny dostupnosti SQL převzetí služeb při selhání. Pomocí skriptu "Azure Site Recovery-SQL-FailoverAG" publikované v ukázkových skriptech. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.
2. Přidat skript akce post připojit nástroj pro vyrovnávání zatížení se oproti virtuálním počítačům webová vrstva (skupina 1). Pomocí skriptu "Azure Site Recovery-AddSingleLoadBalancer" publikované v ukázkových skriptech. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.

![Plán obnovení SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1.  Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **testovací převzetí služeb při selhání**.
4.  Chcete-li spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5.  Pokud je sekundární prostředí, proveďte ověření.
6.  Po dokončení ověření chcete-li vyčistit prostředí převzetí služeb při selhání vyberte **vyčištění testovacího převzetí služeb při selhání**.

Další informace najdete v tématu [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1.  Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **převzetí služeb při selhání**.
4.  Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další postup
* Další informace o vytváření řešení pro zotavení po havárii pro nasazení SAP NetWeaver pomocí Site Recovery najdete v tématu ke stažení dokument white paper [SAP NetWeaver: vytváření řešení pro zotavení po havárii pomocí Azure Site Recovery](http://aka.ms/asr-sap). Dokument white paper popisuje doporučení pro různé architektury SAP, seznam podporovaných aplikací a typy virtuálních počítačů pro SAP v Azure a popisuje možnosti testovací plán pro vaše řešení zotavení po havárii.
* Další informace o [replikaci jiných úloh](site-recovery-workload.md) pomocí Site Recovery.

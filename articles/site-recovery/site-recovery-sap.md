---
title: Ochrana nasazení vícevrstvé SAP NetWeaver aplikace pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak k ochraně nasazení SAP NetWeaver aplikace pomocí Azure Site Recovery.
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
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267328"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>K nasazení aplikace SAP NetWeaver vícevrstvé chránit pomocí Site Recovery

Většina nasazení SAP velikosti a středně velké použít nějaký způsob řešení zotavení po havárii. Význam řešení zotavení po havárii robustní a možností intenzivního testování zvýšilo jako další základní obchodní procesy přesunou do aplikací, jako SAP. Azure Site Recovery byl otestován a integrovat s aplikacemi SAP. Site Recovery překračuje možnosti většinu řešení zotavení po havárii v místě a na nižší celkové náklady na vlastnictví (TCO) než konkurenční řešení.

Site Recovery můžete:
* **Povolit ochranu SAP NetWeaver a bez NetWeaver produkční aplikace, které běží místně** replikace součástí do Azure.
* **Povolit ochranu SAP NetWeaver a bez NetWeaver výrobní aplikace, které běží na Azure** replikace součástí do jiného datového centra Azure.
* **Zjednodušení migrace na cloud** pomocí Site Recovery migrací nasazení SAP do Azure.
* **Zjednodušení SAP upgrade projektu, testování a při vytváření prototypu** vytvořením provozní klonovat na vyžádání pro testování aplikací SAP.

Tento článek popisuje, jak k ochraně nasazení SAP NetWeaver aplikací s použitím [Azure Site Recovery](site-recovery-overview.md). Článek popisuje osvědčené postupy pro ochrana nasazení SAP NetWeaver třívrstvá v Azure replikace do jiné datové centrum Azure pomocí Site Recovery. Popisuje Podporované scénáře a konfigurace a jak provést testovací převzetí služeb při selhání (podrobněji obnovení po havárii) a skutečné převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že budete vědět, jak provést tyto úlohy:

* [Replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Návrh k síti pro obnovení](site-recovery-azure-to-azure-networking-guidance.md)
* [Provést testovací převzetí služeb Azure](azure-to-azure-walkthrough-test-failover.md)
* [Proveďte převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Podporované scénáře
Site Recovery můžete použít k implementaci řešení zotavení po havárii v následujících scénářích:
* SAP systémy s operačním systémem v jedné datové centrum Azure, které se replikují do jiné datové centrum Azure (zotavení po havárii Azure do Azure). Další informace najdete v tématu [Azure do Azure replikace architektura](https://aka.ms/asr-a2a-architecture).
* SAP systémy s operačním systémem na VMware (nebo fyzický) servery místní této replikovat do lokality obnovení po havárii v datovém Azure (zotavení po havárii VMware do Azure). Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [architektura replikace VMware do Azure](https://aka.ms/asr-v2a-architecture).
* SAP systémy s operačním systémem místními technologie Hyper-V, které se replikují do lokality obnovení po havárii v datovém Azure (zotavení po havárii technologie Hyper-V-do Azure). Tento scénář vyžaduje některé další součásti. Další informace najdete v tématu [architektura replikace technologie Hyper-V-do Azure](https://aka.ms/asr-h2a-architecture).

V tomto článku používáme **Azure do Azure** scénáře zotavení po havárii k předvedení funkcí pro obnovení po havárii SAP služby Site Recovery. Protože replikace Site Recovery není specifické pro aplikaci, proces, který je popsán se očekává se rovněž vztahují na jiné scénáře.

### <a name="required-foundation-services"></a>Požadované foundation services
Ve scénáři, které v tomto článku probereme jsou nasazeny následující služby foundation:
* Azure ExpressRoute nebo Azure VPN Gateway
* Alespoň jeden řadič domény služby Active Directory a DNS server, který běží v Azure

Doporučujeme vytvoření této infrastruktury, před nasazením Site Recovery.

## <a name="reference-sap-application-deployment"></a>Nasazení aplikace SAP odkaz

Tuto referenční architekturu ukazuje systémem SAP NetWeaver v prostředí systému Windows Azure s vysokou dostupností.  Tato architektura se nasazuje s velikostí konkrétní virtuální počítač (VM), které můžete změnit tak, aby dokázala pojmout potřebám vaší organizace.

![Diagram typický vzor nasazení SAP](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Pro zotavení po havárii (DR) musí umět převzetí služeb při selhání pro sekundární oblast. Každá úroveň používá k zajištění ochrany při zotavení po havárii jinou strategii.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuální počítače se systémem fondu dispečerů webové SAP 
Součást webové dispečera slouží jako nástroj pro vyrovnávání zatížení pro SAP provoz mezi servery aplikace SAP. K dosažení vysoké dostupnosti pro komponentu dispečera Web, nástroj pro vyrovnávání zatížení Azure slouží k implementaci paralelní instalační program webové dispečera v konfiguraci kruhového dotazování pro protokol HTTP (S) distribuce přenosů mezi dostupné dispečerů webové ve fondu vyrovnávání. To bude replikován pomocí Azure lokality využívají a skripty pro automatizaci se použije ke konfiguraci Vyrovnávání zatížení v oblasti obnovení po havárii. 

####<a name="vms-running-application-servers-pool"></a>Virtuální počítače se systémem servery fondu aplikací
Ke správě skupin přihlášení pro ABAP aplikační servery, je použít SMLG transakce. Jak rozdělit zatížení mezi fond serverů aplikace SAP SAPGUIs a RFC používá funkce v rámci serveru zpráva centrální služby Vyrovnávání zatížení provozu. To bude replikován pomocí Azure Site Recovery 

####<a name="vms-running-sap-central-services-cluster"></a>Virtuální počítače se systémem SAP centrální služby clusteru
Tuto referenční architekturu centrální Services běží na virtuálních počítačích v aplikační vrstvě. Centrální služby je potenciální jediný bod selhání (SPOF) při svém nasazení na jeden virtuální počítač – typické nasazení při vysoké dostupnosti není povinné.<br>

K implementaci řešení vysoké dostupnosti, lze používat sdílený disk clusteru nebo cluster sdílené složky souborů. Chcete-li nakonfigurovat virtuální počítače pro cluster s podporou sdíleného disku, použijte clusteru převzetí služeb při selhání systému Windows Server. Doporučuje se určující cloud jako určující disk kvora. 
 > [!NOTE]
 > Azure Site Recovery se nereplikuje určující cloudu proto se doporučuje pro nasazení cloudu určující sdílené složky v oblasti obnovení po havárii.

Pro podporu prostředí clusteru převzetí služeb při selhání, [s DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) provádí funkce clusteru sdíleného svazku nastavením replikace nezávislých disků, které jsou ve vlastnictví na uzlech clusteru. Azure nenabízí nativní podporu sdílených disků a proto vyžaduje řešení poskytovaných SIOS. 

Další způsob zpracování, clustering je implementace cluster sdílené složky souborů. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) naposledy upravovaných vzoru centrální služby nasazení pro přístup k adresáři globální /sapmnt prostřednictvím cesty UNC. Tato změna odebere požadavek SIOS nebo jiných řešeních sdíleného disku na virtuálních počítačích centrální služby. Přesto doporučujeme zajistit, že sdílené složky UNC /sapmnt je vysoce dostupný. To lze provést v instanci služby Centrální za použití clusteru převzetí služeb při selhání systému Windows Server se Škálováním se souborovým serverem (SOFS) a funkci prostory úložiště – přímé (S2D) v systému Windows Server 2016. 
 > [!NOTE]
 > Aktuálně podpory Azure Site Recovery pouze havárií bod konzistentní replikace virtuálních počítačů pomocí prostory úložiště – přímé 


## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Azure Site Recovery můžete použít k orchestraci služby při selhání přes úplné nasazení SAP nad oblastmi Azure.
Níže jsou uvedené kroky pro nastavení zotavení po havárii 

1. Replikace virtuálních počítačů 
2. Návrh k síti pro obnovení
3.  Replikace řadiče domény
4.  Replikovat základní datové vrstvy 
5.  Provést testovací převzetí služeb 
6.  Selhání 

Níže je doporučení pro zotavení po havárii jednotlivých úrovní použité v tomto příkladu. 

 **SAP vrstev** | **Doporučení**
 --- | ---
**Fondu dispečerů webové SAP** |  Replikovat pomocí Site recovery 
**Fond serverů aplikace SAP** |  Replikovat pomocí Site recovery 
**SAP centrální služby clusteru** |  Replikovat pomocí Site recovery 
**Virtuální počítače služby Active directory** |  Replikace služby Active directory 
**Databázové servery SQL** |  SQL vždy na replikaci

##<a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete spustit všechny SAP aplikace virtuální počítače replikující se do datového centra obnovení po havárii Azure, postupujte podle pokynů v [replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md).


* Pokyny o ochraně Active Directory a DNS, najdete v tématu [chránit Active Directory a DNS](site-recovery-active-directory.md) dokumentu.

* Pokyny k ochraně databázové vrstvy systémem SQL server, najdete v části [chránit SQL Server](site-recovery-active-directory.md) dokumentu.

## <a name="networking-configuration"></a>Konfigurace sítě

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou chcete virtuální počítač tak, aby. Chcete-li nastavit adresu IP, přejděte na **výpočty a síť nastavení** > **karty síťového rozhraní**.

![Snímek obrazovky, který ukazuje, jak nastavit privátní IP adresy v podokně karty rozhraní sítě obnovení lokality](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různé úrovně ve vícevrstvé aplikace při selhání. Sekvencování se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvé webové aplikace, dokončete kroky popsané v tématu [vytvořit plán obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Probíhá přidávání virtuálních počítačů do skupin převzetí služeb při selhání

1.  Vytvoření plánu obnovení přidáním aplikační server, web dispečera a SAP centrální služeb virtuálních počítačů.
2.  Klikněte na "Upravit" skupiny virtuálních počítačů. Ve výchozím nastavení všechny virtuální počítače jsou součástí skupiny 1, který.



### <a name="add-scripts-to-the-recovery-plan"></a>Přidat skripty do plánu obnovení
Pro vaše aplikace fungovat správně může být zapotřebí provést některé operace na virtuálních počítačích Azure po převzetí nebo během testovací převzetí služeb. Je možné automatizovat některé operace post-převzetí služeb při selhání. Například můžete aktualizovat záznam DNS a změnit vazby a připojení přidáním příslušných skriptů do plánu obnovení.


Nejčastěji používané skripty Azure Site Recovery můžete nasadit do vašeho účtu Automation klepnutím na tlačítko 'nasadit do Azure, níže. Při použití všech publikovaných skriptů, zajistěte, aby že podle pokynů uvedených ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidáte skript předběžné akce na '1 skupiny' převzetí služeb při selhání skupiny dostupnosti SQL. Použít skript, automatické obnovení systému SQL FailoverAG' publikován v ukázkové skripty. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu správně.
2. Přidat skript akce post připojit nástroj pro vyrovnávání zatížení na neúspěšný přes virtuální počítače webová vrstva (skupina 1). Použít skript, automatické obnovení systému-AddSingleLoadBalancer' publikován v ukázkové skripty. Ujistěte se, postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu správně.

![Plán obnovení SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1.  Na portálu Azure vyberte svůj trezor služeb zotavení.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **testovací převzetí služeb při selhání**.
4.  Chcete-li zahájit proces testovací převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5.  Po sekundární prostředí, proveďte ověření.
6.  Po dokončení ověření pro čištění prostředí převzetí služeb při selhání, vyberte **vyčistit testovací převzetí služeb při selhání**.

Další informace najdete v tématu [testovací převzetí služeb při selhání do Azure ve službě Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1.  Na portálu Azure vyberte svůj trezor služeb zotavení.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **převzetí služeb při selhání**.
4.  Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání ve službě Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další postup
* Další informace o vytváření řešení zotavení po havárii pro nasazení SAP NetWeaver pomocí Site Recovery najdete v tématu ke stažení dokumentu white paper [SAP NetWeaver: vytváření řešení zotavení po havárii s Azure Site Recovery](http://aka.ms/asr-sap). V dokumentu white paper popisuje doporučení pro různé architektury SAP, jsou uvedené podporované aplikací a typů virtuálních počítačů pro SAP v Azure a popisuje možnosti testování plán pro vaše řešení pro zotavení po havárii.
* Další informace o [replikace dalších zatížení](site-recovery-workload.md) pomocí Site Recovery.

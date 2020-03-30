---
title: Nastavení zotavení po havárii sap netweaveru pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro SAP NetWeaver pomocí Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190795"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Nastavení zotavení po havárii pro vícevrstvé nasazení aplikace SAP NetWeaver

Většina velkých a středně velkých nasazení SAP používá nějakou formu řešení zotavení po havárii. Důležitost robustních a testovatelných řešení pro zotavení po havárii se zvýšila s tím, jak se do aplikací, jako je SAP, přesouvá více klíčových obchodních procesů. Azure Site Recovery byl testován a integrován s aplikacemi SAP. Site Recovery přesahuje možnosti většiny místních řešení zotavení po havárii a při nižších celkových nákladech na vlastnictví než konkurenční řešení.

S site recovery můžete:
* Povolte ochranu produkčních aplikací SAP NetWeaver a jiných než NetWeaver, které běží místně, replikací součástí do Azure.
* Povolte ochranu produkčních aplikací SAP NetWeaver a jiných než NetWeaver, které běží v Azure, replikací součástí do jiného datového centra Azure.
* Zjednodušení migrace na cloud pomocí Site Recovery migrací nasazení SAP do Azure
* Zjednodušte upgrady projektů SAP, testování a vytváření prototypů vytvořením výrobního klonu na vyžádání pro testování aplikací SAP.

Nasazení aplikací SAP NetWeaver můžete chránit pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek popisuje osvědčené postupy pro ochranu třívrstvé nasazení SAP NetWeaver v Azure při replikaci do jiného datového centra Azure pomocí site recovery. Článek popisuje podporované scénáře a konfigurace a jak provést testování převzetí služeb při selhání (cvičení zotavení po havárii) a skutečné převzetí služeb při selhání.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že víte, jak provést následující úkoly:

* [Replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Návrh obnovovací sítě](site-recovery-azure-to-azure-networking-guidance.md)
* [Proveďte testovací převzetí služeb při selhání v Azure](azure-to-azure-walkthrough-test-failover.md)
* [Převzetí služeb při selhání v Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace instance serveru SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Podporované scénáře

Obnovení webu můžete použít k implementaci řešení zotavení po havárii v následujících scénářích:
* Máte systémy SAP spuštěné v jednom datovém centru Azure a replikujete je do jiného datového centra Azure (zotavení po havárii Azure. 
   Další informace najdete v [tématu architektura replikace Azure-Azure](https://aka.ms/asr-a2a-architecture).
* Máte systémy SAP spuštěné na serverech VMware (nebo fyzických) v místním prostředí. Také replikujete systémy SAP do lokality pro zotavení po havárii v datovém centru Azure (zotavení po havárii VMware-to-Azure). 
   Tento scénář vyžaduje některé další součásti. Další informace naleznete [v tématu Architektura replikace VMware-to-Azure](https://aka.ms/asr-v2a-architecture).
* Máte systémy SAP spuštěné v místním prostředí Hyper-V. Také replikujete systémy SAP do lokality pro zotavení po havárii v datovém centru Azure (zotavení po havárii Technologie Hyper-V-To-Azure).
   Tento scénář vyžaduje některé další součásti. Další informace naleznete v tématu [Architektura replikace Technologie Hyper-V-Azure](https://aka.ms/asr-h2a-architecture).

V tomto článku používáme scénář zotavení po **havárii Azure.** Scénář zobrazuje možnosti zotavení po havárii SAP obnovení webu. Vzhledem k tomu, že replikace obnovení lokality není specifická pro aplikaci, očekává se, že popsaný proces se bude vztahovat i na jiné scénáře.

### <a name="required-foundation-services"></a>Požadované nadační služby
Ve scénáři, který popisujeme v tomto článku, jsou nasazeny následující služby nadace:
* Azure ExpressRoute nebo brána Azure VPN
* Alespoň jeden řadič domény Azure Active Directory a server DNS spuštěný v Azure

Doporučujeme vytvořit tuto infrastrukturu před nasazením site recovery.

## <a name="reference-sap-application-deployment"></a>Referenční nasazení aplikace SAP

Tato referenční architektura běží SAP NetWeaver v prostředí Windows v Azure s vysokou dostupností. Tato architektura se nasadí s konkrétnívelikosti virtuálních počítačů ,, které můžete změnit tak, aby vyhovovaly potřebám vaší organizace.

![Diagram typického vzoru nasazení SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Aspekty zotavení po havárii

Pro zotavení po havárii musí být možné převzetí služeb při selhání do sekundární oblasti. Každá úroveň používá jinou strategii k zajištění ochrany zotavení po havárii.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>Virtuální servery se spuštěnou fondy SAP Web Dispatcher

Komponenta Webový dispečer funguje jako systém vyrovnávání zatížení pro provoz SAP mezi aplikačními servery SAP. Aby bylo dosaženo vysoké dostupnosti pro komponentu Webový dispečer, nástroj Azure Load Balancer implementuje paralelní nastavení webového dispečera. Webový dispečer používá konfiguraci kruhového dotazování pro distribuci přenosů HTTP(S) mezi dostupnými webovými dispečery ve fondu vyvažovačů.

#### <a name="vms-running-application-servers-pools"></a>Virtuální servery se spuštěnou fondy aplikačních serverů
Transakce SMLG spravuje přihlašovací skupiny pro aplikační servery ABAP. Používá funkci vyrovnávání zatížení v rámci serveru zpráv centrálních služeb k distribuci pracovního vytížení mezi fondy aplikačních serverů SAP pro SAPGUI a provoz RFC. Tuto správu můžete replikovat pomocí site recovery.

#### <a name="vms-running-sap-central-services-clusters"></a>Virtuální virtuální společnosti se clustery SAP Central Services
Tato referenční architektura spouští centrální služby na virtuálních počítačích v aplikační vrstvě. Centrální služby je potenciální jediný bod selhání, když v jednom virtuálním počítači. Typické nasazení a vysoká dostupnost nejsou požadavky.

Chcete-li implementovat řešení s vysokou dostupností, můžete použít cluster sdíleného disku nebo cluster sdílené složky. Chcete-li konfigurovat virtuální počítače pro cluster sdíleného disku, použijte cluster Windows Server s podporou převzetí služeb při selhání. Doporučujeme použít cloud důkaz jako svědka kvora.

 > [!NOTE]
 > Vzhledem k tomu, že site recovery nereplikuje cloud důkaz, doporučujeme nasadit cloud důkaz v oblasti zotavení po havárii.

Pro podporu prostředí clusteru s podporou převzetí služeb při selhání provádí funkce clusteru SDÍLENÉHo svazku [aplikace SIOS DataKeeper Cluster Edition.](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) Ve funkci cluster SIOS DataKeeper replikuje nezávislé disky vlastněné uzly clusteru. Vzhledem k tomu, že Azure nativně nepodporuje sdílené disky, vyžaduje řešení poskytovaná sios.

Clustering můžete také zpracovat implementací clusteru sdílené složky. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) nedávno upravil vzor nasazení centrálních služeb pro přístup ke globálním adresářům /sapmnt prostřednictvím cesty UNC. Stále doporučujeme zajistit, aby /sapmnt UNC podíl je vysoce dostupný. Můžete zkontrolovat instanci centrálních služeb. V systému Windows Server 2016 použijte cluster s podporou převzetí služeb při selhání systému Windows Server se souborovým serverem s horizontálním navýšením kapacity (SOFS) a funkcí Storage Spaces Direct (S2D).

 > [!NOTE]
 > Site Recovery v současné době podporuje pouze směrování bodů konzistentní chod virtuálních počítačů, které používají přímé prostory úložiště a pasivní uzel správce dat SIOS.


## <a name="more-disaster-recovery-considerations"></a>Další aspekty zotavení po havárii

Obnovení webu můžete použít k orchestraci převzetí služeb při selhání úplného nasazení SAP napříč oblastmi Azure.
Následují kroky pro nastavení zotavení po havárii:

1. Replikace virtuálních počítačů
1. Návrh obnovovací sítě
1. Replikace řadiče domény
1. Replikovat základní vrstvu dat
1. Provedení testovacího převzetí služeb při selhání
1. Provedení převzetí služeb při selhání

Následuje doporučení pro zotavení po havárii každé vrstvy použité v tomto příkladu.

 **Úrovně SAP** | **Doporučení**
 --- | ---
**Fond webových dispečerů SAP** |  Replikovat pomocí site recovery 
**Fond aplikačních serverů SAP** |  Replikovat pomocí site recovery 
**Cluster centrálních služeb SAP** |  Replikovat pomocí site recovery 
**Virtuální počítače služby Active Directory** |  Použití replikace služby Active Directory 
**Servery služby SQL Database** |  Použití replikace sql serveru always on

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete začít replikovat všechny virtuální počítače aplikací SAP do datového centra Azure pro zotavení po havárii, postupujte podle pokynů v [části Replikovat virtuální počítač do Azure](azure-to-azure-walkthrough-enable-replication.md).

* Pokyny k ochraně služby Active Directory a služby DNS naleznete v informacích o [ochraně služeb Active Directory a dns](site-recovery-active-directory.md).

* Pokyny k ochraně databázové vrstvy spuštěné na serveru SQL Server naleznete v článku [Ochrana serveru SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Konfigurace sítě

Pokud používáte statickou ADRESU IP, můžete zadat adresu IP, kterou má virtuální počítač převzít. Chcete-li nastavit adresu IP, přejděte na **kartu rozhraní Zabezpečení a síť** > **nastavení sítě**.

![Snímek obrazovky, který ukazuje, jak nastavit privátní ADRESU IP v podokně karty rozhraní sítě Site Recovery Network](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení

Plán obnovení podporuje řazení různých vrstev ve vícevrstvé aplikaci během převzetí služeb při selhání. Sekvencování pomáhá udržovat konzistenci aplikace. Při vytváření plánu obnovení pro vícevrstvou webovou aplikaci proveďte kroky popsané v [části Vytvoření plánu obnovení pomocí obnovení webu](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

1. Vytvořte plán obnovení přidáním aplikačního serveru, webového dispečera a virtuálních stránek sap central služeb.
1. Vyberte **Přizpůsobit,** chcete-li virtuální ho seskupit. Ve výchozím nastavení jsou všechny virtuální aplikace součástí skupiny 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení
Aby vaše aplikace fungovaly správně, možná budete muset provést některé operace na virtuálních počítačích Azure. Tyto operace po převzetí služeb při selhání nebo během testu převzetí služeb při selhání. Můžete také automatizovat některé operace po převzetí služeb při selhání. Můžete například aktualizovat položku DNS a změnit vazby a připojení přidáním odpovídajících skriptů do plánu obnovení.

Nejpoužívanější skripty site recovery můžete nasadit do svého účtu Azure Automation výběrem **deploy to Azure**. Při použití libovolného publikovaného skriptu postupujte podle pokynů ve skriptu.

[![Nasazení do Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Přidejte skript před akcí do skupiny 1, který převezme služby pro převzetí služeb při selhání skupiny dostupnosti serveru SQL Server. Použijte skript ASR-SQL-FailoverAG publikovaný v ukázkových skriptech. Postupujte podle pokynů ve skriptu a proveďte požadované změny ve skriptu odpovídajícím způsobem.
1. Přidejte skript po akci, který připojí nástroj pro vyrovnávání zatížení k virtuálním počítačům webové vrstvy (skupina 1). Použijte skript ASR-AddSingleLoadBalancer publikovaný v ukázkových skriptech. Postupujte podle pokynů ve skriptu a podle potřeby proveďte požadované změny ve skriptu.

![Plán obnovy SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. Na webu Azure Portal vyberte trezor služby Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
1. Vyberte **Testovací převzetí služeb při selhání**.
1. Chcete-li spustit proces převzetí služeb při selhání testu, vyberte bod obnovení a virtuální síť Azure.
1. Když je sekundární prostředí nahoru, proveďte ověření.
1. Po dokončení ověření vyčistěte prostředí s podporou převzetí služeb při selhání výběrem **převzetí služeb při selhání testu vyčištění**.

Další informace najdete [v tématu Test převzetí služeb při selhání na Azure v site recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Na webu Azure Portal vyberte trezor služby Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
1. Vyberte **Převzetí služeb při selhání**.
1. Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace naleznete v tématu [Převzetí služeb při selhání v tématu Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další kroky
* Další informace o vytváření řešení zotavení po havárii pro nasazení SAP NetWeaver pomocí site recovery. Podívejte se na dokument white paper [SAP NetWeaver ke stažení: Vytvoření řešení zotavení po havárii s obnovením webu](https://aka.ms/asr_sap). Bílá kniha popisuje doporučení pro různé architektury SAP. Můžete zobrazit podporované aplikace a typy virtuálních počítačů pro SAP v Azure. Existují také možnosti plánu pro testování řešení zotavení po havárii.
* Další informace o [replikaci jiných úloh](site-recovery-workload.md) pomocí site recovery.

---
title: Ochrana nasazení vícevrstvé SAP NetWeaver aplikace pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak k ochraně nasazení SAP NetWeaver aplikace pomocí Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: e2107177663163259d1f731717c4910bc986fc1f
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
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

V tomto článku používáme k předvedení funkcí Site Recovery pro obnovení po havárii SAP scénáře zotavení po havárii Azure do Azure. Protože replikace Site Recovery není specifické pro aplikaci, proces, který je popsán se očekává se rovněž vztahují na jiné scénáře.

### <a name="required-foundation-services"></a>Požadované foundation services
Ve scénáři, které v tomto článku probereme jsou nasazeny následující služby foundation:
* Azure ExpressRoute nebo Azure VPN Gateway
* Alespoň jeden řadič domény služby Active Directory a DNS server, který běží v Azure

Doporučujeme vytvoření této infrastruktury, před nasazením Site Recovery.

## <a name="typical-sap-application-deployment"></a>Typické nasazení aplikace SAP
Velké SAP zákazníci obvykle nasazovat mezi 6 a nejvíce 20 jednotlivých aplikací SAP. Většina těchto aplikací jsou založeny na moduly SAP NetWeaver ABAP nebo Java. Mnoho modulů samostatné menší, konkrétní jiný - NetWeaver SAP a obvykle některé aplikace bez SAP podporují tyto aplikace NetWeaver jádra.  

Je důležité k inventarizaci všechny aplikace SAP, které běží ve vašem prostředí. Potom určení režimu nasazení (dvouvrstvá nebo třívrstvá), verze, opravy, velikosti, sazby změn a trvalost na disku.

![Diagram typický vzor nasazení SAP](./media/site-recovery-sap/sap-typical-deployment.png)

Vrstvu trvalosti databázi SAP Chraňte pomocí nativních nástrojů databázového systému, jako je například replikace systému SQL Server AlwaysOn, Oracle Data Guard nebo SAP HANA. Jako v databázové vrstvě SAP vrstvě klienta není chráněný pomocí Site Recovery. Je důležité vzít v úvahu faktory, které ovlivňují tuto vrstvu. Faktory patří zpoždění šíření DNS, zabezpečení a vzdáleného přístupu do datového centra obnovení po havárii.

Site Recovery je doporučené řešení pro aplikační vrstvu, včetně pro SAP SCS a ASC. Jiné aplikace, jako je například aplikace NetWeaver SAP a aplikace, bez SAP, součástí celkového prostředí nasazení SAP. Měli byste je chránit pomocí Site Recovery.

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů
Pokud chcete spustit všechny SAP aplikace virtuální počítače replikující se do datového centra obnovení po havárii Azure, postupujte podle pokynů v [replikace virtuálního počítače do Azure](azure-to-azure-walkthrough-enable-replication.md).

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou chcete virtuální počítač tak, aby. Chcete-li nastavit adresu IP, přejděte na **výpočty a síť nastavení** > **karty síťového rozhraní**.

![Snímek obrazovky, který ukazuje, jak nastavit privátní IP adresy v podokně karty rozhraní sítě obnovení lokality](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různé úrovně ve vícevrstvé aplikace při selhání. Sekvencování se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvé webové aplikace, dokončete kroky popsané v tématu [vytvořit plán obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-scripts-to-the-recovery-plan"></a>Přidat skripty do plánu obnovení
Pro vaše aplikace fungovat správně může být zapotřebí provést některé operace na virtuálních počítačích Azure po převzetí nebo během testovací převzetí služeb. Je možné automatizovat některé operace post-převzetí služeb při selhání. Například můžete aktualizovat záznam DNS a změnit vazby a připojení přidáním příslušných skriptů do plánu obnovení.

### <a name="dns-update"></a>Aktualizace DNS.
Pokud je server DNS nakonfigurovaný pro aktualizace dynamického DNS, virtuálních počítačů obvykle aktualizovat DNS novou IP adresu při spuštění. Pokud chcete přidat na explicitní krok pro aktualizace DNS pomocí nové IP adresy virtuálních počítačů, přidejte [skript pro aktualizaci IP adresa ve službě DNS](https://aka.ms/asr-dns-update) jako akci post-převzetí služeb při selhání u skupiny plánu obnovení.  

## <a name="example-azure-to-azure-deployment"></a>Příklad nasazení Azure do Azure
Následující diagram znázorňuje scénáře zotavení po havárii Site Recovery Azure do Azure:

![Diagram scénáře replikace Azure do Azure](./media/site-recovery-sap/sap-replication-scenario.png)

* Primární datové centrum se Singapur (Azure jihovýchodní Asie). Datacenter obnovení po havárii se Hongkong (Azure východní Asie). V tomto scénáři poskytuje dva virtuální počítače se systémem SQL Server AlwaysOn v synchronním režimu v Singapur místní vysokou dostupnost.
* Sdílené složky SAP ASC poskytuje vysokou dostupnost pro SAP jediné body selhání. Sdílené složky ASC nevyžaduje sdíleného disku clusteru. Aplikace, jako je SIOS nejsou požadované.
* Ochrana pro obnovení po havárii pro vrstvu databázového systému je dosaženo pomocí asynchronní replikaci.
* Tento scénář popisuje "zotavení po havárii symetrický." Tento termín popisuje řešení zotavení po havárii, která je k přesný repliku produkčního. Zotavení po havárii řešení systému SQL Server má místní vysokou dostupnost. Zotavení po havárii symetrický není povinné pro databázové vrstvě. Mnoho zákazníků využít flexibilitu nasazení cloudu rychle sestavíte místní vysokou dostupnost uzlu po o událost obnovení po havárii.
* Diagram znázorňuje SAP NetWeaver ASC a aplikační server vrstvu replikovat pomocí Site Recovery.

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

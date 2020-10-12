---
title: Replikace virtuálních počítačů Azure se systémem Prostory úložiště s přímým přístupem s využitím Azure Site Recovery
description: Přečtěte si, jak replikovat virtuální počítače Azure se systémem Prostory úložiště s přímým přístupem pomocí Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 0b4f18c32639ceb2084febe210a8cfd4c423a0cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135758"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikace virtuálních počítačů Azure s Prostory úložiště s přímým přístupem do jiné oblasti

Tento článek popisuje, jak povolit zotavení po havárii virtuálních počítačů Azure s prostory úložiště s přímým přístupem.

>[!NOTE]
>Pro clustery prostorů úložiště s přímým přístupem jsou podporovány pouze body obnovení konzistentní vzhledem k selháním.
>

[Prostory úložiště s přímým přístupem (S2D)](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) jsou softwarově definované úložiště, které poskytuje způsob, jak vytvořit [clustery hostů](https://techcommunity.microsoft.com/t5/failover-clustering/bg-p/FailoverClustering) v Azure.  Cluster hostů v Microsoft Azure je cluster s podporou převzetí služeb při selhání tvořený IaaS virtuálními počítači. Umožňuje, aby hostované úlohy virtuálních počítačů převzaly služby při selhání napříč clustery hostů a dosáhly se smlouvy SLA pro vyšší dostupnost pro aplikace, než může poskytnout jeden virtuální počítač Azure. To je užitečné ve scénářích, kdy virtuální počítač hostuje kritickou aplikaci, jako je SQL nebo souborový server se škálováním na více systémů.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Zotavení po havárii pomocí prostorů úložiště s přímým přístupem

V typickém scénáři můžete mít hostovaný cluster virtuálních počítačů v Azure pro zajištění vyšší odolnosti vaší aplikace, třeba souborového serveru se škálováním na více instancí. I když to může zajistit vyšší dostupnost vaší aplikace, chcete tyto aplikace ochránit pomocí Site Recovery pro jakékoli selhání úrovně oblasti. Site Recovery replikuje data z jedné oblasti do jiné oblasti Azure a v případě převzetí služeb při selhání vyvolá cluster v oblasti zotavení po havárii.

Pod diagramem je znázorněn cluster s podporou převzetí služeb při selhání virtuálního počítače Azure se dvěma uzly pomocí prostorů úložiště s

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání systému Windows a každý virtuální počítač mají dva nebo více datových disků.
- S2D synchronizuje data na datovém disku a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště prezentuje jako sdílený svazek clusteru (CSV) do clusteru s podporou převzetí služeb při selhání.
- Cluster s podporou převzetí služeb při selhání používá pro datové jednotky sdílený svazek clusteru.

**Požadavky na zotavení po havárii**

1. Při nastavování [cloudového](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) clusteru pro cluster udržujte v oblasti zotavení po havárii složku s kopií clusteru.
2. Pokud převezmete služby virtuálních počítačů při selhání k podsíti v oblasti zotavení po havárii, která se liší od zdrojové oblasti, po převzetí služeb při selhání musí být IP adresa clusteru změněna.  Pokud chcete změnit IP adresu clusteru, musíte použít [skript plánu obnovení Site Recovery.](./site-recovery-runbook-automation.md)</br>
[Ukázkový skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) pro provedení příkazu uvnitř virtuálního počítače pomocí rozšíření vlastních skriptů 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Povolování Site Recovery pro cluster S2D:

1. V trezoru služby Recovery Services klikněte na + replikovat.
1. Vybrat všechny uzly v clusteru a nastavit je jako součást [skupiny konzistence s více virtuálními počítači](./azure-to-azure-common-questions.md#multi-vm-consistency)
1. Vyberte zásadu replikace s vypnutou konzistencí aplikace * (k dispozici je jenom podpora konzistence selhání).
1. Povolení replikace

   ![Ochrana storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Přejděte na replikované položky a uvidíte stav virtuálního počítače.
3. Virtuální počítače jsou chráněné a zobrazují se taky jako součást skupiny konzistence pro víc virtuálních počítačů.

   ![Ochrana storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různých vrstev v vícevrstvé aplikaci během převzetí služeb při selhání. Sekvence pomáhá udržovat konzistenci aplikací. Při vytváření plánu obnovení pro vícevrstvé webové aplikace proveďte kroky popsané v tématu [Vytvoření plánu obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupin s podporou převzetí služeb při selhání

1.  Vytvořte plán obnovení přidáním virtuálních počítačů.
2.  Pokud chcete seskupit virtuální počítače, klikněte na přizpůsobit. Ve výchozím nastavení jsou všechny virtuální počítače součástí skupiny 1.


### <a name="add-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení
Aby vaše aplikace fungovaly správně, možná budete muset provést některé operace na virtuálních počítačích Azure po převzetí služeb při selhání nebo během testovacího převzetí služeb při selhání. Některé operace po převzetí služeb při selhání můžete automatizovat. Tady je například připojení nástroje pro vyrovnávání zatížení a změna IP adresy clusteru.


### <a name="failover-of-the-virtual-machines"></a>Převzetí služeb při selhání virtuálních počítačů 
Uzly virtuálních počítačů musí při převzetí služeb při selhání použít [plán obnovení](./site-recovery-create-recovery-plans.md) Site Recovery. 

![Ochrana storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
1.  V Azure Portal vyberte svůj trezor Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili.
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

[Další informace](./azure-to-azure-tutorial-failover-failback.md) o spuštění navrácení služeb po obnovení.

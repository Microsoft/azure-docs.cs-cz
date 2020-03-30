---
title: Replikace virtuálních stránek Azure se spuštěnými prostory úložiště direct pomocí Azure Site Recovery
description: Zjistěte, jak replikovat virtuální počítače Azure se systémem Storage Spaces Direct pomocí Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: 9f394fa8d618c97d74a47ff6e42a002f177cf7d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973652"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Replikace virtuálních počítačů Azure se systémem Storage Spaces Direct do jiné oblasti

Tento článek popisuje, jak povolit zotavení po havárii virtuálních počítačích Azure běží prostory úložiště přímo.

>[!NOTE]
>Pro přímé clustery prostorů úložiště jsou podporovány pouze body obnovení konzistentní s selháním.
>

[Prostory úložiště direct (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) je softwarově definované úložiště, které poskytuje způsob, jak vytvořit [clustery hosta](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) v Azure.  Cluster hosta v Microsoft Azure je cluster s podporou převzetí služeb při selhání složený z virtuálních aplikací IaaS. Umožňuje hostované úlohy virtuálních počítače k převzetí služeb při selhání mezi clustery hosta, dosažení vyšší dostupnost sla pro aplikace, než jeden virtuální počítač Azure může poskytnout. Je to užitečné ve scénářích, kde virtuální modul hostuje kritickou aplikaci, jako je SQL nebo souborový server s horizontálním navýšením kapacity.

## <a name="disaster-recovery-with-storage-spaces-direct"></a>Zotavení po havárii s přímými úložnými prostory

V typickém scénáři můžete mít virtuální počítače clusteru hosta v Azure pro vyšší odolnost vaší aplikace, jako je horizontální navýšení kapacity souborového serveru. I když to může poskytnout vaší aplikaci vyšší dostupnost, chcete chránit tyto aplikace pomocí site recovery pro jakékoli selhání na úrovni oblasti. Site Recovery replikuje data z jedné oblasti do jiné oblasti Azure a vyvolá cluster v oblasti zotavení po havárii v případě převzetí služeb při selhání.

Níže uvedený diagram znázorňuje cluster s podporou převzetí služeb při selhání Azure s dvěma uznami pomocí přímého prostoru úložiště.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)


- Dva virtuální počítače Azure v clusteru s podporou převzetí služeb při selhání Windows a každý virtuální počítač mají dva nebo více datových disků.
- S2D synchronizuje data na datovém disku a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště prezentuje jako sdílený svazek clusteru (CSV) clusteru s podporou převzetí služeb při selhání.
- Cluster s podporou převzetí služeb při selhání používá pro datové jednotky soubor CSV.

**Důležité informace o zotavení po havárii**

1. Při nastavování [cloud důkaz pro](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) clusteru, zachovat důkaz v oblasti zotavení po havárii.
2. Pokud se chystáte převzetí služeb při selhání virtuálních počítačů do podsítě v oblasti zotavení po Havárii, která se liší od zdrojové oblasti, musí být po převzetí služeb při selhání změnit adresu IP clusteru.  Chcete-li změnit adresu IP clusteru, musíte použít [skript plánu obnovení webu.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Ukázkový skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) pro spuštění příkazu uvnitř virtuálního aplikace pomocí rozšíření vlastního skriptu 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>Povolení obnovení lokality pro cluster S2D:

1. V trezoru služeb pro obnovení klikněte na tlačítko +replikovat.
1. Vyberte všechny uzly v clusteru a vytvořte je součástí [skupiny konzistence více virtuálních virtuálních bylv.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Vyberte zásady replikace s vypnutou konzistencí aplikace* (je k dispozici pouze podpora konzistence selhání)
1. Povolení replikace

   ![storagespacespřímná ochrana](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Přejděte na replikované položky a uvidíte stav virtuálního počítače.
3. Oba virtuální počítače jsou stále chráněné a jsou také zobrazeny jako součást skupiny konzistence více virtuálních počítačů.

   ![storagespacespřímná ochrana](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje řazení různých vrstev ve vícevrstvé aplikaci během převzetí služeb při selhání. Sekvencování pomáhá udržovat konzistenci aplikace. Při vytváření plánu obnovení pro vícevrstvou webovou aplikaci proveďte kroky popsané v [části Vytvoření plánu obnovení pomocí obnovení webu](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin převzetí služeb při selhání

1.  Vytvořte plán obnovení přidáním virtuálních počítačů.
2.  Klikněte na 'Přizpůsobit' pro seskupení virtuálních počítačů. Ve výchozím nastavení jsou všechny virtuální aplikace součástí skupiny 1.


### <a name="add-scripts-to-the-recovery-plan"></a>Přidání skriptů do plánu obnovení
Aby vaše aplikace fungovaly správně, možná budete muset provést některé operace na virtuálních počítačích Azure po převzetí služeb při selhání nebo během převzetí služeb při selhání testu. Můžete automatizovat některé operace po převzetí služeb při selhání. Zde například připojujeme vyvyčovávání zatížení a měníme ip adresu clusteru.


### <a name="failover-of-the-virtual-machines"></a>Převzetí služeb při selhání virtuálních počítačů 
Oba uzly virtuálních zařízení musí být převzetí služeb při selhání pomocí [plánu obnovení](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) webu 

![storagespacespřímná ochrana](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
1.  Na webu Azure Portal vyberte trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili.
3.  Vyberte **Testovací převzetí služeb při selhání**.
4.  Chcete-li spustit proces převzetí služeb při selhání testu, vyberte bod obnovení a virtuální síť Azure.
5.  Když je sekundární prostředí nahoru, proveďte ověření.
6.  Po dokončení ověření, chcete-li vyčistit prostředí převzetí služeb při selhání, vyberte **možnost Převzetí služeb při selhání testu vyčištění**.

Další informace najdete [v tématu Test převzetí služeb při selhání na Azure v site recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1.  Na webu Azure Portal vyberte trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **Převzetí služeb při selhání**.
4.  Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace naleznete v tématu [Převzetí služeb při selhání v tématu Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) o spuštění navrácení služeb po selhání.

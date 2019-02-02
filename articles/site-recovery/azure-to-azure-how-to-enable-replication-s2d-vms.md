---
title: Konfigurace replikace pro prostory úložiště s přímým přístupem (S2d) virtuálních počítačů ve službě Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nakonfigurovat replikaci pro virtuální počítače s S2D, z jedné oblasti Azure do jiné pomocí služby Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 0b3094abfe1642cb65043729489f3aaed0732df9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55570013"
---
# <a name="replicate-azure-virtual-machines-using-storage-spaces-direct-to-another-azure-region"></a>Replikace virtuálních počítačů Azure pomocí úložiště, které prostorů s přímým přístupem do jiné oblasti Azure

Tento článek popisuje, jak povolit zotavení po havárii virtuálních počítačů Azure spuštěné prostorů úložiště s přímým přístupem.

>[!NOTE]
>Pro přímé clustery prostory úložiště jsou podporovány pouze body obnovení konzistentní vzhledem k selhání.
>

##<a name="introduction"></a>Úvod 
[Prostory úložiště s přímým přístupem (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) je úložiště definovaného softwarem, který poskytuje způsob, jak vytvořit [clustery hostů](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) v Azure.  Cluster hostů v Microsoft Azure je, že Cluster převzetí služeb při selhání se skládá z virtuálních počítačů IaaS. To umožňuje hostované úlohy virtuálních počítačů pro převzetí služeb při selhání mezi clustery hostů dosažení vyšší smlouva SLA o dostupnosti pro aplikace, než můžete zadat jenom jeden virtuální počítač Azure. To je užitečné v situacích, ve kterém virtuální počítač hostující kritické aplikace, jako je SQL nebo určený počet číslic souborový server atd.

##<a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Po havárii pro obnovení z Azure Virtual Machines pomocí prostorů úložiště s přímým přístupem
V rámci typického scénáře může mít virtuální počítače hostující cluster v Azure pro vyšší odolnost vaší aplikace, jako je škálování souborový server. Když to může poskytnout vyšší dostupnost vaší aplikace, které byste rádi chránit tyto aplikace pomocí služby Site Recovery pro všechny úrovně selhání oblasti. Site Recovery replikuje data z jedné oblasti do jiné oblasti Azure a přináší cluster v oblasti pro zotavení po havárii v případě převzetí služeb při selhání.

Následující diagram ukazuje vizuální reprezentaci dva prostory clusteru převzetí služeb při selhání virtuálních počítačů Azure pomocí úložiště direct.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Dva virtuální počítače Azure v clusteru převzetí služeb při selhání s Windows a všechny virtuální počítače mají dvě nebo více datových disků.
- S2D synchronizuje data na datový disk a prezentuje synchronizované úložiště jako fond úložiště.
- Fond úložiště představuje jako sdílený svazek clusteru (CSV) do clusteru převzetí služeb při selhání.
- Cluster převzetí služeb při selhání používá sdílený svazek clusteru pro datové jednotky.

**Aspekty zotavení po havárii**

1. Když nastavujete [cloudu s kopií clusteru](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) pro cluster, ponechte určující sdílené složky v oblasti zotavení po havárii.
2. Pokud chcete převzít služby při selhání virtuálních počítačů do podsítě v oblasti zotavení po Havárii, která se liší od zdrojové oblasti IP adresu clusteru musí být po převzetí služeb při selhání změnit.  Chcete-li změnit IP adresu clusteru budete muset použít Azure Site Recovery [skript plánu obnovení.](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)</br>
[Ukázkový skript](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) ke spuštění příkazu uvnitř virtuálního počítače pomocí rozšíření vlastních skriptů 

###<a name="enabling-site-recovery-for-s2d-cluster"></a>Díky Site Recovery pro S2D cluster:

1. Uvnitř obnovení služby trezoru, klikněte na tlačítko "+ replikovat"
1. Vybere všechny uzly v clusteru a je součástí [skupina konzistence více virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency)
1. Vyberte zásadu replikace s konzistentnost aplikací vypnuto * (pouze při selhání konzistence podpora je k dispozici)
1. Povolení replikace

   ![storagespacesdirect ochrany](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Přejděte na replikované položky a zobrazí se stav virtuálního počítače. 
3. Virtuální počítače chránily získávání a jsou také uvedeny jako součást skupiny konzistence více virtuálních počítačů.

   ![storagespacesdirect ochrany](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Vytváří se plán obnovení
Plán obnovení podporuje sekvencování různé úrovně v vícevrstvou aplikaci při selhání. Klasifikace se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvou webovou aplikaci, dokončete kroky popsané v tématu [vytvořit plán obnovení s využitím Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupiny převzetí služeb při selhání

1.  Vytvořte plán obnovení tak, že přidáte virtuální počítače.
2.  Klikněte na "Vlastní" seskupení virtuálních počítačů. Ve výchozím nastavení všechny virtuální počítače jsou součástí "Skupina 1".


### <a name="add-scripts-to-the-recovery-plan"></a>Přidat skripty do plánu obnovení
Pro vaše aplikace fungovala správně může být nutné udělat některé operace na virtuálních počítačích Azure po převzetí služeb nebo při selhání testu. Můžete automatizovat některé operace post-převzetí služeb při selhání. Například tady jsme se připojit nástroj pro vyrovnávání zatížení a mění IP clusteru.


###<a name="failover-of-the-virtual-machines"></a>Převzetí služeb při selhání virtuálních počítačů 
Musí být převzetí služeb při selhání pomocí obou uzlů virtuálních počítačů [plánu obnovení Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) 

![storagespacesdirect ochrany](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání
1.  Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili.
3.  Vyberte **Testovací převzetí služeb při selhání**.
4.  Chcete-li spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5.  Pokud je sekundární prostředí, proveďte ověření.
6.  Po dokončení ověření chcete-li vyčistit prostředí převzetí služeb při selhání vyberte **vyčištění testovacího převzetí služeb při selhání**.

Další informace najdete v tématu [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1.  Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
2.  Vyberte plán obnovení, který jste vytvořili pro aplikace SAP.
3.  Vyberte **Převzetí služeb při selhání**.
4.  Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).
## <a name="next-steps"></a>Další postup

[Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) o spuštění navrácení služeb po obnovení.

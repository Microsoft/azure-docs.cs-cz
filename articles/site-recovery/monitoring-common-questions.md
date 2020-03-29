---
title: Časté otázky týkající se monitorování azure site recovery
description: Získejte odpovědi na běžné otázky týkající se monitorování azure site recovery pomocí integrovanémonitorování a Azure Monitor (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: c1d30a9cdd2cd6ca288edd609a2e2e7bee9174d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68718259"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Časté otázky týkající se monitorování obnovení webu

Tento článek odpovídá na běžné otázky týkající se monitorování [Azure Site Recovery](site-recovery-overview.md), pomocí integrované monitorování webu obnovení a Azure Monitor (Log Analytics).

## <a name="general"></a>Obecné

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Jak se hodnota RPO protokoluje odlišná od nejnovějšího dostupného bodu obnovení?

Site Recovery používá vícekrokový a synchronní proces k replikaci počítačů do Azure.

- V předposledním kroku replikace jsou nedávné změny v počítači spolu s metadaty zkopírovány do účtu úložiště protokolu nebo mezipaměti.
- Tyto změny spolu se značkou, která identifikuje obnovitelný bod, jsou zapsány na účet úložiště nebo spravovaný disk v cílové oblasti.
- Obnovení webu nyní může generovat obnovitelný bod pro počítač.
- V tomto okamžiku rpo byla splněna pro změny nahrané do účtu úložiště tak daleko. Jinými slovy rpo počítače v tomto okamžiku se rovná množství času, který uplynul od časového razítka odpovídající obnovitelného bodu.
- Služba Site Recovery nyní vybere nahraná data z účtu úložiště a použije je na repliky disků vytvořených pro počítač.
- Obnovení webu pak generuje bod obnovení a zpřístupní tento bod pro obnovení při převzetí služeb při selhání.
- Nejnovější dostupný bod obnovení tedy označuje časové razítko odpovídající nejnovějšímu bodu obnovení, který již byl zpracován, a použitý na disky repliky.


Nesprávný systémový čas na replikujícím se zdrojovém počítači nebo na serverech místní infrastruktury zkosí vypočítanou hodnotu RPO. Chcete-li zajistit přesné vykazování rpo, ujistěte se, že systémové hodiny jsou přesné na všech serverech a počítačích.



## <a name="inbuilt-site-recovery-logging"></a>Vestavěné protokolování obnovení webu


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Proč se počet virtuálních bodů v zobrazení infrastruktury úložiště liší od celkového počtu zobrazeného v replikovaných položkách?

Zobrazení infrastruktury úložiště je vymezeno scénáři replikace. Do počtu zobrazení jsou zahrnuty pouze počítače v aktuálně vybraném scénáři replikace. Kromě toho počítáme jenom virtuální počítače, které jsou nakonfigurované pro replikaci do Azure. Převzetí selhání přes počítače nebo počítače replikovat zpět do místního webu, se nepočítají v zobrazení.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Proč se počet replikovaných položek v základních věcech liší od celkového počtu replikovaných položek na řídicím panelu?

Do počtu zobrazeného v části Essentials jsou zahrnuty pouze počítače, pro které byla dokončena počáteční replikace. Celkový počet replikovaných položek zahrnuje všechny počítače v trezoru, včetně těch, pro které právě probíhá počáteční replikace.

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor


### <a name="how-often-does-site-recovery-send-diagnostic-logs-to-azure-monitor-log"></a>Jak často site recovery odesílá diagnostické protokoly do protokolu monitorování Azure? 

- AzureSiteRecoveryReplicationStats a AzureSiteRecoveryRecoveryPoints se odesílají každých 15 minut.  
- AzureSiteRecoveryReplicationDataUploadRate a AzureSiteRecoveryProtectedDiskDataData jsou odesílány každých pět minut. 
- AzureSiteRecoveryJobs je odeslána na aktivační událost a dokončení úlohy.
- AzureSiteRecoveryEvents se odesílá vždy, když je generována událost. 
- AzureSiteRecoveryReplicatedItems se odesílá vždy, když dojde ke změně prostředí. Doba aktualizace dat je obvykle 15 minut po změně. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Jak dlouho jsou data uchována v protokolech Azure Monitoru? 

Ve výchozím nastavení je uchovávání informací po dobu 31 dnů. Období můžete prodloužit v části **Využití a odhadované náklady** v pracovním prostoru Analýzy protokolů. Klikněte na **uchovávání dat**a zvolte rozsah.

### <a name="whats-the-size-of-the-diagnostic-logs"></a>Jaká je velikost diagnostických protokolů? 

Obvykle je velikost protokolu 15-20 KB. 


## <a name="next-steps"></a>Další kroky

Zjistěte, jak monitorovat integrované [monitorování site recovery](site-recovery-monitor-and-troubleshoot.md)nebo Azure [Monitor](monitor-log-analytics.md).



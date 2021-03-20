---
title: Běžné otázky týkající se monitorování Azure Site Recovery
description: Získejte odpovědi na běžné otázky týkající se Azure Site Recovery monitoring, pomocí integrovaného monitorování a Azure Monitor (Log Analytics).
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 07/31/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 66ce267581d4748ea51a3dcbd7caa61907115cc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82131150"
---
# <a name="common-questions-about-site-recovery-monitoring"></a>Běžné otázky týkající se monitorování Site Recovery

Tento článek obsahuje odpovědi na běžné dotazy týkající se monitorování Azure [Site Recovery](site-recovery-overview.md), použití předem připraveného Site Recovery monitoringu a Azure Monitor (Log Analytics).

## <a name="general"></a>Obecné

### <a name="how-is-the-rpo-value-logged-different-from-the-latest-available-recovery-point"></a>Jak se protokol RPO liší od posledního dostupného bodu obnovení?

Site Recovery používá k replikaci počítačů do Azure více kroků asynchronní proces.

- V předposledním kroku replikace se poslední změny v počítači, společně s metadaty, zkopírují do účtu úložiště protokolu nebo mezipaměti.
- Tyto změny, spolu se značkou, která identifikuje obnovitelný bod, se zapisují do účtu úložiště nebo spravovaného disku v cílové oblasti.
- Site Recovery teď může pro tento počítač vygenerovat obnovitelný bod.
- V tomto okamžiku bylo dosaženo cíle bodu obnovení, aby se změny nahrály do účtu úložiště ještě daleko. Jinými slovy, plán RPO se v tomto okamžiku rovná času, který uplyne z časového razítka odpovídající obnovitelnému bodu.
- Nyní Site Recovery nahraná data z účtu úložiště přebírá a použije je na disky repliky vytvořené pro daný počítač.
- Site Recovery pak vygeneruje bod obnovení a zpřístupní ho k obnovení při převzetí služeb při selhání.
- Proto Nejnovější dostupný bod obnovení indikuje časové razítko odpovídající nejnovějšímu bodu obnovení, který již byl zpracován a který je použit na disky repliky.


Nesprávná systémová doba v replikačním zdrojovém počítači, nebo na místních serverech infrastruktury, bude počítat vypočítanou hodnotu RPO. Pro přesné vytváření sestav RPO se ujistěte, že jsou systémové hodiny přesné na všech serverech a počítačích.



## <a name="inbuilt-site-recovery-logging"></a>Protokolování sestavení Site Recovery


### <a name="why-is-the-vm-count-in-the-vault-infrastructure-view-different-from-the-total-count-shown-in-replicated-items"></a>Proč se počet virtuálních počítačů v zobrazení infrastruktury trezoru liší od celkového počtu zobrazeného v replikovaných položkách?

Zobrazení infrastruktury trezoru je vymezené scénáři replikace. Do počtu pro zobrazení jsou zahrnuté jenom počítače v aktuálně vybraném scénáři replikace. Kromě toho počítáme jenom virtuální počítače, které jsou nakonfigurované na replikaci do Azure. Počítače, které převezmou služby při selhání, nebo počítače, které se replikují zpátky na místní lokalitu, se v zobrazení nezapočítávají.

### <a name="why-is-the-count-of-replicated-items-in-essentials-different-from-the-total-count-of-replicated-items-on-the-dashboard"></a>Proč se počet replikovaných položek v Essentials liší od celkového počtu replikovaných položek na řídicím panelu?

Do počtu zobrazeného v základní části jsou zahrnuté jenom počítače, pro které se počáteční replikace dokončila. Celkový počet replikovaných položek zahrnuje všechny počítače v trezoru, včetně těch, pro které aktuálně probíhá počáteční replikace.

## <a name="azure-monitor-logging"></a>Protokolování Azure Monitor


### <a name="how-often-does-site-recovery-send-resource-logs-to-azure-monitor-log"></a>Jak často Site Recovery odesílat protokoly prostředků do protokolu Azure Monitor? 

- AzureSiteRecoveryReplicationStats a AzureSiteRecoveryRecoveryPoints se odesílají každých 15 minut.  
- AzureSiteRecoveryReplicationDataUploadRate a AzureSiteRecoveryProtectedDiskDataChurn se odesílají každých pět minut. 
- AzureSiteRecoveryJobs se odesílá na Trigger a dokončuje úlohu.
- AzureSiteRecoveryEvents se odesílá pokaždé, když se vygeneruje událost. 
- AzureSiteRecoveryReplicatedItems se odesílá vždy, když dojde ke změně prostředí. Čas aktualizace dat je typicky 15 minut po změně. 

### <a name="how-long-is-data-kept-in-azure-monitor-logs"></a>Jak dlouho se data uchovávají v protokolech Azure Monitor? 

Ve výchozím nastavení je uchovávání po dobu 31 dnů. Období můžete zvýšit v části **využití a odhadované náklady** v pracovním prostoru Log Analytics. Klikněte na **uchovávání dat** a vyberte rozsah.

### <a name="whats-the-size-of-the-resource-logs"></a>Jaká je velikost protokolů prostředků? 

Obvykle je velikost protokolu 15-20 KB. 


## <a name="next-steps"></a>Další kroky

Naučte se monitorovat pomocí [Site Recovery integrovaného monitorování](site-recovery-monitor-and-troubleshoot.md)nebo [Azure monitor](monitor-log-analytics.md).



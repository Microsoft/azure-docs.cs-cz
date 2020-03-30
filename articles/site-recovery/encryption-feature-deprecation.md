---
title: Vyřazení funkce šifrování dat Azure Site Recovery | Dokumenty společnosti Microsoft
description: Podrobnosti regarig Azure Site Recovery funkce šifrování dat
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134993"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Vyřazení funkce šifrování dat obnovení lokality

Tento dokument popisuje podrobnosti o vyřazení a nápravnou akci, kterou je třeba provést, pokud používáte funkci šifrování dat obnovení webu při konfiguraci zotavení po havárii virtuálních počítačů Hyper-V do Azure. 

## <a name="deprecation-information"></a>Informace o vyřazení


Funkce šifrování dat obnovení webu byla k dispozici zákazníkům, kteří chrání virtuální počítače Hyper-V, aby bylo zajištěno, že replikovaná data byla chráněna před bezpečnostními hrozbami. **prosince 2019**bude tato funkce zastaralá . Je nahrazován pokročilejší funkcí [Šifrování v klidovém stavu,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) která používá [šifrování služby úložiště](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). S SSE, data se zašifruje před uchování do úložiště a dešifrovat při načítání a po převzetí služeb při selhání do Azure, vaše virtuální počítače se spustí z účtů šifrovaného úložiště, což umožňuje lepší cíl doby obnovení (RTO).

Vezměte prosím na vědomí, že pokud jste stávající zákazník, který používá tuto funkci, měli byste obdržet komunikaci s podrobnostmi o vyřazení a kroky nápravy. 


## <a name="what-are-the-implications"></a>Jaké jsou důsledky?

**prosince 2019**nebude povoleno provádět převzetí služeb při selhání žádné virtuální ms, které stále používají funkci vyřazeného šifrování. 

## <a name="required-action"></a>Požadovaná akce
Chcete-li pokračovat v úspěšných operacích převzetí služeb při selhání a replikace postupujte podle následujících kroků:

Postupujte takto pro každý virtuální virtuální mísu: 
1.  [Zakázat replikaci](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Vytvořte novou zásadu replikace](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Povolte replikaci](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) a vyberte účet úložiště s povolenou funkcí SSE.

Po dokončení počáteční replikace na účty úložiště s povolenou službou SSE budou virtuální počítače používat šifrování v klidovém stavu s obnovením webu Azure.


## <a name="next-steps"></a>Další kroky
Naplánujte kroky nápravy a proveďte je nejdříve. V případě, že máte nějaké dotazy týkající se tohoto vyřazení, obraťte se na podporu společnosti Microsoft. Další informace o scénáři Technologie Hyper-V až Azure naleznete [zde](hyper-v-vmm-architecture.md).


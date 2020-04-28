---
title: Vyřazení funkce šifrování dat Azure Site Recovery | Microsoft Docs
description: Podrobnosti funkce šifrování dat regarig Azure Site Recovery
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74134993"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Vyřazení funkce šifrování dat Site Recovery

Tento dokument popisuje podrobnosti vyřazení a nápravné akce, které je třeba provést, pokud používáte funkci šifrování dat Site Recovery při konfiguraci zotavení po havárii virtuálních počítačů Hyper-V do Azure. 

## <a name="deprecation-information"></a>Informace o zastaralosti


Funkce šifrování dat Site Recovery byla k dispozici pro zákazníky, kteří chrání virtuální počítače Hyper-V, aby bylo zajištěno, že byla replikovaná data chráněna před hrozbami zabezpečení. Tato funkce bude zastaralá od **30. prosince 2019**. V rámci funkce REST, která používá [šifrování služby Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE), ji nahrazujete pokročilým [šifrováním](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) . Pomocí SSE se data šifrují před tím, než se uloží do úložiště a dešifrují při načtení, a po převzetí služeb při selhání do Azure se vaše virtuální počítače spustí ze zašifrovaných účtů úložiště, což vám umožní zlepšit cíl doby obnovení (RTO).

Upozorňujeme, že pokud jste stávající zákazník používající tuto funkci, měli byste obdržet komunikaci s podrobnostmi o vyřazení a nápravných krocích. 


## <a name="what-are-the-implications"></a>Jaké jsou důsledky?

Po **30. prosinci 2019**nebude možné provést převzetí služeb při selhání všemi virtuálními počítači, které pořád používají vyřazení šifrovací funkce. 

## <a name="required-action"></a>Požadovaná akce
Pokud chcete pokračovat v úspěšných operacích převzetí služeb při selhání a replikace, postupujte podle níže uvedených kroků:

Pro každý virtuální počítač použijte tento postup: 
1.  [Zakažte replikaci](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Vytvořte novou zásadu replikace](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Povolte replikaci](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) a vyberte účet úložiště s povoleným SSE.

Po dokončení počáteční replikace do účtů úložiště s povoleným SSE budou vaše virtuální počítače používat šifrování v klidovém prostředí Azure Site Recovery.


## <a name="next-steps"></a>Další kroky
Naplánujte provedení nápravných kroků a nejdříve je spusťte. Pokud máte dotazy týkající se tohoto vyřazení, obraťte se na podpora Microsoftu. Další informace o scénáři technologie Hyper-V do Azure najdete v [tomto](hyper-v-vmm-architecture.md)článku.


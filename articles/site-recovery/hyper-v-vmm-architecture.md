---
title: Obnova po havárii technologie Architecture-Hyper-V do sekundární lokality s azure site recovery
description: Tento článek obsahuje přehled architektury pro zotavení po havárii místních virtuálních počítačích Hyper-V do sekundárního webu VMM system center s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 3e81e353d2912f56a932ce118a0424e45e758df7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133011"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura – replikace hyper-V do sekundární lokality

Tento článek popisuje komponenty a procesy využívané při replikaci místních virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality VMM s využitím služby [Azure Site Recovery](site-recovery-overview.md) na webu Azure Portal.
a

## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika poskytují zobrazení na vysoké úrovni součástí používaných pro replikaci technologie Hyper-V do sekundární lokality.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure | V předplatném Azure vytvoříte trezor služby Recovery Services sloužící k orchestraci a správě replikace mezi umístěními VMM.
**Server VMM** | Potřebujete primární a sekundární umístění VMM. | Doporučujeme jeden server VMM v primární lokalitě a další v sekundární lokalitě.
**Server Hyper-V** |  Jeden nebo několik hostitelských serverů Hyper-V v primárním a sekundárním cloudu VMM. | Data se replikují mezi primárním a sekundárním hostitelským serverem Hyper-V přes síť LAN nebo VPN na základě protokolu Kerberos nebo ověření certifikátem.  
**Virtuální počítače Hyper-V** | Na hostitelském serveru Hyper-V. | Zdrojový hostitelský server musí mít alespoň jeden virtuální počítač, který chcete replikovat.

**Místní architektura**

![Z lokálního prostředí do lokálního prostředí](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikace

1. Při spuštění počáteční replikace je pořízen [snímek snímku virtuálního počítače Hyper-V.](https://technet.microsoft.com/library/dd560637.aspx)
2. Virtuální pevné disky na virtuálním počítači jsou replikovány jeden po druhém, do sekundárního umístění.
3. Pokud dojde ke změnám disku během počáteční replikace, nástroj Sledování repliky replik y hyperv sleduje změny jako protokoly replikace Hyper-V (.hrl). Tyto soubory protokolu jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor HRL, který je odeslán do sekundárního umístění. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se odstraní snímek virtuálního počítače a začne rozdílová replikace.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

- Můžete převzít službu při selhání jednoho počítače nebo vytvořit plány zotavení a orchestrovat převzetí služeb při selhání více počítačů.
- Můžete spustit plánované nebo neplánované převzetí služeb při selhání mezi místními lokalitami. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Pokud provedete neplánované převzetí služeb při selhání do sekundární lokality, po selhání počítače v sekundárním umístění nejsou chráněny.
    - Pokud jste spustili plánované převzetí služeb při selhání, počítače v sekundárním umístění chráněné budou.
- Po spuštění počáteční převzetí služeb při selhání, popotazní, chcete-li spustit přístup k zatížení z virtuálního počítače repliky.
- Pokud je primární umístění opět k dispozici, můžete ji vrátit zpět.
    - Zahájíte zpětnou replikaci a zahájíte replikaci ze sekundární lokality do primární. Po zpětné replikaci budou virtuální počítače v chráněném stavu, ale sekundární datové centrum bude stále aktivním umístěním.
    - Chcete-li z primární lokality opět udělat aktivní, zahajte plánované převzetí služeb ze sekundární lokality do primární, následované další zpětnou replikací.



## <a name="next-steps"></a>Další kroky


Podle [tohoto kurzu](hyper-v-vmm-disaster-recovery.md) povolte replikaci technologie Hyper-V mezi cloudy VMM.

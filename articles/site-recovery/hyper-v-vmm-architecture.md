---
title: Architektura – zotavení po havárii technologie Hyper-V do sekundární lokality s Azure Site Recovery
description: Tento článek poskytuje přehled architektury pro zotavení po havárii místních virtuálních počítačů Hyper-V do sekundární lokality System Center VMM s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 716f8e053ad05ba701b3415946cd8d3a56044e62
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953985"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Architektura – replikace Hyper-V do sekundární lokality

Tento článek popisuje komponenty a procesy využívané při replikaci místních virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality VMM s využitím služby [Azure Site Recovery](site-recovery-overview.md) na webu Azure Portal.

> [!WARNING]
> Upozorňujeme, že podpora ASR pro použití konfigurace SCVMM v účtu bude brzy zastaralá a proto doporučujeme, abyste si před pokračováním přečetli podrobnosti o [zastaralosti](scvmm-site-recovery-deprecation.md) .

## <a name="architectural-components"></a>Komponenty architektury

Následující tabulka a grafika obsahují podrobný pohled na součásti používané pro replikaci Hyper-V do sekundární lokality.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure | V předplatném Azure vytvoříte trezor služby Recovery Services sloužící k orchestraci a správě replikace mezi umístěními VMM.
**Server VMM** | Potřebujete primární a sekundární umístění VMM. | Doporučujeme jeden server VMM v primární lokalitě a další v sekundární lokalitě.
**Server Hyper-V** |  Jeden nebo několik hostitelských serverů Hyper-V v primárním a sekundárním cloudu VMM. | Data se replikují mezi primárním a sekundárním hostitelským serverem Hyper-V přes síť LAN nebo VPN na základě protokolu Kerberos nebo ověření certifikátem.  
**Virtuální počítače Hyper-V** | Na hostitelském serveru Hyper-V. | Zdrojový hostitelský server musí mít alespoň jeden virtuální počítač, který chcete replikovat.

**Místní architektura do místní architektury**

![Z lokálního prostředí do lokálního prostředí](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikace

1. Při spuštění počáteční replikace se pořídí snímek [virtuálního počítače Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) .
2. Virtuální pevné disky virtuálního počítače se replikují po jednom do sekundárního umístění.
3. Pokud dojde ke změnám na disku v době, kdy probíhá počáteční replikace, sledování replikace repliky technologie Hyper-V sleduje změny jako protokoly replikace technologie Hyper-V (. hrl). Tyto soubory protokolu jsou umístěné ve stejné složce jako disky. Každý disk má přidružený soubor. hrl, který se odesílá do sekundárního umístění. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní a spustí se rozdílová replikace.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

- Můžete převzít služby při selhání jednoho počítače nebo vytvořit plány obnovení pro orchestraci převzetí služeb při selhání více počítačů.
- Můžete spustit plánované nebo neplánované převzetí služeb při selhání mezi místními lokalitami. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Pokud provedete neplánované převzetí služeb při selhání sekundární lokalitou, poté, co jsou počítače s podporou převzetí služeb při selhání v sekundárním umístění chráněné
    - Pokud jste spustili plánované převzetí služeb při selhání, počítače v sekundárním umístění chráněné budou.
- Po počátečním spuštění převzetí služeb při selhání se potvrdili, že budete chtít začít přistupovat k úloze z virtuálního počítače repliky.
- Jakmile bude primární umístění opět k dispozici, můžete provést navrácení služeb po obnovení.
    - Můžete zahájit zpětnou replikaci, aby se spustila replikace ze sekundární lokality na primární. Po zpětné replikaci budou virtuální počítače v chráněném stavu, ale sekundární datové centrum bude stále aktivním umístěním.
    - Chcete-li z primární lokality opět udělat aktivní, zahajte plánované převzetí služeb ze sekundární lokality do primární, následované další zpětnou replikací.



## <a name="next-steps"></a>Další kroky


Postupujte podle [tohoto kurzu](hyper-v-vmm-disaster-recovery.md) a Povolte replikaci Hyper-V mezi cloudy VMM.

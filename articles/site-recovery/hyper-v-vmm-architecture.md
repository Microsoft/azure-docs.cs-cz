---
title: Replikace Hyper-V do sekundární lokality architektury v Azure Site Recovery | Microsoft Docs
description: Tento článek obsahuje přehled architektury pro replikaci místních virtuálních počítačů Hyper-V do sekundární lokality System Center VMM s využitím služby Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 39a397edd17327a91882535fbd00222a4ae4dddc
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894292"
---
# <a name="hyper-v-replication-to-a-secondary-site"></a>Replikaci technologie Hyper-V do sekundární lokality

Tento článek popisuje komponenty a procesy využívané při replikaci místních virtuálních počítačů Hyper-V v cloudech System Center Virtual Machine Manager (VMM) do sekundární lokality VMM s využitím služby [Azure Site Recovery](site-recovery-overview.md) na webu Azure Portal.


## <a name="architectural-components"></a>Komponenty architektury

Následující obrázek a tabulka poskytují souhrnné zobrazení komponenty používané pro replikaci technologie Hyper-V do sekundární lokality.

**Komponenta** | **Požadavek** | **Podrobnosti**
--- | --- | ---
**Azure** | Předplatné Azure | V předplatném Azure vytvoříte trezor služby Recovery Services sloužící k orchestraci a správě replikace mezi umístěními VMM.
**Server VMM** | Potřebujete primární a sekundární umístění VMM. | Doporučujeme jeden server VMM v primární lokalitě a další v sekundární lokalitě.
**Server Hyper-V** |  Jeden nebo několik hostitelských serverů Hyper-V v primárním a sekundárním cloudu VMM. | Data se replikují mezi primárním a sekundárním hostitelským serverem Hyper-V přes síť LAN nebo VPN na základě protokolu Kerberos nebo ověření certifikátem.  
**Virtuální počítače Hyper-V** | Na hostitelském serveru Hyper-V. | Zdrojový hostitelský server musí mít alespoň jeden virtuální počítač, který chcete replikovat.

**Místní místní architektury**

![Z lokálního prostředí do lokálního prostředí](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Proces replikace

1. Když se aktivuje počáteční replikace [snímku virtuálního počítače technologie Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) pořízení snímku.
2. Virtuální pevné disky na virtuální počítač se replikují jeden po druhém, do sekundárního umístění.
3. Pokud probíhá počáteční replikace, dojde ke změnám disku 
4. Po dokončení počáteční replikace začne rozdílová replikace. Technologie Hyper-V Replica Replication Tracker sleduje změny jako protokoly replikace technologie Hyper-V (.hrl). Tyto protokolové soubory jsou umístěny ve stejné složce jako disky. Každý disk má přidružený soubor .hrl odeslaný do sekundárního umístění. Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
5. Rozdílové změny na disku v protokolu se synchronizují a sloučí s nadřazeným diskem.


## <a name="failover-and-failback-process"></a>Proces převzetí služeb při selhání a navrácení služeb po obnovení

- Můžete převzít jeden počítač nebo vytvořit plány obnovení, orchestraci převzetí služeb při selhání více počítačů.
- Můžete spustit plánovaném nebo neplánovaném převzetí služeb při selhání mezi místními lokalitami. Pokud spustíte plánovanou operaci, dojde k ukončení zdrojových virtuálních počítačů, aby se zcela předešlo možné ztrátě dat.
    - Pokud neplánované převzetí služeb při selhání pro sekundární lokalitu, můžete provést po převzetí služeb při selhání počítače v sekundárním umístění nejsou chráněné.
    - Pokud jste spustili plánované převzetí služeb při selhání, počítače v sekundárním umístění chráněné budou.
- Po spuštění počáteční převzetí služeb při selhání předat ji spustit, přístup k zatížení z virtuálního počítače repliky.
- Když primární umístění opět k dispozici, může selhat zpět.
    - Zahájení zpětná replikace, do zahájení na primární replikace ze sekundární lokality. Po zpětné replikaci budou virtuální počítače v chráněném stavu, ale sekundární datové centrum bude stále aktivním umístěním.
    - Chcete-li z primární lokality opět udělat aktivní, zahajte plánované převzetí služeb ze sekundární lokality do primární, následované další zpětnou replikací.



## <a name="next-steps"></a>Další postup


Postupujte podle [v tomto kurzu](hyper-v-vmm-disaster-recovery.md) povolit replikaci technologie Hyper-V mezi cloudy VMM.

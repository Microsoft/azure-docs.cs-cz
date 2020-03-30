---
title: Obnovení služeb při selhání virtuálnípočítače VMware nebo fyzické servery z Azure s Azure Site Recovery
description: Zjistěte, jak po obnovení služeb při selhání do Azure, během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495322"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Virtuální počítači VMware v zařízení VMware navrácení do místního webu.

Tento článek popisuje, jak poobnovení virtuálních počítačů Azure do místního webu, po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítače do Azure s Azure Site [Recovery](site-recovery-overview.md). Po navrácení služeb po službě nadále do místního povolili replikaci tak, aby místní virtuální počítače začaly replikovat do Azure.

## <a name="before-you-start"></a>Než začnete

1. Další informace o [navrácení služeb po selhání společnosti VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Ujistěte se, že jste zkontrolovali a dokončili kroky pro přípravu na [navrácení služeb po selhání](vmware-azure-prepare-failback.md)a že jsou nasazeny všechny požadované součásti. Mezi součásti patří procesní server v Azure, místní hlavní cílový server a připojení k webu vpn k webu (nebo soukromý partnerský vztah ExpressRoute) pro navrácení služeb po obnovení.
3. Ujistěte se, že jste dokončili [požadavky na](vmware-azure-reprotect.md#before-you-begin) opětovné protekce a navrácení služeb po obnovení a že jste [povolili opětovné protekci](vmware-azure-reprotect.md#enable-reprotection) virtuálních počítačích Azure, aby se replikovaly z Azure do místního webu. Virtuální chody musí být v replikovaném stavu je pořadí poselhání z doporučovaného stavu.




## <a name="run-a-failover-to-fail-back"></a>Spuštění mísení při selhání pro navrácení služeb po selhání

1. Ujistěte se, že virtuální počítače Azure jsou znovu chráněné a replikovat do místního webu. 
    - Virtuální ho virtuálního mě potřebuje alespoň jeden bod obnovení, aby bylo možné je obnovit.
    - Pokud poobnovení plánu obnovení, všechny počítače v plánu by měl mít alespoň jeden bod obnovení.
2. V úschovně > **replikované položky**vyberte virtuální ms. Klikněte pravým tlačítkem myši na virtuální mši > **neplánovanépřevzetí služeb při selhání**.
3. V **části Potvrdit převzetí služeb při selhání**ověřte směr převzetí služeb při selhání (z Azure).
4. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít.
    - Doporučujeme použít **nejnovější** bod obnovení. Bod konzistentní s aplikací je za nejnovější bod v čase a způsobuje ztrátu některých dat.
    - **Poslední** je místo zotavení konzistentní při selhání.
    - S **nejnovější**, virtuální ho disponibilní služby převzetí služby do jeho nejnovější dostupné místo v čase. Pokud máte replikační skupinu pro konzistenci více virtuálních virtuálních podniků v rámci plánu obnovení, každý virtuální server ve skupině převezme služby při selhání do nezávislého posledního bodu v čase.
    - Pokud používáte bod obnovení konzistentní s aplikací, každý virtuální virtuální ms se nezdaří zpět do svého nejnovějšího dostupného bodu. Pokud plán obnovení obsahuje replikační skupinu, každá skupina se obnoví do svého společného dostupného bodu obnovení.
5. Začíná převzetí služeb při selhání. Site Recovery vypne virtuální počítače Azure.
6. Po dokončení převzetí služeb při selhání zkontrolujte, zda vše funguje podle očekávání. Zkontrolujte, že virtuální počítače Azure jsou vypnuté. 
7. Se všemi ověřenými klikněte pravým tlačítkem myši na > **potvrzení**virtuálního měna a dokončete proces převzetí služeb při selhání. Potvrzení odebere virtuální počítač Azure s převzetím služby, který se nezdařil. 

> [!NOTE]
> Pro virtuální servery Windows site recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb po služby napo dobu popřevzetí služeb při vzpomenete na virtuálním mísu systému Windows, nástroje VMware jsou znovu povolit. 




## <a name="reprotect-from-on-premises-to-azure"></a>Opětovné zabezpečení z místního prostředí do Azure

Po potvrzení navrácení služeb po selhání se virtuální počítače Azure odstraní. Virtuální počítač je zpátky v místním webu, ale není chráněný. Pokud chcete znovu začít replikovat virtuální počítače do Azure, postupujte takto:

1. V úschovně > **replikované položky**vyberte neúspěšné zadní virtuální aplikace a pak vyberte **Znovu chránit**.
2. Zadejte procesní server, který se používá k odesílání dat zpět do Azure.
3. Chcete-li zahájit úlohu opětovného ochrany, vyberte **možnost OK.**

> [!NOTE]
> Po spuštění místního virtuálního počítače trvá až 15 minut, než se agent zaregistruje zpět na konfigurační server. Během této doby znovu protect selže a vrátí chybovou zprávu oznamující, že agent není nainstalován. Pokud k tomu dojde, počkejte několik minut a znovu jej chraňte.

## <a name="next-steps"></a>Další kroky

Po dokončení úlohy znovu se místní virtuální počítač replikuje do Azure. Podle potřeby můžete [spustit další převzetí služeb při selhání](site-recovery-failover.md) do Azure.


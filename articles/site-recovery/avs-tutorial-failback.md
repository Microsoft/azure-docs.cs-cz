---
title: Navrácení služeb po obnovení Azure VMware VMsfrom Azure pomocí Azure Site Recovery
description: Po převzetí služeb při selhání do Azure během zotavení po havárii se dozvíte, jak navrátit se do privátního cloudu řešení Azure VMware.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91814554"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Navrácení virtuálních počítačů do privátního cloudu řešení Azure VMware

Tento článek popisuje, jak navrácení služeb po obnovení virtuálních počítačů Azure do privátního cloudu řešení Azure VMware po [převzetí služeb při selhání](avs-tutorial-failover.md) virtuálních počítačů řešení Azure VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Po navrácení služeb po obnovení Povolte replikaci, aby se virtuální počítače řešení Azure VMware začaly replikovat do Azure.

## <a name="before-you-start"></a>Než začnete

1. Přečtěte si o [navrácení služeb po obnovení VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Ujistěte se, že jste provedli revizi a dokončili kroky pro [navrácení služeb po obnovení](vmware-azure-prepare-failback.md)a že jsou nasazeny všechny požadované součásti. Mezi součásti patří procesový Server v Azure, hlavní cílový server a připojení VPN typu Site-to-Site (nebo privátní partnerské vztahy ExpressRoute) pro navrácení služeb po obnovení.
3. Ujistěte se, že jste dokončili [požadavky](avs-tutorial-reprotect.md#before-you-begin) na přeochranu a navrácení služeb po obnovení a že jste [povolili Přeochranu](avs-tutorial-reprotect.md#enable-reprotection) virtuálních počítačů Azure, aby se replikují z Azure do privátního cloudu řešení Azure VMware. Virtuální počítače musí být v replikovaném stavu, což má za následek navrácení služeb po obnovení.




## <a name="run-a-failover-to-fail-back"></a>Spuštění převzetí služeb při selhání pro navrácení služeb po obnovení

1. Ujistěte se, že virtuální počítače Azure jsou znovu chráněné a jsou replikovány do privátního cloudu řešení Azure VMware.
    - Aby virtuální počítač mohl navrácení služeb po obnovení, musí mít aspoň jeden bod obnovení.
    - Pokud naplánujete navrácení služeb při selhání plánu obnovení, musí mít všechny počítače v plánu alespoň jeden bod obnovení.
2. V trezoru > **replikované položky**vyberte virtuální počítač. Klikněte pravým tlačítkem na virtuální počítač > **neplánované převzetí služeb při selhání**.
3. V části potvrzení převzetí **služeb při selhání**ověřte směr převzetí služeb při selhání (z Azure).
4. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít.
    - Doporučujeme použít **nejnovější** bod obnovení. Bod konzistentní vzhledem k aplikacím je za posledním bodem v čase a způsobuje ztrátu dat.
    - **Nejnovější** je bod obnovení konzistentní vzhledem k selháním.
    - V **nejnovější verzi**se virtuální počítač převezme k nejnovějšímu dostupnému bodu v čase. Pokud máte replikační skupinu pro konzistenci s více virtuálními počítači v rámci plánu obnovení, každý virtuální počítač ve skupině převezme služby při selhání k nezávislému nejnovějšímu bodu v čase.
    - Pokud používáte bod obnovení konzistentní vzhledem k aplikacím, každý virtuální počítač se vrátí k nejnovějšímu dostupnému bodu. Pokud má plán obnovení replikační skupinu, každá skupina se obnoví do jejího společného dostupného bodu obnovení.
5. Začíná převzetí služeb při selhání. Site Recovery vypne virtuální počítače Azure.
6. Po dokončení převzetí služeb při selhání ověřte, že vše funguje podle očekávání. Ověřte, že jsou virtuální počítače Azure vypnuté. 
7. Po ověření všeho klikněte pravým tlačítkem myši na virtuální počítač > **potvrzení**, čímž se dokončí proces převzetí služeb při selhání. Potvrzení odebere virtuální počítač Azure, u kterého došlo k převzetí služeb při selhání. 

> [!NOTE]
> U virtuálních počítačů s Windows Site Recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb po obnovení virtuálního počítače s Windows se znovu povolí nástroje VMware. 




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Opětovné zapnutí ochrany z řešení Azure VMware do Azure

Po potvrzení navrácení služeb po obnovení dojde k odstranění virtuálních počítačů Azure. Virtuální počítač je zpátky v privátním cloudu řešení Azure VMware, ale není chráněný. Pokud chcete znovu začít replikovat virtuální počítače do Azure, postupujte takto:

1. V trezoru > **replikované položky**vyberte virtuální počítače, u kterých došlo k chybě, a pak vyberte **znovu nastavit ochranu**.
2. Zadejte procesový Server, který se používá k odesílání dat zpět do Azure.
3. Výběrem **OK** spusťte úlohu opětovného zapnutí ochrany.

> [!NOTE]
> Po spuštění virtuálního počítače se službou Azure VMware může trvat až 15 minut, než se agent zaregistruje zpátky na konfigurační server. Během této doby se znovu spustí ochrana a vrátí chybovou zprávu s oznámením, že agent není nainstalovaný. Pokud k tomu dojde, počkejte několik minut a znovu nastavte ochranu.

## <a name="next-steps"></a>Další kroky

Po dokončení úlohy opětovné ochrany se virtuální počítač řešení Azure VMware replikuje do Azure. Podle potřeby můžete [Spustit jiné převzetí služeb při selhání](avs-tutorial-failover.md) do Azure.


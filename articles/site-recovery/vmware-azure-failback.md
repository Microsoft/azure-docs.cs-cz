---
title: Převzetí služeb při selhání virtuálních počítačů VMware nebo fyzických serverů z Azure pomocí Azure Site Recovery
description: Přečtěte si, jak po převzetí služeb při selhání do Azure přejít zpátky na místní lokalitu během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004190"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Navrácení služeb po obnovení virtuálních počítačů VMware do místní lokality

Tento článek popisuje, jak navrácení služeb po obnovení virtuálních počítačů Azure do místní lokality po [převzetí služeb při selhání](site-recovery-failover.md) místních virtuálních počítačů do Azure pomocí [Azure Site Recovery](site-recovery-overview.md). Po navrácení služeb po obnovení do místního prostředí povolíte replikaci, aby se místní virtuální počítače začaly replikovat do Azure.

## <a name="before-you-start"></a>Než začnete

1. Přečtěte si o [navrácení služeb po obnovení VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Ujistěte se, že jste provedli revizi a dokončili kroky pro [navrácení služeb po obnovení](vmware-azure-prepare-failback.md)a že jsou nasazeny všechny požadované součásti. Mezi součásti patří procesový Server v Azure, místní hlavní cílový server a připojení VPN typu Site-to-Site (nebo privátní partnerské vztahy ExpressRoute) pro navrácení služeb po obnovení.
3. Ujistěte se, že jste dokončili [požadavky](vmware-azure-reprotect.md#before-you-begin) na opětovné provedení ochrany a navrácení služeb po obnovení a že jste [povolili ochranu](vmware-azure-reprotect.md#enable-reprotection) virtuálních počítačů Azure, aby se z Azure replikují z Azure do místní lokality. Virtuální počítače musí být v replikovaném stavu, což má za následek navrácení služeb po obnovení.




## <a name="run-a-failover-to-fail-back"></a>Spuštění převzetí služeb při selhání pro navrácení služeb po obnovení

1. Ujistěte se, že virtuální počítače Azure jsou znovu chráněné a jsou replikovány na místní lokalitu.
    - Aby virtuální počítač mohl navrácení služeb po obnovení, musí mít aspoň jeden bod obnovení.
    - Pokud naplánujete navrácení služeb při selhání plánu obnovení, musí mít všechny počítače v plánu alespoň jeden bod obnovení.
2. V trezoru > **replikované položky** vyberte virtuální počítač. Klikněte pravým tlačítkem na virtuální počítač > **neplánované převzetí služeb při selhání**.
3. V části potvrzení převzetí **služeb při selhání** ověřte směr převzetí služeb při selhání (z Azure).
4. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít.
    - Doporučujeme použít **nejnovější** bod obnovení. Bod konzistentní vzhledem k aplikacím je za posledním bodem v čase a způsobuje ztrátu dat.
    - **Nejnovější** je bod obnovení konzistentní vzhledem k selháním.
    - V **nejnovější verzi** se virtuální počítač převezme k nejnovějšímu dostupnému bodu v čase. Pokud máte replikační skupinu pro konzistenci s více virtuálními počítači v rámci plánu obnovení, každý virtuální počítač ve skupině převezme služby při selhání k nezávislému nejnovějšímu bodu v čase.
    - Pokud používáte bod obnovení konzistentní vzhledem k aplikacím, každý virtuální počítač se vrátí k nejnovějšímu dostupnému bodu. Pokud má plán obnovení replikační skupinu, každá skupina se obnoví do jejího společného dostupného bodu obnovení.
5. Začíná převzetí služeb při selhání. Site Recovery vypne virtuální počítače Azure.
6. Po dokončení převzetí služeb při selhání ověřte, že vše funguje podle očekávání. Ověřte, že jsou virtuální počítače Azure vypnuté. 
7. Po ověření všeho klikněte pravým tlačítkem myši na virtuální počítač > **potvrzení**, čímž se dokončí proces převzetí služeb při selhání. Potvrzení odebere virtuální počítač Azure, u kterého došlo k převzetí služeb při selhání. 

> [!NOTE]
> U virtuálních počítačů s Windows Site Recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb po obnovení virtuálního počítače s Windows se znovu povolí nástroje VMware. 




## <a name="reprotect-from-on-premises-to-azure"></a>Opětovné zapnutí ochrany z místního prostředí do Azure

Po potvrzení navrácení služeb po obnovení dojde k odstranění virtuálních počítačů Azure. Virtuální počítač je zpátky v místní lokalitě, ale není chráněný. Pokud chcete znovu začít replikovat virtuální počítače do Azure, postupujte takto:

1. V trezoru > **replikované položky** vyberte virtuální počítače, u kterých došlo k chybě, a pak vyberte **znovu nastavit ochranu**.
2. Zadejte procesový Server, který se používá k odesílání dat zpět do Azure.
3. Výběrem **OK** spusťte úlohu opětovného zapnutí ochrany.

> [!NOTE]
> Po spuštění místního virtuálního počítače zabere agent až 15 minut, než se agent vrátí zpátky na konfigurační server. Během této doby se znovu spustí ochrana a vrátí chybovou zprávu s oznámením, že agent není nainstalovaný. Pokud k tomu dojde, počkejte několik minut a znovu nastavte ochranu.

## <a name="next-steps"></a>Další kroky

Po dokončení úlohy opětovné ochrany se místní virtuální počítač replikuje do Azure. Podle potřeby můžete [Spustit jiné převzetí služeb při selhání](site-recovery-failover.md) do Azure.


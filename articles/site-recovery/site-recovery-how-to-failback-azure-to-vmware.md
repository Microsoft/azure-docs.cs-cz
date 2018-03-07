---
title: "Postup zpět z Azure se nepodařilo VMware | Microsoft Docs"
description: "Po převzetí služeb při selhání virtuálních počítačů do Azure můžete zahájit navrácení služeb po obnovení a dovést tak virtuální počítače zpět do místní. Přečtěte si postup pro navrácení služeb po obnovení."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: nisoneji
ms.openlocfilehash: 50dccf6196b7affd3d21087f851b929d0e850f6d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Selhání zpět z Azure do místního serveru

Tento článek popisuje, jak selhání virtuální počítače zpět virtuálních počítačů z Azure do místního prostředí VMware. Postupujte podle pokynů v tomto článku selhání zálohování vaše virtuální počítače VMware nebo Windows nebo Linuxem fyzických serverů po jste při selhání z místní lokality do Azure pomocí [převzetí služeb při selhání ve službě Site Recovery](site-recovery-failover.md) kurzu.

## <a name="prerequisites"></a>Požadavky
- Ujistěte se, že jste četli podrobnosti o [různé typy navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídající upozornění.

> [!WARNING]
> Až budete mít buď není možné navrácení služeb po obnovení [dokončit migraci](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), přesunout virtuální počítač do jiné skupiny prostředků nebo odstranit virtuální počítač Azure. Pokud zakážete ochrany virtuálního počítače, nemůžete navrácení služeb po obnovení.

> [!WARNING]
> Fyzický server Windows Server 2008 R2 SP1, pokud ochranu a převzetí služeb při selhání do Azure, nemůže se zpět.

> [!NOTE]
> Pokud jste při selhání virtuálních počítačů VMware nemůžete navrácení služeb po obnovení na hostitele Hyper-v.


- Než budete pokračovat, proveďte kroky opětovné ochrany tak, aby virtuální počítače jsou ve stavu, replikované a můžete zahájit převzetí služeb při selhání zpátky do místního webu. Další informace najdete v tématu [postupy k nastavení opětné z Azure do místní](site-recovery-how-to-reprotect.md).

- Ujistěte se, že se vCenter je v připojeném stavu před provedením navrácení služeb po obnovení. V opačném odpojení disky a znovu je připojit zpět k virtuálnímu počítači nezdaří.

- Během převzetí služeb při selhání do Azure, nemusí být dostupný na místní lokalitu a proto konfigurační server může být buď není k dispozici nebo vypnutí. Během opětovné ochrany a navrácení služeb po obnovení by měla být na místním serveru configuration spuštěná a v připojeném stavu OK. 

- Během navrácení služeb po obnovení virtuální počítač, musí existovat v konfigurační databázi serveru, nebo navrácení služeb po obnovení nebude úspěšné. Proto se ujistěte, že pořídíte pravidelně naplánované zálohování serveru. Pokud dojde havárii, budete muset obnovit server s původní IP adresu pro navrácení služeb po obnovení fungovat.

- Hlavní cílový server by neměl mít všechny snímky před spuštěním opětovné ochrany nebo navrácení služeb po obnovení.

## <a name="overview-of-failback"></a>Přehled o navrácení služeb po obnovení
Až budete mít převzetí služeb při selhání do Azure, můžete se nezdaří zpět do místního serveru spuštěním následující kroky:

1. [Znovu nastavte ochranu](site-recovery-how-to-reprotect.md) virtuální počítače na Azure tak, aby jejich spuštění k replikaci pro virtuální počítače VMware ve vaší místní lokalitě. Jako součást tohoto procesu musíte také:
    1. Nastavit místní hlavní cílový: Windows hlavnímu cíli pro virtuální počítače s Windows a [hlavního cíle Linuxu](site-recovery-how-to-install-linux-master-target.md) pro virtuální počítače s Linuxem.
    2. Nastavení [procesový server](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Zahájit [znovu nastavte ochranu](site-recovery-how-to-reprotect.md). Se vypnout virtuální počítač na místě a synchronizaci dat Azure virtuálního počítače s místními disky.

1. Jakmile jsou virtuální počítače na platformě Azure replikace do místního serveru, spustíte selhání přes z Azure k místní lokalitě.

1. Po vašich dat se nezdařila zpět, je znovu nastavte ochranu místní virtuální počítače znovu tak, aby jejich spuštění replikaci do Azure.

Pro rychlý přehled najdete v následujícím videu o tom, aby v případě selhání zpět na místní lokalitu.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroky pro navrácení služeb po obnovení

> [!IMPORTANT]
> Před spuštěním navrácení služeb po obnovení, ujistěte se, že jste dokončili vytvoření virtuálních počítačů. Virtuální počítače musí být v chráněném stavu, a jejich stavu by měl být **OK**. Chcete-li znovu nastavte ochranu virtuálních počítačů, přečtěte si [postupy k nastavení opětné](site-recovery-how-to-reprotect.md).

1. Na stránce replikované položky vyberte virtuální počítač a klikněte pravým tlačítkem ji vyberte **neplánované převzetí služeb při selhání**.
2. V **potvrzení převzetí služeb při selhání**, ověřte směr převzetí služeb při selhání (z Azure) a pak vyberte bod obnovení (nejnovější nebo konzistentní nejnovější aplikace), kterou chcete použít převzetí služeb při selhání. Bod konzistentní aplikace je za do nejnovějšího bodu v čase a způsobí ztrátu dat.
3. Během převzetí služeb při selhání Site Recovery vypne virtuální počítače na platformě Azure. Po můžete zkontrolovat, že tento navrácení služeb po obnovení je dokončená, podle očekávání, můžete zkontrolovat, že virtuální počítače na Azure byla vypnuta.
4. **Potvrdit** je potřeba odebrat neúspěšný přes virtuální počítač z Azure.Right kliknutím chráněné položky a pak klikněte na **potvrdit**. Úlohu odebere neúspěšný přes virtuální počítače v Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Jaké bodu obnovení můžete I navrácení služeb po obnovení virtuálních počítačů?

Během navrácení služeb po obnovení máte dvě možnosti pro navrácení služeb po obnovení virtuálního počítače nebo obnovení plánu.

- Pokud vyberete nejnovější zpracované bodu v čase, všechny virtuální počítače převezme služby při selhání k jejich nejnovějšímu dostupnému bodu v čase. V případě, že je replikační skupiny v rámci plánu obnovení, pak každý virtuální počítač z replikační skupiny se převzetí služeb při selhání jeho nezávislé nejnovějšího bodu v čase.

    Virtuální počítač nelze označit zpět, dokud má alespoň jeden bod obnovení. Plán obnovení nelze označit zpět, dokud všechny virtuální počítače mít alespoň jeden bodu obnovení.

> [!NOTE]
> Nejnovější bod obnovení je bod obnovení konzistentní při selhání.

- Pokud vyberete bod obnovení konzistentních s aplikací, navrácení jediného virtuálního počítače obnoví jeho nejnovějšího bodu obnovení k dispozici konzistentních s aplikací. V případě plán obnovení s replikační skupinou každá skupina replikace obnoví na jeho společný bod obnovení.
Body obnovení konzistentní s aplikací může být v čase a může být ztráty dat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co se stane VMware nástroje post navrácení služeb po obnovení?

V případě virtuálního počítače s Windows Azure Site Recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb po obnovení virtuálního počítače s Windows jsou nástroje VMware znovu zapnout. 


## <a name="reprotect-from-on-premises-to-azure"></a>Nastavte znovu z místní do Azure
Po dokončení navrácení služeb po obnovení a Inicializovali jste potvrzení obnovené virtuální počítače v Azure jsou odstraněny. Teď je virtuální počítač zpět na místní lokality, ale nebudou chráněné. Pokud chcete spustit znovu replikovat do Azure, postupujte takto:

1. V **trezoru** > **nastavení** > **replikované položky**, vyberte virtuální počítače, které selhaly zpět a pak klikněte na tlačítko **znovu nastavit ochranu**.
2. Zadejte hodnotu procesového serveru, které se musí použít k odesílání dat zpět do Azure.
3. Klikněte na tlačítko **OK** k zahájení úloh opětovné ochrany.

> [!NOTE]
> Poté, co se místní virtuální počítač se spustí, trvá delší dobu pro agenta k registraci zpět na konfigurační server (až 15 minut). Během této doby opětovné ochrany se nezdaří a vrátí chybovou zprávu s oznámením, že agent není nainstalovaný. Počkejte několik minut a pak zkuste znovu opětovné ochrany.

## <a name="next-steps"></a>Další postup

Po dokončení úloh opětovné ochrany, se replikuje virtuální počítač zpět do Azure a můžete provést [převzetí služeb při selhání](site-recovery-failover.md) znovu přesunout virtuální počítače do Azure.



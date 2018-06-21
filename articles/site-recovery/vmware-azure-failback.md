---
title: Zpět z Azure se nepodařilo VMware s Azure Site Recovery | Microsoft Docs
description: Po převzetí služeb při selhání virtuálních počítačů do Azure můžete spustit navrácení služeb po obnovení, aby virtuální počítače zpět do místní. Přečtěte si postup pro navrácení služeb po obnovení.
author: nsoneji
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/20/2018
ms.author: nisoneji
ms.openlocfilehash: 1e8e2384d6d5672e29e6d7bc28f0772cd5b78519
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287513"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Selhání zpět z Azure do místního serveru

Tento článek popisuje, jak selhání virtuální počítače zpět virtuálních počítačů z Azure do místního prostředí VMware. Postupujte podle pokynů v tomto článku selhání zálohování vaše virtuální počítače VMware nebo Windows nebo Linuxem fyzických serverů po jste při selhání z místní lokality do Azure pomocí [převzetí služeb při selhání v Azure Site Recovery](site-recovery-failover.md) kurzu.

## <a name="prerequisites"></a>Požadavky
- Ujistěte se, že jste četli podrobnosti o [různé typy navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídající upozornění.

> [!WARNING]
> Nelze označit zpět v případě, že byl buď [dokončit migraci](migrate-overview.md#what-do-we-mean-by-migration), přesunout virtuální počítač do jiné skupiny prostředků nebo odstranit virtuální počítač Azure. Pokud zakážete ochrany virtuálního počítače, které nelze navrácení služeb po obnovení.

> [!WARNING]
> Fyzický server Windows Server 2008 R2 SP1, pokud ochranu a převzetí služeb při selhání do Azure, nemůže se zpět.

> [!NOTE]
> Pokud máte převzal virtuální počítače VMware, nelze označit zpět na hostitele Hyper-V.


- Než budete pokračovat, proveďte kroky opětovné ochrany tak, aby virtuální počítače jsou ve stavu, replikované a můžete spustit převzetí služeb při selhání zpátky do místního webu. Další informace najdete v tématu [postupy k nastavení opětné z Azure do místní](vmware-azure-reprotect.md).

- Ujistěte se, že se vCenter je v připojeném stavu před provedením navrácení služeb po obnovení. V opačném odpojení disky a znovu je připojit zpět k virtuálnímu počítači nezdaří.

- Během převzetí služeb při selhání do Azure, pravděpodobně nebudou mít přístup na místní lokalitu a konfigurační server může být k dispozici nebo vypnutí dolů. Během opětovné ochrany a navrácení služeb po obnovení by měla být na místním serveru configuration spuštěná a v připojeném stavu OK. 

- Během navrácení služeb po obnovení virtuální počítač, musí existovat v konfigurační databázi serveru nebo navrácení služeb po obnovení nebude úspěšné. Ujistěte se, že pořídíte pravidelně naplánované zálohování serveru. Pokud dojde k havárii, budete muset obnovit server s původní IP adresu pro navrácení služeb po obnovení fungovat.

- Hlavní cílový server by neměl mít všechny snímky před spuštěním opětovné ochrany nebo navrácení služeb po obnovení.

## <a name="overview-of-failback"></a>Přehled o navrácení služeb po obnovení
Až budete mít převzetí služeb při selhání do Azure, můžete se nezdaří zpět do místního serveru spuštěním následující kroky:

1. [Znovu nastavte ochranu](vmware-azure-reprotect.md) virtuální počítače na Azure tak, aby jejich spuštění k replikaci pro virtuální počítače VMware ve vaší místní lokalitě. Jako součást tohoto procesu musíte také:

    * Nastavte místní hlavní cíl. Použít hlavní cíl Windows pro virtuální počítače s Windows a [hlavního cíle Linuxu](vmware-azure-install-linux-master-target.md) pro virtuální počítače s Linuxem.
    * Nastavení [procesový server](vmware-azure-set-up-process-server-azure.md).
    * Spustit [znovu nastavte ochranu](vmware-azure-reprotect.md) vypněte na místním virtuálním počítači a synchronizaci dat Azure virtuálního počítače s místními disky.

2. Po virtuální počítače na platformě Azure replikovat do místního serveru, spusťte převzetí služeb při selhání z Azure do místní lokality.

3. Po data zpět selže, můžete znovu nastavte ochranu místní virtuální počítače znovu tak, aby jejich spuštění replikaci do Azure.

Pro rychlý přehled najdete v následujícím videu o tom, aby v případě selhání zpět na místní lokalitu:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroky pro navrácení služeb po obnovení

> [!IMPORTANT]
> Než začnete navrácení služeb po obnovení, zajistěte, aby bylo dokončeno vytvoření virtuálních počítačů. Virtuální počítače musí být v chráněném stavu, a jejich stavu by měl být **OK**. Chcete-li znovu nastavte ochranu virtuálních počítačů, přečtěte si [postupy k nastavení opětné](vmware-azure-reprotect.md).

1. Na stránce replikované položky vyberte virtuální počítač. Pravým tlačítkem myši a vyberte **neplánované převzetí služeb při selhání**.
2. V **potvrzení převzetí služeb při selhání**, ověřte směr převzetí služeb při selhání (z Azure). Potom vyberte bod obnovení (nejnovější nebo konzistentní nejnovější aplikace), kterou chcete použít převzetí služeb při selhání. Bod konzistentní aplikace je za do nejnovějšího bodu v čase a způsobí ztrátu dat.
3. Během převzetí služeb při selhání Site Recovery vypne virtuální počítače na platformě Azure. Jakmile zaškrtnete tuto navrácení služeb po obnovení dokončit podle očekávání, můžete zkontrolovat, virtuální počítače na Azure vypnutý.
4. **Potvrdit** je vyžadován k odebrání virtuálního počítače při selhání z Azure. Klikněte pravým tlačítkem na chráněné položky a potom vyberte **potvrdit**. Úlohu odebere virtuální počítače při selhání v Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Jaké bodu obnovení můžete I navrácení služeb po obnovení virtuálních počítačů?

Během navrácení služeb po obnovení máte dvě možnosti pro navrácení služeb po obnovení virtuálního počítače nebo obnovení plánu.

- Pokud vyberete nejnovější zpracované bodu v čase, všechny virtuální počítače převzetí služeb při selhání k jejich nejnovějšímu dostupnému bodu v čase. Pokud je replikační skupiny v rámci plánu obnovení, každý virtuální počítač z replikační skupiny převezme jeho nezávislé nejnovějšího bodu v čase.

  Virtuální počítač nelze označit zpět, dokud má alespoň jeden bod obnovení. Plán obnovení nelze označit zpět, dokud všechny virtuální počítače mít alespoň jeden bodu obnovení.

  > [!NOTE]
  > Nejnovější bod obnovení je bod obnovení konzistentní při selhání.

- Pokud vyberete bodem obnovení konzistentních s aplikací, navrácení jediného virtuálního počítače obnoví jeho nejnovějšího bodu obnovení k dispozici konzistentních s aplikací. V případě plán obnovení s replikační skupinou každá skupina replikace obnoví na jeho společný bod obnovení.
Body obnovení konzistentní s aplikací může být v čase a může být ztráty dat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co se stane VMware nástroje post navrácení služeb po obnovení?

V případě virtuálního počítače s Windows Site Recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb po obnovení virtuálního počítače s Windows jsou opětovně povolena nástroje VMware. 


## <a name="reprotect-from-on-premises-to-azure"></a>Nastavte znovu z místní do Azure
Po dokončení navrácení služeb po obnovení a spustili potvrzení, se odstraní obnovené virtuálních počítačů v Azure. Teď je virtuální počítač zpět na místní lokality, ale nebudou chráněné. Pokud chcete spustit znovu replikovat do Azure, postupujte takto:

1. Vyberte **trezoru** > **nastavení** > **replikované položky**, vyberte virtuální počítače, které se nezdařilo zpět a pak vyberte  **Znovu nastavit ochranu**.
2. Zadejte hodnotu serveru proces, který se musí použít k odesílání dat zpět do Azure.
3. Vyberte **OK** k zahájení úloh opětovné ochrany.

> [!NOTE]
> Poté, co se místní virtuální počítač se spustí, trvá delší dobu pro agenta k registraci zpět na konfigurační server (až 15 minut). Během této doby opětovné ochrany se nezdaří a vrátí chybovou zprávu s oznámením, že agent není nainstalovaný. Počkejte několik minut a pak zkuste znovu opětovné ochrany.

## <a name="next-steps"></a>Další postup

Po dokončení úloh opětovné ochrany replikují virtuální počítač zpět do Azure a můžete provést [převzetí služeb při selhání](site-recovery-failover.md) znovu přesunout virtuální počítače do Azure.



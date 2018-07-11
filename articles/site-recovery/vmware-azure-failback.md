---
title: Selhání obnovení z Azure do VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Po převzetí služeb při selhání virtuálních počítačů do Azure můžete začít navrácení služeb po obnovení přenést virtuální počítače zpět do místní. Přečtěte si postup, jak navrátit služby po obnovení.
author: nsoneji
ms.service: site-recovery
ms.date: 07/06/2018
ms.topic: conceptual
ms.author: nisoneji
ms.openlocfilehash: 9d8398582766a27f1457e3a59bdde0c59298ed50
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919831"
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Selhání obnovení z Azure do místní lokality

Tento článek popisuje, jak převzít služby při back virtuálních počítačů ze služby Azure Virtual Machines, aby v místním prostředí VMware. Postupujte podle pokynů v tomto článku selhání zálohování vašich virtuálních počítačů VMware nebo Windows/Linux, fyzických serverů po jejich selhání z místní lokality do Azure pomocí [převzetí služeb při selhání ve službě Azure Site Recovery](site-recovery-failover.md) kurzu.

## <a name="prerequisites"></a>Požadavky
- Ujistěte se, že jste četli podrobnosti o [různé typy navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídající upozornění.

> [!WARNING]
> Nelze provést navrácení služeb po máte [dokončit migraci](migrate-overview.md#what-do-we-mean-by-migration), virtuální počítač přesunout do jiné skupiny prostředků, nebo odstranit virtuální počítač Azure. Pokud je zakázat ochranu virtuálního počítače, které nelze navrácení služeb po obnovení.

> [!WARNING]
> Fyzický server Windows Server 2008 R2 SP1, je-li chránit a převzetí služeb při selhání do Azure, nemůže být při navrácení služeb obnoví.

> [!NOTE]
> Pokud budete mít převzetí služeb při selhání virtuálních počítačů VMware, nemůžete po obnovení zpět na hostitele Hyper-V.


- Než budete pokračovat, proveďte kroky operace opětovného zapnutí ochrany, tak, aby virtuální počítače jsou ve stavu, replikované a můžete spustit převzetí služeb při selhání zpátky do místní lokality. Další informace najdete v tématu [jak zpětná replikace z Azure do místního](vmware-azure-reprotect.md).

- Ujistěte se, že server vCenter je v připojeném stavu, než se pustíte navrácení služeb po obnovení. V opačném případě odpojení disků a připojení zpět k virtuálnímu počítači nezdaří.

- Během převzetí služeb při selhání do Azure, nemusí být dostupné v místní lokalitě a konfigurační server může být buď není k dispozici, nebo vypnuta dolů. Během operace opětovného zapnutí ochrany a navrácení služeb po obnovení by měla být místní konfigurační server funkční a v připojeném stavu OK. 

- Během navrácení služeb po obnovení virtuální počítač musí existovat v databázi konfiguračního serveru nebo navrácení služeb po obnovení nebude úspěšné. Ujistěte se, že provedete pravidelně naplánovaných záloh serveru. Pokud dojde k havárii, budete muset obnovit server s původní IP adresu pro navrácení služeb po obnovení pro práci.

- Hlavní cílový server by neměl mít všechny snímky než aktivujete opětovné zapnutí ochrany a navrácení služeb po obnovení.

## <a name="overview-of-failback"></a>Přehled o navrácení služeb po obnovení
Jakmile budete mít převzetí služeb při selhání do Azure, můžete navrátit služby zpět do místní lokality pomocí provádí následující kroky:

1. [Znovunastavení ochrany](vmware-azure-reprotect.md) virtuálních počítačů v Azure tak, aby se začaly replikovat virtuální počítače VMware ve vaší místní lokalitě. Jako součást tohoto procesu budete také muset:

    * Nastavte hlavní cíl místní. Použít hlavní cíl Windows pro virtuální počítače s Windows a [hlavního cíle Linuxu](vmware-azure-install-linux-master-target.md) pro virtuální počítače s Linuxem.
    * Nastavení [procesový server](vmware-azure-set-up-process-server-azure.md).
    * Spustit [znovunastavení ochrany](vmware-azure-reprotect.md) vypněte na místním virtuálním počítači a synchronizovat data Azure virtuálního počítače s disky v místním.

2. Po replikaci virtuálních počítačů v Azure do místní lokality, spusťte převzetí služeb při selhání z Azure do místní lokality.

3. Jakmile se vaše data se nepovede zpět, je znovunastavení ochrany místních virtuálních počítačů znovu tak, aby se začaly replikovat do Azure.

Získejte rychlý přehled zhlédnout následující video o tom, jak selhat zpět do místní lokality:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroky pro navrácení služeb po obnovení

> [!IMPORTANT]
> Předtím, než se pustíte do navrácení služeb po obnovení, ujistěte se, že dokončení opětovného nastavování ochrany virtuálních počítačů. Virtuální počítače by měla být v chráněném stavu, a jejich stav by měl být **OK**. Znovunastavení ochrany virtuálních počítačů, přečtěte si téma [jak znovunastavení ochrany](vmware-azure-reprotect.md).

1. Na stránce replikovaných položek vyberte virtuální počítač. Pravým tlačítkem myši a vyberte **neplánované převzetí služeb při selhání**.
2. V **potvrzení převzetí služeb při selhání**, ověřte směr převzetí služeb při selhání (z Azure). Vyberte bod obnovení (nejnovější nebo nejnovější konzistentní vzhledem k aplikacím), který chcete použít převzetí služeb při selhání. Bod konzistentní vzhledem k aplikaci je za nejnovějšího bodu v čase a způsobí ztrátu některých dat.
3. Během převzetí služeb při selhání Site Recovery vypne virtuální počítače v Azure. Až zkontrolujete, že navrácení služeb po obnovení dokončeno podle očekávání, můžete zkontrolovat, že virtuální počítače v Azure vypnout.
4. **Potvrdit** je potřeba odebrat převzetím služeb při selhání virtuálního počítače z Azure. Klikněte pravým tlačítkem na chráněné položky a pak vyberte **potvrzení**. Úloha odebere převzetím služeb při selhání virtuálních počítačů v Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Jaké bodu obnovení můžete I navrácení služeb po obnovení virtuálních počítačů?

Během navrácení služeb po obnovení máte dvě možnosti pro navrácení služeb po obnovení virtuálního počítače nebo obnovení plánu.

- Pokud vyberete nejnovějšího zpracovaného bodu v čase, všechny virtuální počítače převzetí služeb při selhání k jejich nejnovějšímu dostupnému bodu v čase. Pokud je replikační skupiny v rámci plánu obnovení, každý virtuální počítač z replikační skupiny převezme služby při selhání pro její nezávislé nejnovější bod v čase.

  Virtuální počítač nelze neúspěšné zpět, dokud má nejméně jeden bod obnovení. Plán obnovení nelze neúspěšné zpět, dokud se všechny virtuální počítače mají nejméně jeden bodu obnovení.

  > [!NOTE]
  > Nejnovější bod obnovení je bod obnovení konzistentní při selhání.

- Pokud vyberete bod obnovení konzistentní s aplikací, jeden virtuální počítač navrácení služeb po obnovení se obnoví do jeho nejnovější bod obnovení konzistentní s aplikací. V případě plán obnovení s replikační skupinou každou skupinu replikace obnoví k její běžné dostupný bod obnovení.
Body obnovení konzistentní s aplikací může být za serverem v čase a může být ztráta dat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co se stane VMware tools příspěvek navrácení služeb po obnovení?

V případě virtuálního počítače Windows Site Recovery zakáže nástroje VMware tools během převzetí služeb při selhání. Během navrácení služeb po obnovení virtuálního počítače Windows jsou opětovně povolena nástroje VMware tools. 


## <a name="reprotect-from-on-premises-to-azure"></a>Zpětná replikace z místního do Azure
Po dokončení navrácení služeb po obnovení a spustili potvrzení, jsou odstraněny obnovený virtuální počítače v Azure. Teď je virtuální počítač zpět na místní lokalitu, ale nebudou chráněné. Pokud chcete začít znovu replikovat do Azure, postupujte takto:

1. Vyberte **trezor** > **nastavení** > **replikované položky**, vyberte virtuální počítače, které při navrácení služeb obnoví a pak vyberte  **Znovunastavení ochrany**.
2. Zadejte hodnotu procesový server, kterou je potřeba použít k odesílání dat zpět do Azure.
3. Vyberte **OK** zahajte úlohu znovunastavení ochrany.

> [!NOTE]
> Po i místní virtuální počítač se spustí, trvá nějakou dobu agenta k registraci zpět do konfiguračního serveru (až 15 minut). Během této doby opětovného zapnutí ochrany se nezdaří a vrátí chybovou zprávu s informacemi o tom, že agent není nainstalovaný. Počkejte pár minut a pak zkuste znovu operace opětovného zapnutí ochrany.

## <a name="next-steps"></a>Další postup

Po znovunastavení ochrany úloha dokončí, virtuální počítač replikuje zpět do Azure a můžete provádět [převzetí služeb při selhání](site-recovery-failover.md) vašich virtuálních počítačů do Azure přesunout znovu.



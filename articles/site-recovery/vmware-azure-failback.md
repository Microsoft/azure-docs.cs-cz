---
title: Převzetí služeb při selhání virtuálních počítačů VMware nebo fyzických serverů z Azure pomocí Azure Site Recovery
description: Přečtěte si, jak po převzetí služeb při selhání do Azure přejít zpátky na místní lokalitu během zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084059"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Převzetí služeb při selhání virtuálních počítačů VMware a fyzických serverů z Azure do místní lokality

Tento článek popisuje, jak navrácení služeb po obnovení virtuálních počítačů z Azure Virtual Machines do místního prostředí VMware. Podle pokynů v tomto článku navraťte služby virtuálních počítačů VMware nebo fyzických serverů s Windows nebo Linux po převzetí služeb při selhání z místní lokality do Azure pomocí [Azure Site Recovery kurzu převzetí služeb při selhání](site-recovery-failover.md) .

## <a name="prerequisites"></a>Požadavky
- Ujistěte se, že jste si přečetli podrobnosti o [různých typech navrácení služeb po obnovení](concepts-types-of-failback.md) a odpovídajících aspektech.

> [!WARNING]
> Po [dokončení migrace](migrate-overview.md#what-do-we-mean-by-migration), přesunutí virtuálního počítače do jiné skupiny prostředků nebo odstranění virtuálního počítače Azure nemůžete navracet služby po obnovení. Pokud ochranu virtuálního počítače zakážete, nemůžete navracet služby po obnovení.

> [!WARNING]
> Fyzický server s Windows Serverem 2008 R2 SP1, pokud je chráněný a převzít služby při selhání do Azure, se nedá vrátit zpět.

> [!NOTE]
> Pokud jste převzali služby při selhání virtuálních počítačů VMware, nemůžete navrátit služby po obnovení na hostitele Hyper-V.


- Než budete pokračovat, dokončete postup opětovné ochrany, aby byly virtuální počítače v replikovaném stavu, a můžete spustit převzetí služeb při selhání zpátky na místní lokalitu. Další informace najdete v tématu [jak znovu nastavit ochranu z Azure do místního](vmware-azure-reprotect.md)prostředí.

- Před navrácením služeb po obnovení se ujistěte, že je vCenter v připojeném stavu. V opačném případě dojde k chybě odpojení disků a jejich připojení zpátky k virtuálnímu počítači.

- Během převzetí služeb při selhání do Azure nemusí být místní lokalita přístupná a konfigurační server může být buď nedostupný, nebo vypnutý. Při opětovném zapnutí ochrany a navrácení služeb po obnovení by místní konfigurační server měl běžet a být v připojeném stavu OK. 

- Během navrácení služeb po obnovení musí virtuální počítač existovat v databázi konfiguračního serveru nebo navrácení služeb po obnovení nebude úspěšné. Ujistěte se, že provádíte pravidelné naplánování záloh serveru. Pokud dojde k havárii, budete muset obnovit server s původní IP adresou, aby se navrácení služeb po obnovení fungovalo.

- Hlavní cílový server by neměl mít žádné snímky před aktivací opětovného zapnutí ochrany nebo navrácení služeb po obnovení.

## <a name="overview-of-failback"></a>Přehled navrácení služeb po obnovení
Po převzetí služeb při selhání do Azure můžete navrátit služby po obnovení do místní lokality provedením následujících kroků:

1. Znovu nastavte [ochranu](vmware-azure-reprotect.md) virtuálních počítačů v Azure tak, aby se začaly replikovat do virtuálních počítačů VMware ve vaší místní lokalitě. V rámci tohoto procesu budete také potřebovat:

    * Nastavte místní hlavní cíl. Použijte hlavní cíl Windows pro virtuální počítače s Windows a [hlavní cíl Linux](vmware-azure-install-linux-master-target.md) pro virtuální počítače se systémem Linux.
    * Nastavení [procesového serveru](vmware-azure-set-up-process-server-azure.md).
    * Začněte znovu [chránit](vmware-azure-reprotect.md) a vypněte místní virtuální počítač a synchronizujte data virtuálních počítačů Azure s místními disky.

2. Po replikaci virtuálních počítačů v Azure na místní lokalitu spustíte převzetí služeb při selhání z Azure do místní lokality.

3. Po obnovení dat znovu znovu nastavte ochranu místních virtuálních počítačů, aby se začaly replikovat do Azure.

Rychlý přehled najdete v následujícím videu o navrácení služeb po obnovení do místní lokality:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Kroky pro navrácení služeb po obnovení

> [!IMPORTANT]
> Než začnete s navrácením služeb po obnovení, ujistěte se, že jste dokončili ochranu virtuálních počítačů. Virtuální počítače by měly být v chráněném stavu a jejich stav by měl být v **pořádku**. Pokud chcete znovu zapnout ochranu virtuálních počítačů, přečtěte si téma [jak znovu zapnout ochranu](vmware-azure-reprotect.md).

1. Na stránce replikované položky vyberte virtuální počítač. Klikněte na něj pravým tlačítkem a vyberte **neplánované převzetí služeb při selhání**.
2. V části potvrzení převzetí **služeb při selhání**ověřte směr převzetí služeb při selhání (z Azure). Pak vyberte bod obnovení (nejnovější nebo nejnovější konzistentní vzhledem k aplikacím), který chcete použít pro převzetí služeb při selhání. Bod konzistentní vzhledem k aplikacím je za posledním bodem v čase a způsobuje ztrátu dat.
3. Během převzetí služeb při selhání Site Recovery vypne virtuální počítače v Azure. Po kontrole, že se navrácení služeb po obnovení dokončilo podle očekávání, můžete zjistit, že virtuální počítače v Azure jsou vypnuté.
4. K odebrání virtuálního počítače s podporou převzetí služeb při selhání z Azure se vyžaduje **potvrzení** . Klikněte pravým tlačítkem na chráněnou položku a pak vyberte **Potvrdit**. Úloha odebere virtuální počítače s podporou převzetí služeb při selhání v Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>K jakým bodům obnovení můžu navrátit služby virtuálních počítačů?

Během navrácení služeb po obnovení máte dvě možnosti, jak obnovit virtuální počítač nebo plán obnovení.

- Pokud vyberete nejnovější zpracovaný bod v čase, všechny virtuální počítače převezmou služby při selhání do svého posledního dostupného bodu v čase. Pokud je v rámci plánu obnovení nastavená replikační skupina, u každého virtuálního počítače skupiny replikace dojde k převzetí služeb při selhání nezávisle na jeho nenezávislém časovém okamžiku.

  Virtuální počítač nejde navrátit zpátky, dokud nemá aspoň jeden bod obnovení. Plán obnovení nemůžete navrátit zpátky, dokud všechny jeho virtuální počítače nemají alespoň jeden bod obnovení.

  > [!NOTE]
  > Nejnovější bod obnovení je bod obnovení konzistentní vzhledem k selháním.

- Pokud vyberete bod obnovení konzistentní vzhledem k aplikacím, jedno obnovení po obnovení virtuálního počítače se obnoví do nejnovějšího dostupného bodu obnovení konzistentního vzhledem k aplikacím. V případě plánu obnovení s replikační skupinou se každá skupina replikace obnoví do svého společného dostupného bodu obnovení.
Body obnovení konzistentní vzhledem k aplikacím můžou být v čase a může dojít ke ztrátě dat.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Co se stane s nástroji VMware po navrácení služeb po obnovení?

V případě virtuálního počítače s Windows Site Recovery zakáže nástroje VMware během převzetí služeb při selhání. Během navrácení služeb virtuálního počítače s Windows po obnovení jsou znovu povoleny nástroje VMware. 


## <a name="reprotect-from-on-premises-to-azure"></a>Opětovné zapnutí ochrany z místního prostředí do Azure
Po dokončení navrácení služeb po obnovení a spuštění potvrzení se odstraní obnovené virtuální počítače v Azure. Teď je virtuální počítač zpátky na místním webu, ale nebude chráněný. Pokud chcete znovu začít replikovat do Azure, udělejte toto:

1. Vyberte **trezor** > **Nastavení** > **replikovaných položek**, vyberte virtuální počítače, u kterých došlo k chybě, a pak vyberte **znovu nastavit ochranu**.
2. Zadejte hodnotu procesového serveru, který se musí použít k odesílání dat zpět do Azure.
3. Výběrem **OK** spusťte úlohu opětovného zapnutí ochrany.

> [!NOTE]
> Až se místní virtuální počítač spustí, může se agent nějaký čas zaregistrovat zpátky na konfigurační server (až 15 minut). Během této doby se znovu spustí ochrana a vrátí chybovou zprávu s oznámením, že agent není nainstalovaný. Počkejte pár minut a pak zkuste znovu zapnout ochranu.

## <a name="next-steps"></a>Další kroky

Po dokončení úlohy opětovné ochrany se virtuální počítač replikuje zpátky do Azure a můžete provést [převzetí služeb při selhání](site-recovery-failover.md) a přesunout virtuální počítače do Azure znovu.



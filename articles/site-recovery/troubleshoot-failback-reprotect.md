---
title: Řešení chyb během navrácení služeb po obnovení místně z Azure a nastavte znovu do Azure později | Microsoft Docs
description: Tento článek popisuje způsoby řešení běžných chyb v důsledku neúspěšného zpět na místní z Azure a během opětovné ochrany
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677410"
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Řešení chyb zaznamenaný během navrácení služeb po obnovení
Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. Jeden je znovu nastavte ochranu virtuálních počítačů z Azure do místní, druhý je ve skutečnosti provést navrácení služeb po obnovení z Azure do místní.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Řešení chyb při opětovné povolení ochrany virtuálního počítače zpět do místní, po převzetí služeb při selhání
Zobrazí jednu z těchto chyb během opětovné ochrany virtuálního počítače do Azure. K odstraňování, použijte popisuje kroky pro všechny chybové stavy.


### <a name="error-code-95226"></a>Kód chyby 95226

*Opětovné ochrany se nezdařila, protože virtuální počítač Azure se nepodařilo připojit k místní konfigurace serveru.*

To se stane, když 
1. Virtuální počítač Azure nemohl kontaktovat konfigurační server místní a proto nelze být zjištěny a zaregistrovaný na konfiguračním serveru. 
2. Služba InMage Scout Application na virtuální počítač Azure, který vyžaduje, aby byl spuštěn pro komunikaci na konfiguračním serveru místní, nemusí být spuštěna post převzetí služeb při selhání.

Chcete-li vyřešit tento problém
1. Je potřeba zajistit, že je síť virtuálního počítače Azure nakonfigurovaná tak, aby virtuální počítač může komunikovat s místní konfigurační server. K tomuto účelu nastavit Site to Site VPN zpátky do vašeho místního datového centra nebo nakonfigurujte připojení ExpressRoute s soukromý partnerský vztah ve virtuální síti Azure virtuálního počítače. 
2. Pokud již máte síť konfigurována tak, že virtuální počítač Azure může komunikovat s místní konfigurační server, přihlaste se k virtuálnímu počítači a zkontrolujte, InMage Scout Application Service". Pokud zjistíte, že InMage Scout Application Service neběží, spusťte službu ručně a zkontrolujte, že typ spouštění služby je nastavená na hodnotu automaticky.

### <a name="error-code-78052"></a>Kód chyby 78052
Opětovné ochrany se nezdaří s chybovou zprávou: *pro virtuální počítač nejde dokončit ochrana.*

To může dojít z důvodů, na dvě
1. Virtuální počítač, který se opětovnou ochranu je systém Windows Server 2016. Tento operační systém aktuálně není podporována pro navrácení služeb po obnovení, ale bude brzy podporována.
2. Již existuje virtuální počítač se stejným názvem na hlavním cílovém serveru se nedaří zpět.

Chcete-li vyřešit tento problém můžete vybrat jiný hlavní cílový server na jiného hostitele, tak, aby opětovné ochrany bude vytvoření počítače na jiného hostitele, kde názvy nejsou v konfliktu. Můžete také řešení vMotion hlavního cíle na jiného hostitele, kde je název kolize neprovede. Pokud existující virtuální počítač je počítač stray, stačí ho mohli přejmenovat tak, aby můžete získat vytvoření nového virtuálního počítače na stejném hostiteli ESXi.

### <a name="error-code-78093"></a>Kód chyby 78093

*Virtuální počítač neběží ve stavu "zamrzlých", nebo není dostupný.*

K nastavení opětné nezdařené přes virtuální počítač zpět do místní, musíte virtuálním počítači Azure s. Toto je tak, aby služba mobility zaregistruje s konfiguračním serverem místní a můžete spustit replikaci podle komunikovat s procesovým serverem. Pokud je počítač v síti nesprávná nebo není spuštěn ("zamrzlých" stavu nebo ukončení), nelze kontaktovat konfigurační server služba mobility virtuálního počítače zahájíte opětovné ochrany. Virtuální počítač můžete restartovat, aby ho můžete spustit komunikaci back místní. Restartujte úlohu, opětovné ochrany po spuštění virtuálního počítače Azure

### <a name="error-code-8061"></a>Kód chyby 8061

*Úložiště není přístupná z hostitele ESXi.*

Odkazovat [hlavní cíl požadavky](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) a [podporu datastores](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) navrácení služeb po obnovení


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Řešení chyb při provádění navrácení služeb po obnovení zpět na místní virtuální počítač Azure
Zobrazí jednu z těchto chyb během navrácení služeb po obnovení virtuálního počítače Azure zpět na místní. K odstraňování, použijte popisuje kroky pro všechny chybové stavy.

### <a name="error-code-8038"></a>Kód chyby 8038

*Nepovedlo se zprovoznit místní virtuální počítač z důvodu chyby*

To se stane, když je zapínají místní virtuální počítač na hostitele, který nemá dostatek paměti zřízený.

Chcete-li vyřešit tento problém

1. Můžete zřídit další paměť na hostiteli ESXi.
1. řešení vMotion virtuálního počítače na jiného hostitele ESXi, který má dostatek paměti ke spuštění virtuálního počítače.
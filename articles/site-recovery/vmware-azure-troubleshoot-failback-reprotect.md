---
title: "Řešení chyb během navrácení služeb po obnovení virtuálních počítačů Azure do místní VMware s Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje způsoby řešení běžných chyb navrácení služeb po obnovení a vytvoření během navrácení služeb po obnovení do VMware z Azure pomocí Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Řešení potíží s navrácení služeb po obnovení z Azure do VMware

Tento článek popisuje postupy řešení problémů se můžete setkat při selhání zpátky Azure virtuální počítače VMware vaší místní infrastruktuře po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. Pro první krok po převzetí služeb při selhání, je potřeba znovu nastavte ochranu virtuálních počítačů Azure k místnímu tak, aby jejich spuštění replikace. Druhým krokem je pro spouštění převzetí služeb při selhání z Azure k místní lokalitě.

## <a name="troubleshoot-reprotection-errors"></a>Řešení chyb nové provedení ochrany

V této části jsou běžné chyby nové provedení ochrany a jak je opravit.

### <a name="error-code-95226"></a>Kód chyby 95226

**Opětovné ochrany se nezdařila, protože virtuální počítač Azure se nepodařilo připojit k místní konfigurace serveru.**

Tato chyba nastane, když:

* Virtuální počítač Azure nelze kontaktovat konfigurační server místně. Virtuální počítač nelze zjistit a zaregistrovaný na konfiguračním serveru.
* Služba InMage Scout aplikace není spuštěna na virtuálním počítači Azure po převzetí služeb při selhání. Služba je požadován pro komunikaci s místní konfigurační server.

K vyřešení tohoto problému:

* Zkontrolujte, že síť virtuálních počítačů Azure umožňuje virtuálnímu počítači Azure ke komunikaci s místní konfigurační server. Můžete buď nastavit síť VPN site-to-site do vašeho místního datového centra nebo připojení k Azure ExpressRoute nakonfigurovat soukromý partnerský vztah ve virtuální síti virtuálního počítače Azure.
* Pokud virtuální počítač může komunikovat s místní konfigurační server, přihlaste se k virtuálnímu počítači. Zkontrolujte služba InMage Scout aplikace. Pokud se zobrazí, že neběží, spusťte službu ručně. Zkontrolujte, že službu spustit typ nastavena na **automatické**.

### <a name="error-code-78052"></a>Kód chyby 78052

**U virtuálního počítače nelze dokončit ochrana.**

Tento problém může dojít, pokud již existuje virtuální počítač se stejným názvem na hlavním cílovém serveru, ke kterému jste se navrácení služeb po obnovení.

K vyřešení tohoto problému:

* Vyberte jiný hlavní cílový server na jiného hostitele tak, aby nové provedení ochrany vytvoří na počítači na jiného hostitele, kde není názvy dojít ke konfliktu.
* Také můžete řešení vMotion přesunout hlavního cíle na jiného hostitele, kde je název kolize neprovede. Pokud je existující virtuální počítač stray počítač, přejmenujte ji, aby bylo možné vytvořit nový virtuální počítač na stejném hostiteli ESXi.


### <a name="error-code-78093"></a>Kód chyby 78093

**Virtuální počítač neběží ve stavu "zamrzlých", nebo není dostupný.**

K vyřešení tohoto problému:

Virtuální počítač Azure k nastavení opětné virtuálního počítače při selhání, musí být spuštěna, aby služba Mobility zaregistruje se pomocí konfigurace místního serveru a můžete je zahájení replikace pomocí komunikovat s procesovým serverem. Pokud tento počítač je v nesprávné sítí nebo není spuštěná (přestala stavu nebo vypnutí dolů), server konfigurace se nemůže připojit službu Mobility virtuálního počítače zahájíte nové provedení ochrany.

* Restartujte virtuální počítač tak, aby ho můžete spustit komunikaci back místní.
* Restartujte úlohu, opětovné ochrany po spuštění virtuálního počítače Azure.

### <a name="error-code-8061"></a>Kód chyby 8061

**Úložiště není přístupná z hostitele ESXi.**

Zkontrolujte [hlavní cíl požadavky a podporovanými úložišti dat](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) navrácení služeb po obnovení.


## <a name="troubleshoot-failback-errors"></a>Řešení chyb navrácení služeb po obnovení

Tato část popisuje běžné chyby, ke kterým může dojít během navrácení služeb po obnovení.

### <a name="error-code-8038"></a>Kód chyby 8038

**Nepovedlo se zprovoznit místní virtuální počítač z důvodu chyby.**

Tento problém se stane, když je zapínají místní virtuální počítač na hostitele, který nemá dostatek paměti zřízený. 

K vyřešení tohoto problému:

* Zřídit další paměť na hostiteli ESXi.
* Kromě toho můžete řešení vMotion přesunout virtuální počítač do jiného hostitele ESXi, který má dostatek paměti ke spuštění virtuálního počítače.

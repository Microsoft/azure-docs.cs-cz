---
title: Řešení potíží se zotavením po havárii virtuálního počítače VMware pomocí Azure Site Recovery
description: V tomto článku se dozvíte, jak řešit problémy navrácení služeb po obnovení a zotavení po havárii virtuálního počítače VMware do Azure pomocí Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693146"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Řešení potíží s navrácením služeb po obnovení z Azure do místního prostředí

Tento článek popisuje, jak řešit problémy, se kterými se můžete setkat při navrácení služeb po obnovení virtuálních počítačů Azure do místní infrastruktury VMware, po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. V prvním kroku po převzetí služeb při selhání budete muset znovu zapnout ochranu virtuálních počítačů Azure v místním prostředí, aby se spustila replikace. Druhým krokem je spustit převzetí služeb při selhání z Azure pro navrácení služeb po obnovení do místní lokality.

## <a name="troubleshoot-reprotection-errors"></a>Řešení chyb ochrany

Tato část podrobně popisuje běžné chyby ochrany a jejich opravu.

### <a name="error-code-95226"></a>Kód chyby 95226

**Opětovné zapnutí ochrany nebylo úspěšné, protože virtuální počítač Azure se nemohl připojit k místnímu konfiguračnímu serveru.**

K této chybě dochází v těchto případech:

* Virtuální počítač Azure se nemůže připojit k místnímu konfiguračnímu serveru. Virtuální počítač nejde zjistit a zaregistrovat na konfiguračním serveru.
* Po převzetí služeb při selhání neběží na virtuálním počítači Azure službu InMage Scout Application. Služba je nutná pro komunikaci s místním konfiguračním serverem.

Řešení tohoto problému:

* Ověřte, že síť virtuálních počítačů Azure umožňuje virtuálnímu počítači Azure komunikovat s místním konfiguračním serverem. Můžete buď nastavit síť VPN typu Site-to-site k místnímu datovému centru, nebo nakonfigurovat připojení Azure ExpressRoute se soukromým partnerským vztahem ve virtuální síti virtuálního počítače Azure.
* Pokud virtuální počítač může komunikovat s místním konfiguračním serverem, přihlaste se k virtuálnímu počítači. Pak zkontrolujte službu InMage Scout Application. Pokud vidíte, že není spuštěný, spusťte službu ručně. Ověřte, že typ spuštění služby je nastaven na hodnotu **automaticky**.

### <a name="error-code-78052"></a>Kód chyby 78052

**Pro virtuální počítač se nepovedlo dokončit ochranu.**

K tomuto problému může dojít, pokud už na hlavním cílovém serveru existuje virtuální počítač se stejným názvem, na kterém nahráváte zpátky.

Řešení tohoto problému:

* Vyberte jiný hlavní cílový server na jiném hostiteli, aby opětovná ochrana vytvořila počítač na jiném hostiteli, kde se názvy nekolidují.
* Pomocí vMotion můžete také přesunout hlavní cíl na jiného hostitele, kde se kolize názvů nestane. Pokud je stávající virtuální počítač osamocený počítač, přejmenujte ho, aby se nový virtuální počítač mohl vytvořit na stejném hostiteli ESXi.


### <a name="error-code-78093"></a>Kód chyby 78093

**Virtuální počítač není spuštěný ve stavu neodpovídá nebo není dostupný.**

Řešení tohoto problému:

Pokud chcete znovu zapnout ochranu virtuálního počítače s podporou převzetí služeb při selhání, musí být virtuální počítač Azure spuštěný, aby se služba mobility registrovala na místním konfiguračním serveru a mohla zahájit replikaci komunikací s procesovým serverem. Pokud je počítač v nesprávné síti nebo neběží (neodpovídá nebo není vypnutý), nemůže konfigurační server kontaktovat službu mobility na VIRTUÁLNÍm počítači za účelem zahájení ochrany.

* Restartujte virtuální počítač, aby mohl začít komunikovat zpátky místně.
* Po spuštění virtuálního počítače Azure restartujte úlohu opětovné ochrany.

### <a name="error-code-8061"></a>Kód chyby 8061

**Úložiště dat není dostupné z hostitele ESXi.**

Ověřte [hlavní požadavky na hlavní cíl a podporovaná úložiště dat](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) pro navrácení služeb po obnovení.


## <a name="troubleshoot-failback-errors"></a>Řešení chyb navrácení služeb po obnovení

Tato část popisuje běžné chyby, se kterými se můžete setkat během navrácení služeb po obnovení.

### <a name="error-code-8038"></a>Kód chyby 8038

**Nepovedlo se vyvolat místní virtuální počítač z důvodu chyby.**

K tomuto problému dochází, když se místní virtuální počítač zavede na hostiteli, který nemá dostatek zřízené paměti. 

Řešení tohoto problému:

* Zřídit více paměti na hostiteli ESXi.
* Kromě toho můžete pomocí vMotion přesunout virtuální počítač na jiného hostitele ESXi, který má dostatek paměti pro spuštění virtuálního počítače.

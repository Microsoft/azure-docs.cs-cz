---
title: "Řešení chyb během navrácení služeb po obnovení virtuálních počítačů Azure do místní VMware s Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje způsoby řešení běžných chyb navrácení služeb po obnovení a vytvoření během navrácení služeb po obnovení VMware z Azure přes Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 6dcecce78de3caaefb40cb3fe4853d5d550163b4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Řešení potíží s navrácení služeb po obnovení z Azure do VMware

Tento článek popisuje postupy řešení problémů se můžete setkat při selhání zpátky Azure virtuální počítače VMware vaší místní infrastruktuře po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. Po převzetí služeb při selhání budete muset znovu nastavte ochranu virtuálních počítačů Azure místně, tak, aby jejich spuštění replikace. Druhým krokem je pro spouštění převzetí služeb při selhání z Azure, aby v případě selhání zpět na místní lokalitu.

## <a name="troubleshoot-reprotection-errors"></a>Řešení chyb nové provedení ochrany

Tato část podrobně běžných vytvoření chyb a opravte je.

### <a name="error-code-95226"></a>Kód chyby 95226

**Opětovné ochrany se nezdařila, protože virtuální počítač Azure se nepodařilo připojit k místní konfigurace serveru.**

Tato chyba nastane, když:

1. Virtuální počítač Azure nelze kontaktovat konfigurační server místně. Virtuální počítač nelze zjistit a zaregistrovaný na konfiguračním serveru.
2. InMage Scout Application service není spuštěna na virtuálním počítači Azure po převzetí služeb při selhání. Služba je požadován pro komunikaci s místní konfigurační server.

K vyřešení tohoto problému:

1. Zkontrolujte, že síť virtuálních počítačů Azure umožňuje virtuálnímu počítači Azure ke komunikaci s místní konfigurační server. K tomuto účelu nastavit síť VPN site-to-site do vašeho místního datového centra, nebo nakonfigurujte připojení ExpressRoute s soukromý partnerský vztah ve virtuální síti virtuálního počítače Azure.
2. Pokud virtuální počítač může komunikovat s místní konfigurační server, potom se přihlaste do virtuálního počítače a zkontrolujte, InMage Scout Application Service'. Pokud se zobrazí, že není spuštěn, ručně spusťte službu a zkontrolujte, že typ spuštění služby nastavený na hodnotu automaticky.

### <a name="error-code-78052"></a>Kód chyby 78052

**U virtuálního počítače nelze dokončit ochrana.**

To může dojít, pokud již existuje virtuální počítač se stejným názvem na hlavním cílovém serveru, ke kterému jste se navrácení služeb po obnovení.

K vyřešení tohoto problému, proveďte následující:
1. Vyberte jiný hlavní cílový server na jiného hostitele, tak, aby nové provedení ochrany bude vytvoření počítače na jiného hostitele, kde názvy nejsou v konfliktu.
2. Můžete také řešení vMotion hlavního cíle na jiného hostitele, na kterém nedojde k ní kolize názvů. Pokud je existující virtuální počítač stray počítač, přejmenujte ji, aby bylo možné vytvořit nový virtuální počítač na stejném hostiteli ESXi.

### <a name="error-code-78093"></a>Kód chyby 78093

**Virtuální počítač neběží ve stavu "zamrzlých", nebo není dostupný.**

K nastavení opětné nezdařené přes virtuální počítač, musí být spuštěna virtuálního počítače Azure. Toto je tak, aby služba Mobility zaregistruje se konfigurace serveru místní a můžete spustit replikaci podle komunikovat s procesovým serverem. Pokud je počítač připojen nesprávné síti nebo není spuštěn ("zamrzlých" stavu nebo ukončení), nelze kontaktovat konfigurační server služba Mobility na virtuální počítač, spustíte nové provedení ochrany.

1. Restartujte virtuální počítač tak, aby ho můžete spustit komunikaci back místní.
2. Restartujte úlohu, opětovné ochrany po spuštění virtuálního počítače Azure

### <a name="error-code-8061"></a>Kód chyby 8061

**Úložiště není přístupná z hostitele ESXi.**

Zkontrolujte [hlavní cíl požadavky a podporovanými úložišti dat](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) navrácení služeb po obnovení.


## <a name="troubleshoot-failback-errors"></a>Řešení chyb navrácení služeb po obnovení

Tato část popisuje běžné chyby, ke kterým může dojít během navrácení služeb po obnovení.

### <a name="error-code-8038"></a>Kód chyby 8038

**Nepovedlo se zprovoznit místní virtuální počítač z důvodu chyby**

To se stane, když je zapínají místní virtuální počítač na hostitele, který nemá dostatek paměti zřízený. K vyřešení tohoto problému:

1. Zřídit další paměť na hostiteli ESXi.
2. Kromě toho můžete řešení vMotion virtuálního počítače na jiného hostitele ESXi, který má dostatek paměti ke spuštění virtuálního počítače.

---
title: Řešení potíží se zotavením po havárii virtuálního počítače VMware pomocí Azure Site Recovery
description: V tomto článku se dozvíte, jak řešit problémy navrácení služeb po obnovení a zotavení po havárii virtuálního počítače VMware do Azure pomocí Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87423112"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Řešení potíží s navrácením služeb po obnovení z Azure do místního prostředí

Tento článek popisuje, jak řešit problémy, se kterými se můžete setkat při navrácení služeb po obnovení virtuálních počítačů Azure do místní infrastruktury VMware, po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po obnovení v podstatě zahrnuje dva hlavní kroky. V prvním kroku po převzetí služeb při selhání budete muset znovu zapnout ochranu virtuálních počítačů Azure v místním prostředí, aby se spustila replikace. Druhým krokem je spustit převzetí služeb při selhání z Azure pro navrácení služeb po obnovení do místní lokality.

## <a name="common-issues"></a>Běžné problémy

- Pokud provádíte zjišťování vCenter uživatele, který je jen pro čtení, a ochranu virtuálních počítačů, ochrana bude úspěšná a funguje převzetí služeb při selhání. Během ochrany dojde k selhání, protože úložiště dat nelze zjistit. Příznakem je, že úložiště dat nejsou uvedena během opakované ochrany. Chcete-li tento problém vyřešit, můžete aktualizovat přihlašovací údaje pro vCenter pomocí odpovídajícího účtu, který má oprávnění, a potom úlohu opakovat.
- Při navrácení služeb po obnovení virtuálního počítače se systémem Linux a jeho spuštění v místním prostředí můžete zjistit, že byl balíček správce sítě z tohoto počítače odinstalován. Tato odinstalace nastane, protože balíček správce sítě se odebere, když se virtuální počítač obnoví v Azure.
- Když je virtuální počítač se systémem Linux nakonfigurovaný se statickou IP adresou a převezme služby při selhání do Azure, IP adresa se získá z protokolu DHCP. Když převezmete služby při selhání do místní sítě, virtuální počítač bude nadále používat protokol DHCP k získání IP adresy. Ručně se přihlaste k počítači a v případě potřeby nastavte IP adresu zpět na statickou adresu. Virtuální počítač s Windows může znovu získat svoji statickou IP adresu.
- Pokud používáte buď bezplatnou edici ESXi 5,5 nebo edici vSphere 6 hypervisoru, převzetí služeb při selhání proběhne úspěšně, ale navrácení služeb po obnovení nebude úspěšné. Pokud chcete povolit navrácení služeb po obnovení, upgradujte na buď zkušební licenci programu.
- Pokud se nemůžete připojit ke konfiguračnímu serveru z procesového serveru, pomocí programu Telnet ověřte připojení ke konfiguračnímu serveru na portu 443. Můžete se také pokusit testovat konfigurační server z procesového serveru z procesu. Procesový Server by měl mít také prezenční signál, pokud je připojený ke konfiguračnímu serveru.
- Server s Windows Serverem 2008 R2 SP1, který je chráněný jako fyzický místní server, se nedá vrátit z Azure do místní lokality.
- Nemůžete navrátit služby po obnovení v následujících případech:
    - Migrovali jste počítače do Azure. 
    - Přesunuli jste virtuální počítač do jiné skupiny prostředků.
    - Odstranili jste virtuální počítač Azure.
    - Zakázali jste ochranu virtuálního počítače.
    - Virtuální počítač jste vytvořili ručně v Azure. Počítač by měl být zpočátku chráněný místně a před provedením ochrany se převzal do Azure.
    - Selhání můžete provést pouze pro hostitele ESXi. Nemůžete navrácení služeb po obnovení virtuálních počítačů VMware nebo fyzických serverů do hostitelů Hyper-V, fyzických počítačů nebo pracovních stanic VMware.


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
* Pomocí VMotion můžete také přesunout hlavní cíl na jiného hostitele, kde se kolize názvů nestane. Pokud je stávající virtuální počítač osamocený počítač, přejmenujte ho, aby se nový virtuální počítač mohl vytvořit na stejném hostiteli ESXi.


### <a name="error-code-78093"></a>Kód chyby 78093

**Virtuální počítač není spuštěný, neodpovídá nebo není dostupný.**

Řešení tohoto problému:

Pokud chcete znovu zapnout ochranu virtuálního počítače s podporou převzetí služeb při selhání, musí být virtuální počítač Azure spuštěný, aby se služba mobility registrovala na místním konfiguračním serveru a mohla zahájit replikaci komunikací s procesovým serverem. Pokud je počítač v nesprávné síti nebo neběží (neodpovídá nebo není vypnutý), nemůže konfigurační server kontaktovat službu mobility na VIRTUÁLNÍm počítači za účelem zahájení ochrany.

* Restartujte virtuální počítač, aby mohl začít komunikovat zpátky místně.
* Po spuštění virtuálního počítače Azure restartujte úlohu opětovné ochrany.

### <a name="error-code-8061"></a>Kód chyby 8061

**Úložiště dat není dostupné z hostitele ESXi.**

Ověřte [hlavní požadavky na hlavní cíl a podporovaná úložiště dat](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) pro navrácení služeb po obnovení.


## <a name="troubleshoot-failback-errors"></a>Řešení chyb navrácení služeb po obnovení

Tato část popisuje běžné chyby, se kterými se můžete setkat během navrácení služeb po obnovení.

### <a name="error-code-8038"></a>Kód chyby 8038

**Nepovedlo se vyvolat místní virtuální počítač z důvodu chyby.**

K tomuto problému dochází, když se místní virtuální počítač zavede na hostiteli, který nemá dostatek zřízené paměti. 

Řešení tohoto problému:

* Zřídit více paměti na hostiteli ESXi.
* Kromě toho můžete pomocí VMotion přesunout virtuální počítač na jiného hostitele ESXi, který má dostatek paměti pro spuštění virtuálního počítače.

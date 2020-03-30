---
title: Poradce při potížích s obnovením služeb při selhání v zotavení po havárii virtuálního počítače VMware pomocí azure site recovery
description: Tento článek popisuje způsoby řešení problémů se navrácením služeb po obnovení služeb a opětovnou ochranou během zotavení po havárii virtuálního počítače VMware do Azure s Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498105"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Řešení potíží s navrácením služeb po obnovení z Azure do místního prostředí

Tento článek popisuje, jak řešit problémy, se kterými se můžete setkat při navrácení služeb virtuálních počítačích Azure do místní infrastruktury VMware, po převzetí služeb při selhání do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

Navrácení služeb po selhání v podstatě zahrnuje dva hlavní kroky. V prvním kroku po převzetí služeb při selhání je potřeba znovu chránit virtuální počítače Azure do místního prostředí, aby se začaly replikovat. Druhým krokem je spuštění převzetí služeb při selhání z Azure a vrátit se zpět do místního webu.

## <a name="common-issues"></a>Běžné problémy

- Pokud provedete zjišťování vCenter jen pro čtení a chráníte virtuální počítače, ochrana bude úspěšná a funguje převzetí služeb při selhání. Během opětovnéochrany se nezdaří převzetí služeb při selhání, protože úložiště dat nelze zjistit. Příznakem je, že úložiště dat nejsou uvedeny během opětovné ochrany. Chcete-li tento problém vyřešit, můžete aktualizovat pověření vCenter s příslušným účtem, který má oprávnění a potom opakovat úlohu.
- Když navrátíte virtuální počítač SIP a spustíte ho místně, uvidíte, že balíček Network Manager byl odinstalován z počítače. K této odinstalaci dochází, protože balíček Network Manager se odebere při obnovení virtuálního počítače v Azure.
- Když je virtuální počítač s Linuxem nakonfigurován se statickou IP adresou a přejde do Služby Microsoft Azure, ip adresa se získá ze služby DHCP. Při převzetí služeb při selhání do místního počítače virtuální počítač nadále používat DHCP k získání IP adresy. Ručně se přihlaste k počítači a v případě potřeby nastavte adresu IP zpět na statickou adresu. Virtuální počítač se systémem Windows může znovu získat statickou adresu IP.
- Pokud používáte bezplatnou edici ESXi 5.5 nebo bezplatnou edici vSphere 6 Hypervisor, převzetí služeb při selhání proběhne úspěšně, ale navrácení služeb po selhání se nepodaří. Chcete-li povolit navrácení služeb po selhání, upgradujte na zkušební licenci obou programů.
- Pokud se nemůžete dostat na konfigurační server z procesního serveru, zkontrolujte pomocí programu Telnet připojení ke konfiguračnímu serveru na portu 443. Můžete se také pokusit o příkaz ping na konfigurační server z procesního serveru. Procesní server by měl mít také prezenční signál, když je připojen ke konfiguračnímu serveru.
- Server Windows Server 2008 R2 SP1, který je chráněný jako fyzický místní server, nemůže být neúspěšný zpět z Azure na místní web.
- Za následujících okolností nelze vrátit zpět:
    - Jste migrovali počítače do Azure. [Další informace](migrate-overview.md#what-do-we-mean-by-migration).
    - Přesunuli jste virtuální ho do jiné skupiny prostředků.
    - Odstranili jste virtuální počítač Azure.
    - Zakázali jste ochranu virtuálního provozu.
    - Virtuální počítač jste vytvořili ručně v Azure. Počítač by měl být zpočátku chráněné místně a převzetí služby při selhání azure před opětovnou ochranou.
    - Můžete selhat pouze hostitele ESXi. Virtuální počítače VMware nebo fyzické servery v zařízení VMware nebo fyzických serverech nelze navrácení služeb po jejich pouzdřite hostitelům, fyzickým počítačům nebo pracovním stanicím VMware.


## <a name="troubleshoot-reprotection-errors"></a>Poradce při potížích s opětovnou ochranou

Tato část podrobně popisuje běžné chyby opětovnéochrany a jejich opravu.

### <a name="error-code-95226"></a>Kód chyby 95226

**Opětovné přisuzování se nezdařilo, protože virtuální počítač Azure se nemohl dostat k místnímu konfiguračnímu serveru.**

K této chybě dochází, když:

* Virtuální počítač Azure nemůže dosáhnout místní konfigurační server. Virtuální počítače nelze zjistit a zaregistrovat na konfiguračním serveru.
* Služba aplikace InMage Scout není spuštěna na virtuálním počítači Azure po převzetí služeb při selhání. Služba je potřebná pro komunikaci s místním konfiguračním serverem.

Řešení tohoto problému:

* Zkontrolujte, zda síť virtuálních počítačů Azure umožňuje virtuálnímu počítači Azure komunikovat s místním konfiguračním serverem. Můžete buď nastavit VPN site-to-site do místního datového centra, nebo nakonfigurovat připojení Azure ExpressRoute s privátním partnerského vztahu ve virtuální síti virtuálního počítače Azure.
* Pokud virtuální počítač může komunikovat s místním konfiguračním serverem, přihlaste se k virtuálnímu počítači. Pak zkontrolujte aplikační službu InMage Scout. Pokud zjistíte, že není spuštěna, spusťte službu ručně. Zkontrolujte, zda je typ spuštění služby nastaven na **možnost Automaticky**.

### <a name="error-code-78052"></a>Kód chyby 78052

**Ochranu nelze dokončit pro virtuální počítač.**

K tomuto problému může dojít, pokud je již virtuální počítač se stejným názvem na hlavním cílovém serveru, na který se lhaní zpět.

Řešení tohoto problému:

* Vyberte jiný hlavní cílový server na jiném hostiteli, aby znovuprotekci vytvořilpočítač na jiném hostiteli, kde se názvy nesrazí.
* Můžete také použít vMotion přesunout hlavní cíl na jiného hostitele, kde nedojde ke kolizi názvu. Pokud je existující virtuální počítač zbloudilý počítač, přejmenujte jej tak, aby nový virtuální počítač lze vytvořit na stejném hostiteli ESXi.


### <a name="error-code-78093"></a>Kód chyby 78093

**Virtuální ho virtuálního ms není spuštěn, ve stavu hung nebo není přístupný.**

Řešení tohoto problému:

Chcete-li znovu chránit virtuální počítač s připojením k selhání, musí být virtuální počítač Azure spuštěntak, aby se služba Mobility registrovala u konfiguračního serveru místně a mohla začít replikovat komunikací s procesním serverem. Pokud je počítač v nesprávné síti nebo není spuštěn (nereaguje nebo nefunguje), konfigurační server nemůže dosáhnout služby mobility na virtuálním počítači a zahájit reprotekci.

* Restartujte virtuální počítač, aby mohl začít komunikovat zpět místně.
* Po spuštění virtuálního počítače Azure restartujte úlohu reprotect.

### <a name="error-code-8061"></a>Kód chyby 8061

**Úložiště dat není přístupné z hostitele ESXi.**

Zkontrolujte [požadavky hlavního cíle a podporovaná úložiště dat](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) pro navrácení služeb po selhání.


## <a name="troubleshoot-failback-errors"></a>Poradce při potížích s chybami navrácení služeb po selhání

Tato část popisuje běžné chyby, se kterými se může dojít při navrácení služeb po selhání.

### <a name="error-code-8038"></a>Kód chyby 8038

**Nepodařilo se vyvolat místní virtuální počítač z důvodu chyby.**

K tomuto problému dochází, když místní virtuální počítač je vyvolána na hostitele, který nemá dostatek zřízené paměti. 

Řešení tohoto problému:

* Zřídit více paměti na hostitele ESXi.
* Kromě toho můžete použít vMotion přesunout virtuální ho do jiného hostitele ESXi, který má dostatek paměti pro spuštění virtuálního zařízení.

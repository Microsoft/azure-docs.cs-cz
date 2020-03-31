---
title: Aktualizace a upgrady komponent v Azure Site Recovery
description: Obsahuje přehled aktualizací služby Azure Site Recovery a upgradů komponent.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257820"
---
# <a name="service-updates-in-site-recovery"></a>Aktualizace služeb v obnovení webu

Tento článek obsahuje přehled aktualizací [Azure Site Recovery](site-recovery-overview.md) a popisuje, jak upgradovat součásti site recovery.

Site Recovery zveřejňuje aktualizace služeb v pravidelných intervalech. Aktualizace zahrnují nové funkce, vylepšení podpory, aktualizace součástí a opravy chyb. Chcete-li využít nejnovější funkce a opravy, doporučujeme spustit nejnovější verze součástí site recovery. 
 
 
## <a name="updates-support"></a>Podpora aktualizací

### <a name="support-statement-for-azure-site-recovery"></a>Prohlášení o podpoře pro Azure Site Recovery

Doporučujeme vždy upgradovat na nejnovější verze komponent:

**S každou novou verzí "N" součásti Azure Site Recovery, která je vydána, všechny verze pod "N-4" jsou považovány za mimo podporu**. 

> [!IMPORTANT]
> Oficiální podpora je pro upgrade z > verze N-4 na n verzi. Pokud například používáte n-6, musíte nejprve upgradovat na N-4 a poté upgradovat na N.


### <a name="links-to-currently-supported-update-rollups"></a>Odkazy na aktuálně podporované kumulativní aktualizace

 Projděte si nejnovější kumulativní (verze N) v [tomto článku](site-recovery-whats-new.md). Nezapomeňte, že site recovery poskytuje podporu pro verze N-4.



## <a name="component-expiry"></a>Konec platnosti součásti

Site Recovery vás upozorní na součásti s prošlou platností (nebo blížící se vypršení platnosti) e-mailem (pokud jste se přihlásili k odběru e-mailových oznámení) nebo na řídicím panelu trezoru na portálu.

- Kromě toho, pokud jsou k dispozici aktualizace, v zobrazení infrastruktury pro váš scénář na portálu se vedle komponenty zobrazí tlačítko **Aktualizace k dispozici.** Toto tlačítko vás přesměruje na odkaz pro stažení nejnovější verze komponenty.
-  Oznámení řídicího panelu trezorů nejsou dostupná, pokud replikujete virtuální počítače Hyper-V. 

E-maily oznámení jsou odesílány následujícím způsobem.

**Čas** | **Frequency**
--- | ---
60 dní před vypršením platnosti součásti | Jednou dvakrát týdně
Dalších 53 dní | Jednou týdně
Posledních 7 dní | Jednou denně
Po uplynutí doby platnosti | Jednou dvakrát týdně


### <a name="upgrading-outside-official-support"></a>Upgrade mimo oficiální podporu

Pokud je rozdíl mezi verzí komponenty a nejnovější verzí větší než čtyři, je to považováno za nepodporující. V tomto případě upgradujte následujícím způsobem: 

1. Upgradujte aktuálně nainstalovanou součást na aktuální verzi plus čtyři. Pokud je například vaše verze 9.16, upgradujte na verzi 9.20.
2. Poté upgradujte na další kompatibilní verzi. Takže v našem příkladu, po upgradu 9.16 na 9.20, upgradujte na 9.24. 

Postupujte stejným způsobem pro všechny příslušné součásti.

### <a name="support-for-latest-operating-systemskernels"></a>Podpora nejnovějších operačních systémů/jader

> [!NOTE]
> Pokud máte naplánované okno údržby a je v něm zahrnuto restartování, doporučujeme nejprve upgradovat součásti site recovery a potom pokračovat ve zbývajících plánovaných aktivitách v okně údržby.

1. Před upgradem verzí operačního systému nebo jádra ověřte, zda je cílová verze podporována site recovery. 

    - [Podpora virtuálních počítačích Azure.](azure-to-azure-support-matrix.md#replicated-machine-operating-systems)
    - Podpora [serveru VMware/fyzického](vmware-physical-azure-support-matrix.md#replicated-machines)
    - [Podpora Hyper-V.](hyper-v-azure-support-matrix.md#replicated-vms)
2. Projděte [si dostupné aktualizace](site-recovery-whats-new.md) a zjistěte, co chcete upgradovat.
3. Upgradujte na nejnovější verzi site recovery.
4. Upgradujte operační systém/jádro na požadované verze.
5. Restartování.


Tento proces zajišťuje, že operační systém počítače nebo jádro je upgradován na nejnovější verzi a že nejnovější změny obnovení webu potřebné pro podporu nové verze jsou načteny do počítače.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače Azure do Azure

V tomto scénáři důrazně doporučujeme [povolit automatické aktualizace](azure-to-azure-autoupdate.md). Obnovení webu můžete povolit takto:

- Během procesu povolit replikaci.
- Nastavením nastavení aktualizace rozšíření uvnitř úložiště.

Chcete-li aktualizace spravovat ručně, postupujte takto:

1. V úschovně > **replikované položky**klikněte v horní části obrazovky na toto oznámení: 
    
    **Je k dispozici nová aktualizace agenta replikace obnovení sítě. Klepnutím nainstalujete ->**

4. Vyberte virtuální chod, pro které chcete aktualizaci nainstalovat, a klikněte na **OK**.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Obnovení zotavení po havárii virtuálního počítače v v systému VMware/fyzického serveru do Azure

1. Na základě aktuální verze a [prohlášení o podpoře](#support-statement-for-azure-site-recovery)nainstalujte aktualizaci nejprve na místní konfigurační server podle těchto [pokynů](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Pokud máte horizontální navýšení kapacity procesu servery, aktualizujte je další, podle [těchto pokynů](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Chcete-li aktualizovat agenta mobility v každém chráněném počítači, naleznete [v tomto](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) článku.

### <a name="reboot-after-mobility-service-upgrade"></a>Restartujte počítač po upgradu služby Mobility

Restartování se doporučuje po každém upgradu služby Mobility, aby bylo zajištěno, že všechny nejnovější změny jsou načteny do zdrojového počítače.

Restartování není povinné, pokud rozdíl mezi verzí agenta během posledního restartování a aktuální verzí není větší než čtyři.

Příklad v tabulce ukazuje, jak to funguje.

|**Verze agenta (poslední restartování)** | **Upgrade na** | **Povinný restart?**|
|---------|---------|---------|
|9.16 |  9.18 | Není povinné|
|9.16 | 9.19 | Není povinné|
| 9.16 | 9.20 | Není povinné
 | 9.16 | 9.21 | Povinné.<br/><br/> Upgradujte na 9.20 a restartujte počítač před upgradem na 9.21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače Hyper-V do Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Mezi webem Hyper-V a Azure

1. Stáhněte si aktualizaci pro poskytovatele obnovení webu Microsoft Azure.
2. Nainstalujte zprostředkovatele na každý server Hyper-V registrovaný v site recovery. Pokud používáte cluster, upgradujte na všech uzlech clusteru.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mezi místním webem VMM a Azure
1. Stáhněte si aktualizaci pro poskytovatele obnovení webu Microsoft Azure.
2. Nainstalujte zprostředkovatele na server VMM. Pokud je VMM nasazenv clusteru, nainstalujte zprostředkovatele do všech uzlů clusteru.
3. Nainstalujte nejnovějšího agenta služby Microsoft Azure Recovery Services na všechny hostitele nebo uzly clusteru Hyper-V.


## <a name="between-two-on-premises-vmm-sites"></a>Mezi dvěma místními weby VMM
1. Stáhněte si nejnovější aktualizaci pro poskytovatele obnovení webu Microsoft Azure.
2. Nainstalujte nejnovějšího zprostředkovatele na server VMM, který spravuje sekundární lokalitu pro obnovení. Pokud je VMM nasazenv clusteru, nainstalujte zprostředkovatele do všech uzlů clusteru.
3. Po aktualizaci lokality pro obnovení nainstalujte zprostředkovatele na server VMM, který spravuje primární lokalitu.

## <a name="next-steps"></a>Další kroky

Sledujte naši stránku [Aktualizace Azure](https://azure.microsoft.com/updates/?product=site-recovery) a sledujte nové aktualizace a vydání.

---
title: Aktualizace a upgrady komponent v Azure Site Recovery
description: Poskytuje přehled aktualizací služby Azure Site Recovery a upgrady komponent.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: ramamill
ms.openlocfilehash: a1ea8b6fb9800d796670161288be0d86ce6ffc42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89424935"
---
# <a name="service-updates-in-site-recovery"></a>Aktualizace služby v Site Recovery

Tento článek obsahuje přehled aktualizací [Azure Site Recovery](site-recovery-overview.md) a popisuje, jak upgradovat součásti Site Recovery.

Site Recovery pravidelně publikují aktualizace služby. Aktualizace zahrnují nové funkce, vylepšení podpory, aktualizace součástí a opravy chyb. Aby bylo možné využívat nejnovější funkce a opravy, doporučujeme provozovat nejnovější verze Site Recovery komponent. 
 
 
## <a name="updates-support"></a>Podpora aktualizací

### <a name="support-statement-for-azure-site-recovery"></a>Příkaz Support pro Azure Site Recovery

Doporučujeme vždy upgradovat na nejnovější verze součástí:

**U všech nových verzí n Azure Site Recovery součásti, která je vydaná, jsou všechny verze nižší než n-4 považovány za nepodporované**. 

> [!IMPORTANT]
> Oficiální podpora je určena k upgradu z verze > N-4 na verzi N. Pokud například používáte službu s N-6, je nutné nejprve upgradovat na N-4 a pak upgradovat na N.


### <a name="links-to-currently-supported-update-rollups"></a>Odkazy na aktuálně podporované kumulativní aktualizace

 Projděte si nejnovější kumulativní aktualizaci (verze N) v [tomto článku](site-recovery-whats-new.md). Pamatujte, že Site Recovery poskytuje podporu pro N-4 verze.



## <a name="component-expiry"></a>Konec platnosti součásti

Site Recovery upozorňuje na komponenty s vypršenou platností (nebo blížící se k vypršení platnosti) e-mailem (Pokud se přihlásíte k odběru e-mailových oznámení) nebo na řídicím panelu trezoru na portálu

- Až budou aktualizace k dispozici, zobrazí se vedle komponenty v zobrazení infrastruktura pro váš scénář tlačítko **aktualizace k dispozici** . Toto tlačítko vás přesměruje na odkaz pro stažení nejnovější verze součásti.
-  Oznámení řídicího panelu trezory nejsou k dispozici, pokud provádíte replikaci virtuálních počítačů Hyper-V. 

E-mailová oznámení se odesílají takto.

**Čas** | **Frekvence**
--- | ---
60 dní před vypršením platnosti součásti | Jednou týdně
Dalších 53 dní | Jednou týdně
Posledních 7 dní | Jednou denně
Po vypršení platnosti | Jednou týdně


### <a name="upgrading-outside-official-support"></a>Upgrade mimo oficiální podporu

Je-li rozdíl mezi verzí komponenty a nejnovější verzí verze větší než 4, je považován za nepodporu. V takovém případě proveďte upgrade následujícím způsobem: 

1. Upgradujte aktuálně nainstalovanou komponentu na aktuální verzi a čtyři. Například pokud vaše verze je 9,16, pak upgradujte na 9,20.
2. Pak upgradujte na následující kompatibilní verzi. Takže v našem příkladu se po upgradu 9,16 na 9,20 upgradujte na 9,24. 

Pro všechny příslušné součásti použijte stejný postup.

### <a name="support-for-latest-operating-systemskernels"></a>Podpora pro nejnovější operační systémy/jádra

> [!NOTE]
> Pokud máte plánované časové období údržby a v něm je zahrnutý restart, doporučujeme nejprve upgradovat Site Recovery součásti a pak pokračovat ve zbývající části naplánovaných aktivit v časovém intervalu pro správu a údržbu.

1. Před upgradem operačního systému/verze jádra ověřte, zda je cílová verze Site Recovery podporována. 

    - Podpora [virtuálních počítačů Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) .
    - Podpora [VMware/fyzických serverů](vmware-physical-azure-support-matrix.md#replicated-machines)
    - Podpora [technologie Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) .
2. Projděte si [dostupné aktualizace](site-recovery-whats-new.md) a zjistěte, co chcete upgradovat.
3. Upgradujte na nejnovější verzi Site Recovery.
4. Upgradujte operační systém/jádro na požadované verze.
5. Restartování.


Tento proces zajistí, že operační systém počítače/jádro bude upgradován na nejnovější verzi a že nejnovější Site Recovery změny potřebné k podpoře nové verze se načtou do počítače.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače Azure do Azure

V tomto scénáři důrazně doporučujeme [Povolit automatické aktualizace](azure-to-azure-autoupdate.md). Site Recovery můžete dovolit, aby se aktualizace spravovaly následujícím způsobem:

- Během procesu povolení replikace.
- Nastavením nastavení aktualizace rozšíření v rámci trezoru.

Pokud chcete ručně spravovat aktualizace, můžete vybrat jednu z následujících možností:

1. Když je k dispozici nová aktualizace agenta, Site Recovery v trezoru k hornímu okraji stránky oznámení. V trezoru > **replikované položky** klikněte v horní části obrazovky na toto oznámení: 
    
    **K dispozici je nová aktualizace agenta replikace Site Recovery. Kliknutím nainstalujete >** <br/><br/>Vyberte virtuální počítače, pro které chcete aktualizaci použít, a potom klikněte na **OK**.

2. Na stránce Přehled zotavení po havárii virtuálního počítače najdete pole "stav agenta", které říká "kritický upgrade", pokud má agent vypršení platnosti. Klikněte na něj a postupujte podle pokynů k ručnímu upgradu virtuálního počítače.

## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače VMware nebo fyzického serveru do Azure

1. Na základě vaší aktuální verze a [příkazu podpory](#support-statement-for-azure-site-recovery)nainstalujte nejprve aktualizaci na místní konfigurační server, a [to podle těchto pokynů](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server). 
2. Pokud máte procesní servery se škálováním na více instancí, aktualizujte je dále pomocí [těchto pokynů](vmware-azure-manage-process-server.md#upgrade-a-process-server).
3. Pokud chcete aktualizovat agenta mobility na každém chráněném počítači, přečtěte si [Tento](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) článek.

### <a name="reboot-after-mobility-service-upgrade"></a>Po upgradu služby mobility restartujte počítač

Po každém upgradu služby mobility se doporučuje restartovat počítač, aby se zajistilo, že se na zdrojovém počítači načtou všechny nejnovější změny.

Restart není povinný, pokud rozdíl mezi verzí agenta během posledního restartování a aktuální verzí je větší než 4.

Příklad v tabulce ukazuje, jak to funguje.

|**Verze agenta (poslední restartování)** | **Upgrade na** | **Povinná restartování?**|
|---------|---------|---------|
|9,16 |  9,18 | Není povinné|
|9,16 | 9,19 | Není povinné|
| 9,16 | 9,20 | Není povinné
 | 9,16 | 9,21 | Povinné.<br/><br/> Upgradujte na 9,20 a pak restartujte počítač před upgradem na 9,21.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Zotavení po havárii virtuálního počítače Hyper-V do Azure

### <a name="between-a-hyper-v-site-and-azure"></a>Mezi lokalitou Hyper-V a Azure

1. Stáhněte aktualizaci pro poskytovatele Microsoft Azure Site Recovery.
2. Nainstalujte zprostředkovatele na každý server Hyper-V zaregistrovaný v Site Recovery. Pokud používáte cluster, proveďte upgrade na všech uzlech clusteru.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Mezi místním serverem VMM a Azure
1. Stáhněte aktualizaci pro poskytovatele Microsoft Azure Site Recovery.
2. Nainstalujte zprostředkovatele na server VMM. Pokud je v clusteru nasazený nástroj VMM, nainstalujte poskytovatele na všechny uzly clusteru.
3. Nainstalujte nejnovějšího agenta Microsoft Azure Recovery Services na všechny hostitele nebo uzly clusteru Hyper-V.


## <a name="between-two-on-premises-vmm-sites"></a>Mezi dvěma místními lokalitami VMM
1. Stáhněte si nejnovější aktualizaci poskytovatele Microsoft Azure Site Recovery.
2. Nainstalujte nejnovějšího poskytovatele na serveru VMM, který spravuje sekundární lokalitu pro obnovení. Pokud je v clusteru nasazený nástroj VMM, nainstalujte poskytovatele na všechny uzly clusteru.
3. Po aktualizaci lokality pro obnovení nainstalujte zprostředkovatele na server VMM, který spravuje primární lokalitu.

## <a name="next-steps"></a>Další kroky

Na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?product=site-recovery) Sledujte nové aktualizace a vydání.

---
title: Azure konzoly sériového portu GRUB a režimu jednoho uživatele | Dokumentace Microsoftu
description: Pomocí konzoly sériového portu pro grub ve službě Azure virtual machines.
services: virtual-machines-linux
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 54b5fb4ace64cc304a8eb8ce6d762d33ae18619a
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "40177579"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Použít pro volání SysRq a NMI konzoly sériového portu

## <a name="system-request-sysrq"></a>Požadavek na systém (SysRq)
SysRq je posloupnost kláves srozumitelné pro linuxového jádra systému operace, které můžete aktivovat sadu předdefinovaných akcí. Tyto příkazy jsou často používány při řešení potíží s virtuálního počítače nebo obnovení nelze provést prostřednictvím tradiční správy (například pokud je zablokovaná virtuálního počítače). Pomocí funkce SysRq konzoly sériového portu Azure bude napodobovat stisknutí SysRq klíče a znaky zadané na fyzické klávesnice.

Jakmile se doručí SysRq pořadí, bude konfigurace jádra řídit, jak systém reaguje. Informace o povolení a zakázání SysRq, najdete v článku *příručky pro správce SysRq* [text](https://aka.ms/kernelorgsysreqdoc) | [markdownu](https://aka.ms/linuxsysrq).  

Sériové konzoly Azure je možné odeslat SysRq na virtuálním počítači Azure pomocí klávesnice ikony na příkazovém řádku je uvedeno níže.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Výběrem "příkazu SysRq Send" se otevře dialogové okno, které se poskytují běžné možnosti SysRq ani nebude přijímat posloupnost SysRq příkazy zadané do dialogového okna.  To umožňuje provádět základní operace, například bezpečné restartování pomocí pro řadu SysRq: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Příkaz SysRq nelze použít na virtuálních počítačích, které se zastaví nebo jejichž jádra je ve stavu, jako nereagující. (například poplachu jádra).

### <a name="enable-sysrq"></a>Povolit SysRq 
Jak je popsáno v *příručky pro správce SysRq* SysRq výše, lze nakonfigurovat tak, aby všechny, jsou k dispozici žádný nebo jenom určité příkazy. Můžete povolit všechny příkazy SysRq pomocí níže uvedených kroků, ale nebude byly zachovány při restartování:
```
echo "1" >/proc/sys/kernel/sysrq
```
K zajištění SysReq konfigurace trvalosti, vám pomůžou následující povolit všechny příkazy SysRq
1. Přidání tohoto řádku */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Restartování nebo aktualizaci sysctl spuštěním <br>
    `sysctl -p`

### <a name="command-keys"></a>Příkaz klíče 
Z SysRq příručky pro správce výše:

|Příkaz| Funkce
| ------| ----------- |
|``b``  |   Okamžitě restartovat systém bez synchronizace nebo odpojení disků.
|``c``  |   Provede zhroucení systému NULL ukazatel přistoupit přes ukazatel. Havarijní výpis bude provedena v případě nakonfigurované.
|``d``  |   Ukazuje všech zámků, které jsou uloženy.
|``e``  |   Odeslat SIGTERM všech procesů kromě init.
|``f``  |   Zavolá killer oom k ukončení procesu nároky paměť, ale nejsou nějaké Pokud nic lze ukončit.
|``g``  |   Používá kgdb (ladicí program jádra)
|``h``  |   Zobrazí nápovědu (libovolné jiné klávesy než ty, které tady najdete také zobrazíte nápovědu, ale ``h`` se snadno zapamatuje :-)
|``i``  |    Odeslat SIGKILL všech procesů kromě init.
|``j``  |    Nuceně "Právě uvolnit ji" - zmrazené podle FIFREEZE ioctl systémy souborů.
|``k``  |    Klíč zabezpečeného přístupu (SAK) ukončí všechny programy v konzole pro aktuální virtuální. Poznámka: Naleznete v tématu důležité poznámky níže v části SAK.
|``l``  |    Zobrazuje backtrace zásobníku pro všechny aktivní procesory.
|``m``  |    Bude vypsat aktuální informace o paměti ke konzole.
|``n``  |    Aby nice možné RT úlohy
|``o``  |    Se vypne systému (Pokud je nakonfigurovaná a podporované).
|``p``  |    Bude vypsat aktuální registry a příznaky ke konzole.
|``q``  |    Bude výpis paměti na procesor seznam všech ozbrojené hrtimers (ale ne regulární timer_list časovače) a podrobné informace o všech zařízeních clockevent.
|``r``  |    Vypne režim nezpracovaná klávesnice a nastaví na XLATE.
|``s``  |    Pokusí se synchronizovat všechny připojené systémy souborů.
|``t``  |    Se vypsat seznam aktuálních úkolů a jejich informace ke konzole.
|``u``  |    Se pokusí znovu připojit všechny připojené systémy souborů jen pro čtení.
|``v``  |    Vynuceně obnoví framebuffer konzoly
|``v``  |    Způsobí, že ETM vyrovnávací paměti s výpisem paměti [ARM specifické]
|``w``  |    Vypíše úlohy, které jsou ve stavu nepřerušitelný zdroj (blokováno).
|``x``  |    Používá rozhraní xmon na platformách Pocket PC/powerpc. Zobrazit globální PMU zaregistruje na sparc64. Vypsat všechny položky MIPS vyrovnávací paměti TLB.
|``y``  |    Zobrazit globální registruje procesor [konkrétní SPARC 64]
|``z``  |    Vypsat ftrace vyrovnávací paměti
|``0``-``9`` | Nastaví úroveň protokolu konzoly, řízení, které zprávy jádra se vytisknou do konzoly. (``0``pro příklad by ji činilo tak, aby pouze mimořádných zpráv jako poplachy nebo OOPSes by ji činilo ke konzole.)

### <a name="distribution-specific-documentation"></a>Distribuce příslušnou část dokumentace ###
Distribuce příslušnou část dokumentace na SysRq a kroky konfigurace Linux při přijetí příkazu SysRq "Selhání" Vytvořit výpis stavu systému najdete na odkazech níže:
#### <a name="ubuntu"></a>Ubuntu ####
 - [Výpis jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)
#### <a name="red-hat"></a>Red Hat ####
- [Co je SysRq zařízení a jak ji používat?](https://access.redhat.com/articles/231663)
- [Jak používat funkci SysRq ke shromažďování informací ze serveru RHEL](https://access.redhat.com/solutions/2023)
#### <a name="suse"></a>SUSE ####
- [Konfigurace funkce capture core s výpisem paměti jádra](https://www.suse.com/support/kb/doc/?id=3374462)
#### <a name="coreos"></a>CoreOS ####
- [Shromažďování údajů o protokoly chyb](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Maskable přerušení (NMI) 
Maskable přerušení (NMI) slouží k vytvoření signál, který nebude ignorovat softwaru na virtuálním počítači. V minulosti NMIs se používají ke sledování hardwarových problémů v systémech, které vyžaduje konkrétní odezvy.  Dnes, programátoři a správce systému často používají NMI jako mechanismus pro ladění nebo řešení potíží s systémy, které jsou zablokovaná.

Konzole sériového portu je možné odeslat NMI na virtuálním počítači Azure pomocí klávesnice ikony na příkazovém řádku je uvedeno níže. Jakmile se doručí NMI, konfigurace virtuálního počítače bude řídit, jak systém reaguje.  Operační systémy lze nakonfigurovat, aby při selhání a vytvořit výpis stavu paměti operačního systému Linux dostane NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Pro systémy Linux, které podporují sysctl jádra parametry konfigurace můžete povolit nějaké při příjmu tohoto NMI pomocí následující:
1. Přidání tohoto řádku */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Restartování nebo aktualizaci sysctl spuštěním <br>
    `sysctl -p`

Další informace o konfiguraci jádra systému Linux, včetně `unknown_nmi_panic`, `panic_on_io_nmi`, a `panic_on_unrecovered_nmi`, naleznete v tématu: [dokumentaci/proc/sys/jádra / *](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Distribuce příslušnou část dokumentace na NMI a kroky konfigurace Linux při přijetí NMI vytvořit výpis stavu systému najdete na odkazech níže:
 
 ### <a name="ubuntu"></a>Ubuntu 
 - [Výpis jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

 ### <a name="red-hat"></a>Red Hat 
 - [Co je NMI a k čemu mohu využít?](https://access.redhat.com/solutions/4127)
 - [Jak je nakonfigurovat systém selhání při vložení NMI přepínač?](https://access.redhat.com/solutions/125103)
 - [Případ chyby vypsat příručky pro správce](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Konfigurace funkce capture core s výpisem paměti jádra](https://www.suse.com/support/kb/doc/?id=3374462)
### <a name="coreos"></a>CoreOS 
- [Shromažďování údajů o protokoly chyb](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Další postup
* Na hlavní stránce dokumentace sériové konzoly Linux se nachází [tady](serial-console.md).
* Přihlásit se pomocí konzoly sériového portu [kontrole a do režimu jednoho uživatele](serial-console-grub-single-user-mode.md)
* Je taky dostupná ke konzole sériového portu [Windows](../windows/serial-console.md) virtuálních počítačů
* Další informace o [Diagnostika spouštění](boot-diagnostics.md)
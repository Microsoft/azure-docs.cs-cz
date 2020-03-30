---
title: Azure Serial Console pro volání SysRq a NMI
description: Použití sériové konzoly pro volání SysRq a NMI ve virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5eba50e7cfc00c081622972ca09f305b34c19c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035003"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Použití sériové konzoly pro volání nemaskovaných přerušení a SysRq

## <a name="system-request-sysrq"></a>Systémový požadavek (SysRq)
SysRq je posloupnost klíčů rozuměl linuxového jádra operačního systému, který může vyvolat sadu předdefinovaných akcí. Tyto příkazy se často používají, když řešení potíží s virtuálním počítačem nebo obnovení nelze provést prostřednictvím tradiční správy (například pokud virtuální počítač neodpovídá). Pomocí funkce SysRq služby Azure Serial Console napodobuje stisknutí klávesy SysRq a znaky zadané na fyzické klávesnici.

Po doručení sekvence SysRq bude konfigurace jádra řídit, jak systém reaguje. Informace o povolení a zakázání sysRq naleznete v *sysrq admin průvodce* [text](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

Konzolu Azure Serial Console lze použít k odeslání SysRq do virtuálního počítače Azure pomocí ikony klávesnice v panelu příkazů uvedených níže.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Výběrem "Odeslat Příkaz SysRq" se otevře dialogové okno, které bude poskytovat společné možnosti SysRq nebo přijmout posloupnost příkazů SysRq zadaných do dialogového okna.  To umožňuje série SysRq provádět operace na vysoké úrovni, jako je `REISUB`například bezpečné restartování pomocí: .

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Příkaz SysRq nelze použít na virtuálních počítačích, které jsou zastaveny nebo jejichž jádro je ve stavu bez odezvy. (například panika jádra).

### <a name="enable-sysrq"></a>Povolit sysRq 
Jak je popsáno v *SysRq Admin Guide* výše, SysRq lze nakonfigurovat tak, aby všechny, žádný, nebo pouze některé příkazy jsou k dispozici. Můžete povolit všechny příkazy SysRq pomocí níže uvedeného kroku, ale nepřežije restart:
```
echo "1" >/proc/sys/kernel/sysrq
```
Chcete-li konfiguraci SysReq provést jako trvalou, můžete povolit všechny příkazy SysRq následujícím postupem.
1. Přidání tohoto řádku do */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Restartování nebo aktualizace sysctlu spuštěním <br>
    `sysctl -p`

### <a name="command-keys"></a>Příkazové klávesy 
Z SysRq Admin Guide výše:

|Příkaz| Funkce
| ------| ----------- |
|``b``  |   Bude okamžitě restartovat systém bez synchronizace nebo odpojení disků.
|``c``  |   Provede zhroucení systému ukazatelem NULL, který je dereference. Crashdump bude přijata, pokud nakonfigurován.
|``d``  |   Zobrazí všechny uzamykané zámky.
|``e``  |   Odeslat SIGTERM do všech procesů, s výjimkou init.
|``f``  |   Zavolá oom vrah zabít proces paměti prase, ale nepropadejte panice, pokud nic nemůže být zabit.
|``g``  |   Používá kgdb (ladicí program jádra)
|``h``  |   Zobrazí nápovědu (jakýkoli jiný klíč než ty, ``h`` které jsou zde uvedeny, zobrazí také nápovědu, ale je snadno zapamatovatelné :-)
|``i``  |    Odeslat SIGKILL do všech procesů, s výjimkou init.
|``j``  |    Násilně "Jen rozmrazit" - souborové systémy zmrazené FIFREEZE ioctl.
|``k``  |    Secure Access Key (SAK) Zabije všechny programy na aktuální virtuální konzole. Poznámka: Viz důležité komentáře níže v sekci SAK.
|``l``  |    Zobrazuje zpětné sledování zásobníku pro všechny aktivní procesory.
|``m``  |    Vypíše aktuální informace o paměti do konzole.
|``n``  |    Používá se k tomu, aby rt úkoly byly pěkné
|``o``  |    Vypne systém (pokud je nakonfigurován a podporován).
|``p``  |    Vypíše aktuální registry a příznaky do konzole.
|``q``  |    Bude výpis na CPU seznamy všech ozbrojených hrtimers (ale ne pravidelné timer_list časovače) a podrobné informace o všech zařízeních clockevent.
|``r``  |    Vypne režim zdvojené klávesnice a nastaví jej na XLATE.
|``s``  |    Pokusí se synchronizovat všechny připojené souborové systémy.
|``t``  |    Vypíše seznam aktuálních úkolů a jejich informací do konzole.
|``u``  |    Pokusí se znovu připojit všechny připojené souborové systémy jen pro čtení.
|``v``  |    Silně obnovuje konzolu framebuffer
|``v``  |    Příčiny výpisu vyrovnávací paměti ETM [SPECIFICKÉ PRO ARM]
|``w``  |    Vypíše úkoly, které jsou ve stavu nepřerušitelné (blokovány).
|``x``  |    Používá xmon rozhraní na ppc / powerpc platformy. Zobrazit globální PMU registry na sparc64. Vypsat všechny položky TLB na MIPS.
|``y``  |    Zobrazit globální registry procesorů [SPARC-64 specific]
|``z``  |    Vysaďte vyrovnávací paměť ftrace
|``0``-``9`` | Nastaví úroveň protokolu konzoly a řídí, které zprávy jádra budou na konzoli vytištěny. (``0``, například by to tak, že pouze tísňové zprávy, jako jsou PANIC nebo OOPSes by se do konzole.)

### <a name="distribution-specific-documentation"></a>Dokumentace specifická pro distribuci ###
Dokumentace pro konkrétní distribuci na SysRq a kroky ke konfiguraci Linuxu k vytvoření výpisu stavu systému, když obdrží příkaz SysRq "Crash", naleznete v následujících odkazech:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Výpis stavu jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Co je zařízení SysRq a jak ho mohu používat?](https://access.redhat.com/articles/231663)
- [Jak používat zařízení SysRq ke shromažďování informací ze serveru RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurace zachycení výpisu stavu jádra jádra](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Shromažďování protokolů selhání](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Nemaskovatelné přerušení (NMI) 
Nemaskovatelné přerušení (NMI) je navrženo tak, aby vytvořilo signál, který software ve virtuálním počítači nebude ignorovat. Historicky, NMI byly použity ke sledování problémů s hardwarem v systémech, které vyžadují konkrétní doby odezvy.  Programátoři a správci systému dnes často používají NMI jako mechanismus pro ladění nebo odstraňování problémů se systémy, které nereagují.

Konzolu Serial Console lze použít k odeslání NMI do virtuálního počítače Azure pomocí ikony klávesnice na panelu příkazů zobrazeném níže. Po doručení NMI bude konfigurace virtuálního počítače řídit, jak systém reaguje.  Operační systémy Linux lze nakonfigurovat tak, aby havarovaly a vytvořily výpis paměti, který operační systém obdrží nmi.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

U linuxových systémů, které podporují sysctl pro konfiguraci parametrů jádra, můžete při příjmu tohoto NMI povolit paniku pomocí následujícího:
1. Přidání tohoto řádku do */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Restartování nebo aktualizace sysctlu spuštěním <br>
    `sysctl -p`

Další informace o konfiguracích jádra Linuxu, `unknown_nmi_panic`včetně , `panic_on_io_nmi`, a `panic_on_unrecovered_nmi`, viz: Dokumentace pro [/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Dokumentace pro konkrétní distribuci nmi a kroky konfigurace Linuxu pro vytvoření výpisu stavu systému při přijetí NMI naleznete v následujících odkazech:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Výpis stavu jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Co je NMI a k čemu ho mohu použít?](https://access.redhat.com/solutions/4127)
 - [Jak mohu nakonfigurovat systém tak, aby havaroval, když je posunut přepínač NMI?](https://access.redhat.com/solutions/125103)
 - [Průvodce správcem výpisu z havárie](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Konfigurace zachycení výpisu stavu jádra jádra](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Shromažďování protokolů selhání](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Další kroky
* Hlavní stránka dokumentace serial console linuxu je [umístěna zde](serial-console.md).
* Spuštění do grubu pomocí konzoly Serial Console [a přepzení režimu jednoho uživatele](serial-console-grub-single-user-mode.md)
* Konzola Serial Console je k dispozici také pro virtuální servery [windows.](../windows/serial-console.md)
* Další informace o [diagnostice spouštění](boot-diagnostics.md)
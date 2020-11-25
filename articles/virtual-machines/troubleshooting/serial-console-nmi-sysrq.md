---
title: Sériová konzola Azure pro volání SysRq a NMI | Microsoft Docs
description: Použití sériové konzoly pro volání SysRq a NMI na virtuálních počítačích Azure.
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
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022849"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>Použití konzole sériového rozhraní Azure pro volání SysRq a NMI

## <a name="system-request-sysrq"></a>Požadavek na systém (SysRq)
SysRq je posloupnost klíčů, které rozumí jádro operačního systému Linux, které může aktivovat sadu předem definovaných akcí. Tyto příkazy se často používají v případě, že řešení potíží s virtuálním počítačem nebo obnovení nelze provést pomocí tradiční správy (například pokud virtuální počítač neodpovídá). Použití funkce SysRq v konzole sériového kódu Azure napodobuje stisknutí klávesy SysRq a znaků, které jsou zadány na fyzické klávesnici.

Po doručení sekvence SysRq bude konfigurace jádra řídit, jak systém reaguje. Informace o povolení a zakázání SysRq najdete v tématu *Příručka pro správu SysRq* – [text](https://aka.ms/kernelorgsysreqdoc)  |  [Markdownu](https://aka.ms/linuxsysrq).

Pomocí ikony klávesnice na panelu příkazů, která se zobrazuje níže, můžete k odeslání SysRq na virtuální počítač Azure použít konzolu Azure Serial.

![Snímek obrazovky s konzolou sériového rozhraní Azure Ikona klávesnice se zvýrazní a její nabídka se zobrazí. Tato nabídka obsahuje položku příkazu Odeslat SysRq.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Když vyberete příkaz Odeslat SysRq, otevře se dialogové okno, ve kterém budou uvedeny běžné možnosti SysRq, nebo přijmout sekvenci SysRq příkazů zadaných do dialogového okna.  To umožňuje řadě SysRq provádět operace vysoké úrovně, jako je bezpečné restartování pomocí: `REISUB` .

![Snímek obrazovky dialogového okna Odeslat příkaz SysRq do hosta Je vybrána možnost pro zadání příkazů a pole obsahuje REISUB.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Příkaz SysRq se nedá použít na virtuálních počítačích, které jsou zastavené nebo jejichž jádro je v nereagující stavu. (například nouzové jádro jádra).

### <a name="enable-sysrq"></a>Povolit SysRq
Jak je popsáno v *příručce pro správu SysRq* výše, SysRq je možné nakonfigurovat tak, aby byly k dispozici všechny, žádné nebo pouze určité příkazy. Všechny příkazy SysRq můžete povolit pomocí následujícího kroku, ale nezůstane restartováním:
```
echo "1" >/proc/sys/kernel/sysrq
```
Chcete-li nastavit konfiguraci SysReq jako trvalou, můžete provést následující postup, kterým povolíte všechny příkazy SysRq.
1. Přidání tohoto řádku do */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Restartování nebo aktualizace sysctl spuštěním <br>
    `sysctl -p`

### <a name="command-keys"></a>Příkazové klávesy
V příručce pro správu SysRq výše:

|Příkaz| Funkce
| ------| ----------- |
|``b``  |   Provede restart systému hned bez synchronizace nebo odpojení disků.
|``c``  |   Provede selhání systému pomocí zpětného odkazu na ukazatel s hodnotou NULL. V případě nakonfigurovaného výpisu bude proveden výpis.
|``d``  |   Zobrazuje všechny zámky, které jsou uchovávány.
|``e``  |   Odešle SIGTERM všem procesům, s výjimkou init.
|``f``  |   Zavolá OOM Killer, který ukončí proces HOG paměti, ale nejedná se o nemožnost, pokud by nebylo možné nic ukončit.
|``g``  |   Používá se v kgdb (ladicí program jádra).
|``h``  |   Zobrazí se vám také obrazovka (jakýkoli jiný klíč, než jaké jsou uvedeny v seznamu, ale budete ``h`` si moct snadno zapamatovat:-)).
|``i``  |    Odešle SIGKILL všem procesům, s výjimkou init.
|``j``  |    Vynuceně "Stačí ho rozmrazit" – systémové systémy zmrazené FIFREEZE IOCTL.
|``k``  |    Klíč zabezpečeného přístupu (SAK) ukončuje všechny programy v aktuální virtuální konzole. Poznámka: v části SAK se podívejte na důležité komentáře.
|``l``  |    Zobrazuje zpětné trasování zásobníku pro všechny aktivní procesory.
|``m``  |    Vypíše informace o aktuální paměti do konzoly.
|``n``  |    Slouží k zajištění skvělého možného úkolu RT
|``o``  |    Vypne váš systém (Pokud je nakonfigurovaný a podporovaný).
|``p``  |    Vypíše aktuální registry a příznaky do konzoly.
|``q``  |    Vypíše na seznam PROCESORů všechny ozbrojené hrtimers (ale ne pravidelné časovače timer_list) a podrobné informace o všech zařízeních clockevent.
|``r``  |    Vypne režim nezpracovaných kláves a nastaví ho na XLATE.
|``s``  |    Pokusí se synchronizovat všechny připojené systémy souborů.
|``t``  |    Vypíše seznam aktuálních úloh a jejich informace do konzoly.
|``u``  |    Pokusí se znovu připojit všechny připojené systémy souborů jen pro čtení.
|``v``  |    Vynuceně obnoví konzolu framebuffer.
|``v``  |    Způsobuje výpis vyrovnávací paměti ETM [specifický pro ARM]
|``w``  |    Vypíše úlohy, které jsou v nenepřerušitelných (blokovaných) stavu.
|``x``  |    Používá se rozhraním xmon na platformách Pocket PC/PowerPC. Zobrazit globální Registry PMU v sparc64 Vypsat všechny položky TLB v MIPS
|``y``  |    Zobrazit Registry globálních PROCESORů [specifické pro SPARC-64]
|``z``  |    Výpis vyrovnávací paměti fTRACE
|``0``-``9`` | Nastaví úroveň protokolu konzoly a řídí, které zprávy jádra budou vytištěny do konzoly. ( ``0`` například by to vedlo k tomu, že by konzola měla dělat jenom nouzové zprávy, jako je například nouzové nebo nesmysly.)

### <a name="distribution-specific-documentation"></a>Dokumentace ke konkrétní distribuci ###
Dokumentaci určenou pro distribuci na SysRq a postup pro konfiguraci systému Linux, aby při přijetí příkazu SysRq "Crash" vytvořily výpis stavu systému, naleznete v následujících odkazech:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Výpis stavu selhání jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Co je zařízení SysRq a jak ho můžu použít?](https://access.redhat.com/articles/231663)
- [Jak pomocí zařízení SysRq shromažďovat informace ze serveru RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Nakonfigurovat zachytávání výpisu jádra jádra](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Shromažďování protokolů selhání](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Přerušení bez maskování (NMI)
Nemaskovatelné přerušení (NMI) je navržené k vytvoření signálu, který software na virtuálním počítači nebude ignorovat. Historicky jsme NMIs použili k monitorování problémů s hardwarem v systémech, které vyžadovaly určitou dobu odezvy.  V současné době se programátoři a správci systému často používají NMI jako mechanismus pro ladění nebo odstraňování potíží se systémy, které nereagují.

Pomocí ikony klávesnice na panelu příkazů, která je uvedená níže, se dá použít Konzola sériového portu k odeslání NMI virtuálnímu počítači Azure. Po doručení NMI bude konfigurace virtuálního počítače řídit, jak systém reaguje.  Operační systémy Linux se dají nakonfigurovat tak, aby selhaly, a vytvoří výpis paměti. operační systém obdrží NMI.

![Snímek obrazovky s konzolou sériového portu Ikona klávesnice se zvýrazní a její nabídka se zobrazí. Tato nabídka obsahuje položku pro odeslání nemaskované položky přerušení.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Povolit NMI
Pro systémy Linux, které podporují sysctl pro konfiguraci parametrů jádra, můžete povolit nenouzové při příjmu tohoto NMI pomocí následujících kroků:
1. Přidání tohoto řádku do */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Restartování nebo aktualizace sysctl spuštěním <br>
    `sysctl -p`

Další informace o konfiguracích jádra systému Linux, včetně `unknown_nmi_panic` , `panic_on_io_nmi` a, `panic_on_unrecovered_nmi` najdete v tématu: [dokumentace pro/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Dokumentaci určenou pro distribuci na NMI a postup pro konfiguraci systému Linux, aby při přijetí NMI mohl vytvořit výpis stavu systému, naleznete v následujících odkazech:

### <a name="ubuntu"></a>Ubuntu
 - [Výpis stavu selhání jádra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Co je NMI a k čemu ho můžu použít?](https://access.redhat.com/solutions/4127)
 - [Jak můžu nakonfigurovat systém tak, aby došlo k chybě, když je nabízený přepínač NMI?](https://access.redhat.com/solutions/125103)
 - [Průvodce pro správce výpisu stavu systému](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Nakonfigurovat zachytávání výpisu jádra jádra](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Shromažďování protokolů selhání](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Další kroky
* Hlavní stránka dokumentace pro Linux na sériové konzole se nachází [tady](serial-console-linux.md).
* Pomocí sériové konzoly spusťte [grub a zadejte režim single user](serial-console-grub-single-user-mode.md) .
* Pro virtuální počítače s [Windows](serial-console-windows.md) je dostupná taky konzola sériového portu.
* Další informace o [diagnostice spouštění](boot-diagnostics.md)
---
title: Linux VM boty grub záchranu
description: Spuštění virtuálního počítače se nezdařilo, protože virtuální počítač vstoupil do záchranné konzole
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/28/2019
ms.author: tiag
ms.openlocfilehash: c24a840716841d04537ac5b77bcaf26fca4b78cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561945"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Linux VM boty grub záchranu

Zjistili jsme, že váš virtuální počítač (VM) vstoupil do záchranné konzole. K problému dochází, když váš virtuální počítač s Linuxem měl nedávno použité změny jádra, jako je například upgrade jádra, a již se nespouští správně kvůli chybám jádra během spouštěcího procesu. Během zaváděcího procesu, když se zavaděč pokusí najít jádro Linuxu a předat mu řízení zaváděcího zařízení, virtuální počítač vstoupí do záchranné konzole, když se nezdaří předání.

Pokud zjistíte, že se v budoucnu nemůžete připojit k virtuálnímu počítači, můžete zobrazit snímek obrazovky s virtuálním počítačem pomocí okna diagnostiky spouštění na webu Azure Portal. To vám může pomoct s diagnostikou problému a určením, jestli je jeho příčinou podobná chyba spuštění.

## <a name="recommended-steps"></a>Doporučené kroky

Postupujte podle pokynů ke zmírnění v závislosti na chybě, kterou obdržíte:

### <a name="error---unknown-filesystem"></a>Chyba – neznámý souborový systém

* Pokud se zobrazuje chyba **Neznámý souborový systém**, může tato chyba vyplývat z poškození systému souborů na spouštěcím oddílu nebo nesprávné konfigurace jádra.

   * Pokud jde o problémy se systémem souborů, postupujte podle pokynů v článku [Linux Recovery: Can SSH to Linux VM kvůli chybám systému souborů (fsck, inodes)](https://blogs.msdn.microsoft.com/linuxonazure/2016/09/13/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes/).
   * Problémy s jádrem navazují na kroky v článku [Obnovení Linuxu: Ruční oprava problémů bez spuštění souvisejících s problémy s jádrem](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-manually-fixing-non-boot-issues-related-to-kernel-problems/)nebo [Linux Recovery: Oprava problémů bez spuštění souvisejících s problémy s jádrem pomocí chroot](https://blogs.msdn.microsoft.com/linuxonazure/2016/10/09/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Chyba - Soubor nebyl nalezen.

* Pokud se zobrazuje chyba **Chyba 15: Soubor nebyl nalezen nebo počáteční ram disk** nebo soubor **initrd/initramfs nebyl nalezen**, postupujte podle následujících kroků.

    * Pro chybějící `/boot/grub2/grub.cfg` soubor `initrd/initramfs` nebo pokračujte následujícím procesem:

    1. Ujistěte se, `/etc/default/grub` že existují a mají správné / požadované nastavení. Pokud nevíte, které jsou výchozí nastavení, můžete zkontrolovat s funkčním virtuálním počítačem.

    2. Dále spusťte následující příkaz pro obnovení jeho konfigurace:`grub2-mkconfig -o /boot/grub2/grub.cfg`

   * `/boot/grub/menu.lst`Pokud je chybějící soubor , tato chyba je pro starší verze operačního systému (**RHEL 6.x**, **Centos 6.x** a **Ubuntu 14.04**), takže příkazy se mohou lišit. Budete muset stočit starý server a test, abyste zajistili, že budou k dispozici správné příkazy.

### <a name="error---no-such-partition"></a>Chyba - Žádný takový oddíl

* Pokud se zobrazuje chyba **Žádný takový oddíl**, podívejte se na případ scénář : ["žádný takový oddíl" chyba při pokusu o spuštění virtuálního počítače po pokusu o rozšíření jednotky operačního systému](https://blogs.technet.microsoft.com/shwetanayak/2017/03/12/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive/).

### <a name="error---grubcfg-file-not-found"></a>Chyba - soubor grub.cfg nebyl nalezen.

* Pokud se vám zobrazuje **chyba /boot/grub2/grub.cfg, která nebyla nalezena**, postupujte podle následujících kroků.

    * Pro chybějící `/boot/grub2/grub.cfg` soubor `initrd/initramfs` nebo pokračujte následujícím procesem:

    1. Ujistěte se, `/etc/default/grub` že existují a mají správné / požadované nastavení. Pokud nevíte, které jsou výchozí nastavení, můžete zkontrolovat s funkčním virtuálním počítačem.

    2. Dále spusťte následující příkaz pro `grub2-mkconfig -o /boot/grub2/grub.cfg`obnovení jeho konfigurace: .

   * `/boot/grub/menu.lst`Pokud je chybějící soubor , tato chyba je pro starší verze operačního systému (**RHEL 6.x**, **Centos 6.x** a **Ubuntu 14.04**), takže příkazy by mohly odložit. Stočte starý server a otestujte jej, abyste zajistili, že budou k dispozici správné příkazy.

## <a name="next-steps"></a>Další kroky

* [Přehled agenta virtuálního počítače Azure](../extensions/agent-windows.md)
* [Rozšíření a funkce virtuálních strojů pro Windows](../extensions/features-windows.md)


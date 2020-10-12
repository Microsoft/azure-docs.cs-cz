---
title: Spuštění virtuálního počítače se systémem Linux pro grub záchranu
description: Virtuální počítač se nepovedlo spustit, protože virtuální počítač přešel do záchranné konzoly.
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
ms.openlocfilehash: 5a2fd7fcfdae8559bfb39bffff7c73c7082a86aa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543278"
---
# <a name="linux-vm-boots-to-grub-rescue"></a>Spuštění virtuálního počítače se systémem Linux pro grub záchranu

Zjistili jsme, že váš virtuální počítač přešel do záchranné konzoly. K tomuto problému dochází, když se v poslední době použily změny jádra, jako je například upgrade jádra, a že se už nespouští správně z důvodu chyb jádra během procesu spouštění. Když se během procesu spouštění pokusí spouštěcí zavaděč najít jádro systému Linux a předat do něj řízení spouštění, virtuální počítač přejde do záchranné konzole, když se operace předání nepovede.

Pokud zjistíte, že se v budoucnu nemůžete připojit k virtuálnímu počítači, můžete zobrazit snímek obrazovky virtuálního počítače pomocí okna Diagnostika spouštění v Azure Portal. To vám může pomoct s diagnostikou problému a určením, jestli je jeho příčinou podobná chyba spuštění.

## <a name="recommended-steps"></a>Doporučené kroky

Postupujte podle následujících kroků pro zmírnění rizika v závislosti na chybě, kterou obdržíte:

### <a name="error---unknown-filesystem"></a>Chyba – neznámý systém souborů

* Pokud se vám zobrazuje chyba **Neznámý systém**souborů, může tato chyba způsobit poškození systému souborů ve spouštěcím oddílu nebo nesprávnou konfiguraci jádra.

   * V případě problémů se systémem souborů postupujte podle kroků v článku [obnovení ze systému Linux: z důvodu chyb systému souborů (fsck, uzlů inode) nelze provést SSH pro virtuální počítač se systémem Linux](/archive/blogs/linuxonazure/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck-inodes).
   * V případě problémů s jádrem postupujte podle kroků v článku [Postup obnovení virtuálního počítače se systémem Azure Linux z problémů se spouštěním souvisejících s jádrem](https://support.microsoft.com/help/4091524/how-recover-azure-linux-vm-from-kernel-related-boot-related-issues)nebo [obnovením systému Linux: Oprava potíží s nespouštěním, které souvisí s problémy jádra pomocí chroot](http://linuxonazure.azurewebsites.net/linux-recovery-fixing-non-boot-issues-related-to-kernel-problems-using-chroot/).
   
### <a name="error---file-not-found"></a>Chyba – soubor nebyl nalezen.

* Pokud se zobrazí chyba chyba **15: soubor nebyl nalezen nebo počáteční disk RAM** nebo **soubor image initrd/initramfs nebyl nalezen**, postupujte podle následujících kroků.

    * Pro chybějící soubor `/boot/grub2/grub.cfg` nebo `initrd/initramfs` pokračujte následujícím procesem:

    1. Ujistěte se `/etc/default/grub` , že existuje a má správné nebo požadované nastavení. Pokud nevíte, které výchozí nastavení máte, můžete se podívat na funkční virtuální počítač.

    2. Potom spuštěním následujícího příkazu znovu vygenerujte svou konfiguraci: `grub2-mkconfig -o /boot/grub2/grub.cfg`

   * Pokud je chybějící soubor `/boot/grub/menu.lst` , tato chyba je určena pro starší verze operačního systému (**RHEL 6. x**, **CentOS 6. x** a **Ubuntu 14,04**), takže se příkazy mohou lišit. Abyste měli jistotu, že jsou k dispozici správné příkazy, budete muset spustit starý server a otestovat test.

### <a name="error---no-such-partition"></a>Chyba – žádný takový oddíl

* Pokud se zobrazí chyba, že **Tento oddíl není**k dispozici, při pokusu o prodloužení jednotky operačního systému se při pokusu o [spuštění virtuálního počítače pořiďte v části scénář "žádný takový oddíl"](/archive/blogs/shwetanayak/case-scenario-no-such-partition-error-while-trying-to-start-the-vm-after-attempting-to-extend-the-os-drive).

### <a name="error---grubcfg-file-not-found"></a>Chyba: soubor grub. cfg se nenašel.

* Pokud se vám zobrazuje chyba **/boot/grub2/grub.cfg soubor nebyl nalezen**, postupujte podle následujících kroků.

    * Pro chybějící soubor `/boot/grub2/grub.cfg` nebo `initrd/initramfs` pokračujte následujícím procesem:

    1. Ujistěte se `/etc/default/grub` , že existuje a má správné nebo požadované nastavení. Pokud nevíte, které výchozí nastavení máte, můžete se podívat na funkční virtuální počítač.

    2. Potom spuštěním následujícího příkazu znovu vygenerujte svou konfiguraci: `grub2-mkconfig -o /boot/grub2/grub.cfg` .

   * Pokud je chybějící soubor `/boot/grub/menu.lst` , tato chyba je určena pro starší verze operačního systému (**RHEL 6. x**, **CentOS 6. x** a **Ubuntu 14,04**), takže příkazy mohou odložit. Uvolněte původní server a otestujte ho, abyste zajistili, že jsou k dispozici správné příkazy.

## <a name="next-steps"></a>Další kroky

* [Přehled agenta virtuálního počítače Azure](../extensions/agent-windows.md)
* [Rozšíření a funkce virtuálních počítačů pro Windows](../extensions/features-windows.md)

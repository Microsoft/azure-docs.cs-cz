---
title: Poradce při potížích s chybami při spuštění ve virtuálních počítačích Azure Linux | Dokumenty společnosti Microsoft
description: Tento článek pomáhá propojit s články k řešení chyb při spuštění ve virtuálních počítačích Azure Linux.
services: virtual-machines-linux
documentationCenter: ''
author: vilibert
manager: spogge
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2019
ms.author: vilibert
ms.openlocfilehash: 37cb201751f72918838efe5837aa0e357d483f24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74408739"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Poradce při potížích s chybami při zavádění virtuálních počítačů Azure Linux

Tento článek uvádí běžné chyby při spuštění systému Linux virtuální počítač (VM) v Microsoft Azure. Další informace o chybách naleznete v článcích v části **Chyby a řešení spouštění.**

## <a name="boot-errors-and-solutions"></a>Chyby a řešení zaváděcích systémů

* [Záchrana GRUB](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Další kroky

- [Konzola sériového portu virtuálního zařízení](serial-console-linux.md)

Poradce při potížích s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí Azure:

- [Oprava virtuálních počítačů Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Disk Swap – to lze automatizovat pomocí buď:
- [Skripty pro obnovení prostředí Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [bash Zotavení Skripty](https://github.com/sribs/azure-support-scripts)

- [Cli](troubleshoot-recovery-disks-linux.md)
- [Portál Azure](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video pro výměnu disku:

Pokud nemáte přístup k GRUB podívejte se na [toto](https://youtu.be/m5t0GZ5oGAc) video a uvidíte, jak můžete snadno automatizovat postup odkládání disků obnovit virtuální počítač

## <a name="unofficial-solution"></a>Neoficiální řešení

Obnovení virtuálního virtuálního míse se může také pokusit pomocí nepodporovaného beta [skriptu ALAR](https://github.com/malachma/azure-auto-recover)
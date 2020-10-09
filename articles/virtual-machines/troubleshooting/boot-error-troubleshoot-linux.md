---
title: Řešení chyb při spuštění v Azure Linux Virtual Machines | Microsoft Docs
description: Tento článek vám pomůže s odkazy na články, které vám pomůžou vyřešit chyby při spuštění v Azure Linux Virtual Machines.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74408739"
---
# <a name="troubleshoot-azure-linux-virtual-machines-boot-errors"></a>Řešení potíží s chybami při spuštění Azure Linux Virtual Machines

V tomto článku jsou uvedené běžné chyby při spuštění, které se mohou zobrazit při spuštění virtuálního počítače se systémem Linux v nástroji Microsoft Azure. Další informace o chybách najdete v článcích v části **chyby spuštění a řešení** .

## <a name="boot-errors-and-solutions"></a>Chyby a řešení spouštění

* [GRUB zachránit](troubleshoot-vm-boot-error.md)

## <a name="next-steps"></a>Další kroky

- [Sériová konzola virtuálních počítačů](serial-console-linux.md)

Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure:

- [Oprava virtuálního počítače Azure](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)

 Prohození disku – tuto možnost lze automatizovat pomocí těchto akcí:
- [Skripty pro obnovení Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
- [Skripty pro obnovení bash](https://github.com/sribs/azure-support-scripts)

- [Rozhraní příkazového řádku](troubleshoot-recovery-disks-linux.md)
- [Azure Portal](troubleshoot-recovery-disks-portal-linux.md)


## <a name="disk-swap-video"></a>Video o prohození disku:

Pokud nemáte přístup k GRUB, podívejte se na [Toto](https://youtu.be/m5t0GZ5oGAc) video a podívejte se, jak můžete snadno automatizovat postup odkládacího disku pro obnovení virtuálního počítače.

## <a name="unofficial-solution"></a>Neoficiální řešení

Pokus o obnovení virtuálního počítače se taky dá provést s nepodporovaným skriptem BETA [ALAR](https://github.com/malachma/azure-auto-recover)
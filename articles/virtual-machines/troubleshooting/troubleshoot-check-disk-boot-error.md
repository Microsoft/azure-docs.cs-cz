---
title: Kontrola systému souborů při zavádění virtuálního počítače Azure| Dokumenty společnosti Microsoft
description: Přečtěte si, jak vyřešit problém, že virtuální montovna při spuštění zobrazuje systém souborů Kontrola kontroly| Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 86938c582745cb0759eda9cd0693f407471a0529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921483"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows zobrazuje "kontrola systému souborů" při zavádění virtuálního počítače Azure

Tento článek popisuje chybu "Kontrola systému souborů", ke které se může dojít při spuštění virtuálního počítače (VM) Windows v Microsoft Azure.


## <a name="symptom"></a>Příznak 

Virtuální ho virtuálního mísa systému Windows se nespustí. Když zkontrolujete spouštěcí snímky obrazovky v [diagnostice spouštění](boot-diagnostics.md), zjistíte, že proces Kontrola disku (chkdsk.exe) běží s jednou z následujících zpráv:

- Skenování a opravy jednotky (C:)
- Kontrola systému souborů na C:

## <a name="cause"></a>Příčina

Pokud je v systému souborů nalezena chyba systému souborů NTFS, systém Windows při příštím restartování zkontroluje a opraví konzistenci disku. Obvykle k tomu dochází, pokud virtuální počítač měl nějaké neočekávané restartování nebo pokud proces vypnutí virtuálního počítače byl náhle přerušen.

## <a name="solution"></a>Řešení 

Systém Windows se spustí normálně po dokončení procesu kontroly disku. Pokud se virtuální počítač zasekl v procesu kontrola disku, zkuste spustit kontrolní disk na virtuálním počítači offline:
1.  Pořiďte snímek disku operačního systému ovlivněného virtuálního počítače jako zálohu. Další informace naleznete [v tématu Snímek disku](../windows/snapshot-copy-managed-disk.md).
2.  [Připojte disk operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na virtuálním počítači pro obnovení spusťte zkontrolovat disk na připojeném disku operačního systému. V následující ukázce je písmeno ovladače připojeného disku operačního systému E: 
        
        chkdsk E: /f
4.  Po dokončení zkontrolovat disk odpojte disk od virtuálního počítače pro obnovení a znovu připojte disk k ovlivněnému virtuálnímu počítače jako disk operačního systému. Další informace naleznete [v tématu Poradce při potížích s virtuálním počítačem se systémem Windows připojením disku operačního systému k virtuálnímu virtuálnímu počítače pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

---
title: Kontroluje se systém souborů při spouštění virtuálního počítače Azure | Microsoft Docs
description: Přečtěte si, jak vyřešit problém, který virtuální počítač při spouštění spouští, a při spuštění systému souborů | Microsoft Docs
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
ms.openlocfilehash: ba3138076da89610bef878583e424153594c5dcc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75981374"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Windows při spuštění virtuálního počítače Azure zobrazuje "kontrolu systému souborů"

Tento článek popisuje chybu "Kontrola systému souborů", se kterou se můžete setkat při spuštění virtuálního počítače s Windows v Microsoft Azure.

> [!NOTE] 
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu nasazení Správce prostředků, který doporučujeme použít pro nová nasazení místo modelu nasazení Classic.

## <a name="symptom"></a>Příznak 

Virtuální počítač s Windows se nespustí. Když zkontrolujete spouštěcí snímky obrazovky v [diagnostice spouštění](boot-diagnostics.md), uvidíte, že proces kontroly disku (Chkdsk. exe) je spuštěný s jednou z následujících zpráv:

- Kontrola a oprava jednotky (C:)
- Kontroluje se systém souborů na jednotce C:

## <a name="cause"></a>Příčina

Pokud se v systému souborů najde chyba NTFS, systém Windows zkontroluje a opraví konzistenci disku při příštím restartování. K tomu obvykle dochází v případě, že virtuální počítač měl nějaké neočekávané restartování nebo pokud byl proces vypnutí virtuálního počítače prudkě přerušený.

## <a name="solution"></a>Řešení 

Systém Windows se spustí normálně po dokončení procesu kontroly disku. Pokud je virtuální počítač zablokovaný v procesu kontroly disku, zkuste na virtuálním počítači spustit kontrolní disk offline:
1.  Pořídit snímek disku s operačním systémem ovlivněného virtuálního počítače jako zálohy. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).
2.  [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na virtuálním počítači pro obnovení spusťte kontrolu disku na připojeném disku s operačním systémem. V následující ukázce je písmeno připojeného disku s operačním systémem E: 
        
        chkdsk E: /f
4.  Po dokončení kontroly disku odpojte disk od virtuálního počítače pro obnovení a pak tento disk znovu připojte k ovlivněnému virtuálnímu počítači jako disk s operačním systémem. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

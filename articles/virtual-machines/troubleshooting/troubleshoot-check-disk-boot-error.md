---
title: Kontrola systému souboru při spuštění virtuálního počítače Azure | Dokumentace Microsoftu
description: Zjistěte, jak vyřešit problém, že virtuální počítač zobrazí systému souborů kontroluje při dalším spuštění | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 91285fd3bf42158068040833146377cb6c48e4e8
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413386"
---
# <a name="windows-shows-checking-file-system-when-booting-an-azure-vm"></a>Ukazuje Windows "Kontrola systému souborů" při dalším spuštění virtuálního počítače Azure

Tento článek popisuje chybu "Kontrola systému souborů", které můžete narazit při spuštění Windows virtuální počítač (VM) v Microsoft Azure.

> [!NOTE] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje pomocí modelu nasazení Resource Manageru, který vám doporučujeme používat pro nová nasazení namísto modelu nasazení classic.

## <a name="symptom"></a>Příznak 

Virtuální počítač Windows nespustí. Když vrátíte se změnami na snímcích obrazovky spouštěcí [Diagnostika spouštění](boot-diagnostics.md), uvidíte, že proces zkontrolovat Disk (chkdsk.exe) je spuštěn s jedním z následujících zpráv:

- Kontrola a oprava disk (C:)
- Kontrola systému souborů na jednotce C:

## <a name="cause"></a>Příčina

Pokud je nalezena chyba systému souborů NTFS v systému souborů, bude Windows zkontrolujte a opravte konzistence disku při příštím restartování. K tomu obvykle dochází Pokud libovolný neočekávaného restartování virtuálního počítače, nebo pokud proces vypnutí virtuálního počítače bylo náhle přerušeno.

## <a name="solution"></a>Řešení 

Windows se spustí normálně po dokončení procesu zkontrolovat Disk. Pokud virtuální počítač se zasekla v automatickém procesu zkontrolujte disku, zkuste spustit zkontrolovat Disk ve virtuálním počítači v režimu offline:
1.  Pořízení snímku disku s operačním systémem virtuálního počítače ovlivněný jako záložní. Další informace najdete v tématu [pořízení snímku disku](../windows/snapshot-copy-managed-disk.md).
2.  [Připojte disk s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).  
3.  Na virtuální počítač pro obnovení spusťte zkontrolovat Disk na připojeném disku s operačním systémem. V následujícím příkladu písmeno jednotky z připojeného disku s operačním systémem je E: 
        
        chkdsk E: /f
4.  Po dokončení zkontrolujte Disk odpojit disk od virtuální počítač pro obnovení a znovu připojte disk k virtuálnímu počítači ovlivněné jako disk s operačním systémem. Další informace najdete v tématu [odstranění virtuálního počítače s Windows pomocí připojení disku s operačním systémem pro virtuální počítač pro obnovení](troubleshoot-recovery-disks-portal-windows.md).

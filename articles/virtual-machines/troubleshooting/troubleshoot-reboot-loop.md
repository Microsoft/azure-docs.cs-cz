---
title: Windows restartovat smyčku ve Virtuálním počítači Azure | Dokumentace Microsoftu
description: Zjistěte, jak řešit Windows restartovací smyčce | Dokumentace Microsoftu
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 693f28c04be5cd0acf1d5face2630a3f6d62328c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380953"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Smyčka restartování Windows na Virtuálním počítači Azure
Tento článek popisuje restartovací smyčce, může se stát na Windows virtuální počítač (VM) v Microsoft Azure.

## <a name="symptom"></a>Příznak

Při použití [Diagnostika spouštění](./boot-diagnostics.md) zobrazíte na snímcích obrazovky virtuálního počítače zjistíte, že spuštění virtuálního počítače, ale získání přerušení procesu spouštění a proces se spouští nad.

![Na obrazovce Start 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Příčina

Restartovací smyčce nastává z důvodu následujících důvodů:

### <a name="cause-1"></a>Příčiny 1

Služba třetí strany, který je označený jako kritický a nelze ji proto spustit. To způsobí, že operační systém.

### <a name="cause-2"></a>Příčiny 2

Některé změny byly provedeny v operačním systému. Obvykle se týkají instalace aktualizace, instalace aplikací nebo nové zásady. Možná budete muset zkontrolovat následující protokoly pro další podrobnosti:

- Protokoly událostí
- CBS.logWindows
- Protokolu

### <a name="cause-3"></a>Příčina 3

To může být způsobeno poškození systému souborů. Je však obtížné diagnostikovat a identifikovat změny, které způsobí poškození operačního systému.

## <a name="solution"></a>Řešení

Chcete-li vyřešit tento problém [zálohování disku s operačním systémem](../windows/snapshot-copy-managed-disk.md), a [připojení disku s operačním systémem k zachránit VM](../windows/troubleshoot-recovery-disks-portal.md)a pak postupujte podle možnosti řešení odpovídajícím způsobem, nebo zkuste řešení jeden po druhém.

### <a name="solution-for-cause-1"></a>Řešení příčiny 1

1. Jakmile disk s operačním systémem je připojen k funkčním virtuálním počítači, ujistěte se, že disk je označený jako **Online** v nástroji Správa disků konzoly a poznamenejte si písmeno jednotky oddílu, který obsahuje **\Windows** složky.

2. Pokud disk je nastavená na **Offline**, nastavte ji na **Online**.

3. Vytvoření kopie **\Windows\System32\config** složku v případě potřeby vrácení zpět na změny.

4. Na stanici virtuálního počítače otevřete Editor registru Windows (regedit).

5. Vyberte **HKEY_LOCAL_MACHINE** klíče a pak vyberte **souboru** > **načíst Hive** z nabídky.

6. Přejděte k souboru systému v **\Windows\System32\config** složky.

7. Vyberte **otevřít**, typ **BROKENSYSTEM** názvu, rozbalte **HKEY_LOCAL_MACHINE** klíč a pak se zobrazí další klíč s názvem **BROKENSYSTEM** .

8. Zkontrolujte, které ControlSet spouštění z počítače. Zobrazí se jeho číslo klíče v následujícím klíči registru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Kontrola, což je závažnost služby agenta virtuálního počítače pomocí následujícího klíče registru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Pokud není nastavena hodnota klíče registru **2**, pak přejděte na další omezení rizik.

11. Pokud hodnota klíče registru je nastavena na **2**, pak změňte hodnotu z **2** k **1**.

12. Pokud některý z následujících klíčů neexistuje a mají hodnotu **2** nebo **3**a potom změňte hodnoty **1** odpovídajícím způsobem:

  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
  - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Vyberte **BROKENSYSTEM** klíče a pak vyberte **souboru** > **načíst Hive** z nabídky.

14. Odpojte disk s operačním systémem od virtuálního počítače pro řešení potíží.

15. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte asi 2 minuty, Azure k uvolnění tohoto disku.

16. [Vytvoření nového virtuálního počítače z disku s operačním systémem](../windows/create-vm-specialized.md).

17. Pokud je problém vyřešen, pak je třeba přeinstalovat systém [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Řešení příčiny 2

Obnovení virtuálního počítače k poslední známé platné konfigurace, postupujte podle kroků v [spuštění virtuálního počítače Windows Azure s poslední známá funkční konfigurace](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Řešení pro příčina 3

1. Jakmile disk je připojený k řešení potíží virtuální počítač, ujistěte se, že disk je označený jako **Online** v konzole Správa disků.

2. Vytvoření kopie **\Windows\System32\config** složku v případě potřeby vrácení zpět na změny.

3. Kopírování souborů v **\Windows\System32\config\regback** složky a nahradit soubory v **\Windows\System32\config** složky.

4. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte asi 2 minuty, Azure k uvolnění tohoto disku.

5. [Vytvoření nového virtuálního počítače z disku s operačním systémem](../windows/create-vm-specialized.md).

>[!NOTE]
>Následující postup by měla sloužit pouze jako poslední prostředek. Při obnovení z regback může obnovit přístup k počítači, operační systém není považovat za stabilní vzhledem k tomu, že se data ztratí v registru mezi časové razítko hive a aktuální den. Potřebujete k vytvoření nového virtuálního počítače a jejich migrovat data.

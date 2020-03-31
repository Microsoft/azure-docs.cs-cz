---
title: Smyčka restartování Windows na virtuálním počítači Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak řešit potíže s restartováním systému Windows | Dokumenty společnosti Microsoft
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443581"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Smyčka restartování Windows na virtuálním počítači Azure
Tento článek popisuje smyčku restartování, ke které může dojít na virtuálním počítači (VM) Windows v Microsoft Azure.

## <a name="symptom"></a>Příznak

Když použijete [diagnostiku spuštění](./boot-diagnostics.md) k získání snímků obrazovky virtuálního počítače, zjistíte, že se virtuální počítač spouští, ale proces spouštění se přerušuje a proces začíná znovu.

![Úvodní obrazovka 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Příčina

K restartování smyčky dochází z důvodu následujících příčin:

### <a name="cause-1"></a>Příčina 1

Existuje služba jiného výrobce, která je označena jako kritická a nelze ji spustit. To způsobí, že operační systém restartovat.

### <a name="cause-2"></a>Příčina 2

V operačním systému byly provedeny některé změny. Obvykle se jedná o instalaci aktualizace, instalaci aplikace nebo nové zásady. Další podrobnosti bude pravděpodobně potřeba zkontrolovat v následujících protokolech:

- Protokoly událostí
- CBS.logWindows
- Aktualizovat.log

### <a name="cause-3"></a>Příčina 3

Mohlo by to způsobit poškození systému souborů. Je však obtížné diagnostikovat a identifikovat změnu, která způsobuje poškození operačního systému.

## <a name="solution"></a>Řešení

Chcete-li tento problém vyřešit, [zálohujte disk operačního systému](../windows/snapshot-copy-managed-disk.md)a [připojte disk operačního systému k záchrannému virtuálnímu počítače](../windows/troubleshoot-recovery-disks-portal.md)a podle toho postupujte podle možností řešení nebo vyzkoušejte řešení jeden po druhém.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Jakmile je disk operačního systému připojen k funkčnímu virtuálnímu počítače, ujistěte se, že je v konzole pro správu disků označen jako **online,** a poznamenejte si písmeno jednotky oddílu, který obsahuje složku **\Windows.**

2. Pokud je disk nastaven na **offline**, nastavte jej na **možnost Online**.

3. Vytvořte kopii složky **\Windows\System32\config** v případě, že je potřeba vrátit změny zpět.

4. Na záchranném virtuálním počítači otevřete Editor registru systému Windows (regedit).

5. Vyberte klávesu **HKEY_LOCAL_MACHINE** a pak z nabídky vyberte**Podregistr načtení** **souboru.** > 

6. Přejděte do souboru SYSTEM ve složce **\Windows\System32\config.**

7. Vyberte **otevřít**, zadejte příkaz **BROKENSYSTEM,** rozbalte **HKEY_LOCAL_MACHINE** klíč a pak se zobrazí další klíč s názvem **BROKENSYSTEM**.

8. Zkontrolujte, ze kterého ovládacího prvkuNastavte, ze kterého je počítač spuštěn. Číslo klíče uvidíte v následujícím klíči registru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Zkontrolujte, která je kritičnost služby agenta virtuálního soudu prostřednictvím následujícího klíče registru.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Pokud hodnota klíče registru není nastavena na **hodnotu 2**, přejděte k dalšímu zmírnění.

11. Pokud je hodnota klíče registru nastavena na **hodnotu 2**, změňte hodnotu z **2** na **1**.

12. Pokud některý z následujících klíčů existují a mají hodnotu **2** nebo **3**, a potom změňte tyto hodnoty na **1** odpovídajícím způsobem:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Vyberte klávesu **BROKENSYSTEM** a v nabídce **vyberte** > **File Unload Hive.**

14. Odpojte disk operačního systému od virtuálního počítače pro řešení potíží.

15. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte přibližně 2 minuty, než Azure tento disk uvolní.

16. [Vytvořte nový virtuální virtuální počítače z disku operačního systému](../windows/create-vm-specialized.md).

17. Pokud je problém vyřešen, bude pravděpodobně nutné přeinstalovat [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Obnovte virtuální počítač na poslední známou funkční konfiguraci, postupujte podle pokynů v části [Jak spustit virtuální počítač Azure Windows s poslední známou funkční konfigurací](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Řešení pro příčinu 3
>[!NOTE]
>Následující postup by měl být použit pouze jako poslední prostředek. Při obnovení z regback může obnovit přístup k počítači, operační systém není považován za stabilní, protože je ztracena data v registru mezi časové razítko podregistru a aktuální den. Musíte vytvořit nový virtuální hod a vytvořit plány pro migraci dat.

1. Jakmile je disk připojen k virtuálnímu počítače pro řešení potíží, ujistěte se, že je v konzole pro správu disků označen jako **online.**

2. Vytvořte kopii složky **\Windows\System32\config** v případě, že je potřeba vrátit změny zpět.

3. Zkopírujte soubory ve složce **\Windows\System32\config\regback** a nahraďte soubory ve složce **\Windows\System32\config.**

4. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte přibližně 2 minuty, než Azure tento disk uvolní.

5. [Vytvořte nový virtuální virtuální počítače z disku operačního systému](../windows/create-vm-specialized.md).



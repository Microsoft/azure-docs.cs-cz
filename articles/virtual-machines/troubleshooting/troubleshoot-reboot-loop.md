---
title: Smyčka restartování Windows na virtuálním počítači Azure | Microsoft Docs
description: Přečtěte si, jak řešit potíže s restartováním systému Windows | Microsoft Docs
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
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629636"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Smyčka restartování Windows na virtuálním počítači Azure
Tento článek popisuje smyčku restartování, se kterou se můžete setkat na virtuálním počítači s Windows v Microsoft Azure.

## <a name="symptom"></a>Příznak

Když použijete [diagnostiku spouštění](./boot-diagnostics.md) k získání snímků obrazovky virtuálního počítače, zjistíte, že se virtuální počítač spouští, ale proces spouštění se přerušil a proces se spouští.

![Úvodní obrazovka 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Příčina

K následujícím příčinám je restartování smyčky.

### <a name="cause-1"></a>Příčina 1

Existuje služba třetí strany, která je označena jako kritická a nelze ji spustit. To způsobí, že se operační systém restartuje.

### <a name="cause-2"></a>Příčina 2

V operačním systému byly provedeny nějaké změny. Obvykle se vztahují k instalaci aktualizací, instalaci aplikací nebo nové zásadě. Možná budete muset vyhledat další podrobnosti v následujících protokolech:

- Protokoly událostí
- CBS. logWindows
- Update. log

### <a name="cause-3"></a>Příčina 3

Může to způsobit poškození systému souborů. Je ale obtížné diagnostikovat a identifikovat změnu, která způsobuje poškození operačního systému.

## <a name="solution"></a>Řešení

> [!TIP]
> Pokud máte nedávno zálohovaný virtuální počítač, můžete zkusit [obnovit virtuální počítač ze zálohy](../../backup/backup-azure-arm-restore-vms.md) a opravit problém při spouštění.

Pokud chcete tento problém vyřešit, [zazálohujte disk s operačním systémem](../windows/snapshot-copy-managed-disk.md)a [Připojte disk s operačním systémem k záchrannému virtuálnímu počítači](./troubleshoot-recovery-disks-portal-windows.md)a podle toho, jaké možnosti řešení chcete použít, nebo zkuste řešení jednu po jedné.

### <a name="solution-for-cause-1"></a>Řešení 1. příčiny

1. Po připojení disku s operačním systémem k pracovnímu virtuálnímu počítači se ujistěte, že je disk označený jako **online** v konzole pro správu disků, a poznamenejte si písmeno oddílu, který obsahuje složku **\Windows** .

2. Pokud je disk nastavený na **offline**, nastavte ho na **online**.

3. Vytvořte kopii složky **\Windows\System32\config** v případě, že je nutné vrátit zpět změny.

4. Na záchranném virtuálním počítači otevřete Editor registru systému Windows (Regedit).

5. Vyberte **HKEY_LOCAL_MACHINE** klíč a v nabídce vyberte možnost  >  **načíst** soubor.

6. Přejděte k SYSTÉMOVÉmu souboru ve složce **\Windows\System32\config** .

7. Vyberte **otevřít**, jako název zadejte **BROKENSYSTEM** , rozbalte klíč **HKEY_LOCAL_MACHINE** a potom se zobrazí další klíč s názvem **BROKENSYSTEM**.

8. Ověřte, ze kterého ControlSet počítač spouští. V následujícím klíči registru se zobrazí jeho číslo klíče.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Pomocí následujícího klíče registru ověřte, která z nich je kritická pro službu agenta virtuálního počítače.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Pokud hodnota klíče registru není nastavená na hodnotu **2**, pokračujte na další zmírnění.

11. Pokud je hodnota klíče registru nastavená na hodnotu **2**, změňte hodnotu z **2** na **1**.

12. Pokud existuje některý z následujících klíčů, které mají hodnotu **2** nebo **3**, a pak tyto hodnoty změňte na **1** odpovídající:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Vyberte klíč **BROKENSYSTEM** a pak z nabídky vyberte **soubor**  >  **Uvolnit podregistr** .

14. Odpojte disk s operačním systémem od virtuálního počítače pro řešení potíží.

15. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte 2 minuty, než Azure uvolní tento disk.

16. [Vytvořte nový virtuální počítač z disku s operačním systémem](../windows/create-vm-specialized.md).

17. Pokud je problém vyřešen, bude pravděpodobně nutné přeinstalovat [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Řešení 2. příčiny

Obnovte virtuální počítač do poslední známé funkční konfigurace, postupujte podle kroků v tématu [Jak spustit virtuální počítač Azure s Windows s poslední známou funkční konfigurací](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Řešení pro příčinu 3
>[!NOTE]
>Následující postup by se měl použít jenom jako poslední prostředek. I když obnovení z Regback může obnovit přístup k počítači, operační systém není považován za stabilní, protože v registru dojde ke ztrátě dat mezi časovým razítkem podregistru a aktuálním dnem. Musíte vytvořit nový virtuální počítač a vytvořit plány pro migraci dat.

1. Jakmile je disk připojený k virtuálnímu počítači pro řešení potíží, ujistěte se, že je disk označený jako **online** v konzole pro správu disků.

2. Vytvořte kopii složky **\Windows\System32\config** v případě, že je nutné vrátit zpět změny.

3. Zkopírujte soubory ve složce **\Windows\System32\config\regback** a nahraďte soubory ve složce **\Windows\System32\config** .

4. Odeberte disk z virtuálního počítače pro řešení potíží a počkejte 2 minuty, než Azure uvolní tento disk.

5. [Vytvořte nový virtuální počítač z disku s operačním systémem](../windows/create-vm-specialized.md).

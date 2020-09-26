---
title: Upgrade virtuálního počítače Azure pomocí SUSE Linux Enterprise Server na SUSE 15 SP1 | Microsoft Docs
description: Tento článek popisuje obecné kroky, jak použít systém migrace SUSE pro upgrade systému SUSE Linux Enterprise Server na SUSE 15 SP1 pro virtuální počítač Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91359507"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Upgrade virtuálního počítače Azure s SLES 12 až SLES 15 SP1

Tento článek popisuje obecné kroky, jak upgradovat SUSE Linux Enterprise Server (SLES) 12 na SLES 15 SP1 pro virtuální počítač Azure (VM). Další informace najdete v tématu použití Průvodce upgradem [SUSE pro distribuci systému](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) a [příručky k upgradu SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Podporované možnosti upgradu
Aby bylo možné pokračovat v SLES 15 SP1, musí být aktuální verze SLES SLES 12 SP4 nebo 12 SP5.

![Snímek obrazovky o podporované cestě upgradu](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Požadavky

- Naplánujte aktivitu migrace v rámci schváleného okna prostoje. Důvodem je to, že během migrace se virtuální počítač restartuje.
- Před aktivitou migrace proveďte kompletní zálohu virtuálního počítače.
- Pokud zálohování není nakonfigurované, vytvořte snímek zálohy disku s operačním systémem.
- [Ověřte, jestli virtuální počítač má generaci v1 nebo Generation v2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Upgrade z SUSE 12 SP4 nebo SP5 na SUSE 15 SP1

1. Nainstalujte si nejnovější balíček pro virtuální počítač:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Po dokončení instalace restartujte virtuální počítač.

3. Ověřte jádro a verzi operačního systému. Ujistěte se, že je verze SUSE 12 SP4 nebo SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Nainstalujte **SUSE-Migration-sle15-Activation**. Po instalaci balíčku **SUSE-Migration-sle15-Activation** se do balíčku závislostí automaticky nainstaluje jiný balíček **SLES15 – migrace** . 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Po dokončení instalace spusťte `reboot` příkaz pro restartování virtuálního počítače.

6. Otevřete [Azure Portal](https://portal.azure.com), vyberte virtuální počítač a potom vyberte **sériová konzola**. Uvidíte, že se systém zastaví při restartu: restartování systému. Tento proces by měl trvat přibližně 15-45 minut. U virtuálního počítače 2. generace se může zablokovat na obrazovce restartovat počítač: restartování systému. V takovém případě počkejte na 45 minut. Pokud stále ještě nepostupuje, v Azure Portal na virtuálním počítači otevřete stránku **Přehled** , zastavte virtuální počítač a pak ho znovu spusťte.

     ![Snímek obrazovky se zprávami v konzole sériového portu](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Po restartování systému s novým jádrem se zobrazí následující zpráva.

     ![Snímek obrazovky se zprávami v konzole sériového portu po restartování systému s novým jádrem](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Ověřte jádro a verzi operačního systému, abyste zkontrolovali, jestli se systém úspěšně Upgradoval.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Ověření verze generování virtuálního počítače

K ověření verze generování můžete použít jednu z následujících metod:

- V terminálu SLES spusťte příkaz `dmidecode | grep -i hyper` . Pokud se jedná o virtuální počítač generace V1, nevrátí se žádný výstup. U virtuálních počítačů generace v2 se zobrazí následující výstup:

     ![Snímek obrazovky s výstupem pro virtuální počítač 2. generace](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- V [Azure Portal](https://portal.azure.com)přejít na **vlastnosti**  virtuálního počítače a potom zkontrolujte pole **generování virtuálního počítače** .
